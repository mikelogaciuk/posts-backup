---
layout: post
title: Uruchamiamy RKE2 z pomocą Vagrant'a
date: 2022-12-12
category: ["sysops", "devops", "vagrant", "rke2"]
---

![pic](/img/logo-horizontal-rke2.svg)

- [Wstęp](#wstęp)
- [Wymagania](#wymagania)
- [Vagrantfile](#vagrantfile)
- [Piszemy IaC](#piszemy-iac)
- [Shell](#shell)
- [Testy](#testy)
- [Rake](#rake)
- [Uwagi](#uwagi)
- [Podsumowanie](#podsumowanie)

## Wstęp

Aby uruchomić własne RKE2 na potrzeby developerskie przy pomocy Vagranta, generalnie nie potzebujemy doktoratu.

**Vagrant** to narzędzie do wirtualizacji, które pozwala na tworzenie i zarządzanie wirtualnymi maszynami na lokalnym komputerze. Vagrant wykorzystuje tzw. boxy, czyli gotowe obrazy wirtualnych maszyn, które można pobrać z internetu.

**RKE2** to narzędzie do zarządzania klastrami Kubernetes. RKE2 umożliwia instalację i konfigurację klastrów Kubernetes w sposób szybki i łatwy.

## Wymagania

Ogólnie rzecz biorąc wystarczy abyśmy mieli zainstalowanego `Vagranta` (preferowany Linux np. Debian), oraz najlepiej `KVM`, `QEMU` oraz stosowne pluginy do Vagranta.

Zapewne jesteś DevOps'em tak więc nad tym elementem rozwodził się nie będę, a w razie potrzeby wszystkie materiały potrzebne do instalacji powyższych znajdziesz w oficjalnej dokumentacji. Wszak, DevOps czy SysOps potrafi szukać czyż nie?

## Vagrantfile

Na początku musimy utworzyć folder, w moim przypadku będzie to `~/repos/rke2`.

Z racji, że jest to tylko PoC, nie będziemy wystawiać trzech node'ów z racji, że raczej nikt nie wystawia produkcji w Vagrancie.

Pierwsze co, tworzymy folder i plik `Vagrantfile`

```shell
mkdir -p /home/${whoami}/repos/vagrant
cd /home/${whoami}/repos/vagrant
touch Vagrantfile
```

Uruchamiamy **Code'a**:

```shell
code Vagrantfile
```

## Piszemy IaC

Pliki `Vagrantfile` zaczynamy od frazy:

```ruby
Vagrant.configure("2") do |config|
```

Następnie definiujemy jej obraz oraz adres:

```ruby
  config.vm.define "rke2" do |machine|
    machine.vm.box = "bento/ubuntu-22.04"
    machine.vm.network "private_network", ip: "192.168.33.189"
```

Port przekierowujemy w ten sposób:

```ruby
    # Classic 80/443 ports
    machine.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 443, host: 8443, host_ip: "127.0.0.1"

    # Kubernetes ports
    machine.vm.network "forwarded_port", guest: 6443, host: 6443, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 2379, host: 2379, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 2380, host: 2380, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 10250, host: 10250, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 10257, host: 10257, host_ip: "127.0.0.1"
    machine.vm.network "forwarded_port", guest: 10259, host: 10259, host_ip: "127.0.0.1"
    (30000...32767).each do |port|
      machine.vm.network "forwarded_port", guest: port, host: port, host_ip: "127.0.0.1"
    end
```

Z racji, że Vagrant dzieli sie plikami między host'em, a maszyną wirtualną, to by móc skorzystać z tej tajemnej mocy, musimy aktywować synchronizację.

Na potrzeby tego, wymagany będzie na Twoim hoście **NFS**. No, ale zakładamy, że go masz zainstalowanego.

Do pliku tak więc dodajemy:

```ruby
    # Folder sync
    machine.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_version: 4, nfs_udp: false
```

W celu wykonania podstawowego `provisioningu` maszyny, potrzebujemy jeszcze konfiguracji samej maszyny.

W tym przypadku ustawiamy `providera` oraz detale maszyny:

```ruby
    # Libvirt part
    machine.vm.provider :libvirt do |libvirt|
      libvirt.driver = "kvm"
      libvirt.cpu_model = "EPYC-Rome"
      libvirt.cpus = 2
      libvirt.memory = 4096
      libvirt.storage_pool_name = "kvm"
      libvirt.storage :file, :size => '40G'
    end
```

W przypadku modelu CPU, możemy spróbować użyć jednego z poniższych:

```txt
    486
    pentium
    pentium2
    pentium3
    pentiumpro
    coreduo
    n270
    core2duo
    qemu32
    kvm32
    cpu64-rhel5
    cpu64-rhel6
    qemu64
    kvm64
    Conroe
    Penryn
    Nehalem
    Nehalem-IBRS
    Westmere
    Westmere-IBRS
    SandyBridge
    SandyBridge-IBRS
    IvyBridge
    IvyBridge-IBRS
    Haswell-noTSX
    Haswell-noTSX-IBRS
    Haswell
    Haswell-IBRS
    Broadwell-noTSX
    Broadwell-noTSX-IBRS
    Broadwell
    Broadwell-IBRS
    Skylake-Client
    Skylake-Client-IBRS
    Skylake-Client-noTSX-IBRS
    Skylake-Server
    Skylake-Server-IBRS
    Skylake-Server-noTSX-IBRS
    Cascadelake-Server
    Cascadelake-Server-noTSX
    Icelake-Client
    Icelake-Client-noTSX
    Icelake-Server
    Icelake-Server-noTSX
    Cooperlake
    Snowridge
    athlon
    phenom
    Opteron_G1
    Opteron_G2
    Opteron_G3
    Opteron_G4
    Opteron_G5
    EPYC
    EPYC-IBPB
    EPYC-Rome
    EPYC-Milan
    Dhyana
```

## Shell

Dla tych, którzy pracują z Terraformem i image'ami, jasnym jest, że na każdym z obrazów trzeba wykonać `cloud-init`.

W przypadku Vagrant'a, dysponujemy bardzo prostą składnią, która umożliwia Nam konfigurację VM'a z poziomu `shella`.

W Naszym przypadku instalujemy pakiety, wcześniej aktualizujemy definicje managera pakietów, instalujemy `helm'a` oraz `kubectl'a`:

```ruby
    machine.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install apt-transport-https ca-certificates curl btop htop git unzip wget curl -y
      sudo swapoff -a
      swapoff -a && sudo sed -i '/ swap / s/^/#/' /etc/fstab
      sudo snap install helm --classic && sudo snap install kubectl --classic
```

Następnie instalujemy i inicjalizujemy Naszego 'PoC'owego Kubernetesa w postaci `RKE2`:

```ruby
    machine.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install apt-transport-https ca-certificates curl btop htop git unzip wget curl -y
      sudo swapoff -a
      swapoff -a && sudo sed -i '/ swap / s/^/#/' /etc/fstab
      sudo snap install helm --classic && sudo snap install kubectl --classic

      # Install RKE2 server
      curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE="server" sh -

      # Start RKE2 server
      systemctl enable rke2-server.service
      systemctl start rke2-server.service

      # Write the token to a file
      mkdir -p /vagrant
      cat /var/lib/rancher/rke2/server/node-token > /vagrant/node-token

      cp /etc/rancher/rke2/rke2.yaml /vagrant/kubeconfig.yaml

      # Set the KUBECONFIG environment variable for all users
      sudo chmod +r /etc/rancher/rke2/rke2.yaml
      echo "export KUBECONFIG=/etc/rancher/rke2/rke2.yaml" >> /home/vagrant/.bashrc

      echo '1' > /vagrant/node1_ready
    SHELL
  end
end
```

## Testy

Po paru minutach pracy Vagranta, możemy zalogować się do maszyny i wykonać:

```shell
$ vagrant ssh && kubectl get pods -A

NAMESPACE     NAME                                                    READY   STATUS      RESTARTS      AGE
kube-system   cloud-controller-manager-vagrant                        1/1     Running     1 (46m ago)   46m
kube-system   etcd-vagrant                                            1/1     Running     0             46m
kube-system   helm-install-rke2-canal-6wmh5                           0/1     Completed   0             46m
kube-system   helm-install-rke2-coredns-xzkg5                         0/1     Completed   0             46m
kube-system   helm-install-rke2-ingress-nginx-htgz5                   0/1     Completed   0             46m
kube-system   helm-install-rke2-metrics-server-vzsfp                  0/1     Completed   0             46m
kube-system   helm-install-rke2-snapshot-controller-crd-4w7fs         0/1     Completed   0             46m
kube-system   helm-install-rke2-snapshot-controller-smkfd             0/1     Completed   1             46m
kube-system   helm-install-rke2-snapshot-validation-webhook-cng55     0/1     Completed   0             46m
kube-system   kube-apiserver-vagrant                                  1/1     Running     0             46m
kube-system   kube-controller-manager-vagrant                         1/1     Running     0             46m
kube-system   kube-proxy-vagrant                                      1/1     Running     0             46m
kube-system   kube-scheduler-vagrant                                  1/1     Running     0             46m
kube-system   rke2-canal-br7cz                                        2/2     Running     0             46m
kube-system   rke2-coredns-rke2-coredns-565dfc7d75-wgz42              1/1     Running     0             46m
kube-system   rke2-coredns-rke2-coredns-autoscaler-6c48c95bf9-jgznp   1/1     Running     0             46m
kube-system   rke2-ingress-nginx-controller-b77vn                     1/1     Running     0             45m
kube-system   rke2-metrics-server-c9c78bd66-sj545                     1/1     Running     0             46m
kube-system   rke2-snapshot-controller-6f7bbb497d-k58l5               1/1     Running     0             46m
kube-system   rke2-snapshot-validation-webhook-65b5675d5c-dpq5h       1/1     Running     0             46m
```

## Rake

Jeżeli chcemy pobawić się bezpośrednio z Naszego host'a, to możemy użyć tego `Rakefile`, który podmienia Nam konfigurację dla pliku `KUBECONFIG`:

```ruby
require 'yaml'
require 'fileutils'

def get_actual_dir
  File.dirname(File.expand_path(__FILE__))
end

def get_user
  %x(whoami).strip
end

def get_node_int_i
  %x('vagrant ssh )
end

username = get_user
kube_config_path = "home/#{username}/.kube/config/"
vagrant_kube_config_path = "#{get_actual_dir}/kubeconfig.yaml"

desc "Clean up old folders"
task :destroy do
  %x("vagrant destroy -f")
  %x("rm -rf .vagrant")
  %x("rm -rf kubeconfig.yaml")
  %x("rm -rf node-token")
  %x("rm -rf node1_ready")
  puts "Cleaning up old folders"
end

desc "Config management"
namespace :config do
  desc "Use RKE2 Kubeconfig"
  task :vagrant do
    file = File.read('kubeconfig.yaml')
    data = YAML.load(file)
    data['clusters'][0]['cluster']['server'] = 'https://192.168.33.189:6443'
    YAML.dump(data, File.open('kubeconfig.yaml', 'w'))

    %x(echo "export KUBECONFIG=#{vagrant_kube_config_path}" >> ~/.bashrc)
    %x(echo "export KUBECONFIG=#{vagrant_kube_config_path}" >> ~/.zshrc)
    puts "Using RKE2 Kubeconfig. Type 'source ~/.bashrc' or '.zshrc' to apply changes."
  end
  desc "Use original Kubeconfig"
  task :original do
    %x(echo "export KUBECONFIG=#{kube_config_path}" >> ~/.bashrc)
    %x(echo "export KUBECONFIG=#{kube_config_path}" >> ~/.zshrc)
    puts "Using original Kubeconfig. Type 'source ~/.bashrc' or '.zshrc' to apply changes."
  end
end
```

## Uwagi

Jak widać po powyższym tekście, Vagrant może stanowić ciekawą alternatywę dla kontenerów z racji jego prostej modularnej budowy oraz możliwości używania `Ruby` bezpośrednio w konfiguracji maszyn.

Gdyż tak naprawdę, chcąc utworzyć od razu trzy node'y, wystarczy zrobić to tak:

```ruby
Vagrant.configure("2") do |config|
  (1..3).each do |i|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.

    localhost = "127.0.0.1"

    # Provision three RKE2 nodes (1 server, 2 agents)
    config.vm.define "rke2-node-#{i}" do |node|
      node.vm.box = "bento/ubuntu-22.04"
      node.vm.network "private_network", ip: "192.168.33.10#{i}"

    (...)

        if i == 1
        node.vm.provision "shell", inline: <<-SHELL

          # Install RKE2 server
          curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE="server" sh -

          # Start RKE2 server
          systemctl enable rke2-server.service
          systemctl start rke2-server.service

          # Write the token to a file
          mkdir -p /vagrant
          cat /var/lib/rancher/rke2/server/node-token > /vagrant/node-token

          cp /etc/rancher/rke2/rke2.yaml /vagrant/kubeconfig.yaml

          (...)
        end

    end
end
```

## Podsumowanie

Zalety środowiska developerskiego RKE2 z wykorzystaniem Vagrant'a, w mej opinii są następujące:

- Łatwość konfiguracji: Wystawienie PoC'owego / developerskiego RKE2 przy pomocy Vagranta jest banalnie proste. W przypadku 3 node'owych maszyn, trzeba trochę pokombinować jak przekazać z automatu tokeny. Niemniej jednak, wystawianie 3 node'ów na potrzeby developerskie jest delikatnym overkill'em. No chyba, że planujemy przećwiczyć upgrade'y do nowszych wersji Kubernetes. Czy sprawdzić jak zachować się klaster gdy 'popsujemy' jeden z node'ów.
- Elastyczność: Vagrant umożliwia wybór dowolnych boxów, które można wykorzystać do utworzenia wirtualnych maszyn. Począwszy od Windowsów po niezliczoną ilość Linuxów. Choć prym wiodą dystrybucje Ubuntu, Debiana. Natomiast własna instancja RKE2 umożliwia dostosowanie konfiguracji klastra Kubernetes do własnych potrzeb w bezpiecznym wyizolowanym środowisku bez szkody dla produkcji.
- Oszczędność kosztów: Vagrant i RKE2 pozwalają na tworzenie i zarządzanie klastrami Kubernetes na lokalnym komputerze, daje to Nam napewno oszczędność kosztów związanych np. z infrastrukturą chmurową.

Jak widać utworzenie własnego klastra nie jest trudne. Oczywiście jest to nadal tylko instancja developerska. Raczej rzadkim jest praktyka wystawiania maszyny QEMU z poziomu Vagrant'a na produkcji. Z reguły, jeżeli ktoś używa kombinacji QEMU/KVM - to jako narzędzie infrastruktury jako kodu wybiera raczej `Terraforma`.

Dodatkowo, instancje produkcyjne wymagają znacznie większej uwagi i konfiguracji. Począwszy od firewall'a (najlepiej software'owego oraz sprzętowego), po cały aspekt certyfikatów, doboru ingress'ów czy konfiguracji storage'u etc.
