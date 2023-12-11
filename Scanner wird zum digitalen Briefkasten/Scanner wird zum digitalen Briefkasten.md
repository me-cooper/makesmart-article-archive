# Scanner wird zum digitalen Briefkasten

Für viele klingt es ungreifbar und für mich wird es auch ein Experiment, aber ich möchte meinen Briefkasten digitalisieren. Dabei möchte ich einen Raspberry Pi als Server verwenden, um den Scanner smart zu machen. Smart heißt in diesem Sinne:

- Die Bedienung das Scanners soll auch trotz Modifikation einfach vonstatten gehen
- zudem soll die smarte Bedienung einfach und Intuitiv sein
- wenn mehrere Dokumente nacheinander gescannt wurden, sollen diese zu einer mehrseitigen PDF zusammengefasst werden
- ich möchte meine Nextcloud mit ins Boot holen, um die Dokumente nach dem Scan auf der Cloud verfügbar zu haben

Also wenn alles gut läuft, haben wir nicht nur einen smarten Scanner der ohne zusätzliche Software von Drittanbietern auskommt, sondern auch eine Headless-Bedienung mit der Anbindung zur eigenen Cloud - die sich von nun an auch als digitaler Briefkasten bezeichnen kann.

[![nextcloud_briefkasten](https://web.archive.org/web/20230321102213im_/https://my.makesmart.net/assets/uploads/files/1675435766272-nextcloud_briefkasten.png)](https://web.archive.org/web/20230321102213mp_/https://my.makesmart.net/assets/uploads/files/1675435766272-nextcloud_briefkasten.png)

##  Scanner Inbetriebnahme

Zuerst muss der Scanner mit dem Raspberry Pi verbunden und identifiziert werden. Also ab mit dem Scanner an den Raspberry Pi und nachsehen, ob das Gerät an irgendeiner Stelle erkannt wird. Ich habe ein frisches System und werde einen EPSON Scanner hinzufügen, damit ihr die Schritte so übernehmen könnt. Ob es ein EPSON-Scanner ist oder ein Scanner einer anderen Marke macht hierbei nicht den Unterschied zwischen win und fail.

Mein EPSON Stylus SX235W hat eigentlich schon von Haus aus WLAN und könnte die gewünschten Funktionen evtl. (nicht geprüft) auch ohne DIY Setup umsetzen, aber sehr wahrscheinlich bin ich dann wieder auf zusätzliche Apps und meinen Computer angewiesen. Trotz Wlan ist der Scanner also nicht mehr der neuste, und genau da kommt unser eigenes Setup ins Spiel.

Bevor wir mit der Installation von Software beginnen, sollte die Paketliste und die darin enthaltenen Pakete geupdated werden.

```shell
sudo apt update -y && sudo apt upgrade -y
```

Danach wird SANE installiert. SANE (Scanner Access Now Easy) ist ein Kommandozeilen-Interface, das dazu dient, Scanner wie etwa Multifunktionsgeräte oder Flachbettscanner an einem Computer zu bedienen und zu steuern.

```shell
sudo apt install sane sane-utils
```

Die Pakete die heruntergeladen werden sind ziemlich groß. Bestätigt die Installation mit der gewünschten Eingabe des Systems.

```shell
Nach dieser Operation werden 445 MB Plattenplatz zusätzlich benutzt.
Möchten Sie fortfahren? [J/n]
```

Mit dem Installieren von `sane` und `sane-utils` kam auch das Paket `scanimage` hinzu. Über dieses Toll kann der Scanner absofort angesprochen werden.

Ich prüfe dazu erstmal alle verfügbaren Scanner am Raspberry Pi mit folgendem Befehl:

```shell
sudo scanimage --list-devices
user@server:~ $ sudo scanimage --list-devices
Created directory: /var/lib/snmp/cert_indexes

No scanners were identified. If you were expecting something different,
check that the scanner is plugged in, turned on and detected by the
sane-find-scanner tool (if appropriate). Please read the documentation
which came with this software (README, FAQ, manpages).
```

Jetzt schalte den Scanner ein und schaue nach, ob er ordnungsgemäß erkannt wird.

```shell
user@server:~ $ sudo scanimage --list-devices
device `epson2:libusb:001:004' is a Epson PID 0885 flatbed scanner
```

Wenn das der einzige Scanner ist, der angeschlossen werden soll, könnt ihr mithilfe des folgenden Befehls direkt ein Dokument scannen.

```
sudo scanimage --format=jpeg --resolution=300 -p > output.jpg
```

Es wird ein Bild mit dem Namen `output.jpg` erzeug. Ich bin bereits per FTP mit dem Server verbunden und sehe das Bild in dem Verzeichnis, in dem ich den Befehl aufgerufen habe.

Um das Dokument in Farbe zu scannen, kann der `--mode color` hinzugefügt werden.

```
sudo scanimage --format=jpeg --resolution=300 --mode color -p > output.jpg
```

Mit diesen beiden Befehlen können jetzt Dokumente und Bilder eingescannt werden.

##  Konvertieren der Bilder zu einer PDF-Datei

Um die einzeln eingescannten Dokumenten-JPGs nun als PDF zusammenzufassen kann ich euch das Tool `imagemagick` empfehlen. Mit imagemagick könnt ihr unter anderem ganz einfach Bilder zu PDF konvertieren lassen. Dabei werden einzelne Dateien unterstützt:

```shell
convert ouptput.jpg -quality 100 output.pdf
```

Um mehrere Dateien gleichzeitig zu einer PDF zusammenzufassen empfiehlt es sich, einen kleinen Namespace anzulegen. Als Beispiel haben wir drei Dateien:

- scan_10.jpg
- scan_11.jpg
- scan_12.jpg

Alle drei Bilder lassen sich der Nummerierung nach zu einer PDF zusammenfassen:

```bsh
convert scan_*.jpeg -quality 100 collectedPDF.pdf
```

Das Sternchen steht dabei für einen Platzhalter und das Kommandozeilen Tool sucht sich automatisch die Bilder in der Passenden Reihenfolge.

***Tipp**: Fangt bei 10 zu zählen an, nicht bei 1. Falls ihr weniger als 10 Dokumente hat spielt es keine Rolle. Solltet ihr aber Dokument 11 oder 12 haben, so kann es passieren, dass die Reihenfolge durcheinanderrutscht.*

##  Die eigene Scanner API mit Node.JS

Scannen mithilfe von Befehlen ist schön und recht. Das bietet uns aber leider noch keine Möglichkeit, den Scanner von einem anderen Gerät aus zu bedienen. Um den Scanner automatisiert bedienen zu können, aber ich mir eine kleine Node.JS API programmiert.

Diese API lässt es über den Endpoint `192.168.0.1:4444/scan` zu, einen Scanvorgang zu starten. Über einen weiteren Endpoint der API `192.168.0.1/collect` lassen sich die einzelnen Scan-JPGs als PDF zusammenfassen.

Beim Aufruf der API führt meine Node.JS Anwendung zum einen die oben genannten Befehle aus.

```shell
sudo scanimage --format=jpeg --resolution=300 --mode color -p > output.jpg
convert scan_*.jpeg -quality 100 collectedPDF.pdf
```

Zum anderen wurde auch die komplette Logik in Node.JS implementiert, da man so einfach mehr Möglichkeiten hat.

Auf den Programmcode von Node.JS möchte ich nicht detailliert eingehen, aber im Endeffekt beschränkt er sich auf einen einfachen Node.JS -Express-Webserver der die Endpoints bereitstellt. Dazu werden lediglich folgende Pakete und Variablen global benötigt.

###  VARIABLEN UND MODULE

```javascript
const express = require('express');
const execSh = require("exec-sh");
var fs = require('fs');

// Einstellungen
var newSession = true;
var scanSite = 10;
```

###  API ZUM SCANNEN

```javascript
app.get('/scan', async (req, res) => {
    if (!fs.existsSync("scans")){
        fs.mkdirSync("scans");
    }
    scanSite = scanSite + 1;
    if(newSession){
        scanSite = 10;
        newSession = false;
    }
    var singleFileName = "scan_" + scanSite;
    // Direkt die Antwort senden, damit der Kurzbefehl nicht so lange braucht
    res.status(200).send({
        code: 200,
        status: "unknown",
        filename: singleFileName
    });
    execSh("echo 'Versuche Terminal zu öffnen ...'", { cwd: "/home" }, (err) => {
        if (err) {
            console.log("Kein Zugriff auf das Terminal möglich", err.code);
            return;
        }
        // Hier wird der Terminal Befehl zum Scannen abgesetzt
        const child = execSh(["sudo bash ./bash/scan.sh 300 " + singleFileName], true, (err, stdout, stderr) => {
            if (err) {
                console.log("error: ", err);
                return;
            }               
        });
    });
});
```

###  API ZUM ZUSAMMENFASSEN DER SCANS

```javascript
app.get('/collect', (req, res) => {
    // Filename = Datum
    var filename = getdate();
    // Damit der Kurzbefehl nicht so lange braucht,
    // wird direkt eine Antwort vom Server geschickt
    res.status(200).send({
        code: 200,
        status: "unknown",
        filename: filename
    });
    // Hier werden Shell Befehle abgesetzt 
    execSh("echo 'Versuche Terminal zu öffnen ...'", { cwd: "/home" }, (err) => {
        if (err) {
            console.log("Kein Zugriff auf das Terminal möglich", err.code);
            return;
        }
        // Hier werden die Scans konvertiert und zusammengefasst
        const child = execSh(["sudo convert ./scans/scan_*.jpg -compress jpeg " + filename + ".pdf"], true, (err, stdout, stderr) => {
            if (err) {
                console.log("Anforderung: Collect Scans. \n Keine Dateien im Ordner `scans` vorhanden.");
                return;
            }      
            // Hier wird die neue zusammengefasste Datei in das Verzeichnis "inbox" verschoben
            var oldPath = filename + ".pdf";
            var newPath = "inbox/" + filename + ".pdf";
            fs.rename(oldPath, newPath, function (err) {
                if (err) throw err
                console.log('Datei wurde verschoben.')
                fs.rmSync("scans", { 
                    recursive: true, 
                    force: true 
                });
                // Damit mein Programm weiß, dass der Namespace wieder zurückgesetzt wird
                // Wichtig für den Namespace scan_10, scan_11, scan_12, ...
                newSession = true;
            })       
        });
    });
});
```

##  Scannen am Smartphone

Um die Bedienung noch einfacher zu machen, habe ich meine Node.JS-API via Homebridge in Apple Home eingebunden und kann so den Scanner per Sprachbefehl bedienen. Das ist aber eher “für den Fall der Fälle”. Also wenn man sein Handy nicht griffbereit hat. Denn in der Regel kann ich den Scanner mit zwei Kurzbefehlen, ebenfalls auf iOS bedienen. Diese Kurzbefehle callen die API und diese führt dann entsprechende Aktionen aus.

Über den Kurzbefehl “Scan hochladen” wird vorher noch gefragt, ob ich dem Dokument einen Namen geben will. Wenn das der Fall ist. Wird auf den Endpoint`192.168.0.1/collect/:name` zurückgegriffen, über den ich einen Namen vergeben kann. Wird kein Name vergeben, wird der aktuelle Zeitstempel als Name für den Scan verwendet.

##  Deine eigene Scanner API

In meinem Fall ist es ein Node.JS Backend, dass die Anfragen verwaltet, auswertet und dann lediglich die Befehle zum Scannen und Zusammenfassen absetzt. Aber im Prinzip kann es alles sein. Ihr könnt eine eigene Logik definieren und dann die Aktionen zum Scannen und Zusammenfassen mit eurem eigenen Programm in Verbindung bringen. PHP, Ruby, C++ oder Java. Am Ende des Tages sind es zwei Befehle, die euren Scanner smart machen.

```shell
sudo scanimage --format=jpeg --resolution=300 --mode color -p > output.jpg
convert scan_*.jpeg -quality 100 collectedPDF.pdf
```

##  Nextcloud Einbindung

Es klingt total spannend und spektakulär, dass die Scans in der Nextcloud laden. Aber tatsächlich ist es ein FTP-Server, der die Dateien auf der Nextcloud verfügbar macht.

```javascript
fs.rename(alteDatei, neueDatei, function (err) {
    if (err) throw err
    console.log('Datei wurde verschoben.')
    // Ordner für einzelene Scans löschen
    fs.rmSync("scans", { 
        recursive: true, 
        force: true 
    });
    newSession = true;
})     
```

Dieser kleine Code-Block in meinem Node.JS Programm verschiebt die Datei nach erfolgreicher Konvertierung in ein anderes Verzeichnis und macht Platz für die neuen Scans. Das Verzeichnis in das die Scans verschoben werden, ist via External Storage FTP in Nextcloud eingebunden. Das macht alle Dateien im Ordner `inbox` auf der Nextcloud verfügbar. Dort aber im Namen von `01_Breifkasten`.

[![nextcloud_external_storage](https://web.archive.org/web/20230321102213im_/https://my.makesmart.net/assets/uploads/files/1675435727714-nextcloud_external_storage-resized.png)](https://web.archive.org/web/20230321102213mp_/https://my.makesmart.net/assets/uploads/files/1675435727714-nextcloud_external_storage-resized.png)