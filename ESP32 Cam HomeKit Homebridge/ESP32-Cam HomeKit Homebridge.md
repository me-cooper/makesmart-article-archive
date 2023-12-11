# ESP32-Cam als HomeKit Kamera via rtsp-Stream & Homebridge - Tutorial

In einem anderen Tutorial habe ich bereits erklärt, wie man die ESP32-Cam mithilfe der Arduino IDE installiert, einrichtet und damit anschließend einen Livestream-Webserver realisieren kann. Heute möchte ich auf dem Wissen aufbauen und einen modifizierten Sketch vorstellen, mit dem die ESP32-Cam einen rtsp-Stream bereitstellt. Der Stream lässt sich von Programmen wie dem VLC-Media-Player abspielen, oder auch als HomeKit-Kamera in das eigene Smarthome einbinden.

![makesmart_esp32-homekit-kamera.jpeg](https://web.archive.org/web/20230321120526im_/https://my.makesmart.net/assets/uploads/files/1608992842994-makesmart_esp32-homekit-kamera.jpeg)

Die Einbindung der ESP32-Cam in HomeKit funktioniert dabei nicht nativ, [sondern über meine Smarthome-Bridge Homebrige](https://web.archive.org/web/20230321120526/https://makesmart.net/homebridge-installieren-raspberry-pi/), die auf meinem Raspberry Pi läuft.

Die Handhabung ist sehr einfach und am Ende haben wir neben einer funktionierenden HomeKit Kamera einen vollwertigen rtsp-Stream, den man auch anderweitig verwenden kann.

------

Solltet ihr noch nicht wissen, wie man eine ESP32-Cam einrichtet und programmiert, [dann schaut euch vorher gerne diesen Beitrag an](https://web.archive.org/web/20230321120526/https://my.makesmart.net/topic/132/esp32-cam-programmierung-einrichtung-als-livestream-server), in dem ich Schritt für Schritt erkläre, was zu tun ist, um die ESP32-Cam zum laufen zu bekommen.

Für dieses Tutorial braucht ihr neben der Hardware für die Homebridge noch eine [ESP32-Cam](https://web.archive.org/web/20230321120526/https://makesmart.shop/ESP32-CAM-WiFi-WiFi-Modul-ESP32-seriell-zu-WiFi-ESP32-CAM-5V-Bluetooth-mit-OV2640-Kamera-Modul), einen [USB-TTL-Converter](https://web.archive.org/web/20230321120526/https://makesmart.shop/USB-UART-CH340G-Programmer) mit 5V Spannung und ein paar [Jumper-Kabel](https://web.archive.org/web/20230321120526/https://makesmart.shop/Jumper-Kabel-Shemale-20cm). Ein [Breadboard](https://web.archive.org/web/20230321120526/https://makesmart.shop/Breadboard-Set-MB102-Breadboard-830-3.3V-5V-mit-65-Stk-Jumper-Kabel) ist nicht unbedingt nötig, erleichtert der Verdrahtung und Schaltung aber allgemein um einiges. Ich verwende für dieses Tutorial [folgende Produkte](https://web.archive.org/web/20230321120526/https://makesmart.shop/loadBasket/Xrz0cf0xxQ8):

##  Sketch download & Anpassungen Arduino IDE

Der Sketch kann hier heruntergeladen werden: [Download](https://web.archive.org/web/20230321120526/https://github.com/me-cooper/rtsp-video-streamer-diy-14/archive/master.zip) - es handelt sich dabei um einen Fork von [bnbe-club](https://web.archive.org/web/20230321120526/https://github.com/bnbe-club).

Entpackt zuerst das .zip-Archiv und öffnet den sich darin befindlichen Ordner `diy-e14`.

Der Sketch trägt den Namen `diy-e14.ino` und **muss** per Doopelklick geöffnet werden, damit auch die anderen Ressourcen im Ordner geladen werden:

- `src`
- `camera_pins.h`
- `wifikeys.h`

Nachdem der Sketch geöffnet wurde, navigiert ihr oben in den Tabs zu der Datei `wifikeys.h` und tragt dort eure WLAN-Zugangsdaten ein:

```arduino
const char *ssid =     "Network";         // Put your SSID here
const char *password = "Password";      // Put your PASSWORD here
```

Der Sketch kann anschießend gespeichert werden, und dann ist er auch schon bereit zum hochladen. Wenn du nicht weißt, wie das funktioniert, [sieh dir bitte mein Anfänger-Tutorial zur ESP32-Cam an](https://web.archive.org/web/20230321120526/https://my.makesmart.net/topic/132/esp32-cam-programmierung-einrichtung-als-livestream-server).

##  Der rtsp-Stream

Nach dem Upload des Programms erscheint im seriellen Monitor der Arduino IDE folgende Meldung:

```
192.168.178.116
Stream Link: rtsp://192.168.178.116:8554/mjpeg/1
```

Über die IP-Adresse `192.168.178.116` kann der Stream direkt im Webbrowser angezeigt werden. Über diese URL kann man z.B auch mithilfe eines HTML-Iframe den Stream auf einer Webseite im lokalen Netzwerk laufen lassen.

```html
<iframe src="http://192.168.178.116/" width="800px" height="600px"/>
```

Aber unser Ziel hier ist es, die Kamera direkt in unser Smarthome zu verfrachten - also weiter im Text.

##  Homebridge Plugin installieren

Als Plugin innerhalb der Homebridge verwende ich [homebridge-camera-ffmpeg](https://web.archive.org/web/20230321120526/https://www.npmjs.com/package/homebridge-camera-ffmpeg). Es kann direkt über die Weboberfläche von Homebridge installiert werden.

![makesmart_homebridge_plugin-homebridge-camera-ffmpeg.png](https://web.archive.org/web/20230321120526im_/https://my.makesmart.net/assets/uploads/files/1608993274238-makesmart_homebridge_plugin-homebridge-camera-ffmpeg.png)

------

Nach der Installation öffnet sich ein Konfigurations-Fenster des Plugins. Ihr müsst nur die folgenden Parameter ausfüllen und eintragen - für den Rest werden die default Paramter verwendet

| `Name`               | Kamera                                     |
| :------------------- | :----------------------------------------- |
| `Video Source`       | **-i rtsp://192.168.178.116:8554/mjpeg/1** |
| `Still Image Source` | **none**                                   |
| `Enable Audio`       | **False**                                  |

Die IP-Adresse muss natürlich angepasst werden. ![🙂](https://web.archive.org/web/20230321120526im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg) Die IP-Adresse ist die, die im seriellen Monitor der Arduino IDE angezeigt wurde / wird.

Unter dem Punkt `Video Output` sollte zusätzlich noch der Punkt `Unbridge Camera` deaktiviert werden, da diese sonst die komplette Homebridge-Instanz verlangsamen könnte. Mit dieser Einstellung muss die Kamera manuell hinzugefügt werden - macht aber keinen Unterschied.

Wenn die Einstellungen eingetragen wurden, kann man diese ganz unten per klickt auf `Speichern` speichern. Die Homebridge muss danach neugestartet werden. Wichtig ist es, einen Blick in den Log auf der Startseite zu werfen, da dort der Pairing-Code der Kamera auftaucht.

```
[12/26/2020, 10:57:30 AM] Kamera is running on port 34217.
[12/26/2020, 10:57:30 AM] Please add [Kamera] manually in Home app. Setup Code: 273-85-527
```

##  Pairing mit der Home App

Das Pairing läuft ab wie bei jedem anderem Gerät.

![makesmart_pair_homkit_kamera_esp32-cam.jpeg](https://web.archive.org/web/20230321120526im_/https://my.makesmart.net/assets/uploads/files/1608993446973-makesmart_pair_homkit_kamera_esp32-cam-resized.jpeg)

1. `+` Gerät hinzufügen
2. *Ich habe keinen Code bzw. kann nicht scannen*
3. Kamera auswählen, meine Kamera trägt die Bezeichnung Kamera 3EBC
4. Pairing-Code aus dem Log eingeben:
   `Please add [Kamera] manually in Home app. Setup Code: 273-85-527`
5. Etwas gedulden, bis sich die Kamera verbunden hat

Fertig ist die HomeKit-Kamera mit der ESP32-Cam - Die eigene DIY-Low-Budget Kamera für das Smarthome.

![makesmart_esp32-homekit-kamera.jpeg](https://web.archive.org/web/20230321120526im_/https://my.makesmart.net/assets/uploads/files/1608992842994-makesmart_esp32-homekit-kamera.jpeg)

Dieses Tutorial *sollte* auch mit folgenden ESP32-Cams funktionieren:



¹Produktempfehlungen

<iframe src="https://web.archive.org/web/20230321120526if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07YS2MGQZ&amp;asins=B07YS2MGQZ&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20230321120526if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07S83X9NM&amp;asins=B07S83X9NM&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20230321120526if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07RWJGF6S&amp;asins=B07RWJGF6S&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

**[ Produkte zum Warenkorb hinzufügen](https://web.archive.org/web/20230321120526/https://www.amazon.de/gp/aws/cart/add.html?AWSAccessKeyId=AKIAJTFZSRERNXBNI5PQ&AssociateTag=makesmart05-21&ASIN.1=B07YS2MGQZ&Quantity.1=1&ASIN.2=B07S83X9NM&Quantity.2=1&ASIN.3=B07RWJGF6S&Quantity.3=1&add=add)**

------



##  Gehäuse

Nach der Programmierung und dem Setup der Kamera fehlt eigentlich nur noch ein passendes Gehäuse. Glücklicherweise hat [@dixondsc](https://web.archive.org/web/20230321120526/https://my.makesmart.net/uid/110) ein Gehäuse für die #ESP32-Cam konstruiert und [hier im Forum geteilt](https://web.archive.org/web/20230321120526/https://my.makesmart.net/topic/149/esp32-cam-gehäuse). Lasst ihm gerne ein Dankeschön da! ![🙂](https://web.archive.org/web/20230321120526im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

Das Gehäuse gibt es in zwei Varianten:
*Klicke auf den 3D-Würfel um die Modelle anzuschauen.*

1. [ESP32-Cam Gehäuse mit GoPro-Halterung](https://web.archive.org/web/20230321120526/https://my.makesmart.net/assets/uploads/files/1616270909003-esp32cam_final_gehaeuse_mithalterung.stl) 
2. [ESP32-Cam Gehäuse ohne GoPro-Halterung](https://web.archive.org/web/20230321120526/https://my.makesmart.net/assets/uploads/files/1616270909013-esp32cam_final_gehaeuse_ohnehalterung.stl) 

Und dazu wird jeweils dann noch der Deckel benötigt:

- [ESP32-Cam Deckel](https://web.archive.org/web/20230321120526/https://my.makesmart.net/assets/uploads/files/1616270908978-esp32cam_final_deckel.stl) 

Das Gehäuse ist so konstruiert, dass es über eine MicroUSB-Buchse bestromt wird. Dazu für ein [ ¹USB - DIP Adapter](https://web.archive.org/web/20230321120526/https://www.amazon.de/dp/B07W13X3TD/ref=cm_sw_r_cp_api_i_CXTNV5MBYK0MXK4RQXXA?tag=makesmart05-21) benötigt. [Mehr dazu im Thread von @dixondsc.](https://web.archive.org/web/20230321120526/https://my.makesmart.net/topic/149/esp32-cam-gehäuse)

\#Arduino #Arduino-IDE #ESP32 #ESP32-Cam #Homebridge #HomeKit #Kamera #Smarthome #Stream