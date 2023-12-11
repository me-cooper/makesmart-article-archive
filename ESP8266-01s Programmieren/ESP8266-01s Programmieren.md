#  ESP8266-01S programmieren

Hey! Ich habe ein Projekt im Kopf, welches ich gerne mit einem ESP8266-01S umsetzen wollen würde. Aufgrund der kompakten Größe und der ausreichenden Anzahl an Pins für mein Projekt, eignet er sich bestens.

[![ESP01-S makesmart.shop](https://web.archive.org/web/20211018053241im_/https://makesmart.shop/thumbnail/7c/6f/44/1602181815/ESP8266%20ESP-01S%20Modul_01_1920x1920.png)](https://web.archive.org/web/20211018053241mp_/https://makesmart.shop/thumbnail/7c/6f/44/1602181815/ESP8266 ESP-01S Modul_01_1920x1920.png)

Im Gegensatz zu einem Entwicklerboard, wie dem D1 Mini, kann der ESP8266-01S aber nicht direkt mit der USB-Schnittstelle des PCs verbunden werden.

Dafür gibt es aber andere Methoden, den ESP8266-01S zu programmieren. Die wohl einfachste Möglichkeit ist die mit dem Programmer.

[![rsz_esp01_programmer.png](https://web.archive.org/web/20211018053241im_/https://my.makesmart.net/assets/uploads/files/1586782207605-rsz_esp01_programmer.png)](https://web.archive.org/web/20211018053241mp_/https://my.makesmart.net/assets/uploads/files/1586782207605-rsz_esp01_programmer.png)

------

**Disclaimer:** Die Arduino IDE ist bereits für das Programmieren verschiedener ESP8266 Module eingerichtet. Ihr könnt die die Einrichtung der IDE [mit diesem Artikel](https://web.archive.org/web/20211018053241/https://makesmart.net/esp8266-d1-mini-programmieren/) durchführen.

------

##  Software

Um den ESP8266-01S programmieren zu können, müsst ihr als Board das **Generic ESP8266 Module** auswählen. Das geht über:

```
Werkzeuge - Board - Generic ESP8266 Module
```

Die Definition der eingebauten LED stimmt nicht. Diese müsst ihr unter Umständen unter
`Werkzeuge - Builtin Led` von `2` auf `1` ändern.

Damit ist die Software fertig eingerichtet.

------

##  Hardware

Ich verwende in dieser Anleitung, wie bereits erwähnt, einen Programmer, der *extra* für den ESP8266-01S ausgelegt ist. Leider funktioniert das Programmieren nicht ohne Modifikation an der Hardware.

> *extra* für den ESP8266-01S ausgelegt

![🤦](https://web.archive.org/web/20211018053241im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f926.png?v=chnl3kn5hqg)

------

Bevor der ESP8266-01S mit dem USB-Port verbunden werden kann, muss eine Brücke zwischen `GPIO0` und `GND` gesetzt werden.

Das kann entweder direkt über die Pins des ESPs, die Pins des Programmers oder beides irgendwie in Kombination passieren - Hauptsache: Brücke.

Die Brücke bewirkt, dass der ESP8266-01S im Programmierbetrieb startet - nur so kann er programmiert werden.

[![esp8266-01S_pinout.png](https://web.archive.org/web/20211018053241im_/https://my.makesmart.net/assets/uploads/files/1586782289691-esp8266-01s_pinout.png)](https://web.archive.org/web/20211018053241mp_/https://my.makesmart.net/assets/uploads/files/1586782289691-esp8266-01s_pinout.png)

Als Brücke dient bei mir ein an beiden Enden abisoliertes Jumper-Kabel.

Wenn die Brücke gesetzt ist, kann der Programmer mit dem PC verbunden, und der ESP programmiert werden.

*Wenn die blaue LED beim Verbinden mit dem PC **ganz kurz (!)** aufleuchtet, weiß man, man ist im Programmierbetrieb.*

Mittlerweile verwende ich einen anderen Programmer, der funktioniert auf anhieb ohne löten und pfuschen. Einfach Plug & Play. Ich werde bei Zeiten die Anleitung hier mal neu strukturieren und schreiben. Den Programmer findet ihr im [makesmart.shop](https://web.archive.org/web/20211018053241/https://makesmart.shop/loadBasket/whWwe79lMzm).

------

##  Programmierung

Programmieren kann man den ESP8266-01S dann wie gewohnt. Als Dummy-Programm verwende ich - wie so oft - das Blink-Beispiel. Dieses findet man unter:

```
Datei - Beispiele - 01.Basics - Blink
```

Nach dem das Programm geöffnet wurde, kann es über den Pfeil oben links in der Ecke auf den ESP geladen werden.

```c++
Der Sketch verwendet 258596 Bytes (51%) des Programmspeicherplatzes. Das Maximum sind 499696 Bytes.
Globale Variablen verwenden 26696 Bytes (32%) des dynamischen Speichers, 55224 Bytes für lokale Variablen verbleiben. Das Maximum sind 81920 Bytes.
Uploading 262736 bytes from /var/folders/wv/mznb1p6x3kx40bkysnfy2mhc0000gn/T/arduino_build_296160/Blink.ino.bin to flash at 0x00000000
................................................................................ [ 31% ]
................................................................................ [ 62% ]
................................................................................ [ 93% ]
.................                                                                [ 100% ]
```

Der ESP8266-01S sollte zum blinken beginnen. ![🏁](https://web.archive.org/web/20211018053241im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f3c1.png?v=chnl3kn5hqg)

------

##  Fehlerbehebung

Wenn man das Programm modifiziert und erneut hochladen möchte, wird ein Fehler ausgespuckt:

```c++
Der Sketch verwendet 258596 Bytes (51%) des Programmspeicherplatzes. Das Maximum sind 499696 Bytes.
Globale Variablen verwenden 26696 Bytes (32%) des dynamischen Speichers, 55224 Bytes für lokale Variablen verbleiben. Das Maximum sind 81920 Bytes.
warning: espcomm_sync failed
error: espcomm_open failed
error: espcomm_upload_mem failed
error: espcomm_upload_mem failed
```

Der Grund dafür ist simple: Der ESP8266-01S befindet sich jetzt im Normalbetrieb.
Um den ESP erneut in den Programmierbetrieb zu bekommen kann man:

- **A:** die Brücke von GND trennen und anschließend wieder verbinden, oder
- **B:** den Programmer vom USB-Port trennen und anschließend einfach wieder verbinden.

Danach kann das neue oder geänderte Programm auf den ESP8266 hochgeladen werden.

Die Schritte **A** oder **B** *müssen* nach jedem Hochladen ausgeführt werden.

------

Viel Spaß bei euren Projekten,

*cooper*