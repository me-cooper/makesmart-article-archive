# ESP8266 als nativer HomeKit Kontaktsensor - Tutorial

Hallo miteinander! Heute habe ich mal wieder ein natives ESP8266 HomeKit Zubehör für euch - diesmal ist es ein Kontaktsensor. Der Kontaktsensor lässt sich direkt und ohne weitere Hardware und Software in HomeKit integrieren. Umsetzen werden wir den Kontaktsensor wie immer mit einem ESP8266 D1 Mini. Aber auch ein ESP-01s wäre hier eine noch kleiner Option die gut funktioniert.

Der Kontaktsensor besteht in erster Linie nur aus einem einzigen Taster. Wie auch in meinen anderen nativen HomeKit Tutorials mit dem ESP8266 lässt sich der Taster aber durch alles ersetzen, was man in der Arduino IDE eben implementieren kann. Euch sind wie immer keine Grenzen gesetzt.

###  KONTAKTSENSOR, WAS GENAU?

Ein Kontaktsensor in HomeKit kann verschiedene Formen annehmen, dazu zählen:

1. Fenster
2. Garagentor
3. Jalousien
4. Kontaktsensor
5. Tür

Ein Kontaktsensor kennt nur zwei Zustände `true` oder `false`, `an` oder `aus` oder `1` oder `0`. Man kann mit dem Sensor also repräsentieren, ob eines der von Punkt 1. - 5. genannten Dinge geöffnet oder geschlossen ist. Ich persönlich habe einen solchen Sensor am Schwebetüren-Kleiderschrank. Sobald die Tür aufgeschoben wird, wird die Türe als geöffnet erkannt und die Spots die auf den Kleiderschrank gerichtet sind gehen an.



¹Produktempfehlungen

<iframe src="https://web.archive.org/web/20230207233939if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B08GRZBKXV&amp;asins=B08GRZBKXV&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20230207233939if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B08CDZ4T9J&amp;asins=B08CDZ4T9J&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20230207233939if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B08M33BZXS&amp;asins=B08M33BZXS&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20230207233939if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07D37VDM3&amp;asins=B07D37VDM3&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

