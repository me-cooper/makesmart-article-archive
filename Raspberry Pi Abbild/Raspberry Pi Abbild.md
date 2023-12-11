# Raspberry Pi Abbild der SD-Karte erstellen

Habt ihr euch jemals gefragt, wie man den aktuellen Stand des Betriebssystems / der SD-Karte sichern kann, um das gesicherte Backup jederzeit wieder aufspielen zu können? Nein? Na ja ich schon.

###  WAS BRAUCHE ICH?

Um das Abbild einzig und allein mit dem Pi zu erzeugen, benötigt ihr zusätzlich einen USB-Stick oder eine externe Festplatte, **mit mehr freiem Speicherplatz**, wie die SD-Karte im Pi Kapazität hat.

**Warum?**

Es wird ein 1:1 Abbild erzeugt - 1:1 bedeutet in dem Fall wirklich Bit für Bit. Das Abbild wird genau so groß wie die SD-Karte. Egal wie viele Daten darauf gespeichert sind.

------

**Wichtig**

Der USB-Stick / die Festplatte auf dem das Abbild gespeichert werden soll, sollte **nicht mit FAT32 formatiert sein**, da die maximale Dateigröße für das Dateisystem 4GB ist, was es für Backups absolut unbrauchbar macht. Beim Formatieren unter Windows lässt sich das Dateisystem einfach ändern. **Nehmt am besten NTFS.**

[![formatierungs_manager.png](https://web.archive.org/web/20221201235419im_/https://my.makesmart.net/assets/uploads/files/1585243325144-formatierungs_manager.png)](https://web.archive.org/web/20221201235419mp_/https://my.makesmart.net/assets/uploads/files/1585243325144-formatierungs_manager.png)

------

#  Anleitung

Lasst euch zuerst alle angeschlossenen Blockgeräte des Pi’s anzeigen.
Gebt dazu den Befehl `lsblk` ein.

```shell
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0     179:0    0 14.7G  0 disk
├─mmcblk0p1 179:1    0 43.9M  0 part /boot
└─mmcblk0p2 179:2    0 14.7G  0 part /
```

`mmcblk0` - ist die komplette SD-Karte

`mmcblk0p1` und `mmcblk0p1` sind jeweils Partitionen der SD-Karte.

------

Steckt nun den USB-Stick / Festplatte in den Pi und sendet erneut den Befehl `lsblk` ab.

```shell
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    1 29.9G  0 disk
└─sda1        8:1    1 29.8G  0 part /media/makesmart/283A-4AA0
mmcblk0     179:0    0 14.7G  0 disk
├─mmcblk0p1 179:1    0 43.9M  0 part /boot
└─mmcblk0p2 179:2    0 14.7G  0 part /
```

Wir sehen jetzt ebenfalls den USB-Stick mit der Bezeichnung `sda`.

Die Partition vom USB-Stick mit der Bezeichnung `sda1` ist in den Pfad

```
/media/makesmart/283A-4AA0
```

gemountet. **In diesen Pfad muss das Image später gespeichert werden!**

------

###  ABBILD ERSTELLEN

Um das Abbild der SD-Karte auf den USB-Stick zu kopieren setzen wir folgenden Befehl ab in der Konsole ab:

```shell
sudo dd if=/dev/mmcblk0 of=/media/makesmart/283A-4AA0/abbild.img status=progress
```

------

```shell
if=/dev/mmcblk0
```

Das ist der Befehl für die **input file**. Der Datenträger von dem das Abbild erstellt werden soll. Also Die SD-Karte mit der Bezeichnung `mmcblk0`.

------

```shell
of=/media/makesmart/283A-4AA0/abbild.img
```

Das ist der Pfad für die **output file**, in dem das Image gespeichert werden soll.
Mein USB-Stick ist, wie gesagt, in dem Pfad `/media/makesmart/283A-4AA0/` gemountet. Dem Abbild gebe ich den Namen `abbild.img`.

------

```shell
status=progress
```

Dieser Befehl zeigt eine Prozessbar während dem Vorgang an.

------

Nach dem Absenden des Befehls wird das Abbild erzeugt.

```shell
207868416 bytes (208 MB, 198 MiB) copied, 13 s, 16.0 MB/s
```

Das kann auf einem Pi einige Zeit lang dauern. Bei mir entsteht das Abbild mit einer Geschwindigkeit von `16.0 MB/s`.

------

Da das Abbild nach Abschluss so groß ist wie die SD-Karte Kapazität hat, sollte man das Image danach noch verkleinern.

Eine einfache Möglichkeit ist das Bash-Script [PiShrink](https://web.archive.org/web/20221201235419/https://github.com/Drewsif/PiShrink).