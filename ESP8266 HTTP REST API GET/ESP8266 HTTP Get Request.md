# ESP8266 HTTP-GET Request

Eine HTTP-GET Request macht eigentlich nichts anderes, als einen Link aufzurufen- meinetwegen den Link [http://makesmart-server/meine/api](https://web.archive.org/web/20221006030149/http://makesmart-server/meine/api).

Mal angenommen hinter diesem Link steckt ein PHP-Script, was beim Aufruf der URL ausgeführt wird und anschließend einen Text im Webbrowser anzeigt z.B

```
operation_successful
```

------

Der folgende Code dient als Basic-Code für Projekte mit deinem ESP8266. Der Code führt solch eine HTTP GET-Request aus. Ob hinter der aufgerufenen URL ein PHP-Script läuft oder auch was anderes, spielt keine Rolle. Der ESP übernimmt lediglich den Part des Aufrufens der URL.

Der Rückgabewert der URL (falls vorhanden) wird in einer Variable gespeichert und kann mit dem [ESP8266](https://web.archive.org/web/20221006030149/https://makesmart.shop/loadBasket/5uu5PEm-mLA) weiterverarbeitet werden. Ein Weiterarbeiten mit dem Rückgabewert ist **nicht** zwingend Notwendig.

**Der folgende Code unterstützt nur die Request an einen HTTP-Server.**
Bist du auf der Suche nach einem Code-Snippet für HTTPs Endpoints? Dann empfehle ich dir mein Tutorial zum Thema [HTTPs REST-API mit dem ESP8266 abfragen](https://web.archive.org/web/20221006030149/https://my.makesmart.net/topic/232/https-rest-api-mit-dem-esp8266-abfragen)

```c++
/*
    HTTP-GET Request
    Basic-Code für GET-Request an einen HTTP-Server
    Created by cooper, 2020
    makesmart.net
*/

#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

HTTPClient sender;
WiFiClient wifiClient;

// WLAN-Daten
const char* ssid = "WLAN_SSID";
const char* password = "WLAN_PASSWD";


void setup() {
  Serial.begin(115200);
  
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(200);
    Serial.print(".");
  }

  Serial.println("Verbunden!");


  if (sender.begin(wifiClient, "http://makesmart-server/meine/api")) {

    // HTTP-Code der Response speichern
    int httpCode = sender.GET();
   

    if (httpCode > 0) {
      
      // Anfrage wurde gesendet und Server hat geantwortet
      // Info: Der HTTP-Code für 'OK' ist 200
      if (httpCode == HTTP_CODE_OK) {

        // Hier wurden die Daten vom Server empfangen

        // String vom Webseiteninhalt speichern
        String payload = sender.getString();

        // Hier kann mit dem Wert weitergearbeitet werden
       // ist aber nicht unbedingt notwendig
        Serial.println(payload);
        
        
        
      }
      
    }else{
      // Falls HTTP-Error
      Serial.printf("HTTP-Error: ", sender.errorToString(httpCode).c_str());
    }

    // Wenn alles abgeschlossen ist, wird die Verbindung wieder beendet
    sender.end();
    
  }else {
    Serial.printf("HTTP-Verbindung konnte nicht hergestellt werden!");
  }
  
}


void loop() {

}
```

##  Anpassungen

------

Zuerst müssen die WLAN-Daten angepasst werden

```c++
const char* ssid = "WLAN_SSID";
const char* password = "WLAN_PASSWD";
```

Die API, die aufgerufen wird, ist in folgender Funktion definiert:

```c++
sender.begin(wifiClient, "http://makesmart-server/meine/api");
```

------

Der zurückgegebene String wird mithilfe der `getString()`-Funktion gespeichert.

```c++
String returned_data = sender.getString();
```

------

##  Disclaimer

**Der Code funktioniert nur bei einer Request an einen HTTP-Server.** Ich habe bereits eine zweite Version geschrieben, die Request an einen HTTPS-Server unterstützt. Diese findest du hier: [HTTPs REST-API mit dem ESP8266 abfragen](https://web.archive.org/web/20221006030149/https://my.makesmart.net/topic/232/https-rest-api-mit-dem-esp8266-abfragen)

##  Anwendung

Der Code kann wie bereits erwähnt u.a dafür verwendet werden, Scripte oder Programme hinter einem Webserver auszuführen und zu starten.

------

Mit dieser Methode könnten auch Variablen und Werte übertragen werden - z.B von Sensoren oder anderem Stuff: [ESP8266 GET-Variablen auslesen](https://web.archive.org/web/20221006030149/https://my.makesmart.net/topic/69/esp8266-get-variablen-auslesen)

------

Schon allein das Aufrufen einer URL per Knopfdruck, kann den ESP zu einer Fernbedienung machen. ![😉](https://web.archive.org/web/20221006030149im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f609.png?v=chnl3kn5hqg)

------

… usw. usw.

##  Beispiele

Anbei ein paar Links & Inspirationen zur Verwendung dieses Codes.

- [Battlescore - Spielstand-Zähler: ESP8266 Projekt](https://web.archive.org/web/20221006030149/https://makesmart.net/battlescore-esp8266-projekt/)
- [ESP8266 D1 Mini WLAN-Relais mit API](https://web.archive.org/web/20221006030149/https://makesmart.net/esp8266-wlan-relais/)
- [ESP8266 GET-Variablen auslesen](https://web.archive.org/web/20221006030149/https://my.makesmart.net/topic/69/esp8266-get-variablen-auslesen)

*Die Links werden aktualisiert, wenn ich weiteres passende finde. ![🙂](https://web.archive.org/web/20221006030149im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)*

------

Viel Spaß mit euren Projekten,

*cooper*