**[ Produkte zum Warenkorb hinzufügen](https://web.archive.org/web/20230207233939/https://www.amazon.de/gp/aws/cart/add.html?AWSAccessKeyId=AKIAJTFZSRERNXBNI5PQ&AssociateTag=makesmart05-21&ASIN.1=B08GRZBKXV&Quantity.1=1&ASIN.2=B08CDZ4T9J&Quantity.2=1&ASIN.3=B08M33BZXS&Quantity.3=1&ASIN.4=B07D37VDM3&Quantity.4=1&add=add)**

------



Während ein nativer HomeKit Kontaktsensor ab ca. 30€ losgeht, könnt ihr das ganze mit dieser Variante schon für nur rund 6€ umsetzen. Aber natürlich geht es hier wie immer nicht primär um den Preis sondern um die individuelle und flexible Lösung mit nahezu unbegrenzten Möglichkeiten. ![:heh:](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/02344941-c9a5-4a07-9960-141b30a66ac1-DFB6FF81-BD23-47A0-981A-DA48437A27E7.png?v=chnl3kn5hqg) Einfach immer wieder nice!

Ein paar passende Produkte für dieses Projekt findet ihr [hier](https://web.archive.org/web/20230207233939/https://makesmart.shop/loadBasket/hzFqaBp6M_N).

####  Vorbereitungen

Eigentlich ist das Snippet was ich euch hier vorstelle Plug & Play, jedoch können, wie bereits erwähnt, beliebig viele weitere Abläufe oder Bedingungen im Programm eingefügt werden, dazu benötigt man aber Kenntnisse beim Programmieren des ESP8266 D1 Mini. Solltest du das nicht haben ist es auch kein Problem - du musst lediglich wissen, wie man die [Arduino IDE zum Programmieren der ESP8266 Module einrichtet](https://web.archive.org/web/20230207233939/https://makesmart.net/esp8266-d1-mini-programmieren/).

###  HOMEKIT-ESP8266 - ARDUINO IDE LIBRARY HINZUFÜGEN

Um den bereitgestellten Programmcode verwenden zu können, muss eine Library innerhalb der Arduino IDE installiert werden. Das geht mithilfe des Bibliotheksverwalter. Öffnen könnt ihr ihn über das Menü der Arduino IDE. Die Library trägt den Namen HomeKit-ESP8266 und ist von [Mixiaoxiao](https://web.archive.org/web/20230207233939/https://github.com/Mixiaoxiao/Arduino-HomeKit-ESP8266)

```
Arduino IDE
└───Werkzeuge
    └───Bibliotheken verwalten...
```

[![HomeKit-ESP8266 Arduino IDE Library](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/assets/uploads/files/1598177591980-homekit-esp8266-bibliothek-installieren.png)](https://web.archive.org/web/20230207233939mp_/https://my.makesmart.net/assets/uploads/files/1598177591980-homekit-esp8266-bibliothek-installieren.png)

###  DER NATIVE KONTAKTSENSOR - DER CODE

Der Code besteht aus drei Dateien, diese drei Dateien habe ich euch in ein zip-Archiv gepackt. Das Archiv könnt ihr direkt hier downloaden:

- **Download: [makesmart_homekit_contact_sensor.zip](https://web.archive.org/web/20230207233939/https://my.makesmart.net/assets/uploads/files/1621803283955-makesmart_homekit_contact_sensor.zip)**

Die Ordnerstruktur sieht wie folgt aus:

```
/
├── makesmart_homekit_contact_sensor.ino
├── my_accessory.c
└── wifi_info.h
```

Ihr müsst nur die Datei `makesmart_homekit_contact_sensor.ino` öffnen. Die anderen beiden Dateien werden dann automatisch mit in der Arduino IDE geladen.

**makesmart_homekit_contact_sensor.ino**

- enthält den eigentlichen Code - was passiert wann?

**my_accessory.c**

- in dieser Datei wird der Kontaktsensor für HomeKit definiert

**wifi_info.h**

- enthält Daten zur Verbindung mit dem WLAN

###  ANPASSUNGEN IM CODE

Was ihr auf jeden Fall machen müsst ist, die WLAN Daten zu ändern. Dazu benötigt ihr nur die folgenden beiden Zeilen in der Datei `wifi_info.h`

```cpp
const char *ssid = "SSID";
const char *password = "PSK";
```

Solltet ihr den Code 1:1 übernehmen, müsst ihr noch folgende Zeile im `setup()` in der Datei `makesmart_homekit_contact_sensor.ino` auskommentieren oder löschen, da ihr sonst mit jedem Reset das Pairing verliert.

```cpp
void setup() {
  Serial.begin(115200);
  wifi_connect();
  //homekit_storage_reset();
  my_homekit_setup();
  pinMode(inputPin, INPUT); 
}
```

Solltet ihr den Code anpassen oder modifizieren wollen, empfehle ich die Zeile erst nach dem ersten Pairing auszukommentieren. So könnt ihr das Programm immer wieder Überschreiben aber das Pairing bleibt bestehen - nur mit aktualisiertem Programmcode. ![👍](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44d.png?v=chnl3kn5hqg)

###  WEITERE INFORMATIONEN

Hier in meinem vorbereiteten Programmcode funktioniert der Kontaktsensor mit nur einem Taster. Der Taster ist an `GPIO0` also Pin `D3` angeschlossen.

```cpp
// GPIO0 D3; GPIO2 = D4;
byte inputPin = 0; // GPIO0 = D3
```

Es könnte auch der Pin `D4` verwendet werden. Nur dann leuchtet bei Betätigung (also immer wenn die Tür zu ist) auch die blaue LED am D1 Mini.

Wichtig zu wissen ist, dass die Kontaktsensoren meisten NC-Schalter sind, also sind sie im Ruhezustand immer geschlossen und öffnen bei Betätigung. Das ist auch der Grund warum wir einen GPIO mit Pull-Up Widerstand verwenden - weswegen auch nur zwei und nicht drei Drähte benötigt werden.

[![esp8266-homekit-kontaktsensor-schaltplan.png](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/assets/uploads/files/1621805279871-esp8266-homekit-kontaktsensor-schaltplan.png)](https://web.archive.org/web/20230207233939mp_/https://my.makesmart.net/assets/uploads/files/1621805279871-esp8266-homekit-kontaktsensor-schaltplan.png)
Sollte z.B der Pin `D8` verwendet werden wird die Türe standardmäßig immer als `geöffnet` angezeigt und bei Betätigung des Tasters ist sie demnach geschlossen.

Hier hilft auch ein Blick in das [Datenblatt des D1 Mini](https://web.archive.org/web/20230207233939/https://makesmart.net/esp8266-d1-mini-datenblatt/).

###  DAS HOCHLADEN DES PROGRAMMS

Nach diesen Informationen könnt ihr das Programm schon auf den ESP8266 hochladen. Die einzige Änderung die ihr machen solltet ist, die Taktrate des ESP8266 von 80MHz auf 160MHz zu stellen, das sonst immer wieder Verbindungsunterbrechungen auftreten können.

Das geht über die Einstellungen

```
Werkzeuge` -> `CPU Taktrate: 160MHz
```

Nach dem Upload kann das Gerät wie gewohnt zu HomeKit hinzufügen. (Gerät hinzufügen / **+** oben rechts in der Home App)
Sollte das Pairing nicht auf Anhieb klappen, reicht es meistens, den ESP einmal zu resetten und es anschließend erneut zu versuchen.

[![esp8266-homekit-kontaktsensor-pairing.JPEG](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/assets/uploads/files/1621805712358-esp8266-homekit-kontaktsensor-pairing-resized.jpeg)](https://web.archive.org/web/20230207233939mp_/https://my.makesmart.net/assets/uploads/files/1621805712358-esp8266-homekit-kontaktsensor-pairing.jpeg)

Danach habt ihr euren [DIY Kontaktsensor nativ in HomeKit eingebunden](https://web.archive.org/web/20230207233939/https://makesmart.shop/loadBasket/hzFqaBp6M_N) und könnt ihr für Automationen verwenden! ![🙂](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

Als Taster muss natürlich nicht zwingend ein Tactile Switch verwendet werden. Am Kleiderschrank z.B habe ich einen Mikroschalter mit Schaltfahne verbaut. ![👍](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44d.png?v=chnl3kn5hqg)

Suchst du noch anderes flexibles DIY HomeKit-Zubehör mit unbegrenzten Möglichkeiten? Dann schau dir gerne die anderen Tutorials zu dieser Library an! ![:programmingparrot:](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/85c54979-2b42-4d6a-8357-458d97c45f28-programmingParrot.gif?v=chnl3kn5hqg)

- [ESP8266 D1 Mini Relais als HomeKit Schalter ohne Bridge](https://web.archive.org/web/20230207233939/https://makesmart.net/esp8266-d1-mini-homekit-schalter-ohne-bridge/)
- [Natives HomeKit Schloss im Eigenbau mit dem ESP8266](https://web.archive.org/web/20230207233939/https://makesmart.net/natives-homekit-schloss-im-eigenbau-mit-dem-esp8266/)

Suchst du gezielt nach weiteren Inhalten mit dieser Library? Dann schau doch mal in der Community unter dem Tag [homekit-esp8266](https://web.archive.org/web/20230207233939/https://my.makesmart.net/tags/homekit-esp8266) vorbei. ![:programmingparrot:](https://web.archive.org/web/20230207233939im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/85c54979-2b42-4d6a-8357-458d97c45f28-programmingParrot.gif?v=chnl3kn5hqg)