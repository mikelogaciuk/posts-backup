---
layout: post
title: Prefect OSS jako PoC na Kubernetesie
date: 2023-07-07
category:
  ["sysops", "devops", "kubernetes", "prefect", "dataops", "python", "poc"]
---

![pic](/img/prefect-cloud-dashboard.png)

- [Wstęp](#wstęp)
- [Wersje](#wersje)
- [Dockerfile](#dockerfile)
- [Kerberos](#kerberos)
- [Postgres](#postgres)
- [Kubernetes](#kubernetes)
  - [API](#api)
  - [Ingress](#ingress)
  - [Sekrety](#sekrety)
  - [Workery](#workery)
- [Zależności](#zależności)
- [Rakefile](#rakefile)
- [Wdrożenie](#wdrożenie)
  - [Secrety oraz API](#secrety-oraz-api)
  - [CLI](#cli)
  - [Startujemy workery](#startujemy-workery)
- [Produkcja](#produkcja)
- [Wnioski](#wnioski)

## Wstęp

Prefect to platforma do orkestracji zadań napisana w całości w Pythonie, która umożliwia Nam tworzenie, obserwowanie i reagowanie na potoki danych.

Po za powyższymi, Perfect może służyć nawet do automatyzacji zadań, takich jak uruchamianie testów, wdrażanie oprogramowania i zarządzanie infrastrukturą oraz do budowania aplikacji opartych na sztucznej inteligencji, takich jak modele uczenia maszynowego czy zwyczajne systemy rekomendacyjne.

## Wersje

Prefect jest dostępny w dwóch wersjach:

- **Prefect Cloud**: Pełna i płatna wersja Prefect'a z funkcjonalnością niedostępną w wersji OSS np. RBAC. Wersja cloud może pracować w konfiguracji hybrydowej (w tym przypadku agenty uruchamiamy bezpośrednio we własnej infrastrukturze).
- **Prefect Open Source**: Jak nazwa wskazuje, darmowa edycja opensource z pewnymi ograniczeniami. W tym całkowity brak kontroli użytkowników. Taki sam zabieg zresztą stosuje konkurent tj Dagster.

By uruchomić Prefect'a na Naszym klastrze na potrzeby PoC'owe w trybie 'jakotako' wypadało by chociaż ograniczyć dostęp do UI. To możemy zrobić z pomocą ingressa pod postacią Nginx'a.

## Dockerfile

Na początek tworzymy `Dockerfile`, zwłaszcza, że zapewne będziemy chcieli przenosić dane np. z Oracle SQL do Microsoft SQL Server. Więc warto za wczasu dodać sterowniki obydwu producentów.

```shell
mkdir -p repos/prefect && cd prefect
touch dockerfile docker-compose.yml prefect-api.yml prefect-agents.yml prefect-ingress.yml prefect-config.yml krb5.conf requirements_build.txt rakefile
bundle init
code dockerfile
```

Następnie edytujemy image wedle własnego uznania lub np. jak poniżej:

```dockerfile
FROM prefecthq/prefect:2.10.20-python3.10 AS build

RUN echo "Europe/Warsaw" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata

RUN apt update

RUN DEBIAN_FRONTEND=noninteractive apt-get -yq install krb5-user libgssapi-krb5-2
RUN apt install -yq --no-install-recommends \
    curl gnupg cron wget unzip cifs-utils \
    build-essential python3-dev unixodbc-dev freetds-dev \
    libmariadb-dev libsasl2-dev libaio1 htop \
    unixodbc odbcinst

RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
CMD ["source", "~/.bashrc"]

RUN wget https://packages.microsoft.com/debian/11/prod/pool/main/m/msodbcsql17/msodbcsql17_17.10.5.1-1_amd64.deb
RUN wget https://packages.microsoft.com/debian/11/prod/pool/main/m/msodbcsql18/msodbcsql18_18.3.2.1-1_amd64.deb
RUN ACCEPT_EULA=Y dpkg -i msodbcsql17_17.10.5.1-1_amd64.deb
RUN ACCEPT_EULA=Y dpkg -i msodbcsql18_18.3.2.1-1_amd64.deb

WORKDIR /opt/oracle
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    && wget https://download.oracle.com/otn_software/linux/instantclient/instantclient-basiclite-linuxx64.zip \
    && unzip instantclient-basiclite-linuxx64.zip \
    && rm -f instantclient-basiclite-linuxx64.zip \
    && cd /opt/oracle/instantclient* \
    && rm -f *jdbc* *occi* *mysql* *README *jar uidrvci genezi adrci \
    && echo /opt/oracle/instantclient* > /etc/ld.so.conf.d/oracle-instantclient.conf \
    && ldconfig \
    && apt-get autoremove -yqq --purge \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /

COPY requirements_build.txt /
RUN pip install --no-cache-dir -r /requirements_build.txt

RUN apt-get autoremove -yqq --purge \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY config/krb5/krb5.conf /etc/
```

**Uwaga**: Plik na potrzeby artykułu nie jest zoptymalizowany. Brak w nim choćby stagingu, przez co jego rozmiar wynosi około jednego gigabajta.

Następnie uzupełniamy compose'a:

```yaml
# For image building purposes only

version: "3"

services:
  prefect-image:
    build:
      context: .
      tags: [internal-cr:9999/prefect-core:latest]
    image: prefect/core
```

## Kerberos

Uzupełniamy plik `krb5.conf` podmieniając wpisy dla 'company.local' na Nasze własne wpisy, lub ignorujemy:

```conf
[libdefaults]
        default_realm = company.local

# The following krb5.conf variables are only for MIT Kerberos.
        kdc_timesync = 1
        ccache_type = 4
        forwardable = true
        proxiable = true
        rdns = false


# The following libdefaults parameters are only for Heimdal Kerberos.
        fcc-mit-ticketflags = true

[realms]
        company.local = {
                kdc = datacenter01.company.local
                admin_server = datacenter01.company.local
        }

[domain_realm]
        .company.local = COMPANY.LOCAL
```

## Postgres

Zakładam, że już i tak masz gdzieś wystawionego Postgres'a. Tym samym część dot. jego wystawienia zupełnie pominę.

Potrzebujemy jedynie nowej bazy i użytkownika wraz z hasłem pod Nasze API.

## Kubernetes

### API

Teraz, powinniśmy zająć się Naszym statefulsetem dla samego API.

Zaczynami od edycji `prefect-api.yml`:

```shell
code prefect-api.yml
```

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: prefect-api
  namespace: prefect
spec:
  selector:
    matchLabels:
      app: prefect-api # has to match .spec.template.metadata.labels
  serviceName: "prefect-api-headless"
  replicas: 1 # by default is 1
  template:
    metadata:
      labels:
        app: prefect-api # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      imagePullSecrets:
        - name: regcred ## Our internal container registry config
      containers:
        - name: prefect-api
          image: internal-cr:9999/prefect-core:latest
          command:
            [
              "prefect",
              "server",
              "start",
              "--host",
              "0.0.0.0",
              "--port",
              "4200",
              "--log-level",
              "WARNING",
            ]
          imagePullPolicy: "Always"
          resources:
            requests:
              cpu: 512m
              memory: 1024Mi
          ports:
            - containerPort: 4200
          env:
            - name: PREFECT_SERVER_ANALYTICS_ENABLED
              value: "false"
            - name: PREFECT_API_KEY
              valueFrom:
                secretKeyRef:
                  name: prefect-config
                  key: api_key
            - name: PREFECT_API_DATABASE_CONNECTION_URL
              valueFrom:
                secretKeyRef:
                  name: prefect-config
                  key: api_db
---
apiVersion: v1
kind: Service
metadata:
  name: prefect-api-headless
  namespace: prefect
  labels:
    app: prefect-api
spec:
  ports:
    - name: "4200"
      port: 4200
      targetPort: 4200
      protocol: TCP
  selector:
    app: prefect-api
  type: ClusterIP
---
apiVersion: v1
kind: Service
metadata:
  name: prefect-api
  namespace: prefect
  labels:
    app: prefect-api
spec:
  selector:
    app: prefect-api
  type: NodePort
  sessionAffinity: None
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 10800
  ports:
    - name: prefect-api
      protocol: TCP
      port: 4200
      targetPort: 4200
```

### Ingress

Teraz czas zająć się ingressem:

```shell
code prefect-ingress.yaml
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: prefect-api
  namespace: prefect
spec:
  ingressClassName: nginx
  rules:
    - host: prefect.company.local
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: prefect-api
                port:
                  number: 4200
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: prefect-ui
  namespace: prefect
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: prefect-login-auth
spec:
  #ingressClassName: nginx
  rules:
    - host: prefect.company.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: prefect-api
                port:
                  number: 4200
```

Dzięki takiej konfiguracji endpoint `/API` nie jest zabezpieczony Nginx'em i jest dostępny z CLI dla każdego kto zna adres API oraz posiada klucz.

Natomiast front zostaje ukryty za NGINX'em i staje się niedostępny dla osób postronnych.

Optymalnym jest zakrycie front'u dodatkowym sidecarem z własnym mechanizmem logowania, lecz dziś nie jest to obszar na potrzeby tego artykułu.

### Sekrety

Do poprawnego działania mechanizmu potrzebujemy secretu dla Naszego PoC'owego usera do frontu, oraz konfiguracji klucza pod API wraz z konfiguracją bazy:

Otwieramy plik:

```shell
code prefect-config.yml
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: prefect-config
  namespace: prefect
  labels:
    app: prefect
type: Opaque
data:
  api_key: OUR_SECURE_API_KEY #  Encode 'not so secure' api key to base64
  api_url: http://prefect-api-0.prefect-api-headless.prefect.svc.cluster.local:4200/api # Our API service inside the K8S cluster. Encode to b64
  api_external: OUR_EXTERNAL_URL # Self explained. Encode to b64
  api_db: postgresql+asyncpg://prefect:SECRET_PWD@postgres-server:5432/prefectdb # Again, encode it to base64 with a proper credentials
---
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: prefect-login-auth
  namespace: prefect
  labels:
    app: prefect
data:
  auth: OUR_BCRYPTED_HTPASSWORD # Generate bcrypted htpassword for UI and encode it again to b64.
```

W mojej opinii powyższa konfiguracja tłumaczy się sama. Oczywiście na potrzeby produkcyjne należy podejść do tematu ciut inaczej. O czym później.

### Workery

Następnie dobrze by było wystawić jeszcze agenty lub już bardziej workery dla Naszego Prefect'a, gdyż te możemy w razie potrzeby skalować.

W teorii mozna je uruchomić wszędzie, może to być box w Vagrancie (na potrzeby PoC'a chyba idealny), dedykowana maszyna wirtualna, bare-metal czy zwykły kontener.

W tym przypadku użyjemy również Kubernetes'a:

```shell
code prefect-agents.yml
```

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: prefect-agents
  namespace: prefect
spec:
  selector:
    matchLabels:
      app: prefect-agents # has to match .spec.template.metadata.labels
  serviceName: "prefect-agents-headless"
  replicas: 1 # by default is 1
  template:
    metadata:
      labels:
        app: prefect-agents # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      imagePullSecrets:
        - name: regcred
      containers:
        - name: prod-main
          image: internal-cr:9999/prefect-core:latest
          command:
            [
              "bash",
              "-c",
              "prefect agent start --pool production --work-queue prod-main",
            ]
          imagePullPolicy: "Always"
          resources:
            requests:
              cpu: 512m
              memory: 1024Mi
          env:
            - name: PREFECT_SERVER_ANALYTICS_ENABLED
              value: "false"
            - name: PREFECT_API_KEY
              valueFrom:
                secretKeyRef:
                  name: prefect-config
                  key: api_key
            - name: PREFECT_API_URL
              valueFrom:
                secretKeyRef:
                  name: prefect-config
                  key: api_url
        - name: prod-secondary
          image: internal-cr:9999/prefect-core:latest
          command:
            [
              "bash",
              "-c",
              "prefect agent start --pool production --work-queue prod-secondary",
            ]
          imagePullPolicy: "Always"
          resources:
            requests:
              cpu: 512m
              memory: 1024Mi
          env:
            - name: PREFECT_SERVER_ANALYTICS_ENABLED
              value: "false"
            - name: PREFECT_API_KEY
              valueFrom:
                secretKeyRef:
                  name: prefect-config
                  key: api_key
            - name: PREFECT_API_URL
              valueFrom:
                secretKeyRef:
                  name: prefect-config
                  key: api_url
```

Produkcyjnie najlepsza praktyką jest to by workery skonfigurować jako osobne pod'y.

Aczkolwiek na potrzeby developerskie, jeden pod z dwoma lub więcej kontenerami w zupełności Nam wystarczy.

## Zależności

Następnie uzupełniamy plik `requirements`:

```shell
code requirements.txt
```

```txt
virtualenv
prefect-alert
prefect-email
prefect-docker
prefect-gitlab
prefect-kubernetes
prefect-kv
prefect-shell
prefect-slack
prefect-sqlalchemy
pandas
numpy
pyspark
polars[datalake, fsspec]
duckdb
pandera
sqlalchemy
sqlmodel
cx_Oracle
pyodbc
pymssql
pymysql
python-dotenv
pytest
autopep8
aiohttp
httpx
requests
```

## Rakefile

Rakefile (Ruby) edytujemy wedle uznania lub jak poniżej:

```ruby
desc "Build the image"
task :build do
  puts "Building the image..."
  %x[docker compose build --no-cache]
  puts "Done!"
end

desc "Push the image"
task :push do
  puts "Pushing the image..."
  %x[docker push internal-cr:9999/prefect-core]
  puts "Done!"
end

desc "Rollout"
task :rollout do
  puts "Deploying..."
  %x[kubectl -n prefect apply -f prefect-config -f prefect-api.yml -f prefect-agents.yml]
  puts "Rolling out the API and agents..."
  %x[kubectl -n prefect rollout restart statefulset prefect-api prefect-agents]
  puts "Done!"
end
```

Rake przyda Nam się na etapie PoC'a gdy będziemy dokonywać zmian w strukturze obrazu.

## Wdrożenie

### Secrety oraz API

Na początek musimy uruchomić API wraz konfiguracją, oraz zbudować obraz i umieścić go w repozytorium:

```shell
docker compose build && docker push internal-cr:9999/prefect-core
```

```shell
kubectl -n prefect apply -f prefect-config -f prefect-api.yml
```

### CLI

Dodatkowo powinniśmy konfigurować CLI do pracy z wystawionym przez Nas API:

```shell
virtualenv venv
pip install prefect
prefect config set PREFECT_API_KEY= 'YOUR_KEY' && \
prefect config set PREFECT_API_URL='https://prefect.internal.company/api'
```

A także dodać work-poola oraz kolejkę pod workera:

```shell
prefect work-pool create production && \
prefect work-queue create 'prod-main' --pool production && \
prefect work-queue create 'prod-secondary' --pool production
```

### Startujemy workery

Agenty startujemy oczywiście analogicznie:

```shell
kubectl -n prefect apply -f prefect-agents.yml
```

I voula!

```shell
$ kubectl -n prefect get pods
NAME                       READY   STATUS    RESTARTS      AGE
postgres-0                 1/1     Running   0             200d
prefect-agents-0           7/7     Running   0             15s
prefect-api-0              1/1     Running   0             15s
```

Teraz możemy zalogować się do Naszego PoC'a przy pomocy zewnętrznego adresu oraz ustalonych przez Nas credentiali zacząć pracę z kodem.

## Produkcja

Na potrzeby produkcji należy przedewszystkim zastąpić secrety w base64 czymś bardziej wyrafinowanym np. HashiCorp Vault'em.

Dodatkowo root'a oraz inne pathy należy przekierować do sidecar'u, z wewnętrznym mechanizmem logowania i z niego dopiero kierować ruch dalej - do niedostępnego z zewnątrz serwisu.

## Wnioski

Uruchomienie Prefect'a w całości w Kubernetesie,we własnej infrastrukturze wraz z dodatkową warstwą 'ukrywającą'. Wcale nie musi być trudne.

Po spełnieniu warunków produkcyjnych, odpowiednim ustawieniu HPA. Możemy zyskać całkiem przyjemne środowisko orkiestracji przepływów danych.
