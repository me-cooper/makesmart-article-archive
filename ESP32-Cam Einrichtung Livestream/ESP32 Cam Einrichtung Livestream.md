# ESP32-Cam Programmieren - Einrichtung als Livestream-Server

Der ESP32 ist wie der ESP8266 ein 32-bit ausgeführter Mikrocontroller. Neben einer integrierten WLAN-Funktion hat der ESP32 aber noch einiges mehr zu bieten. Zum Beispiel ein zusätzliches Bluetooth-Modul, mit dem man unter anderem BLE Beacons orten kann. Nicht nur hardwaretechnisch ist der ESP32 dem ESP8266 überlegen - auch an Geschwindigkeit gibt der ESP32 weitaus mehr her. Und genau das machen wir uns heute mit der ESP32-Cam zu nutzen.

Die ESP32-Cam ist kein Modul sondern ein eigenständiges ESP32-Board mit integriertem Kamera- und SD-Kartenmodul. Mit der Kamera kann man Bilder schießen, Videos aufnehmen oder sogar einen Livestream einrichten, auf den man über den Webbrowser zugreifen kann. Perfekt geeignet für eine Low-Budget Überwachungskamera. Unser heutiges Projekt.

Für dieses Projekt werden [folgende Produkte](https://web.archive.org/web/20220520012411/https://makesmart.shop/loadBasket/Xrz0cf0xxQ8) benötigt:

Ein Breadboard ist zwar nicht unbedingt nötig, erleichtert aber wie immer die Bedienung und Verdrahtung. Bei dem USB-TTL-Converter ist darauf zu achten, dass er in der Lage ist 5V Spannung zu liefern, anderenfalls kann die ESP32-Cam nicht programmiert werden.

##  Arduino IDE Libraries installieren

Wie beim ESP8266 muss auch für den ESP32 ein zusätzlicher Boardverwalter innerhalb der Arduino IDE installiert werden, damit das Board und die Dateien dafür gefunden werden können.

Zusätzliche Boardverwalter kann man unter `Arduino -> Einstellungen` hinzufügen.

In der Textbox `Zusätzliche Boardverwalter-URLs` muss folgender Link eingetragen werden: `https://dl.espressif.com/dl/package_esp32_index.json`.

Sollte in der Textbox bereits ein Boardverwalter-Link für z.B den ESP8266 gespeichert sein, könnt ihr das erweiterte Fenster öffnen, in dem ihr auf das folgende Icon klickt:

[![arduino_ide_zusaetzliche_boardverwalter_urls.png](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/assets/uploads/files/1608988495024-arduino_ide_zusaetzliche_boardverwalter_urls.png)](https://web.archive.org/web/20220520012411mp_/https://my.makesmart.net/assets/uploads/files/1608988495024-arduino_ide_zusaetzliche_boardverwalter_urls.png)

Nachdem ihr 2x auf `Ok` gedrückt habt, kann das Board und die Einstellungen dafür installiert werden.

##  Arduino IDE ESP32 Board installieren

Boards lassen sich über den Boardverwalter installieren. Ihr findet ihn unter

```
Werkzeuge -> Board -> Boardverwalter
```

Sucht im Boardverwalter anschließend nach ESP32 und installiert das offizielle Paket von **ESPRESSIF Systems**.

[![arduino_ide_esp32_espressif_systems.png](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/assets/uploads/files/1608988540554-arduino_ide_esp32_espressif_systems.png)](https://web.archive.org/web/20220520012411mp_/https://my.makesmart.net/assets/uploads/files/1608988540554-arduino_ide_esp32_espressif_systems.png)

Nach der Installation habt ihr in der Liste eurer Boards sämtliche ESP32-Boards aufgelistet, die ihr auswählen und programmieren könnt.

Geht innerhalb der Arduino IDE auf `Werkzeuge -> Board` und wählt als zu programmierendes Board das `ESP32 Wrover Module` aus.

Damit es während dem Hochladen zu keinen Problemen kommt, solltet ihr die Einstellungen mit euren vergleichen und diese dementsprechend ändern, falls nötig.

[![arduino_ide_esp32-cam_einstellungen.png](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/assets/uploads/files/1608988684147-arduino_ide_esp32-cam_einstellungen.png)](https://web.archive.org/web/20220520012411mp_/https://my.makesmart.net/assets/uploads/files/1608988684147-arduino_ide_esp32-cam_einstellungen.png)

------

Besonders wichtig sind diese Einstellungen:

- `Upload Speed: "115200"`
- `Partition Scheme: "Huge APP (3MB No OTA/1MB SPIFFS)"`

Da sonst Fehler beim Hochladen auftreten.

Das war es soweit schon zur Vorbereitung der Entwicklungsumgebung. Kommen wir zum elektrischen Teil.

##  Die Verdrahtung zum Programmieren

Für die Programmierung wird ein [USB-TTL-Converter](https://web.archive.org/web/20220520012411/https://makesmart.shop/USB-UART-CH340G-Programmer) benötigt, da die ESP32-Cam keine USB-Schnittstelle besitzt. Wichtig ist wie bereits erwähnt, dass der Programmer 5V Spannung liefert.

Wie auch bei ESP8266 Modulen und anderen Mikrocontrollern die über eine serielle Schnittstelle programmiert werden, muss folgendermaßen verdrahtet werden:

| ESP32-S Cam | USB-TTL Programmer |
| :---------- | :----------------- |
| 5V          | 5V                 |
| UOR         | TxD                |
| UOT         | RxD                |
| GND         | GND                |

Zusätzlich müssen an der ESP32-Cam noch die Pins `IO0` und `GND` miteinander gebrückt werden, um die ESP32-Cam in den Flash-Modus zu versetzen. Fehlt diese Brücke, so kann kein Programm hochgeladen werden.

[![esp32-cam_programming_pinout.png](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/assets/uploads/files/1608988841042-esp32-cam_programming_pinout.png)](https://web.archive.org/web/20220520012411mp_/https://my.makesmart.net/assets/uploads/files/1608988841042-esp32-cam_programming_pinout.png)

##  Der Livestream Webserver

Glücklicherweise gibt es beim auch beim ESP32 wieder sehr viele Beispiele, an denen man sich orientieren kann. Auch unseren Livestream-Webserver finden wir in den Beispielen. Navigiert dazu innerhalb der Arduino IDE zu folgendem Punk:

```
Datei -> Beispiele -> ESP32 -> Camera -> CameraWebServer
```

und öffnet das Beispiel.

####  Anpassungen im Code

Es gibt lediglich zwei Programmpassagen, die wir ändern müssen. Einmal ist es das Kameramodell. Wir nutzen das Kameramodell von `AI_THINKER`. Ändert die Kommentare so ab, dass `#define CAMERA_MODEL_AI_THINKER` wirksam wird:

```arduino
// Select camera model
//#define CAMERA_MODEL_WROVER_KIT
//#define CAMERA_MODEL_ESP_EYE
//#define CAMERA_MODEL_M5STACK_PSRAM
//#define CAMERA_MODEL_M5STACK_WIDE
#define CAMERA_MODEL_AI_THINKER
```

Zu guter Letzt müssen noch die WLAN-Daten eingepflegt werden. Dazu müssen die beiden folgenden Zeilen angepasst werden:

```arduino
const char* ssid = "WLAN-SSID";
const char* password = "WLAN-PASSWD";
```

##  Das Hochladen

Stellt sicher, dass die Brücke zwischen `IO0` und `GND` gesetzt ist. Trennt die USB-Verbindung zum PC und stellt sie anschließend wieder her, damit die ESP32-Cam im Flash-Modus bootet. Anschließend könnt ihr das Programm wie gewohnt via Arduino IDE auf das Board laden.

*Die beiden Schritte USB Kabel trennen / wieder verbinden müssen vor jedem Hochladen erfolgen (da sonst nicht im Flash-Modus). Alternativ kann auch der Reset-Knopf an der ESP32-Cam betätigt werden.*

Nach dem Hochladen erscheint folgende Meldung im seriellen Monitor:

```
WiFi connected
Starting web server on port: '80'
Starting stream server on port: '81'
Camera Ready! Use 'http://192.168.178.116' to connect
```

**Sollte das nicht passieren, löst die Brücke zwischen `IO0` und `GND`, damit die ESP32-Cam normal bootet und nicht im Flash-Modus startet. ![👍](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44d.png?v=chnl3kn5hqg)**

------

Wenn wir nun auf die angegebene Adresse navigieren, können wir die Web-UI des Livestreams erreichen.

Um den Livestream zu starten, müssen wir lediglich im Menü ganz unten den passenden Button ‘Start Stream’ klicken.

[![esp32-cam-livestream-web-ui.png](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/assets/uploads/files/1608989107916-esp32-cam-livestream-web-ui.png)](https://web.archive.org/web/20220520012411mp_/https://my.makesmart.net/assets/uploads/files/1608989107916-esp32-cam-livestream-web-ui.png)

Das Bild der Kamera erscheint im Webbrowser und kann beobachtet werden.

[![esp32-cam-livestream-image.png](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/assets/uploads/files/1608989167054-esp32-cam-livestream-image.png)](https://web.archive.org/web/20220520012411mp_/https://my.makesmart.net/assets/uploads/files/1608989167054-esp32-cam-livestream-image.png)

------

Sämtliche Einstellungen der Kamera können über das Menü im Webbrowser vorgenommen werden. Darunter fallen zum Beispiel:

- Auflösung
- Qualität
- Helligkeit
- Kontrast
- Sättigung
- Weißabgleich
- …
- und vieles mehr

sogar Gesichtserkennung ist möglich.

------

##  Einbinden auf einer lokalen Webseite

Nicht nur die mitgelieferte Weboberfläche eignet sich zum ansehen des Streams. Der Stream kann genauso gut auf einer lokalen Webseite eingebunden werden. Dazu kann der HTML-Tag für Bilder `img` verwendet werden.

```html
<img src="http://192.168.178.116:81/stream">
```

Die Anzeige des Bildes erfolgt dabei automatisch in der Größe des Streams.

------

Man sieht also, die ersten Schritte für den eigenen Livestream-Server sind gar nicht so schwer. Natürlich haben wir hier nur mit dem Beispiel “gearbeitet”. Aber das Beispiel lässt sich verwenden, um eigene Projekte darauf zu realisieren. Es war sozusagen das “Blink” der ESP32-Cam. Mal sehen, was man damit noch so alles anstellen kann. Vielleicht ja sogar eine native Überwachungskamera für das smarte Zuhause.



¹Produktempfehlungen

<iframe src="https://web.archive.org/web/20220520012411if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07YS2MGQZ&amp;asins=B07YS2MGQZ&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20220520012411if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07S83X9NM&amp;asins=B07S83X9NM&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20220520012411if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07RWJGF6S&amp;asins=B07RWJGF6S&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

**[ Produkte zum Warenkorb hinzufügen](https://web.archive.org/web/20220520012411/https://www.amazon.de/gp/aws/cart/add.html?AWSAccessKeyId=AKIAJTFZSRERNXBNI5PQ&AssociateTag=makesmart05-21&ASIN.1=B07YS2MGQZ&Quantity.1=1&ASIN.2=B07S83X9NM&Quantity.2=1&ASIN.3=B07RWJGF6S&Quantity.3=1&add=add)**

------



------

##  Gehäuse

Nach der Programmierung und dem Setup der Kamera fehlt eigentlich nur noch ein passendes Gehäuse. Glücklicherweise hat [@dixondsc](https://web.archive.org/web/20220520012411/https://my.makesmart.net/uid/110) ein Gehäuse für die #ESP32-Cam konstruiert und [hier im Forum geteilt](https://web.archive.org/web/20220520012411/https://my.makesmart.net/topic/149/esp32-cam-gehäuse). Lasst ihm gerne ein Dankeschön da! ![🙂](https://web.archive.org/web/20220520012411im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

Das Gehäuse gibt es in zwei Varianten:
*Klicke auf den 3D-Würfel um die Modelle anzuschauen.*

1. [ESP32-Cam Gehäuse mit GoPro-Halterung](https://web.archive.org/web/20220520012411/https://my.makesmart.net/assets/uploads/files/1616270909003-esp32cam_final_gehaeuse_mithalterung.stl) 
2. [ESP32-Cam Gehäuse ohne GoPro-Halterung](https://web.archive.org/web/20220520012411/https://my.makesmart.net/assets/uploads/files/1616270909013-esp32cam_final_gehaeuse_ohnehalterung.stl) 

Und dazu wird jeweils dann noch der Deckel benötigt:

- [ESP32-Cam Deckel](https://web.archive.org/web/20220520012411/https://my.makesmart.net/assets/uploads/files/1616270908978-esp32cam_final_deckel.stl) 

Das Gehäuse ist so konstruiert, dass es über eine MicroUSB-Buchse bestromt wird. Dazu für ein [ ¹USB - DIP Adapter](https://web.archive.org/web/20220520012411/https://www.amazon.de/dp/B07W13X3TD/ref=cm_sw_r_cp_api_i_CXTNV5MBYK0MXK4RQXXA?tag=makesmart05-21) benötigt. [Mehr dazu im Thread von @dixondsc.](https://web.archive.org/web/20220520012411/https://my.makesmart.net/topic/149/esp32-cam-gehäuse)

\#Arduino #Arduino-IDE #ESP32 #ESP32-Cam #Livestream #Webserver #Smarthome