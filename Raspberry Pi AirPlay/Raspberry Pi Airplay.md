# Raspberry Pi mit Stereoanlage wird zum AirPlay Speaker

### 1. WAS BRAUCHT MAN?

1. **einen Raspberry Pi** [mit installierten Betriebssystem](https://web.archive.org/web/20211202135853/https://my.makesmart.net/topic/42/tutorial-betriebssystem-auf-den-raspberry-pi-brennen)
   Es ist egal, ob bereits [eine Cloud](https://web.archive.org/web/20211202135853/https://my.makesmart.net/topic/37/programm-nextcloud-installer) oder ein [Homebridge-Server auf dem Pi](https://web.archive.org/web/20211202135853/https://makesmart.net/homebridge-installieren-raspberry-pi/) oder was auch immer installiert ist. Der Pi kann zusätzlich dafür verwendet werden, um als AirPlay-Empfänger zu dienen.
2. **eine Stereoanlage mit 3,5mm Klinkenanschluss als Lautsprecher**
   Der Lautsprecher wird über die Klinke mit dem Pi verbunden. Ich mache das hier auf einem Raspberry Pi 2. Es kann aber jeder andere Pi mit Klinken-Ausgang sein.

#  Die Installation

Macht zuerst ein Update der Paketliste und anschießend ein Upgrade der Pakete:

```shell
sudo apt update
sudo apt upgrade
```

Führt anschließend nacheinander folgende Befehle aus:

```shell
wget -q https://github.com/me-cooper/rpi-audio-receiver/archive/master.zip
unzip master.zip
rm master.zip
cd rpi-audio-receiver-master
./install.sh
```

Danach könnt ihr theoretisch schon den Namen des Lautsprechers festlegen:

```shell
Hostname [raspberry pi]: raspberrypi
Pretty hostname [Raspberry Pi]: Raspberry Pi
```

Ich **verwende erstmal die vorgegebenen Daten in den eckigen Klammern**, da ich bestehende Software nicht crashen will. Den Namen des Lautsprechers passe ich später an.

Falls ihr das Risiko eingehen möchtet:
Die Eingabe bei `Pretty hostname` würde als Name des Lautsprechers verwendet werden.

------

Danach werden ein paar Fragen gestellt, was alles installiert werden soll. Neben der AirPlay-Funktion wird im Script noch folgendes angeboten:

- **Bluetooth**
- **Spotify Connect**
- **UPnP**
- **Snapcast**

```shell
Do you want to install Bluetooth Audio (BlueALSA)? [y/N]
Do you want to install Shairport Sync AirPlay Audio Receiver (shairport-sync v)? [y/N]
Do you want to install Spotify Connect (spotifyd)? [y/N]
Do you want to install UPnP renderer (gmrender-resurrect)? [y/N]
Do you want to install Snapcast client (snapclient})? [y/N]
```

Ich habe lediglich den AirPlay-Server installiert.

```shell
Do you want to install Shairport Sync AirPlay Audio Receiver (shairport-sync v)? [y/N] y
```

------

Nach der Installation ist der Lautsprecher in den AirPlay-Geräten sichtbar und kann verwendet werden.

------

#  Name des Lautsprechers ändern

Der Name des Lautsprechers kann mit

```shell
sudo nano /etc/shairport-sync.conf
```

geändert werden.

```
general = {
  name = "Stereoanlage";
}
```

Nach dem Speichern mit `Strg + O`und dem Verlassen mit `Strg + X` sollte der Raspberry Pi neugestartet werden, damit der Name des Lautsprechers aktualisiert wird.

```shell
sudo reboot now
```

------

Danach kann die Stereoanlage am Pi als AirPlay-Speaker verwendet werden.
Nicht nur am Handy. Auch der Apple TV und das MacBook / iPad / … können die Stereoanlage als Lautsprecher auswählen.

[![airplay_stereoanlage.jpg](https://web.archive.org/web/20211202135853im_/https://my.makesmart.net/assets/uploads/files/1585393244498-airplay_stereoanlage.jpg)](https://web.archive.org/web/20211202135853mp_/https://my.makesmart.net/assets/uploads/files/1585393244498-airplay_stereoanlage.jpg)

Viel Spaß mit dem AirPlay-Lautsprecher… haut rein! ![👌](https://web.archive.org/web/20211202135853im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44c.png?v=chnl3kn5hqg)

####  Lautstärke des Audio-Ausgangs ändern

Um die Lautstärke des Klinkenausgangs zu ändern, könnt ihr folgenden Befehl verwenden:

```shell
alsamixer
```

Danach kann die Lautstärke über die Pfeiltasten angepasst werden.
Das Menü könnt ihr mit `Strg + C` verlassen.