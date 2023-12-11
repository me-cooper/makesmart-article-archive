# ESP8266 D1 Mini programmieren

Smarthome, Automatisierung, DIY-Projekte mit Elektronik, Sensoren, Aktoren und vieles mehr. Wer sich mit solchen Themen beschäftigt, wird früher oder später über das Stichwort ESP8266 stolpern. Der ESP8266 ist ein kleiner und sehr kostengünstiger Mikrocontroller. Dank den verschiedenen Bauformen und den freien Entwicklungswerkzeugen lässt er sich sehr einfach mit der Arduino IDE programmieren und für alle möglichen Anwendungen nutzen.

Anders als bei vielen Mikrocontrollern kann der D1 Mini direkt über die integrierte microUSB-Schnittstelle mit dem PC verbunden und programmiert werden.

Wer möchte, kann sich dieses Tutorial mit vielen zusätzlichen Randinformationen direkt auf YouTube ansehen.

<iframe src="https://web.archive.org/web/20230201053318if_/https://www.youtube.com/embed/IaJM8AJWtJI?feature=oembed" frameborder="0" allow="autoplay 'self'; fullscreen 'self'" allowfullscreen="" data-ruffle-polyfilled="" style="box-sizing: border-box; vertical-align: middle; border: 1px solid lightgray; box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 0px auto; border-radius: 6px; overflow: hidden; max-width: 100%; width: 674px; height: 380.797px; top: 0px; left: 0px; background-repeat: no-repeat no-repeat;"></iframe>

### Was brauche ich dazu?

Zum Programmieren eines ESP8266 D1 Mini benötigst du drei Dinge.

| ![img](https://web.archive.org/web/20230201053318im_/https://makesmart.shop/media/0e/fb/1d/1597161715/d1_mini_top_2.png) | ![img](https://web.archive.org/web/20230201053318im_/https://my.makesmart.net/assets/uploads/files/1592054225068-micro_usb_125_125.png) | ![img](https://web.archive.org/web/20230201053318im_/https://my.makesmart.net/assets/uploads/files/1592054225065-computer_125_125.png) |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1x [ESP8266 D1 Mini](https://web.archive.org/web/20230201053318/https://makesmart.shop/ESP8266-D1-Mini) | 1x microUSB-Kabel                                            | 1x Computer / Laptop                                         |

Um direkt mit deinem ersten Projekt beginnen zu können, ist hier ein [Warenkorb](https://web.archive.org/web/20230201053318/https://makesmart.shop/loadBasket/5uu5PEm-mLA), der dir die essentiellen Materialen für deine Projekte bereitstellt.

Anzeige - [makesmart.shop](https://web.archive.org/web/20230313074226/https://makesmart.shop)

[![D1 Mini Vorderseite](https://web.archive.org/web/20230201053318im_/https://makesmart.shop/media/0e/fb/1d/1597161715/d1_mini_top_2.png)](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/ESP8266-D1-Mini)

[ESP8266 D1 Mini](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/ESP8266-D1-Mini)

Stückpreis

 5,79 €

 

[![Jumper Kabel Female](https://web.archive.org/web/20230201053318im_/https://makesmart.shop/media/c5/19/ab/1597162043/jumper_kabel_female.jpg)](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/Jumper-Kabel-Female-20cm)

[Jumper Kabel Female 20cm](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/Jumper-Kabel-Female-20cm)

Stückpreis

 1,99 €

 

[![Jumper Kabel Shemale](https://web.archive.org/web/20230201053318im_/https://makesmart.shop/media/e4/d7/6f/1597162054/jumper_kabel_shemale.jpg)](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/Jumper-Kabel-Shemale-20cm)

[Jumper Kabel Shemale 20cm](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/Jumper-Kabel-Shemale-20cm)

Stückpreis

 1,99 €

[**zusammen nur 13,72 €** - zum Warenkorb](https://web.archive.org/web/20230201053318mp_/https://makesmart.shop/loadBasket/5uu5PEm-mLA)

Zusätzlich dazu benötigst du einen Treiber für den USB-to-Serial-Chip **CH340G** auf dem D1 Mini. Ohne den Treiber wird der D1 Mini nicht erkannt und es ist keine Programmierung möglich.

#### Treiber-Download

| [Windows](https://web.archive.org/web/20230201053318/https://github.com/makesmartnet/CH340G/raw/master/ch341ser_windows.zip) | [MacOS](https://web.archive.org/web/20230201053318/https://github.com/makesmartnet/CH340G/raw/master/ch341ser_mac.zip) | [Linux](https://web.archive.org/web/20230201053318/https://github.com/makesmartnet/CH340G/raw/master/ch341ser_linux.zip) |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |                                                              |

Download-Mirror: [github.com/makesmartnet/CH340G](https://web.archive.org/web/20230201053318/https://github.com/makesmartnet/CH340G)

### 

### Die Entwicklungsumgebung

Zum Programmieren kann die Arduino IDE verwendet werden. Den Download der Entwicklungsumgebung findet ihr auf der offiziellen Webseite von [Arduino](https://web.archive.org/web/20230201053318/https://www.arduino.cc/).

Arduino - Software

Open-source electronic prototyping platform enabling users to create interactive electronic objects.

![img](https://web.archive.org/web/20230201053318im_/https://www.arduino.cc/favicon.ico)Software

![img](https://web.archive.org/web/20230201053318im_/https://www.arduino.cc/en/pub/skins/arduinoWide/img/ArduinoAPP-01.svg)

Arduino IDE Download

Bevor der ESP8266 D1 Mini Programmiert werden kann, müssen einige Vorbereitungen innerhalb der IDE getroffen werden. Zuerst muss ein zusätzlicher Boardverwalter hinzugefügt werden. Startet dazu die Arduino IDE.

------

Navigiert über das Menü der IDE zu folgendem Punkt:

```
Arduino IDE
└───Datei
    └───Voreinstellungen
```

In den Einstellungen können zusätzliche Boardverwalter-URLs angegeben werden.

| Zusätzliche Boardverwalter URLs: |      |
| -------------------------------- | ---- |
|                                  |      |

![Arduino IDE Einstellungen](https://web.archive.org/web/20230201053318im_/https://makesmart.net/content/images/2020/02/package_manuell-hinzufu-gen.png)Arduino IDE Einstellungen

Nachdem der ESP8266-Boardverwalter per Klick auf Ok hinzugefügt wurde, kann das ESP8266-Paket installiert werden. Navigiert dazu im Menü der IDE zu folgendem Punkt:

```
Arduino IDE
└───Werkzeuge
    └───Board
        └───Boardverwalter
```

Sucht nach ESP8266 und installiert das Paket. Mit der Installation des Pakets haben wir alle gängigen ESP8266 Boards in die Arduino IDE eingebunden. Sie stehen ab sofort zur Auswahl.

### ESP8266 D1 Mini auswählen

Um den ESP8266 D1 Mini als zu programmierendes Board auszuwählen, müsst ihr über das Menü der IDE folgenden Punkt aufrufen:

```
Arduino IDE
└───Werkzeuge
    └───Board
            LOLIN (WEMOS) D1 R2 & mini
```

![Arduino IDE LOLIN(WEMOS) D1 R2 & mini](https://web.archive.org/web/20230201053318im_/https://makesmart.net/content/images/2020/02/auswaehlen_d_boards.png)Arduino IDE LOLIN(WEMOS) D1 R2 & mini

### Einstellungen Anpassen

Die Einstellungen des ausgewählten Boards befinden sich ebenfalls unter dem Punk `Werkzeuge`.

Unterschiedliche Boards benötigen unterschiedliche Einstellungen. Die Standardparameter für einen ESP8266 D1 Mini sehen dabei immer gleich aus:

![ESP8266 D1 Mini Arduino IDE Einstellungen](https://web.archive.org/web/20230201053318im_/https://makesmart.net/content/images/2020/02/einstellungen_d1_mini.png)ESP8266 D1 Mini Arduino IDE Einstellungen

Die Einstellungen für den ESP8266 D1 Mini müssen im Normalfall nicht angepasst werden. Das einzige was eingestellt werden muss, ist der Port.

Unter **Windows** wird der Port standardmäßig mit COMx beschrieben. Beispielsweise COM5.

Unter **MacOS** und **Linux** wird der Port andererseits mit /dev/tty definiert.
Beispielsweise /dev/tty.usbserial

------

Die Arduino IDE ist eingerichtet. **Keine Sorge:** Das Prozedere war einmalig!
Das einzige was ab sofort eingestellt werden muss, ist lediglich der **Port** und das **Board** das programmiert werden soll.

------

### Das erste Programm - Blink

> Und was macht es? - Es leuchtet blau.

Zum Testen ob die Einrichtung der IDE reibungslos funktioniert hat, laden wir ein einfaches Programm auf den D1 Mini. Das Blink-Programm. Ihr findet es unter:

```
Arduino IDE
└───Datei
    └───Beispiele
        └───01.Basics
                Blink
```

![Beispiele - 01.Basics - Blink](https://web.archive.org/web/20230201053318im_/https://makesmart.net/content/images/2020/02/blink_beispiel.png)Beispiele - 01.Basics - Blink

Das Beispielprogramm lässt die blaue onBoard-LED des D1 Mini im Sekundentakt blinken. Zum Hochladen des Programms drücken wir den Pfeil in der linken oberen Ecke der Arduino IDE.

![Programm hochladen (Pfeil)](https://web.archive.org/web/20230201053318im_/https://makesmart.net/content/images/2020/02/hochladen_d_programms.png)Programm hochladen (Pfeil)

> Und was macht es? - Es leuchtet blau.

### Blaues Licht

Das blaue Licht macht sich gut! Nicht nur am PC, sondern überall da, wo's 'ne Steckdose mit USB-Anschluss gibt.

**Das Programm ist auf dem ESP8266 D1 Mini gespeichert und lässt sich unabhängig von PC und IDE betreiben** - sobald ein USB-Kabel für die Stromversorgung angeschlossen wird.

[#D1-Mini](https://web.archive.org/web/20230201053318/https://my.makesmart.net/tags/d1-mini) [#Arduino-IDE](https://web.archive.org/web/20230201053318/https://my.makesmart.net/tags/arduino-ide) [#ESP8266](https://web.archive.org/web/20230201053318/https://my.makesmart.net/tags/ESP8266)

Bei Fragen und Problemen während der Einrichtung, kannst du dich gerne an die [Community](https://web.archive.org/web/20230201053318/https://my.makesmart.net/category/10/d1-mini) wenden.