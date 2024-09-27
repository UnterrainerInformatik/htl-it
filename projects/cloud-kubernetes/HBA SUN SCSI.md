# HBA SUN SCSI
- Passwort für Monitor-Rechner und alles andere im Cluster
	- Bylanz+13?
- IP Adressen
	- Monitoring Rechner (DHCP) 

## Brocade 300
- Wird vom Admin-Rechner aus administriert über ein Web-Interface
- Es darf nur eine bestimmte Java-Version installiert sein (Java-Update ist auf der Admin-Maschine abgedreht)
- Wenn man die GUI über den Browser (localhost) startet, dann geht es nicht, weil erstmal das Java-Update gemacht werden will. In der Datei `Brocade 300.cmd` am Desktop steht die genauere Anleitung, wie man dort aber trotzdem hinkommt (Link).
- Zuerst muss man sicher aber mit Putty am Switch anmelden, sonst geht die GUI nicht ordentlich.
- Putty ist schon eingerichtet... anmelden... warten und dann `Enter` drücken (sonst nur schwarzer Bildschirm) und anmelden mit `root` und Passwort.
- Im Putty sind dann die Brocade-eigenen Befehle zum Setzen und auslesen der IP Config verfügbar (ipAddrShow, ipAddrSet... ip{tab-drücken})
- Danach auf die Webseite gehen und vielleicht startet dann die JAVA-GUI... Falls nicht... nochmal probieren... ev. als Admin ausführen... ev. ohne Putty-Anmeldung versuchen... geht nicht immer
- Im Admin-Interface gibt es mehrere Punkte
	- Domain Config
		- Ist dafür da, dass 2 oder mehrere Brocade Switches sich untereinander abgleichen (HA)
	- Zone Config
		- Damit 2 Geräte miteinander reden können, müssen sie in der gleichen Zone sein
		- Zone erstellen
			- Im mittleren Karteireiter machen, nicht im rechten (speichert nicht)
	- Switch Config
		- User-Verwaltung
		- Lizenzen
		- ...
	- Port Config
		- Ports anzeigen
			- Fehler, Datendurchsatz
		- Ports umbenennen
- https://www.elasticsky.de/2012/02/zoning-brocade-300/
- Zones
```bash
alicreate "right", "50:05:07:68:03:02:7d:8b"
alishow
zone --show
cfgcreate "leocloud", "right; z_uranus"
cfgsave
cfgenable "leocloud"
#später... für jeden
cfgadd "leocloud", "z_mars"

# Zone löschen
zonedelete "leocloud"
```