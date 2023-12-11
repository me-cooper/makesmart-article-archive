Vor kurzem habe ich ein [TikTok-Video](https://web.archive.org/web/20211025123402/https://www.tiktok.com/@me_cooper/video/6877641243111820546) veröffentlicht, indem ich die Vorteile eines smarten Türschlosses aufzeige. Konkret habe ich dabei einen ESP8266 D1 Mini mithilfe von Homebridge in ein HomeKit Schloss verwandelt. Die Nachfrage nach einem Tutorial war erstaunlich groß - aus diesem Grund habe ich mich an die Arbeit gemacht und eine Lösung gefunden, die völlig ohne Bridge auskommt: Ein smartes Türschloss nativ in HomeKit eingebunden - Marke: [DIY mit einem ESP8266](https://web.archive.org/web/20211025123402/https://makesmart.shop/loadBasket/xslfI-_FNnI).

[![esp8266_makesmart_native_homekit_lock](https://web.archive.org/web/20211025123402im_/http://i.epvpimg.com/RuFRcab.png)](https://web.archive.org/web/20211025123402mp_/http://i.epvpimg.com/RuFRcab.png)

------

**Falls du planst, dass das Schloss ein Relaismodul betätigt, dann empfehle ich dir meine Firmware zu verwenden. Dort musst du erstens \**nichts programmieren\** und zweiten dauert das ganze nur 5 Minuten.** **[HomeKit Schloss mit ESP8266 in 5 Minuten](https://web.archive.org/web/20211025123402/https://my.makesmart.net/topic/140/natives-homekit-schloss-im-eigenbau-mit-dem-esp8266/2)**

Falls du aus dem Schloss etwas eigenes machen möchtest, lies einfach weiter.

###  VORBEREITUNGEN

Ganz ohne *Programmierung* kommt man leider auch mit dieser Möglichkeit nicht aus. Wobei sich das Programmieren lediglich erstens: auf das Ändern der Zugangsdaten des eigenen WLAN beschränkt, und zweitens: Man muss selber festlegen, was passieren soll, wenn das Schloss geöffnet oder geschlossen wird. Das Öffnen und Schließen kann zum Beispiel das einfache Betätigen eines Relaismoduls sein. Aber mehr dazu später.

Falls du noch keine Ahnung hast, wie du einen ESP8266 D1 Mini programmieren kannst, kann ich dir [diesen Artikel](https://web.archive.org/web/20211025123402/https://makesmart.net/esp8266-d1-mini-programmieren/) empfehlen. Sobald du soweit bist, kannst du hier fortfahren.

####  HOMEKIT-ESP8266 - Arduino IDE Library hinzufügen

Um den bereitgestellten Programmcode verwenden zu können, muss eine Library innerhalb der Arduino IDE installiert werden. Das geht mithilfe des Bibliotheksverwalter. Öffnen könnt ihr ihn über das Menü der Arduino IDE. Die Library trägt den Namen HomeKit-ESP8266 und ist von [Mixiaoxiao](https://web.archive.org/web/20211025123402/https://github.com/Mixiaoxiao/Arduino-HomeKit-ESP8266)

```
Arduino IDE
└───Werkzeuge
    └───Bibliotheken verwalten...
```

[![homekit-esp8266-bibliothek-installieren-arduino-ide](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/assets/uploads/files/1598177591980-homekit-esp8266-bibliothek-installieren.png)](https://web.archive.org/web/20211025123402mp_/https://my.makesmart.net/assets/uploads/files/1598177591980-homekit-esp8266-bibliothek-installieren.png)

###  DAS NATIVE HOMEKIT SCHLOSS - DER CODE

Um den ESP8266 in ein natives HomeKit Schloss zu verwandeln, ist mehr als nur eine Datei Programmcode notwendig. Genauer gesagt sind es drei Dateien. Diese drei Dateien habe ich alle schon für euch vorbereitet. Downloaden könnt ihr die Dateien direkt hier:

[makesmart_homekit_lock.zip](https://web.archive.org/web/20211025123402/https://github.com/me-cooper/makesmart-homekit-lock-esp8266-native/raw/master/makesmart_homekit_lock.zip)

Entpackt das .zip-Archiv an einem beliebigen Ort auf eurem Computer. In dem entpackten Order befinden sich drei Dateien:

```
makesmart_homekit_lock
├── makesmart_homekit_lock.ino
├── my_accessory.c
└── wifi_info.h
```

**makesmart_homekit_lock.ino**

- enthält den eigentlich Code - was passiert wann?

**my_accessory.c**

- in dieser Datei wird das Schloss für HomeKit definiert

**wifi_info.h**

- enthält Daten zur Verbindung mit dem WLAN

Ihr müsst lediglich die Datei `makesmart_homekit_lock.ino` öffnen, die anderen beiden Dateien öffnen sich dann ebenfalls.

###  ANPASSUNGEN IM CODE

####  1. WLAN-Zugangsdaten

Zuerst solltet ihr in der Datei `wifi_info.h` die Zugangsdaten zu eurem WLAN anpassen.
Das geht mithilfe der beiden folgenden Zeilen:

```cpp
const char *ssid = "WLAN-SSID";
const char *password = "WLAN-PASS";
```

####  2. Was soll das Schloss machen?

Jetzt kommt es ganz darauf an, was ihr mit dem Schloss betätigen möchtet. In meinem TikTok Video habe ich die Gegensprechanlage parallel geschalten und konnte so die Türe unten im Treppenhaus über das Schloss in HomeKit öffnen. ![:schmartie:](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/33f64431-f732-4678-88eb-2dad61d38a2d-schmartie.gif?v=chnl3kn5hqg) Aber genau hier kommt der großartige Vorteil der Library ins Spiel. Ihr könnt alles umsetzen, was ich in der Arduino IDE eben umsetzen könnt. ![🔥](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f525.png?v=chnl3kn5hqg)

Ich habe zwei Funktionen für euch vorbereitet, ihr findet sie ganz am Ende von `makesmart_homekit_lock.ino`.

```cpp
/* Diese beiden Funktionen werden aufgerufen, wenn das Schloss in HomeKit betätigt wird */
void open_lock(){
  Serial.println("Schloss öffnen"); 
  // hier beliebige Aktion einfügen 
}

void close_lock(){
  Serial.println("Schloss schließen");  
  // hier beliebige Aktion einfügen
}
```

Wie die Kommentare schon sagen, werden die Funktionen aufgerufen, wenn das Schloss geöffnet oder geschlossen wird.

Um ein Relais anzusteuern, müsste der Code folgendermaßen bearbeitet werden:

```cpp
/* Diese beiden Funktionen werden aufgerufen, wenn das Schloss in HomeKit betätigt wird */
void open_lock(){
  Serial.println("Schloss öffnen"); 
  // hier beliebige Aktion einfügen 
  digitalWrite(relay, HIGH);
}

void close_lock(){
  Serial.println("Schloss schließen");  
  // hier beliebige Aktion einfügen
  digitalWrite(relay, LOW);
}
```

Zusätzlich dazu muss man dann noch oben in der Datei die Variable `relay` definieren und den Pin im `setup()` als Output setzen.

```cpp
int relay = 15; // Pin D8 
void setup() {
  pinMode(relay, OUTPUT);
  Serial.begin(115200);
  wifi_connect();
  homekit_storage_reset();
  my_homekit_setup();
}
```

Fehlt nur noch die Verdrahtung des Relais mit dem ESP8266 D1 Mini. Das ist in diesem Beispiel die selbe wie beim [ESP8266 D1 Mini Relais als HomeKit Schalter ohne Bridge](https://web.archive.org/web/20211025123402/https://makesmart.net/esp8266-d1-mini-homekit-schalter-ohne-bridge/). Ein weiteres Tutorial über natives HomeKit Zubehör mit dem ESP8266.

[![esp8266_homekit_lock_schaltplan](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/assets/uploads/files/1598181409142-esp8266_homekit_relais_schaltplan.png)](https://web.archive.org/web/20211025123402mp_/https://my.makesmart.net/assets/uploads/files/1598181409142-esp8266_homekit_relais_schaltplan.png)

| KY-19 Relaismodul | ESP8266 D1 Mini |
| :---------------- | :-------------- |
| S                 | D8              |
| +                 | 5V              |
| -                 | G               |



<iframe src="https://web.archive.org/web/20211025123402if_/https://www.youtube.com/embed/2ibsKGOjLU4" data-ruffle-polyfilled="" style="box-sizing: border-box; top: 0px; left: 0px; width: 544px; height: 360px; max-width: 85%; display: inline;"></iframe>



Und fertig ist das native HomeKit Schloss mit einem ESP8266 D1 Mini und einem Relais.

**Falls du planst, das mit dem Relais wirklich 1:1 so umzusetzen wie auf dem Video, kannst du statt dieser Anleitung hier auch meine Firmware verwenden und Zeit sparen.**

**[HomeKit Schloss mit dem ESP8266 in unter 5 Minuten](https://web.archive.org/web/20211025123402/https://my.makesmart.net/topic/140/natives-homekit-schloss-im-eigenbau-mit-dem-esp8266/2)**

------

####  Weitere Informationen

Wie bereits erwähnt könnt ihr in die Funktionen `close_lock()` `open_lock()` alles einfügen, was man innerhalb der Arduino IDE umsetzen kann. Vielleicht habt ihr ja ein paar Ideen oder Anwendungen? Vielleicht wirklich mit einem elektronischen Schloss? Oder vielleicht sogar ein Servo-Motor? Ich würde mich freuen davon zu hören.



¹Produktempfehlungen

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07GSVG4Q6&amp;asins=B07GSVG4Q6&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07KZDFKPW&amp;asins=B07KZDFKPW&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B0823P6PW6&amp;asins=B0823P6PW6&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B01N9RXGHY&amp;asins=B01N9RXGHY&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07TYG14N6&amp;asins=B07TYG14N6&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B01EV70C78&amp;asins=B01EV70C78&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20211025123402if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B01N4VCYUK&amp;asins=B01N4VCYUK&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

**[ Produkte zum Warenkorb hinzufügen](https://web.archive.org/web/20211025123402/https://www.amazon.de/gp/aws/cart/add.html?AWSAccessKeyId=AKIAJTFZSRERNXBNI5PQ&AssociateTag=makesmart05-21&ASIN.1=B07GSVG4Q6&Quantity.1=1&ASIN.2=B07KZDFKPW&Quantity.2=1&ASIN.3=B0823P6PW6&Quantity.3=1&ASIN.4=B01N9RXGHY&Quantity.4=1&ASIN.5=B07TYG14N6&Quantity.5=1&ASIN.6=B01EV70C78&Quantity.6=1&ASIN.7=B01N4VCYUK&Quantity.7=1&add=add)**

------



------

###  DER UPLOAD

Bevor ihr den Sketch auf den ESP8266 D1 Mini hochladet, solltet ihr die Taktrate der CPU des D1 Minis anpassen. Für die Library sind 160 MHz von Vorteil, da sie dann stabiler läuft.

```
Arduino IDE
└─── Werkzeuge
    └─── CPU Frequency: 160 MHz
```

Zudem solltet ihr folgende Einstellung bzgl. des Uploads ändern, um komisches- oder Fehlverhalten während der Entwicklung & Programmierung zu verhindern.

```
Arduino IDE
└─── Werkzeuge
    └─── Erase Flash: All Flash Contents
```

####  Zum Schluss

Wenn ihr mit eurem Setup zufrieden seid und das Schloss fertig eingerichtet wurde, solltet ihr die Zeile `homekit_storage_reset();` im `setup()` auskommentieren, da ihr den D1 Mini nach einem Stromverlust sonst immer wieder neu koppeln müsst. Wenn die Zeile auskommentiert ist, wird der Storage nicht resettet und der D1 Mini kann nach dem Programmen an seinen Platz verlegt, und ab sofort als natives HomeKit Schloss verwendet werden. ![🔥](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f525.png?v=chnl3kn5hqg)

```cpp
void setup() {
  Serial.begin(115200);
  wifi_connect();
  //homekit_storage_reset();
  my_homekit_setup();
}
```

------

Habt ihr noch Bock auf etwas zum [Thema Smarthome](https://web.archive.org/web/20211025123402/https://my.makesmart.net/tags/smarthome)? Dann schaut gerne in die [HomeKit-Community](https://web.archive.org/web/20211025123402/https://my.makesmart.net/tags/homekit). Wenn ihr eher am Programmieren interessiert seid und mehr zum Thema ESP8266 & Co. erfahren möchtet? Dann schaut doch lieber in die [ESP8266-Community](https://web.archive.org/web/20211025123402/https://my.makesmart.net/tags/esp8266).

Suchst du gezielt nach weiteren Tutorials mit dieser Library? Dann schau doch mal in der Community unter dem Tag [homekit-esp8266](https://web.archive.org/web/20211025123402/https://my.makesmart.net/tags/homekit-esp8266) vorbei. ![:programmingparrot:](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/85c54979-2b42-4d6a-8357-458d97c45f28-programmingParrot.gif?v=chnl3kn5hqg)

------

Link: [zur makesmart HomeKit Lock-Firmware](https://web.archive.org/web/20211025123402/https://my.makesmart.net/topic/140/natives-homekit-schloss-im-eigenbau-mit-dem-esp8266/2)

\#Arduino-IDE #D1-Mini #ESP8266 #HomeKit #HomeKit-ESP8266 #Smarthome

------

Möchtest du mich und die makesmart Plattform **direkt** unterstützen? Dann überleg’ dir doch, ob du [die Produkte für dein nächstes Projekt](https://web.archive.org/web/20211025123402/https://makesmart.shop/loadBasket/ThyF7FK4_NB) nicht im [makesmart.shop](https://web.archive.org/web/20211025123402/http://makesmart.shop/) kaufen möchtest. Danke! ![🙂](https://web.archive.org/web/20211025123402im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

Bis dann!