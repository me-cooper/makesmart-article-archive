# ESP8266 Webserver mit mDNS Grundgerüst

Im Zuge eines kommenden Videos auf [meinem YouTube Kanal](https://web.archive.org/web/20220627113041/https://www.youtube.com/channel/UC2r_EN8Ttkr2cKcrPLIcPQw) in dem es um den Webserver auf dem ESP8266 geht, stelle ich hier schon mal die passenden Codes bereit. Neben dem bereits erwähnten Webserver wird ebenfalls ein mDNS-Server auf dem ESP8266 eingerichtet. Das ermöglicht es uns den ESP neben der IP-Adresse auch über eine lokale Domain zu erreichen. (Ein längt überfälliger Codeschnipsel btw. ![:heh:](https://web.archive.org/web/20220627113041im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/02344941-c9a5-4a07-9960-141b30a66ac1-DFB6FF81-BD23-47A0-981A-DA48437A27E7.png?v=chnl3kn5hqg))

Der Vorteil an solchen lokalen Domains ist, dass selbst wenn sich die IP-Adresse des ESPs durch dementsprechende Netzwerkeinstellungen mal ändern sollte, bleibt die Domain statisch und es gibt keine Probleme bei Einbindungen o.ä.
Wichtig zu erwähnen wäre evtl., dass so ein mDNS bei vielen (oder allen(?)) Android-Geräten leider nicht unterstützt wird, sodass in diesem Fall dann auf die IP-Adresse zurückgegriffen werden muss.

Lokale Domains enden auf `.local` und eine Domain über die der ESP8266 erreicht werden kann, sieht dann zum Beispiel so aus:

- [http://mistert.local/](https://web.archive.org/web/20220627113041/http://mistert.local/)
- [http://esp8266-sensor.local/](https://web.archive.org/web/20220627113041/http://esp8266-sensor.local/)
- [http://servosteuerung.local/](https://web.archive.org/web/20220627113041/http://servosteuerung.local/)

------

Für den gleich folgenden Code müssen keine Libraries installiert werden. Alles was wir brauchen wird bereits standardmäßig mit dem Installieren der ESP8266 Boards mitgeliefert. Falls du das noch nicht getan hast, kannst du dir dazu gerne mein anschauen oder [diese Anleitung verwenden](https://web.archive.org/web/20220627113041/https://makesmart.net/esp8266-d1-mini-programmieren/).



<iframe src="https://web.archive.org/web/20220627113041if_/https://www.youtube.com/embed/IaJM8AJWtJI" data-ruffle-polyfilled="" style="box-sizing: border-box; top: 0px; left: 0px; width: 544px; height: 360px; max-width: 85%; display: inline;"></iframe>



###  DER CODE

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
#include <ESP8266mDNS.h>

ESP8266WebServer server(80);

const char* ssid = "makesmartWLAN";
const char* password = "makesmart_passwort";

// == http://makesmartesp.local/ 
const char* dns_name = "makesmartesp";

void eigeneFunktion();

void setup()
{
  Serial.begin(115200);
  Serial.println("ESP Gestartet");

  WiFi.begin(ssid, password);

  Serial.print("Verbindung wird hergestellt ...");
  while (WiFi.status() != WL_CONNECTED)
  {
    delay(500);
    Serial.print(".");
  }
  Serial.println();

  Serial.print("Verbunden! IP-Adresse: ");
  Serial.println(WiFi.localIP());

  // Bei Android-Geräten wird der mDNS oft nicht unterstützt, dann muss auf die IP-Adresse zurückgegriffen werden
  if (MDNS.begin(dns_name)) {
    Serial.println("DNS gestartet, erreichbar unter: ");
    Serial.println("http://" + String(dns_name) + ".local/");
  }

  server.onNotFound([](){
    server.send(404, "text/plain", "Link wurde nicht gefunden!");  
  });

  server.on("/", []() {
    server.send(200, "text/plain", "ESP-Startseite!");
  });

  server.on("/custom", []() {
    server.send(200, "text/plain", "Nur eine Beispiel-Route");
    eigeneFunktion();
  });

  server.begin();
  Serial.println("Webserver gestartet.");
}

void loop() {
  server.handleClient();  
  MDNS.update();
}

void eigeneFunktion(){
  Serial.println("unsere eigene Funktion wird ausgeführt ...");
}
```

Der Code kann Plug- & Play verwendet werden, lediglich die WLAN-Zugangsdaten müssen angepasst werden.

```cpp
const char* ssid = "makesmartWLAN";
const char* password = "makesmart_passwort";
```

Der Name der Domain wird direkt in der darauf folgenden Zeile definiert:

```cpp
// == http://makesmartesp.local/ 
const char* dns_name = "makesmartesp";
```

Sobald man das Programm auf den ESP8266 hochgeladen hat, erfolgen folgende (hoffentlich selbsterklärende) Meldungen im seriellen Monitor:

```text
Verbindung wird hergestellt ..........
Verbunden! IP-Adresse: 192.168.178.89
DNS gestartet, erreichbar unter: 
http://makesmartesp.local/
Webserver gestartet.
```

Und dann ist der Webserver auch schon erfolgreich im lokalen Netzwerk online.

------

###  WEITERE ERLÄUTERUNGEN

Der Webserver wurde mit zwei Routen definiert. Einmal `/` und einmal `/custom`. Auf der Startseite `/` erfolgt eine einfache Textausgabe im Webbrowser.

```cpp
server.on("/", []() {
    server.send(200, "text/plain", "ESP-Startseite!");
});
```

Bei der Route `/custom` wird Beispielhaft gezeigt, wie man Abläufe im Programm per Webseite initiieren kann. In diesem Fall beschränkt sich der Ablauf auf eine Ausgabe im seriellen Monitor.

```cpp
server.on("/custom", []() {
    server.send(200, "text/plain", "Nur eine Beispiel-Route");
    eigeneFunktion();
});

void eigeneFunktion(){
  Serial.println("unsere eigene Funktion wird ausgeführt ...");
}
```

Beim Callen der Route erscheint die Ausgabe im seriellen Monitor:
`unsere eigene Funktion wird ausgeführt ...`

------

Dieser Codeschnipsel ist auf das bare Minimum gekürzt worden und dient als Grundlage für einfache Webserver-Projekte mit dem ESP8266.

Hast du schon ein wenig mehr Erfahrung mit dem ESP8266? Dann lohnt sich ein Blick in folgende Beiträge, die sich allesamt mit HTTP und Webservern beschäftigen.

- [ESP8266 GET-Variablen auslesen](https://web.archive.org/web/20220627113041/https://my.makesmart.net/topic/69/esp8266-get-variablen-auslesen)
- [ESP8266 HTTP-GET Request](https://web.archive.org/web/20220627113041/https://my.makesmart.net/topic/67/esp8266-http-get-request)
- [ESP8266 Webserver echte HTML Dateien ausliefern - Tutorial](https://web.archive.org/web/20220627113041/https://my.makesmart.net/topic/206/esp8266-webserver-echte-html-dateien-ausliefern-tutorial)
- [Websockets auf dem ESP8266 nutzen - Template](https://web.archive.org/web/20220627113041/https://my.makesmart.net/topic/207/websockets-auf-dem-esp8266-nutzen-template)

Ich wünsche euch viel Spaß beim Umsetzen eurer Projekte! Peace ![🤞](https://web.archive.org/web/20220627113041im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f91e.png?v=chnl3kn5hqg)

Unterstütze mich mit dem Kauf deines Zubehörs im [makesmart.shop](https://web.archive.org/web/20220627113041/http://makesmart.shop/) ![❤](https://web.archive.org/web/20220627113041im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/2764.png?v=chnl3kn5hqg)
-> [zum ESP8266 D1 Mini Starterset-Warenkorb](https://web.archive.org/web/20220627113041/https://makesmart.shop/loadBasket/5uu5PEm-mLA)



¹Produktempfehlungen

<iframe src="https://web.archive.org/web/20220627113041if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B0754N794H&amp;asins=B0754N794H&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20220627113041if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B01EV70C78&amp;asins=B01EV70C78&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20220627113041if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B01N4VCYUK&amp;asins=B01N4VCYUK&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20220627113041if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07VN1GNMK&amp;asins=B07VN1GNMK&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

<iframe src="https://web.archive.org/web/20220627113041if_/https://ws-eu.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&amp;OneJS=1&amp;Operation=GetAdHtml&amp;MarketPlace=DE&amp;source=ss&amp;ref=as_ss_li_til&amp;ad_type=product_link&amp;tracking_id=makesmart05-21&amp;language=de_DE&amp;marketplace=amazon&amp;region=DE&amp;placement=B07BVXT1ZK&amp;asins=B07BVXT1ZK&amp;show_border=false&amp;link_opens_in_new_window=true" data-ruffle-polyfilled="" style="box-sizing: border-box; max-width: 85%; display: inline; border: 1px solid rgb(211, 211, 211); box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px; margin: 10px 15px; border-radius: 6px; overflow: hidden; width: 123px; height: 243px;"></iframe>

**[ Produkte zum Warenkorb hinzufügen](https://web.archive.org/web/20220627113041/https://www.amazon.de/gp/aws/cart/add.html?AWSAccessKeyId=AKIAJTFZSRERNXBNI5PQ&AssociateTag=makesmart05-21&ASIN.1=B0754N794H&Quantity.1=1&ASIN.2=B01EV70C78&Quantity.2=1&ASIN.3=B01N4VCYUK&Quantity.3=1&ASIN.4=B07VN1GNMK&Quantity.4=1&ASIN.5=B07BVXT1ZK&Quantity.5=1&add=add)**

------



------

Dieses Tutorial gibt es auch als Videoanleitung: