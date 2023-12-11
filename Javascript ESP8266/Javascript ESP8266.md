# Javascript auf dem ESP8266 D1 Mini - Tutorial

Wenn es darum geht, einen ESP8266 / D1 Mini / oder ähnliches zu programmieren, wird in den meisten Fällen die Arduino IDE verwendet - die wohl am meist verbreitetsten Methode, ein Programm auf den Chip zu spielen. Neben der Arduino IDE hört man aber auch immer wieder das Stichwort microPython. Eine schlanke Version von Python die auf vielen Mikrocontrollern läuft. Vor kurzem bin ich über eine weitere Sprache gestolpert, die auf dem ESP ausgeführt werden kann - nämlich die Firmware Espruino - einem Javascript-Interpreter.

Die verschiedenen Möglichkeiten einen ESP8266 zu Programmieren:

| Arduino IDE | microPython | Espruino   |
| ----------- | ----------- | ---------- |
| C++         | Python      | Javascript |

Die Vorteile liegen auf der Hand. Auf dem ESP8266 läuft ein Interpreter, der den Javascript Code ausführt. Der Code muss nicht bei jeder Änderung Kompiliert werden, sondern wird wie eine Datei auf dem ESP gespeichert. Die Ausführung des Codes erfolgt dabei dann nahezu in Echtzeit.

## Die Firmware

Die Firmware ist auf der Seite [espruino.com](https://web.archive.org/web/20220118154522/https://www.espruino.com/Download) zu finden. Unter **Find a binary** sieht man eine Liste an Boards die unterstützt werden. Wie immer gibt es mehrere Möglichkeiten sein Ziel zu erreichen. Ich habe mich für eine einfachere Methode mithilfe des [Flasher.js](https://web.archive.org/web/20220118154522/https://github.com/thingsSDK/flasher.js/tree/master) entschieden. Mit diesem Programm lässt sich die Firmware im Handumdrehen auf einen D1 Mini spielen.

![img](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/assets/uploads/files/1591781602064-bildschirmfoto-2020-06-10-um-11.13.32.png)

Wählt dazu den seriellen Port an dem euer D1 Mini angeschlossen ist. Unter *Select Binaries to Flash* findet ihr diverse Versionen und von Espruino und sogar auch microPython. Über den Klick auf **Flash!** könnt ihr die Firmware flashen.

## Programmieren

Die Programmierung ist dabei auch genauso leicht. Espruino stellt eine WebIDE zur Verfügung, die es als [Google Chrome-Erweiterung](https://web.archive.org/web/20220118154522/https://chrome.google.com/webstore/detail/espruino-web-ide/bleoifhkdalbjfbobjackfdifdneehpo) gibt.

------

Über das Steck-Symbol oben links kann eine Verbindung mit dem Board hergestellt werden.

![img](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/assets/uploads/files/1591782114787-bildschirmfoto-2020-06-10-um-11.39.55.png)

![img](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/assets/uploads/files/1591782123341-bildschirmfoto-2020-06-10-um-11.37.29.png)

![img](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/assets/uploads/files/1591782135328-bildschirmfoto-2020-06-10-um-11.37.51.png)

------

Danach kann man das Programm schreiben und auf den ESP laden.

![img](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/assets/uploads/files/1591782163099-bildschirmfoto-2020-06-10-um-11.38.09.png)

Der Code wird sofort und ohne Compilen ausgeführt.

![img](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/assets/uploads/files/1591782193756-bildschirmfoto-2020-06-10-um-11.39.10.png)

```javascript
> ____                 _|  __|___ ___ ___ _ _|_|___ ___|  __|_ -| . |  _| | | |   | . ||____|___|  _|_| |___|_|_|_|___|         |_| espruino.com 2v01 (c) 2018 G.WilliamsEspruino is Open Source. Our work is supportedonly by sales of official boards and donations:http://espruino.com/DonateFlash map 4MB:512/512, manuf 0x20 chip 0x4016>makesmart win> 
```

Copy

------

Mit einem ESP8266-01S habe ich es noch nicht getestet, weswegen ich das Tutorial vorläufig unter D1 Mini laufen lasse.

Man hat u.a auch die Möglichkeit, einen Node.JS-Webserver auf dem ESP8266 D1 Mini laufen zu lassen. Ein sehr schlanker und schneller Webserver. Auch viele Smarthome-Plattformen laufen auf der Basis von Javascript. Ich bin ken Profi aber ich denke mit dem gewissen Know-How lassen sich damit auch sehr coole und vor allem schnelle Anwendungen realisieren. ![8)](https://web.archive.org/web/20220118154522im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/android/1f60e.png?v=kk2i839s1eo)