---
layout: post
title: Przygotowanie Linuxa do pracy SRE
date: 2024-07-13
category:
  [
    "devops",
    "sysops",
    "dataops",
    "sreops",
    "notatki",
    "cheatsheet",
    "linux",
    "debian",
    "ubuntu",
  ]
---

![header](/img/sre_ai.jpeg)

## Wstęp

W życiu każdego DevOps'a czy DataOps'a lub SRE, przychodzi moment kiedy zwiedzeni chęcia zabawy innym distro, wykonujemy czystą instalkę.

Z racji, że mój mózg najwidoczniej nie ma nieskończonej pamięci...

Przygotowałem sobie listę rzeczy, które muszę lub i mogę zainstalować - by móc spokojnie pracować.

W tym przypadku instrukcje dostosowane są do systemów z rodziny Debiana (aktualnie korzystam z Kubuntu).

## Spis treści

- [Wstęp](#wstęp)
- [Spis treści](#spis-treści)
- [Git](#git)
- [Zsh](#zsh)
- [Oh My Zsh](#oh-my-zsh)
  - [Plugins](#plugins)
- [Fonts](#fonts)
- [Powerlevel10k](#powerlevel10k)
- [Build essentials](#build-essentials)
- [Snap](#snap)
- [Flatpak](#flatpak)
- [Global Protect OpenConnect](#global-protect-openconnect)
  - [Debian 12+](#debian-12)
- [Homebrew](#homebrew)
- [Docker](#docker)
- [Instalacja](#instalacja)
  - [Dodanie Dockera do sudoers](#dodanie-dockera-do-sudoers)
  - [IP Range](#ip-range)
  - [Nerdctl](#nerdctl)
  - [Portainer](#portainer)
- [QEMU/KVM](#qemukvm)
  - [Instalacja stacka](#instalacja-stacka)
  - [Granty](#granty)
  - [Mostek sieciowy](#mostek-sieciowy)
  - [Test](#test)
- [Kubernetes](#kubernetes)
  - [Kubectl](#kubectl)
  - [VMware Tanzu](#vmware-tanzu)
  - [Helm](#helm)
  - [Argo](#argo)
- [CLI’s](#clis)
  - [Terraform](#terraform)
  - [Vagrant](#vagrant)
  - [AWS](#aws)
  - [EKS](#eks)
  - [Azure](#azure)
- [Aplikacje dla developerów](#aplikacje-dla-developerów)
  - [Visual Studio Code](#visual-studio-code)
  - [JetBrains](#jetbrains)
- [Inne paczki](#inne-paczki)
  - [Kerberos](#kerberos)
  - [Oracle Client](#oracle-client)
  - [Microsoft SQL Server ODBC](#microsoft-sql-server-odbc)
- [Języki](#języki)
  - [Python](#python)
  - [Ruby](#ruby)
  - [LSP](#lsp)
    - [YJIT](#yjit)
    - [Ruby Kernel dla Jupytera](#ruby-kernel-dla-jupytera)
    - [TruffleRuby GraalVM](#truffleruby-graalvm)
  - [Crystal](#crystal)
  - [Go](#go)
  - [Rust](#rust)
  - [Nim](#nim)
  - [Gleam](#gleam)
  - [Scala](#scala)
  - [Erlang](#erlang)
  - [Elixir](#elixir)
  - [Typescript](#typescript)
  - [Haskell](#haskell)
    - [Dart](#dart)
  - [Flutter](#flutter)
  - [Chrome](#chrome)
  - [PHP](#php)
- [Bonus](#bonus)
  - [DuckDB](#duckdb)

## Git

W celu instalacji **Git'a**, wykonujemy:

```bash
sudo apt install git && git config --global credential.helper store
git config --global user.name "John Doe"  && git config --global user.email johndoe@example.com
```

## Zsh

**Zsh** instalujemy tak:

```bash
sudo apt update && apt install zsh
```

Wymuszamy Zsh jako domyślny shell:

```bash
chsh -s /usr/bin/zsh
```

Wylogowujemy się i logujemy ponownie i potwierdzamy z pomocą `2`:

```bash
(2)  Populate your ~/.zshrc with the configuration recommended
     by the system administrator and exit (you will need to edit
     the file by hand, if so desired).
```

## Oh My Zsh

By zainstalować **Oh My Zsh**:

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Powinniśmy zobaczyć:

```bash
         __                                     __
  ____  / /_     ____ ___  __  __   ____  _____/ /_
 / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \
/ /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / /
\____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/
                        /____/                       ....is now installed!


Before you scream Oh My Zsh! look over the `.zshrc` file to select plugins, themes, and options.
```

Różne `themes'y` dostępne są pod adresem [tutaj](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes).

W celu ustawienia skórki, ustawiamy zmienną `ZSH_THEME` w`~/.zshrc`,np. na `ZSH_THEME=robbyrussell`.

### Plugins

Plugin'y instalujemy tak:

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

```bash
fpath+=${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions/src
plugins=(git aliases debian branch github zsh-autosuggestions zsh-syntax-highlighting zsh-completions zsh-interactive-cd zsh-navigation-tools)
```

## Fonts

Najlepsze czcionki dla nerdów znajdziemy na NerdFonts, [tutaj](https://www.nerdfonts.com/font-downloads).

Czcionki wypakowujemy tu: `/usr/share/fonts/` lub do `~/.fonts` i odbudowujemy cache:

```bash
fc-cache -f -v
```

## Powerlevel10k

Kolejnym wartym zainteresowania plugin'em do Zsh jest Powerlevel10k:

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

UStawiamy `ZSH_THEME="powerlevel10k/powerlevel10k"` w `~/.zshrc`.

Dla tzw `Powerlevel10k instant prompts`, dodajemy do `.zshrc` to:

```bash
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi
```

## Build essentials

Tzw `build-essentials` dla programistów instalujemy tak:

```bash
sudo apt install build-essential
```

## Snap

Snap'a (jeżeli potrzebujemy), instalujemy z pomocą:

```bash
$ su root
apt update
apt install snapd
```

Oraz (po przelogowaniu):

```bash
sudo snap install core
```

## Flatpak

Dla FlatPaka:

```bash
sudo apt install flatpak gnome-software-plugin-flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Global Protect OpenConnect

Klienta [GlobalProtect OpenConnect](https://github.com/yuezk/GlobalProtect-openconnect) można zainstalować tak:

Instalujemy go tak:

```bash
wget http://launchpadlibrarian.net/704701349/libwebkit2gtk-4.0-37_2.43.3-1_amd64.deb
wget http://launchpadlibrarian.net/704701345/libjavascriptcoregtk-4.0-18_2.43.3-1_amd64.deb
sudo dpkg --install *.deb
sudo apt-get install gir1.2-gtk-3.0 gir1.2-webkit2-4.0
sudo add-apt-repository ppa:yuezk/globalprotect-openconnect
sudo apt-get update
sudo apt-get install globalprotect-openconnect
```

### Debian 12+

Na Debianie 12 robimy to tak:

```bash
sudo apt get update
sudo apt install openconnect network-manager-openconnect network-manager-openconnect-gnome
```

Możemy połączyć się z poziomu UI lub konsoli:

```bash
sudo openconnect --protocol=gp global.protect.server -u user-name
```

## Homebrew

Instalacja `homebrew` na Linuxie jest oczywiście możliwa i robimy to tak:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Następnie dodajemy wpis do `.zsh`:

```bash
(echo; echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"') >> /home/$USER/.zprofile
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
```

## Docker

## Instalacja

`Dockera` instalujemy tak:

```bash
sudo apt-get install \
  ca-certificates \
  curl \
  gnupg \
  lsb-release

sudo mkdir -p /etc/apt/keyrings
```

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Dodanie Dockera do sudoers

Wykonujemy:

```bash
sudo groupadd docker
sudo usermod -aG docker ${USER} && sudo newgrp docker
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
sudo chmod 666 /var/run/docker.sock
```

### IP Range

W celu zmiany zakresu IP dla kontenerów, dokonujemy zmiany w:

```bash
sudo nano /etc/docker/daemon.json
```

Paste:

```bash
{
  "bip": "192.168.200.1/24",
  "fixed-cidr": "192.168.200.1/25",
  "insecure-registries": ["registry:32000"]
}
```

Restartujemy usługi:

```bash
sudo systemctl daemon-reload && sudo systemctl restart docker
```

### Nerdctl

`Nerdctl`'a instalujemy tak:

```bash
brew install nerdctl
```

### Portainer

Jeżeli ktoś potrzebuje, to:

```bash
docker volume create portainer_data \
&& docker run -d -p 8000:8000 -p 9443:9443 \
--name portainer --restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v portainer_data:/data portainer/portainer-ce:latest
```

Then visit [https://localhost:9443/](https://localhost:9443/).

## QEMU/KVM

### Instalacja stacka

Instalacja `QEMU and KVM` wygląda tak:

```bash
sudo apt install -y qemu-kvm libvirt-daemon  bridge-utils virtinst libvirt-daemon-system
sudo apt install -y virt-top libguestfs-tools libosinfo-bin  qemu-system virt-manager
```

### Granty

Dodajemy swojego usera do grupy `kvm`:

```bash
sudo systemctl stop libvirtd
sudo usermod -aG libvirt ${USER}
sudo usermod -aG libvirt-qemu ${USER}
sudo usermod -aG kvm ${USER}
sudo usermod -aG input ${USER}
sudo usermod -aG disk ${USER}
sudo virsh net-start default
sudo virsh net-autostart default
sudo systemctl start libvirtd
```

Oraz ustawiamy uprawnienia w `/etc/libvirt/qemu.conf`

```bash
# Some examples of valid values are:
#
#       user = "qemu"   # A user named "qemu"
#       user = "+0"     # Super user (uid=0)
#       user = "100"    # A user named "100" or a user with uid=100
#
#user = "libvirt-qemu"
user = "your_usr"

# The group for QEMU processes run by the system instance. It can be
# specified in a similar way to user.
#group = "libvirt-qemu"
group = "libvirt"

# Whether libvirt should dynamically change file ownership
# to match the configured user/group above. Defaults to 1.
# Set to 0 to disable file ownership changes.
# dynamic_ownership = 0
```

Odświeżamy grupę

```bash
su - ${USER}
```

Oraz dodajemy alias w `~/.zshrc`:

```bash
echo "alias virsh='EDITOR=nano sudo virsh && cd /etc/libvirt/qemu'" >> ~/.zshrc
```

Restart usługi:

```bash
source ~/.zshrc

sudo systemctl restart libvirtd
```

Start domeny:

```bash
virsh
list --all
start mydomain
```

### Mostek sieciowy

Na początek konfigurujemy w `/etc/network/interfaces`:

```bash
# Bridge setup
iface br0 inet dhcp
  bridge_ports eth0 eno1
```

Restartujemy usługę:

```bash
sudo service network-manager restart
```

Sprawdzamy czy `br0` jest up:

```bash
$ ip a s br0
3: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether fe:54:00:b5:c9:02 brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.1/24 brd 10.10.10.255 scope global br0
       valid_lft forever preferred_lft forever
    inet6 fe80::7c49:43ff:feaf:4d4b/64 scope link
       valid_lft forever preferred_lft forever
```

You can check if everything is good with:

```bash
virsh net-list --all
```

### Test

Sprawdzić działanie możemy poprzez:

```bash
❯ sudo systemctl status libvirtd
○ libvirtd.service - Virtualization daemon
     Loaded: loaded (/lib/systemd/system/libvirtd.service; enabled; preset: enabled)
     Active: inactive (dead) since Thu 2023-07-06 20:57:55 CEST; 29min ago
   Duration: 2min 116ms
TriggeredBy: ● libvirtd-ro.socket
             ● libvirtd.socket
             ● libvirtd-admin.socket
       Docs: man:libvirtd(8)
             https://libvirt.org
   Main PID: 68980 (code=exited, status=0/SUCCESS)
        CPU: 112ms

lip 06 20:55:55 hea-runnerw-03 systemd[1]: Starting libvirtd.service - Virtualization daemon...
lip 06 20:55:55 hea-runnerw-03 systemd[1]: Started libvirtd.service - Virtualization daemon.
lip 06 20:57:55 hea-runnerw-03 systemd[1]: libvirtd.service: Deactivated successfully.
```

```bash
sudo virt-install \
--name deb11 \
--ram 2048 \
--vcpus 2 \
--disk path=/var/lib/libvirt/images/deb11-vm.qcow2,size=20 \
--os-type linux \
--os-variant debian9 \
--network bridge=br1 \
--graphics none \
--console pty,target_type=serial \
--location 'http://ftp.debian.org/debian/dists/bullseye/main/installer-amd64/' \
--extra-args 'console=ttyS0,115200n8 serial'
```

## Kubernetes

### Kubectl

```bash
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

# If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg # allow unprivileged APT programs to read this keyring

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list   # helps tools such as command-not-found to work correctly

sudo apt-get update
sudo apt-get install -y kubectl
```

I dodajemy do `.zshrc`:

```bash
source <(kubectl completion zsh)
```

W przypadku gdy napotkamy błąd: `2: command not found: compdef`, dodajemy poniższe do `~/.zshrc`:

```bash
autoload -Uz compinit
compinit
```

### VMware Tanzu

Dla klientów dystrybucji Tanzu dla K8S, wymagany jest plugin `vsphere-plugin`, który wrzucamy do `/usr/bin/` oraz:

```bash
echo 'export PATH=$PATH:/usr/bin/vsphere-plugin/bin' >> ~/.zshrc
```

### Helm

Instalacja `Helma` jest banalna:

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

Włączamy completions w `.zshrc`:

```bash
source <(helm completion zsh)
```

### Argo

CLI pod `Argo` instalujemy z `brew`, gdzie `argo` jest dla `Workflows`, a `argocd` dla oczywiśćie `ArgoCD`:

```bash
brew install argo argocd
```

## CLI’s

### Terraform

CLI dla `Terraform`:

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

Dodajemy completions w `.zshrc`:

```bash
autoload -U +X bashcompinit && bashcompinit
complete -o nospace -C /usr/bin/terraform terraform

zstyle ':completion:*' menu select
fpath+=~/.zfunc
```

### Vagrant

Tego pana nie trzeba przedstawiać:

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant
```

Jeżeli chcemy używać Vagranta wraz z Libvirt (QEMU/KVM):

```bash
vagrant plugin install vagrant-libvirt && vagrant plugin install vagrant-mutate
```

### AWS

`AWSCLI` instalujemy tak:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin
rm -r aws awscliv2.zip
```

### EKS

Dla `eksctl` CLI wykonujemy:

```bash
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Verify checksum
curl -sL "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo mv /tmp/eksctl /usr/local/bin
```

### Azure

CLI pod `Azure`'a, instalujemy via:

```bash
sudo apt update && sudo apt install azure-cli
```

## Aplikacje dla developerów

### Visual Studio Code

Code'a instalujemy poprzez repo Microsoft'u:

```bash
sudo apt install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg

sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```

By włączyć `fancy italics`, jak niżej:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1718701703520/2e2af2c7-83da-4857-bbfd-cda59f96f79c.png align="center")

Dodajemy do configu'u:

```json
  "editor.tokenColorCustomizations": {
    "[*Light*]": {
      "textMateRules": [
        {
          "scope": "ref.matchtext",
          "settings": {
            "foreground": "#000"
          }
        }
      ]
    },
    "[*Dark*]": {
      "textMateRules": [
        {
          "scope": "ref.matchtext",
          "settings": {
            "foreground": "#fff"
          }
        }
      ]
    },
    "[Mosaic]": {
      "textMateRules": [
        {
          "scope": [
            // Keywords to be in italic
            "comment",
            "entity.name.type.class", // class names
            "keyword", // import, export, return...
            "constant", // String, Number, Boolean..., this, super
            "storage.modifier", // static keyword
            "storage.type.class.js" // class keyword
          ],
          "settings": {
            "fontStyle": "italic"
          }
        },
        {
          "scope": [
            // Keywords to exclude from italics (VSCode has some defaults for italics)
            "invalid",
            "keyword.operator",
            "constant.numeric.css",
            "keyword.other.unit.px.css",
            "constant.numeric.decimal.js",
            "constant.numeric.json"
          ],
          "settings": {
            "fontStyle": ""
          }
        }
      ]
    }
  }
```

### JetBrains

`Jetbrains Toolbox`, pobieramy z [tej strony](https://www.jetbrains.com/toolbox-app/) i wrzucamy do `/usr/bin/`.

## Inne paczki

### Kerberos

Kerberos'a instalujemy via apt:

```bash
sudo apt install krb5-user -y
```

Edytujemy konfig:

```bash
sudo nano /etc/krb5.conf
```

Na wzór:

```bash
[libdefaults]
        default_realm = company.local

[realms]
company.local = {
        kdc = DC0.company.local
        admin_server = DC0.company.local
}
[domain_realm]
        .company.local = COMPANY.LOCAL
```

Następnie logujemy się via:

```bash
kinit username@COMPANY.LOCAL
```

Sprawdzamy czy jesteśmy zalogowani:

```bash
$klist

Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: username@COMPANY.LOCAL

Valid starting       Expires              Service principal
27.05.2022 08:59:22  27.05.2022 18:59:22  krbtgt/COMPANY.LOCAL@COMPANY.LOCAL
renew until 28.05.2022 08:59:17
```

Tworzenie własnego \`keytaba\`:

```bash
ktutil

addent -password -p username@COMPANY.LOCAL -k 1 -e RC4-HMAC
wkt /dir/username.keytab
```

### Oracle Client

Klienta `Oracle SQL` instalujemy tak:

```bash
# For Debian
sudo apt install -y libaio1 unixodbc unixodbc-dev freetds-dev freetds-bin tdsodbc

# For Ubuntu
sudo apt install -y unixodbc-dev libaio-dev libaio1t64
ln -s /usr/lib/x86_64-linux-gnu/libaio.so.1t64 /usr/lib/x86_64-linux-gnu/libaio.so.1
```

```bash
sudo mkdir -p /opt/oracle
cd /opt/oracle/
sudo wget -P /opt/oracle https://download.oracle.com/otn_software/linux/instantclient/19800/instantclient-basic-linux.x64-19.8.0.0.0dbru.zip
sudo wget -P /opt/oracle https://download.oracle.com/otn_software/linux/instantclient/19800/instantclient-sqlplus-linux.x64-19.8.0.0.0dbru.zip
sudo wget -P /opt/oracle https://download.oracle.com/otn_software/linux/instantclient/19800/instantclient-sdk-linux.x64-19.8.0.0.0dbru.zip
sudo wget -P /opt/oracle https://download.oracle.com/otn_software/linux/instantclient/19800/instantclient-odbc-linux.x64-19.8.0.0.0dbru.zip
sudo wget -P /opt/oracle https://download.oracle.com/otn_software/linux/instantclient/19800/instantclient-tools-linux.x64-19.8.0.0.0dbru.zip
sudo unzip instantclient-basic-linux.x64-19.8.0.0.0dbru.zip -d /opt/oracle
sudo unzip instantclient-sqlplus-linux.x64-19.8.0.0.0dbru.zip -d /opt/oracle
sudo unzip instantclient-sdk-linux.x64-19.8.0.0.0dbru.zip -d /opt/oracle
sudo unzip instantclient-odbc-linux.x64-19.8.0.0.0dbru.zip -d /opt/oracle
sudo unzip instantclient-tools-linux.x64-19.8.0.0.0dbru.zip -d /opt/oracle
export ORACLE_HOME=/opt/oracle/instantclient_19_8
export PATH=$ORACLE_HOME:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME:$LD_LIBRARY_PATH
sudo ldconfig
```

Dodatkowo do `/etc/odbcinst.ini`, można dodać:

```ini
[Oracle19]
Description     = Oracle ODBC driver for Oracle 19c
Driver          = /opt/oracle/instantclient_19_8/libsqora.so.19.1
```

### Microsoft SQL Server ODBC

Klienta `MSSQL` instalujemy jak poniżej:

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | sudo tee /etc/apt/trusted.gpg.d/microsoft.asc

#Debian 12
curl https://packages.microsoft.com/config/debian/12/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list

sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql17
# optional: for bcp and sqlcmd
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.zshrc
source ~/.bashrc
# optional: for unixODBC development headers
sudo apt-get install -y unixodbc-dev
# optional: kerberos library for debian-slim distributions
sudo apt-get install -y libgssapi-krb5-2
```

Lub tak:

```bash
wget https://packages.microsoft.com/debian/12/prod/pool/main/m/msodbcsql17/msodbcsql17_17.10.5.1-1_amd64.deb
wget https://packages.microsoft.com/debian/12/prod/pool/main/m/msodbcsql18/msodbcsql18_18.3.2.1-1_amd64.deb
sudo dpkg -i msodbcsql17_17.10.5.1-1_amd64.deb
sudo dpkg -i msodbcsql18_18.3.2.1-1_amd64.deb
```

## Języki

### Python

W każdym chyba distro, mamy zainstalowanego Python'a.

W teorii jedyne czego potrzebujemy to `poetry` oraz `virtualenv`:

```bash
sudo apt install python3-virtualenv python3-poetry
```

### Ruby

Dla Ruby, najlepiej zainstalować `Rbenv` w celu kontroli wersji, które posiadamy w systemie:

```bash
sudo apt update && sudo apt install rbenv libyaml-dev libffi-dev
echo 'eval "$(/usr/bin/rbenv init - zsh)"' >> ~/.zshrc
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```

Sprawdzanie wersji dostępnych na ten moment do instalacji:

```bash
# List latest stable versions:
$ rbenv install --list
3.1.6
3.2.5
3.3.5
jruby-9.4.8.0
mruby-3.3.0
picoruby-3.0.0
truffleruby-24.0.2
truffleruby+graalvm-24.0.2
```

Wersje lokalne sprawdzamy tak:

```shell
$ rbenv versions
  system
* 3.1.2 (set by /home/${USER}/.rbenv/version)
  truffleruby+graalvm-22.1.0
```

Konkretną wersję instalujemy tak:

```shell
rbenv install 3.3.5
```

Buildy konfigurujemy tak:

```bash
rbenv global 3.3.5   # Set the default Ruby version for this machine

rbenv local 3.3.5    # Set the Ruby version for this directory

rbenv uninstall 3.1.2
```

### LSP

Language server protocol dla Ruby instalujemy, tak:

```bash
gem install bundler solargraph ruby-lsp rubocop rubocop-packaging rubocop-performance rubocop-rspec rubocop-shopify rubocop-thread_safety
```

#### YJIT

By zainstalować YJIT potrzebujemy wykonać instalacje w taki sposób (Rust jest wymagany):

```bash
RUBY_CONFIGURE_OPTS="--enable-yjit" rbenv install 3.3.5
```

Dla Debiana z doinstalowanym OpenSSL z `brew`:

```bash
RUBY_CONFIGURE_OPTS="--enable-yjit --with-openssl-dir=$OPENSSL_PREFIX" rbenv install 3.3.5
```

Dodatkowe flagi do eksportu znajdują się w sekcji dot. [TruffleRuby na GraalVM](#truffleruby-graalvm).

#### Ruby Kernel dla Jupytera

Wykonujemy:

```bash
$ gem install iruby && source ~/.zshrc
$ iruby register --force
```

I przeładowujemy shella.

#### TruffleRuby GraalVM

By zainstalować TruffleRuby (z GraalVM) wykonujemy:

```bash
rbenv install truffleruby+graalvm-24.0.2
```

Czasem możemy potrzebować `OpenSSL v1.1`, więc wykonujemny:

```bash
brew install openssl@1.1
```

Następnie do `.zshrc` dodajemy:

```bash
# Ruby GraalVM
export LANG=en_US.UTF-8
export PATH="/home/linuxbrew/.linuxbrew/opt/openssl@1.1/bin:$PATH"
export OPENSSL_PREFIX="/home/linuxbrew/.linuxbrew/opt/openssl@1.1"
export LDFLAGS="-L/home/linuxbrew/.linuxbrew/opt/openssl@1.1/lib"
export CPPFLAGS="-I/home/linuxbrew/.linuxbrew/opt/openssl@1.1/include"
export RUBY_CONFIGURE_OPTS="--with-openssl-dir=$OPENSSL_PREFIX"
```

### Crystal

`Crystal` - to taki kompilowalny kuzyn Ruby.

Instalujemy go przez:

- skrypt .sh
- lub `apt`.

Skrypt:

```bash
curl -fsSL https://crystal-lang.org/install.sh | sudo bash
```

Apt:

```bash
echo 'deb http://download.opensuse.org/repositories/devel:/languages:/crystal/Debian_Testing/ /' | sudo tee /etc/apt/sources.list.d/devel:languages:crystal.list
curl -fsSL https://download.opensuse.org/repositories/devel:languages:crystal/Debian_Testing/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/devel_languages_crystal.gpg > /dev/null
sudo apt update
sudo apt install crystal
```

Poniższe są wymagane w systemie, by poprawnie zainstalować Crystala:

```bash
sudo apt install libssl-dev libxml2-dev libyaml-dev libgmp-dev libz-dev
```

Dla LSP **Crystaline** wymagane jest:

```bash
mkdir -p .crystalline && cd .crystalline
wget https://github.com/elbywan/crystalline/releases/latest/download/crystalline_x86_64-unknown-linux-musl.gz -O crystalline.gz &&\
gzip -d crystalline.gz &&\
chmod u+x crystalline
```

Następnie w konfiguracji VS Code ustawiamy **absolutną** ścieżkę binarki:

```json
 "crystal-lang.server": "/home/${USER}/.crystalline/crystalline",
```

Dla **LazyVim'a**, tworzymy plik:

```bash
touch ~/.config/nvim/lua/plugins/crystal.lua
```

Oraz dodajemy:

```bash
return {
  {
    "vim-crystal/vim-crystal",
    ft = "crystal",
  },
}
```

Następnie w pliku `~/.config/nvim/lua/config/lazy.lua` dodajemy:

```bash
{ import = "plugins.crystal" }
```

### Go

W momencie pisania tekstu, najnowszym build'em było `1.23.0`:

```bash
cd ~/Downloads
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz
mkdir -p ~/.go
```

Następnie dodajemy do `PATH` w \`.`` zshrc` ``:

```bash
export GOROOT=/usr/local/go
export GOPATH=$HOME/.go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

Sprawdzamy:

```bash
$ go version
go version go1.23.0 linux/amd64
```

### Rust

`Rust'a` instalujemy z `rustup`'em:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

I dodajemy:

```bash
# Rust
export PATH=$HOME/.cargo/bin:$PATH
```

Podniesienie wersji wykonujemy z pomocą:

```bash
rustup update
```

### Nim

Jeżeli ktoś potrzebuje `Nim`'a to:

```bash
curl https://nim-lang.org/choosenim/init.sh -sSf | sh
```

Dodajemy wpis:

```bash
export PATH=/home/$USER/.nimble/bin:$PATH
```

LSP instalujemy tak:

```bash
nimble install nimlsp
```

Lub:

```bash
/home/${USER}/.nimble/bin/nimble install nimlangserver --accept
```

### Gleam

`Gleam`'a musimy skompilować (potrzebny do tego jest Nam Rust) i robi to się dosyć prosto:

```shell
cd /tmp
git clone https://github.com/gleam-lang/gleam.git
cd gleam
```

```shell
$ make install

cd compiler-cli && cargo install --path . --force --locked
  Installing gleam v1.4.1 (/tmp/gleam/compiler-cli)
    Updating crates.io index
  Downloaded base16 v0.2.1
  Downloaded askama v0.12.1
  Downloaded askama_escape v0.10.3
  (...)
   Compiling same-file v1.0.6
   Compiling askama_escape v0.10.3
```

```shell
$ gleam -V
gleam 1.4.1
```

PS. Należy pamiętać o tym, że Gleam kompiluje się do Erlang'a (BEAM) więc i go potrzebujemy.

### Scala

`Scala`'ę instalujemy tak:

```bash
sudo apt install default-jre && sudo apt install default-jdk

curl -fL https://github.com/coursier/coursier/releases/latest/download/cs-x86_64-pc-linux.gz | gzip -d > cs && chmod +x cs && ./cs setup

echo "export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64" >> ~/.zshrc
```

### Erlang

`Erlang`'a instalujemy najlepiej z repozytorium od Rabbita:

```bash
sudo add-apt-repository ppa:rabbitmq/rabbitmq-erlang
sudo apt update
sudo apt install erlang erlang-dev erlang-dialyzer erlang-xmerl
```

### Elixir

Po instalacji Erlanga, możemy zainstalować `Elixira`.

W momencie pisania tekstu najnowszym był `1.17` oraz `Erlang OTP 26`:

```bash
wget https://github.com/elixir-lang/elixir/releases/download/v1.17.0/elixir-otp-26.zip
sudo unzip elixir-otp-26.zip -d /usr/local/elixir
echo 'export PATH="$PATH:/usr/local/elixir/bin"' >> ~/.zshrc
echo 'export MIX_HOME="$HOME/.mix"' >> ~/.zshrc
echo 'export PATH="$PATH:$HOME/.mix/escripts"' >> ~/.zshrc
mkdir -p ~/.mix
source ~/.zshrc
mix do local.rebar --force, local.hex --force
mix archive.install hex phx_new
mix escript.install hex livebook
```

### Typescript

`Typescript`'a wraz `Nodejs` instalujemy via `nvm`:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

nvm install 22 && node -v && npm -v & npm install typescript yarn -g && tsc -v
```

### Haskell

`Haskell`'a najlepiej zainstalować poprzez `GHCup`, który to instaluje kompilator GHC wraz z LSP i Cabal'em.

Dla Ubuntu instalujemy pierw:

```bash
sudo apt install build-essential curl libffi-dev libffi8ubuntu1 libgmp-dev libgmp10 libncurses-dev
```

Dla Debiana:

```bash
sudo apt install build-essential curl libffi-dev libffi8ubuntu1 libgmp-dev libgmp10 libncurses-dev
```

I finalnie GHCup:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```

#### Dart

Instalacja `Dart`'a wygląda tak:

```bash
sudo apt update
sudo apt  install apt-transport-https
wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/dart.gpg
echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' | sudo tee /etc/apt/sources.list.d/dart_stable.list
sudo apt update && sudo apt install dart
```

Dodajemy wpis w `.zshrc`:

```bash
export PATH="$PATH:/usr/lib/dart/bin"
```

### Flutter

Instalacja `Flutter`'a:

```bash
sudo apt update && sudo apt install clang cmake ninja-build pkg-config libgtk-3-dev liblzma-dev
sudo snap install flutter --classic
flutter sdk-path && flutter --disable-telemetry
```

### Chrome

Dla Flutter'a potrzebny jest Nam `Chrome`:

```bash
su -

cat << EOF > /etc/apt/sources.list.d/google-chrome.list
deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
EOF

wget -O- https://dl.google.com/linux/linux_signing_key.pub |gpg --dearmor > /etc/apt/trusted.gpg.d/google.gpg

sudo apt update && sudo apt install google-chrome-stable
```

### PHP

By zainstalować PHP oraz Composer'a (packet manager), należy:

```shell
sudo add-apt-repository ppa:ondrej/php -y && sudo apt update
sudo apt install php8.3 -y
sudo apt install php8.3-cgi php8.3-cli php8.3-common php8.3-curl
```

```shell
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer
```

W razie czego, Laravel'a instalujemy tak:

```shell
composer global require laravel/installer
```

Dodatkowo można zainstalować `frankenphp` w przypadku niechęci do `Apache'a`:

```shell
curl https://frankenphp.dev/install.sh | sh
mv frankenphp /usr/local/bin/

# Serve the public/ directory
frankenphp php-server -r public/

# Run a command-line script
frankenphp php-cli script.php
```

Generalnie w świecie `PHP` wiele się zmieniło od mojego 'ostatniego razu' z Nim.

## Bonus

### DuckDB

CLI dla `DuckDB` instalujemy z pomocą:

```bash
wget https://github.com/duckdb/duckdb/releases/download/v0.9.2/duckdb_cli-linux-amd64.zip
unzip duckdb_cli-linux-amd64.zip
mv duckdb /usr/bin/
echo "export PATH="$PATH:/usr/bin/duckdb"" >> ~/.zshrc
source ~/.zshrc
```

Headery, używane np. przez Ruby instalujemy tak:

```bash
wget https://github.com/duckdb/duckdb/releases/download/v0.9.2/libduckdb-linux-amd64.zip
unzip libduckdb-linux-amd64.zip -d libduckdb
sudo mv libduckdb/duckdb.* /usr/local/include/
sudo mv libduckdb/libduckdb.so /usr/local/lib
sudo ldconfig /usr/local/lib
gem install duckdb
```
