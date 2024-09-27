# Projektübersicht
## Team
[[Cloud]]
## Beschreibung

### Spare Names
| Earth<br><br>Sun<br><br>ISS<br><br>Europa<br><br>Io<br><br>Ganymede<br><br>Callisto<br><br>Titan<br><br>Oberon |     | Vorschlag:<br><br>Sonne ist Fileserver :), UY Scuti (größter bekannter Stern) | Monde von Uranus und Rest von Jupiter… etc… |     |     | Loadbalancers:<br><br>blackhole<br><br>Supernova<br><br>Pulsare:<br><br>Vela<br><br>Crab<br><br>Geminga<br><br>Taz<br><br>Eel<br><br>Rabbit |
| -------------------------------------------------------------------------------------------------------------- | --- | ----------------------------------------------------------------------------- | ------------------------------------------- | --- | --- | ------------------------------------------------------------------------------------------------------------------------------------------- |
# Root Passwort
NiceLibrariansDocumentViciously
# Netzwerkkonfiguration
## NEU

| KEY     | VALUE                        |
| ------- | ---------------------------- |
| ip      | 172.18.249.x/24              |
| subnet  | 255.255.255.0                |
| gateway | 172.18.249.254               |
| dns     | 10.191.16.254<br>10.191.80.6 |
### Netz-Aufteilung
1-10 irgendwas wichtiges
10-100 Nodes und Kubernetes Rechner
100-200 Netzwerk-Equipment und SAN Rechner
200-250 VPNs
254 Gateway
## External

| KEY     | VALUE           |
| ------- | --------------- |
| ip      | 192.88.23.x/25  |
| subnet  | 255.255.255.128 |
| gateway | 192.88.23.126   |
| dns     | 8.8.8.8         |
## Internal

| KEY     | VALUE          |
| ------- | -------------- |
| ip      | 10.191.16.x/20 |
| subnet  | 255.255.240.0  |
| gateway | 10.191.17.100  |
| dns     | 8.8.8.8        |
## Beispiel-Config
## sudo nano /etc/network/interfaces
```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
source /etc/network/interfaces.d/*
# The loopback network interface
auto lo
iface lo inet loopback
# The primary network interface
allow-hotplug eno1
iface eno1 inet static
        address 192.88.23.8/25
        gateway 192.88.23.126
# The secondary network interface
allow-hotplug eno2
iface eno2 inet static
        address 10.191.16.74/20
        up route add -net 10.211.211.0 netmask 255.255.255.0 gw 10.191.17.100
        down route del -net 10.211.211.0 netmask 255.255.255.0 gw 10.191.17.100
```
## sudo nano /etc/resolv.conf

```bash
domain cloud.htl-leonding.ac.at
search cloud.htl-leonding.ac.at
#nameserver 10.191.16.254
#nameserver 10.191.80.6
nameserver 8.8.8.8
nameserver 8.8.4.4
```
# SSH Konfiguration
Debian 12 unterstützt wegen der neueren SSH-Version keine RSA-Keys mehr.
Nurmehr ECDSA…
## Key auf lokaler Maschine (Client) anlegen
```bash
# ECDSA...
ssh-keygen -t ecdsa -b 521 -C "mail@example.com"
```
## Key am Server eintragen
```bash
mkdir ~/.ssh
chmod 0700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 0644 ~/.ssh/authorized_keys
```
## Keyfiles mit Abkürzungen auf lokaler Maschine eintragen
ProxyJump funktioniert mit beiden Reverse-Proxies `vela` und `crab`.
```bash
Host vela
  HostName 192.88.23.10
  User psilo
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host crab
  HostName 192.88.23.11
  User psilo
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host saturn
  HostName 172.18.249.19
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host moon
  HostName 172.18.249.18
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host venus
  HostName 172.18.249.17
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host mars
  HostName 172.18.249.16
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host jupiter
  HostName 172.18.249.15
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host uranus
  HostName 172.18.249.14
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host sun
  HostName 172.18.249.13
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

Host scuti
  HostName 172.18.249.12
  User psilo
  ProxyJump vela
  IdentityFile ~/.ssh/htl-kube_ecdsa

# You can use it like this now.  
# You even have tab-completion.  
ssh saturn
```
# Neuer SSH/SUDO-User
```bash
# Create new user with home-directory.
sudo adduser newuser

# Add to sudo group
sudo usermod -aG sudo newuser
```
# Others
```bash
# rename a user...
sudo usermod -l newuser olduser && sudo usermod -d /home/newuser -m newuser && sudo chown -R newuser: /home/newuser && sudo cat /etc/passwd
# full update
sudo apt update -y && sudo apt upgrade -y && sudo apt autoremove -y && sudo apt update -y
```
# Neuer Server Setup Script
```bash
# We assume you're sudo here.
apt update -y
apt upgrade -y
apt install -y sudo htop net-tools pv bc ack mtr tree mc ncdu btop
```
## Neuer IP Scope

## Email von Thomas
```
(ist ein internes Netz, aber mit einer ACL, welche die internen IPs ausklammern sollte).

172.18.249.0/24 (172.18.249.1-172.18.249.253)  
GW 172.18.249.254   
Nameserver 10.191.16.254
oder externer falls interne IPs nicht gebraucht werden  

auf dem Zuleitungsswitch ist nun auf Port 19 (wenn ich mich richtig  
erinnere ist das der linke unter den roten Patchkabeln über denen ein  
Proxmox-Logo ist) das unten genannte VLAN anliegen. Habe es nun auch  
ausprobiert und einen Fehler in der ACL behoben, der verhinderte, dass  
man aufs Internet zugreifen kann.  
  
Somit solltet Ihr den Uplink der Ministeriumscloud auf den Port 19  
umstecken können sobald ihr die IPs geändert habt und ihr solltet  
darüber wieder ins Internet kommen.
```