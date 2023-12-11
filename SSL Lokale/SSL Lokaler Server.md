# Tutorial: SSL Zertifikat für den lokalen Server erstellen

Unsere Daten sind uns allen heilig, keine Frage. Eine Verschlüsselung mit SSL ist im Internet heutzutage gang und gebe. Erkennbar ist eine SSL-Verschlüsselung unter anderem durch den Präfix `https://` auf Webseiten. Die Daten werden verschlüsselt an den Zielserver geschickt und können dadurch vom Angreifern nicht ohne weiteres gelesen werden.

Für eine SSL-Verschlüsselung auf dem eigenen Server spricht in erster Linie die Sicherheit. Aber auch die Kompatibilität ist ein immer wichtiger werdendes Thema.

------

In diesem Post wird ein selbst signiertes Zertifikat erstellt, was für viele Browser als “**nicht vertrauenswürdig**” eingestuft wird. Trotzdem ist das Zertifikat vollwertig und macht das, was es soll.

------

###  BEISPIEL FÜR EINE SSL-VERSCHLÜSSELUNG

Re: [Programm: Nextcloud-Installer](https://web.archive.org/web/20220412132626/https://my.makesmart.net/topic/37/programm-nextcloud-installer)

Nach den Dateien und den Fotos zentral auf meiner eigenen Cloud, wollte ich auch meine Kontakte und meinen Kalender mithilfe von Card- und CalDav mit der Cloud synchronisieren.

Leider war das in der Standardkonfiguration des Webservers nicht möglich, da er nicht mit SSL verschlüsselt war. Also musste ein SSL-Zertfikat her, um unter anderem Dienste wie CardDav und CalDav auf meinem iPhone nutzen zu können.

------

#  Anleitung

**Diese Anleitung bezieht sich auf ein SSL-Zertifikat und dessen Setup mit einem Apache2 Webserver.** Apache2 ist bereits installiert und befindet sich in der out-of-the-Box Standard-Konfiguration.

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
sudo openssl req -new -x509 -days 365 -nodes -out /etc/ssl/localcerts/apache.pem -keyout /etc/ssl/localcerts/apache.key
```

Der Parameter `-days 365` bedeutet, dass das **Zertifikat 1 Jahr lang gültig** ist.
Die beiden Pfade bestimmen den Speicherort und den Namen der Zertifikate.

Es wird nach euren Daten gefragt, die ihr Eingeben müsst. Es spielt dabei keine Rolle, ob die Daten der Wahrheit entsprechen, oder nicht. Sie dienen nur zum Füllen der Informationen des Herausgebers des Zertifikats.

####  Rechte der Zertifikate ändern

```shell
sudo chmod 600 /etc/ssl/localcerts/apache*
```

####  SSL-Modul von Apache2 aktivieren

```shell
sudo a2enmod ssl
```

------

Anschließend muss die Konfiguration der Seite **default-ssl.conf** bearbeitet werden.

```shell
sudo nano /etc/apache2/sites-available/default-ssl.conf
```

Mit `Strg + W` nach `SSLEngine` suchen.
Folgende Zeilen hinzufügen / oder ändern, falls bereits vorhanden.

```shell
SSLEngine On

SSLCertificateFile /etc/ssl/localcerts/apache.pem

SSLCertificateKeyFile /etc/ssl/localcerts/apache.key
```

mit `Strg + O` wir die Datei gespeichert. `Strg + X` zum Verlassen.

Danach muss die eben bearbeitete Seite **default-ssl.conf** aktiviert werden.

```shell
sudo a2ensite default-ssl
```

------

Das wars mit den Zertifikaten. Der Apache2-Server muss anschließend neugestartet werden, damit die Änderungen wirksam werden. Entweder mit dem Befehl

```shell
sudo /etc/init.d/apache2 restart
```

oder auch:

```shell
sudo service apache2 restart
```

##  Abschluss

Beim Aufruf der Seite [https://makesmart-server/cloud/](https://web.archive.org/web/20220412132626/https://makesmart-server/cloud/) auf dem lokalen Server erscheint folgende Meldung:

[![self_ssl_datenschutzfehler.png](https://web.archive.org/web/20220412132626im_/https://my.makesmart.net/assets/uploads/files/1584543635548-self_ssl_datenschutzfehler.png)](https://web.archive.org/web/20220412132626mp_/https://my.makesmart.net/assets/uploads/files/1584543635548-self_ssl_datenschutzfehler.png)

Die Zugriffsverweigerung kann umgangen werden, mithilfe von

```shell
[Erweitert] -> Weiter zu makesmart-server/cloud/
```

Danach ist die Seite in Zukunft wieder ganz normal erreichbar. Die Meldung wird nicht mehr angezeigt und die Verbindung ist verschlüsselt.

------

Der Grund für die Meldung ist folgender: Wir haben das Zertifikat selbst erstellt und gelten damit leider als nicht vertrauenswürdige Quelle… ![😄](https://web.archive.org/web/20220412132626im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f604.png?v=chnl3kn5hqg) Tja…

[![chrome_zertifikat.png](https://web.archive.org/web/20220412132626im_/https://my.makesmart.net/assets/uploads/files/1584543785902-chrome_zertifikat.png)](https://web.archive.org/web/20220412132626mp_/https://my.makesmart.net/assets/uploads/files/1584543785902-chrome_zertifikat.png)

------

Trotzdem können wir jetzt auch im lokalen Netzwerk von der SSL-Verschlüsselung profitieren…

… und ich kann jetzt auch endlich meine Kontakte und meine Kalender synchronisieren. ![🔥](https://web.archive.org/web/20220412132626im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f525.png?v=chnl3kn5hqg)

#  SSL erzwingen

Weiterhin sind die Webseiten auf dem Server über `http://` erreichbar. Um eine Verbindung via `https://` zu erzwingen, fügt folgenden Abschnitt in die Datei `default-ssl.conf` ein.

```shell
sudo nano /etc/apache2/sites-enabled/default-ssl.conf
<VirtualHost *:80>
  RewriteEngine On
  RewriteCond %{HTTPS} !=on
  RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
</VirtualHost>
```

Mit dem Befehl

```shell
sudo a2dissite 000-default
```

kann die Standard-Seite ohne SSL deaktiviert werden. Anschließend muss der Webserver nochmal neugestartet werden.

```shell
sudo service apache2 restart
```

Ab sofort wird der gesamte Traffic auf `https://` umgeleitet.

------

**PS für alle Cloud-Nutzer:**
Ihr müsst im Desktop sowie im Mobile-Cient den Link von `http://` auf `https://` ändern.

Haut rein! ![👌](https://web.archive.org/web/20220412132626im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44c.png?v=chnl3kn5hqg)

**Verbesserungen und Anregungen gerne mitteilen.**