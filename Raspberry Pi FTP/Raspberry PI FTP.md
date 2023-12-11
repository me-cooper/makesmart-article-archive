# FTP-Server auf dem Raspberry Pi installieren

FTP steht für ***F**ile **T**ransfer **P**rotocol* und ist ein Netzwerkprotokoll zur Übertragung von Dateien. FTP wird meistens dafür benutzt, um Dateien vom Client auf den Server hochzuladen, oder Dateien vom Server auf den Client herunterzuladen.

Der Server hinter dem FTP kann hierbei als alles Mögliche dienen:

- als Webserver
- als Dateiserver
- als Downloadserver
- oder einfach nur als ein Server zuhause, zum rumspielen und basteln

Ich für meinen Teil nutze meinen lokalen makesmart-server als Testserver und möchte meine Webanwendungen leichter bearbeiten können. Hierzu soll der FTP-Server dienen. Der FTP-Server ermöglicht es mir unter anderem, die Dateien direkt auf dem Server zu bearbeiten. Damit ist das runterladen - bearbeiten - hochladen, was mir so hart auf die Eier geht, endlich vorbei. ![🍾](https://web.archive.org/web/20221006045745im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f37e.png?v=chnl3kn5hqg)

Als Software verwende ich [ProFTPD](https://web.archive.org/web/20221006045745/http://www.proftpd.org/).

> ProFTPD is a high-performance, extremely configurable, and most of all a secure FTP server, featuring Apache-like configuration …

#  Installation

####  ProFTPD installieren

```shell
sudo apt install proftpd-basic
```

------

####  Konfiguration

Nach der Installation erstellen wir eine Konfigurationsdatei. Die Konfiguration bleibt von Updates der Software unberührt. Wir sind also auf der sicheren Seite. Der Inhalt des Verzeichnisses `conf.d`, in dem wir die Konfiguration speichern, wird von der Software immer automatisch importiert.

```shell
sudo nano /etc/proftpd/conf.d/myftp.conf
```

Die Konfiguration bekommt folgenden Inhalt. *Die Zeilen sind in der Konfiguration erklärt.*

```apacheconf
# Das Home-Verzeichnis ~  ist das Stammverzeichnis der Benutzer in den Gruppen myftp und root

DefaultRoot ~ myftp,root

# Login wird nur Benutzern der Gruppe myftp erlaubt
<Limit LOGIN>
    DenyGroup !myftp
</Limit>
```

Damit diese Änderungen wirksam werden, muss der FTP-Server einmal neugestartet werden.

```shell
sudo systemctl restart proftpd
```

------

####  Gruppe “myftp” erstellen

Nachdem nur Benutzer der Gruppe `myftp` Zugriff auf den FTP-Server erhalten, werden wir diese Gruppe als nächstes auf unserem Server anlegen.

```shell
sudo addgroup myftp
```

------

####  Nutzer der Gruppe hinzufügen

Alle Nutzer, die Zugriff erhalten sollen, müssen dann im nächsten Schritt der Gruppe hinzugefügt werden. Bei mir ist das der einzige Nutzer des Servers: `makesmart`.

```shell
sudo adduser makesmart myftp
```

------

#  Login FTP

Schon jetzt kann man sich mit einem FTP-Client, wie FileZilla, auf dem Server einloggen. Die Daten für den FTP-Login sind die gleichen, wie die Daten die man für den SSH-Login verwendet. In meinem Fall lauten die Zugangsdaten:

| Hostname         | Benutzername | Passwort  | Port |
| :--------------- | :----------- | :-------- | :--- |
| makesmart-server | makesmart    | makesmart | 21   |

![ftp_tls.png](https://web.archive.org/web/20221006045745im_/https://my.makesmart.net/assets/uploads/files/1586973426816-ftp_tls.png)

Auch wenn eine Verschlüsselung in einem geschlossenen lokalem Netzwerk nicht unbedingt notwenidg wäre, werden wir uns darum gleich noch kümmern. ![👍](https://web.archive.org/web/20221006045745im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44d.png?v=chnl3kn5hqg)

------

Nach dem Einloggen befinden wir uns im Home-Verzeichnis des Nutzers, mit dem wir uns eingeloggt haben. ![✔](https://web.archive.org/web/20221006045745im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/2714.png?v=chnl3kn5hqg)

```markup
/home/makesmart
```

In dem Verzeichnis können wir Daten ablegen und bearbeiten, Ordner anlegen und löschen.

------

#  Erweitere Rechte

In meinem Fall wollte ich den FTP-Server installieren, um die Dateien meiner Webanwendungen direkt auf dem Server bearbeiten zu können. Das Verzeichnis befindet sich unter `/var/www/html`. Wenn ich diesen Pfad über den FTP-Client aufrufe, kann ich die Dateien zwar sehen, aber weder löschen, noch bearbeiten oder gar neue erstellen …

```
Status:	  Lösche "/var/www/html/index.html"
Befehl:	  DELE index.html
Antwort:  550 index.html: Permission denied
```

Der Grund dafür sind die fehlenden Berechtigungen für den Ordner. Die Lösung ist sehr simple:
Wir machen unseren Nutzer `makesmart` einfach zum Eigentümer den Ordners.

```shell
sudo chown -R makesmart:makesmart /var/www/html
```

Jetzt können wir, neben dem Home-Verzeichnis, auch die Daten im Ordner `/var/www/html` frei verwalten.

```
Status:	Lösche "/var/www/html/index.html"
Status:	Empfange Verzeichnisinhalt für "/var/www/html"...
Status:	Anzeigen des Verzeichnisinhalts für "/var/www/html" abgeschlossen
```

------

#  Verbindung verschlüsseln

Wie bereits erwähnt, ist eine Verschlüsselung im lokalen Netzwerk nicht unbedingt erforderlich. Wer aber auf der sicheren Seite sein möchte, kann die folgende Anleitung verwenden, um die Verbindung zu verschlüssen.

Für die Verschlüsselung wird ein SSL-Zertifikat benötigt. Wie man ein solches Zertifikat selbst generieren, und für einen Apache2-Webserver verwenden kann, [habe ich bereits in einem Tutorial gezeigt](https://web.archive.org/web/20221006045745/https://my.makesmart.net/topic/41/tutorial-ssl-zertifikat-für-den-lokalen-server-erstellen).

Die gleichen Befehle können genutzt werden, um ein eigenes Zertifikat für den FTP-Server zu erstellen.

------

####  OpenSSL installieren

```shell
sudo apt-get install openssl
```

####  Ordner für die Zertifikate anlegen

```shell
sudo mkdir /etc/ssl/localcerts
```

####  Zertifikat erstellen

```shell
sudo openssl req -new -x509 -days 365 -nodes -out /etc/ssl/localcerts/proftpd.pem -keyout /etc/ssl/localcerts/proftpd.key
```

Der Parameter `-days 365` bedeutet, dass das *Zertifikat 1 Jahr lang gültig* ist.
Die beiden Pfade bestimmen den Speicherort und den Namen der Zertifikate.

Es wird nach euren Daten gefragt, die ihr Eingeben müsst. Es spielt dabei keine Rolle, ob die Daten der Wahrheit entsprechen, oder nicht. Sie dienen nur zum Füllen der Informationen des Herausgebers des Zertifikats (ihr).

####  Rechte der Zertifikate anpassen

```shell
sudo chmod 600 /etc/ssl/localcerts/proftpd*
```

------

Anschließend muss nochmal die zu Beginn erstellte .conf-Datei `myftp.conf` bearbeitet werden.

```shell
sudo nano /etc/proftpd/conf.d/myftp.conf
```

Fügt am Ende folgende Zeilen ein. Die Zeilen bestimmten die Pfade der Zertifikate und einige Einstellungen zu TLS. Unter anderem wird mit der Zeile `TLSRequired on` die Verbindung via TLS erzwungen.

```apacheconf
<IfModule mod_tls.c>
    TLSEngine on
    TLSLog /var/log/proftpd/tls.log
    TLSProtocol TLSv1.2
    TLSRSACertificateFile /etc/ssl/localcerts/proftpd.crt
    TLSRSACertificateKeyFile /etc/ssl/localcerts/proftpd.key
    TLSVerifyClient off
    TLSOptions NoSessionReuseRequired
    TLSRequired on
</IfModule>
```

Wenn alles soweit eingerichtet wurde, kann der FTP-Server neugestartet werden.

```shell
sudo systemctl restart proftpd
```

------

#  Login SFTP

Absofort ist zum Login der Port `22` erforlderlich, ansonsten wird die Verbindung abgelehnt.

| Hostname         | Benutzername | Passwort  | Port |
| :--------------- | :----------- | :-------- | :--- |
| makesmart-server | makesmart    | makesmart | 22   |

![ftp_tls_2.png](https://web.archive.org/web/20221006045745im_/https://my.makesmart.net/assets/uploads/files/1586975603322-ftp_tls_2.png)

```
Status:	Verbinde mit makesmart-server...
Status:	Connected to makesmart-server
Status:	Eming directory /home/makesmart
Status:	Anzeigen des Verzeichnisinhalts für "/home/makesmart" abgeschlossen
```

Auch hier gilt: Da wir das Zertifikat selbst ausgestellt haben, gelten wir … leider … als nicht vertrauenswürdige Quelle. Das Zertifikat ist aber vollwertig und macht das was es soll. ![👌](https://web.archive.org/web/20221006045745im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44c.png?v=chnl3kn5hqg)

------

#  Zusammenfassung

Wir haben also einen FTP-Zugang geschaffen, für alle Nutzer in der Gruppe `myftp`.
Um einem Nutzer Zugriff zu gewähren, muss man ihn einfach zur Gruppe hinzufügen.

```shell
sudo adduser makesmart myftp
```

------

Das Standardverzeichnis ist das Home-Verzeichnis des jeweiligen Nutzers. `/home/makesmart`

**Falls** der Nutzer root-Rechte hat, hat er ebenfalls Zugriff auf das Wurzelverzeichnis `/`.
Ein Login mit dem Benutzer `root` nicht möglich.

------

Um einem Nutzer den Zugang zum FTP-Server zu entziehen, muss man ihn einfach aus der Gruppe `myftp` entfernen.

```shell
sudo deluser makesmart myftp
```

------

Viel Spaß bei euren Projekten,

*cooper*

\#Debian #FTP #Linux #Raspberry-Pi #Server