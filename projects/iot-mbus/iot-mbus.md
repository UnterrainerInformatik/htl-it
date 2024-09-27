# IOT-Mbus
# Installation

```bash

wget [http://www.rscada.se/public-dist/libmbus1_0.8.0_amd64.deb](http://www.rscada.se/public-dist/libmbus1_0.8.0_amd64.deb)

wget [sudo sulibmbus-dev_0.8.0_amd64.deb](http://www.rscada.se/public-dist/libmbus-dev_0.8.0_amd64.deb)

sudo dpkg -i libmbus1_0.8.0_amd64.deb libmbus-dev_0.8.0_amd64.deb

# to test...

which mbus-tcp-scan

# should be /usr/bin/mbus-tcp-scan

# primary scan

sudo mbus-serial-scan -b 9600 /dev/ttyUSB0

# secondary scan

sudo mbus-serial-scan-secondary -b 9600 /dev/ttyUSB0

```

# Connect USB in WSL2

```bash

# install usbipd

https://learn.microsoft.com/en-us/windows/wsl/connect-usb#install-the-usbipd-win-project

# start powershell in admin-mode!

# list devices

usbipd list

# find the one you want to bind

usbipd bind --busid 8-3

# attach it for exclusive WSL use

usbipd attach --wsl --busid 8-3

# now you can switch to WSL and test it

lsusb

# to remove it

usbipd detach --busid 8-3

```

## Install directly on PI (arm)
Wir haben es nicht geschafft den USB-Port auf WSL2 freizugeben, sodass er voll erkannt wird und unter `/dev/ttyUSB0` erreichbar ist.
Deswegen haben wir einen PI4 aufgesetzt (Ubuntu 24.02.LTS) und `libmbus` direkt darauf gebuildet.
```bash
# install build tools
sudo apt install automake autoconf libtool gcc -y

# clone the repository from
# https://github.com/rscada/libmbus
git clone ...

./configure && make install

# fix false shared library path in ubuntu
sudo ln -s /usr/local/lib/libmbus.so.0 /usr/lib/libmbus.so.0

# scan
mbus-serial-scan -b 2400 /dev/ttyUSB0
mbus-serial-scan-secondary -b 2400 /dev/ttyUSB0
# exapmle
mbus-serial-request-data -b 2400 /dev/ttyUSB0 0
```

## Nächste Schritte
- Der Pi sollte dauerhaft im Netz hängen
- Er sollte erreichbar sein
	- SSH
	- auch von 'außen', damit ich gut drauf arbeiten kann
- Er sollte den MQTT-Server erreichen können
- Gerald Unterrainer kann dann ein Script schreiben, das alle 5 Sekunden (der Sensor macht 10) die Werte ausliest und an MQTT weiterschickt. Stichwort: Crontab on-boot script mit sleep 5 und mosquitto-send.
- Damit können wir dann alle Daten an den Server schicken.
- Aufzeichnung
	- IO-Broker mit InfluxDB-Schnittstelle, die die Daten speichert
	- Oder eigene InfluxDB-Installation mit beliebiger Bridge
# Links

- [http://www.rscada.se/libmbus/index.php?lang=en](http://www.rscada.se/libmbus/index.php?lang=en)
- [https://manpages.opensuse.org/Tumbleweed/libmbus/index.html](https://manpages.opensuse.org/Tumbleweed/libmbus/index.html)
- [https://manpages.opensuse.org/Tumbleweed/libmbus/mbus-serial-request-data.1.en.html](https://manpages.opensuse.org/Tumbleweed/libmbus/mbus-serial-request-data.1.en.html)
- [https://github.com/wmbusmeters/wmbusmeters](https://github.com/wmbusmeters/wmbusmeters)
- [https://the78mole.de/taking-your-m-bus-online-with-mqtt/](https://the78mole.de/taking-your-m-bus-online-with-mqtt/)
- [https://forum.heimnetz.de/threads/wired-mbus-usb-adapter-an-raspberry-2b-fuer-mqtt-homematic-jemand-erfahrung.1192/](https://forum.heimnetz.de/threads/wired-mbus-usb-adapter-an-raspberry-2b-fuer-mqtt-homematic-jemand-erfahrung.1192/)
- [https://learn.microsoft.com/en-us/windows/wsl/connect-usb](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)
- [https://learn.microsoft.com/en-us/windows/wsl/connect-usb#install-the-usbipd-win-project](https://learn.microsoft.com/en-us/windows/wsl/connect-usb#install-the-usbipd-win-project)