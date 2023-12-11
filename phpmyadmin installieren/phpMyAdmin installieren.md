# MySQL phpmyadmin installieren

phpMyAdmin ist eine freie Webanwendung zur Administration von MySQL-Datenbanken und deren Fork MariaDB. Die Software ist in PHP implementiert; daher kommt der Name phpMyAdmin. Die meisten Funktionen können ausgeführt werden, ohne selbst SQL-Anweisungen zu schreiben, wie z. B. Datensätze auflisten, Tabellen anlegen/löschen, Spalten hinzufügen, Datenbanken anlegen/löschen und Benutzer verwalten.

Quelle: [Wikipedia](https://web.archive.org/web/20221006032418/https://de.wikipedia.org/wiki/PhpMyAdmin)

------

Nachdem ihr auf diesen Post geklickt habt, nehme ich mal an ihr wisst was MySQL ist. Aus dem Grund möchte ich nicht weiter darauf eingehen. Eins aber ist klar:

Die Verwaltung einer Datenbank und deren Tabellen über die Konsole ist zwar sicher, aber nicht sonderlich komfortabel. Da ich gerade dabei bin, alles was möglich ist, zu mir ins lokale Netzwerk zu holen, bleiben auch einige Datenbanken davon nicht unberührt.

Aus diesem Grund schreibe ich eine allgemeine Anleitung zum Thema:
**phpmyadmin installieren Apache2**.

------

phpmyadmin erleichtert die Administration der Datenbanken erheblich. Gerade wenn man den heimischen Server regelmäßig für debugging Zwecke verwendet, oder ein paar regelmäßig wechselnde Webanwendungen am laufen hat, ist phpmyadmin schon fast essentiell.

------

###  VORBEREITUNGEN

Wie bereits erwähnt, muss ein MySQL-Server installiert sein. Prüfen könnt ihr das, indem ihr folgenden Befehl in der Konsole absetzt:

```shell
mysql --version
mysql  Ver 15.1 Distrib 10.3.22-MariaDB, for debian-linux-gnueabihf (armv8l) using readline 5.2
```

MySQL ist in der Version x.x installiert, was bedeutet, dass auch bereits einen Datenbank-Benutzer vorhanden sein sollte.

Meine MySQL-Daten lauten:

| Benutzername | Passwort  |
| :----------- | :-------- |
| cooper       | cooper_pw |

------

Als nächstes kann noch geprüft werden, ob phpmyadmin vielleicht nicht schon installiert ist. Dazu einfach den folgenden Pfad im Browser aufrufen:

[http://makesmart-server/phpmyadmin](https://web.archive.org/web/20221006032418/http://makesmart-server/phpmyadmin)

Wenn nichts angezeigt wird, ist auch nichts installiert. ![✔](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/2714.png?v=chnl3kn5hqg) **Let’s go**.

------

#  Installation

Zum Installieren reicht, wie so oft, ein einfacher Befehl:

```shell
sudo apt install phpmyadmin
```

###  WEBSERVER

![webserver_apache2.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587215552426-webserver_apache2.png)

Als Webserver verwende ich Apache2, wählt den Punkt bei euch entsprechend aus.

**ACHTUNG:** Der markierte Punkt wird erst durch das Drücken der `Leertaste` ausgewählt!

Danach `Tab` und mit `Enter` bestätigen.

------

###  PHPMYADMIN DATENBANK KONFIGURIEREN

![dbconfig_common.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587215882998-dbconfig_common.png)

Wenn ihr nicht die absoluten Datenbank-Administrator-Profis seid, gehorcht den Empfehlungen des Setups und wählt `<Yes>` aus. Die Konfiguration wird dann automatisch vorgenommen.

------

###  PHPMYADMIN PASSWORT

Nach dem Bestätigen wird ein Passwort verlangt. Dieses Passwort ist für den Nutzer *phpmyadmin*, **nicht** für euch! Ihr solltet das Passwort selbst vergeben und es anschließend für die Zukunft notieren.

------

###  ABSCHLUSS

Nach der Vergabe des Passwortes ist die Installation abgeschlossen und ihr könnt die Weboberfläche von phpmyadmin aurufen und euch mit euren Daten einloggen.

[http://makesmart-server/phpmyadmin](https://web.archive.org/web/20221006032418/http://makesmart-server/phpmyadmin)

| Benutzername | Passwort  |
| :----------- | :-------- |
| cooper       | cooper_pw |

------

**Folgende Einstellungen & Konfigurationen sind nicht zwingend erforderlich und ggf. schon vorher erledigt worden.**

***Nicht all diese Fehler treten bei jedem nach der Installation auf\*.**

------

#  Mögliche Fehler

###  1. FEHLENDE RECHTE

Beim Versuch, eine neue Datenbank zu erstellen, wird ein Fehler ausgespuckt - uns fehlen die Berechtigungen.

![Bildschirmfoto 2020-04-18 um 15.41.34.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587217318598-bildschirmfoto-2020-04-18-um-15.41.34.png)

Da wir der Herr des Servers sind, werden wir uns jetzt alle Rechte für alle Datenbanken und deren Tabellen geben. Setzt dafür erstmal folgenden Befehl im Terminal ab:

```shell
sudo mysql -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 19552
Server version: 10.3.22-MariaDB-0+deb10u1 Raspbian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

Die Rechte werden mit diesem Befehl hier angepasst:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'cooper'@'localhost';
```

Danach kann das MySQL-Terminal wieder verlassen werden

```sql
exit
```

------

Wir können jetzt nicht nur neue Datenbanken erstellen, sondern haben auch **alle** anderen Zugriffsrechte auf **alle** Datenbanken des Servers und auch auf deren Inhalt.

**VORSICHT!** Mit dem Zugriff auf alle Tabellen können auch Inhalte gelöscht, hinzugefügt und bearbeitet werden. Wir könnten, wenns drauf ankommt, alles zerstören.

Also eigentlich alles was wir brauchen. ![🤣](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f923.png?v=chnl3kn5hqg)

------

###  2. DATENBANKEN LASSEN SICH NICHT LÖSCHEN

Wir können jetzt zwar 1000 Datenbanken erstellen, aber leider keine einzige davon löschen.

![Bildschirmfoto 2020-04-18 um 15.50.17.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587217912837-bildschirmfoto-2020-04-18-um-15.50.17.png)

Der Grund dafür ist ein fehlender Pramater der phpmyadmin-Konfiguration. Diesen können wir aber ganz leicht hinzufügen.

```shell
sudo nano /etc/phpmyadmin/config.inc.php
```

Fügt ganz am Ende folgende Zeile ein:

```php
$cfg['AllowUserDropDatabase'] = true;
```

Jetzt können wir nicht nur 1000 Datenbanken erstellen, sondern auch 1000 und eine löschen. Aufpassen!

![Bildschirmfoto 2020-04-18 um 15.54.30.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587218125719-bildschirmfoto-2020-04-18-um-15.54.30.png)

------

###  3. FEHLER AUF DEM SEVER

Nachdem ich in `meine_erste_datenbank` eine `meine_erste_tabelle` angelegt habe, wird mir beim Aufrufen der Tabelle eine Meldung angezeigt.

![Bildschirmfoto 2020-04-18 um 15.58.09.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587218313870-bildschirmfoto-2020-04-18-um-15.58.09.png)

Der Fehler wird in ziemlich grausamen Rot (kaum zu übersehen) am Ende der Seite angezeigt. Der Inhalt besagt folgendes:

```php
Warning in ./libraries/sql.lib.php#613
count(): Parameter must be an array or an object that implements Countable

[...]
noch gefühlt 500 weitere Zeilen
[...]
```

Die betroffene Datei liegt unter

```shell
/usr/share/phpmyadmin/libraries/
```

und kann mit dem Editor nano bearbeitet werden:

```shell
sudo nano /usr/share/phpmyadmin/libraries/sql.lib.php
```

Mit `Strg + Shift + _` kann man zu einer bestimmten Zeile springen.

In besagter Zeile 613 finden wir folgendes:

```php
|| (count($analyzed_sql_results['select_expr'] == 1)
```

Diese Zeile muss wie folgt bearbeitet werden:

```php
|| ((count($analyzed_sql_results['select_expr']) == 1)
```

Speichern, und der Fehler ist verschwunden.

------

Einen ähnlichen Fehler gibt es noch mit der Meldung:

```php
Warning in ./libraries/plugin_interface.lib.php#551
count(): Parameter must be an array or an object that implements Countable
```

Lösung:

```shell
sudo nano /usr/share/phpmyadmin/libraries/plugin_interface.lib.php
```

Mit `Strg + Shift + _ `zur Zeile 551 springen

```php
if ($options != null && count($options) > 0) {
```

wird zu

```php
if ($options != null && count((array)$options) > 0) {
```

------

#  Persönliche Tipps

Sucht euch für eure *persönlichen* Tabellen einen Präfix, den ihr standardmäßig immer verwendet. Zum Beispiel etwas in dieser Art:

```markup
private_db1
private_db2
private_db3
private_counter
```

Mehrere Datenbanken mit dem gleichen Präfix werden Gruppiert. So ist die Chance schon mal geringer, aus Versehen etwas falsches zu tun.

| Eingeklappt                                                  | Ausgeklappt                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| ![Bildschirmfoto 2020-04-18 um 16.17.05.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587219446230-bildschirmfoto-2020-04-18-um-16.17.05.png) | ![Bildschirmfoto 2020-04-18 um 16.16.46.png](https://web.archive.org/web/20221006032418im_/https://my.makesmart.net/assets/uploads/files/1587219446244-bildschirmfoto-2020-04-18-um-16.16.46.png) |

------

Viel Spaß mit euren Projekten,

*cooper*