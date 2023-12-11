# HTTPs REST-API mit dem ESP8266 abfragen

In einem anderem Tutorial habe ich bereits erklärt, wie man [einfache HTTP GET-Requests mit einem ESP8266 ausführen und verarbeiten](https://web.archive.org/web/20220627104010/https://my.makesmart.net/topic/67/esp8266-http-get-request?_=1635680198528) kann. Mit dieser Möglichkeit werden leider keine Anfragen an mit SSL verschlüsselte Verbindungen unterstützt. Aus diesem Grund möchte ich es zum Anlass nehmen, hier ein kurzes Tutorial darüber zu machen, die man eine REST API mit dem ESP8266 abfragen kann, auch wenn die Verbindung mit SSL verschlüsselt ist.

**Wichtig zu sagen ist:** Die folgende Art und Weise ist nicht sicher, da wir **ohne Fingerprint** arbeiten und somit nur eine pseudo verschlüsselte Verbindung haben. Aber so können wir die Daten auslesen und verarbeiten.

Da wir mit einer REST API arbeiten, wäre es von Vorteil, wenn ihr euch mein Tutorial zum Thema [Arduino IDE - Arbeiten mit JSON Objekten für Einsteiger](https://web.archive.org/web/20220627104010/https://my.makesmart.net/topic/141/arduino-ide-arbeiten-mit-json-objekten-für-einsteiger-tutorial) anschauen würdet. Denn Ziel bei diesem Tutorial ist es, die Antwort des Servers ebenfalls als JSON behandeln zu können. Falls ihr nur schnell zum Ziel kommen wollt, reicht es die ArduinoJSON Library zu installieren.

[![ArduinoJSON ESP8266 Arduino IDE Library](https://web.archive.org/web/20220627104010im_/http://i.epvpimg.com/trFKbab.png)](https://web.archive.org/web/20220627104010mp_/http://i.epvpimg.com/trFKbab.png)

------

###  DER ENDPOINT

Als Endpoint der Anfrage verwenden wir eine Beispiel API von [MisterT](https://web.archive.org/web/20220627104010/https://my.makesmart.net/topic/30/mister-t-eisteemaschine-diy-projekt) diese könnt ihr unter [https://rip-my.dev/get/mistert](https://web.archive.org/web/20220627104010/https://rip-my.dev/get/mistert) abfragen:

```json
{
  "name": "MisterT",
  "description": "Wir stehen für eine gesunde Lebensweise. T ",
  "ressources": [
    "/settings",
    "/stats"
  ]
}
```

In dem Array `ressources` stehen zusätzliche Endpunkte, die ihr callen könnt. Wir haben also insgesamt 3 REST-API Endpoints mit denen wir arbeiten können:

1. [https://rip-my.dev/get/mistert](https://web.archive.org/web/20220627104010/https://rip-my.dev/get/mistert)
2. [https://rip-my.dev/get/mistert/settings](https://web.archive.org/web/20220627104010/https://rip-my.dev/get/mistert/settings)
3. [https://rip-my.dev/get/mistert/stats](https://web.archive.org/web/20220627104010/https://rip-my.dev/get/mistert/stats)

Ich entscheide mich für dieses Tutorial für den zweiten Endpoint, da wir dort am meisten Daten zum Testen haben.

###  DER CODE

Als Grundlage verwende ich den Code aus meinem Tutorial [ESP8266 HTTP-GET Request](https://web.archive.org/web/20220627104010/https://my.makesmart.net/topic/67/esp8266-http-get-request). Der einzige Unterschied ist, dass wir mit dem folgenden zwei Code-Anpassungen auch auf HTTPs Endpoints zugreifen können:

```
WiFiClient wifiClient;` wird zu: `WiFiClientSecure wifiClient;
```

Zusätzlich, da wir ohne Fingerprint arbeiten, setzen wir den Client dann vor der eigentlichen REST-Abfrage auf *insecure*:

```cpp
wifiClient.setInsecure();
```

Und schon kann eine GET-Request an einen HTTPs-Server gesendet werden.

```cpp
/*
    HTTPS REST-API GET
    Basic-Code für GET-Request an einen HTTPS-Server
    Created by cooper, 2020
    makesmart.net
*/
 
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
 
HTTPClient sender;
WiFiClientSecure wifiClient;
 
// WLAN-Daten
const char* ssid = "WLAN-SSID";
const char* password = "WLAN-PSK";
 
 
void setup() {
  Serial.begin(115200);
  
  WiFi.begin(ssid, password);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(200);
    Serial.print(".");
  }
 
  Serial.println("Mit dem WLAN verbunden!");
  Serial.println();
 
  wifiClient.setInsecure();
  if (sender.begin(wifiClient, "https://rip-my.dev/get/mistert/settings")) {
    // HTTP-Code der Response speichern
    int httpCode = sender.GET(); 
    if (httpCode > 0) {
      // Anfrage wurde gesendet und Server hat geantwortet
      // Info: Der HTTP-Code für 'OK' ist 200
      if (httpCode == 200) {
        // Hier wurden die Daten vom Server empfangen
        // String vom Webseiteninhalt speichern
        String payload = sender.getString();
        // Hier kann mit dem Wert weitergearbeitet werden
        // ist aber nicht unbedingt notwendig
        Serial.println(payload);
      }else{
        // Falls HTTP-Error
        Serial.print("HTTP-Error: " +  String(httpCode));
      }
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

Das ist mal eine Standard-HTTPs Anfrage an den definierten Server [https://rip-my.dev/get/mistert/settings](https://web.archive.org/web/20220627104010/https://rip-my.dev/get/mistert/settings). Als Rückgabewert erhalten wir im seriellen Monitor das JSON Objekt als einfachen String:

```text
{"adress":"192.168.178.27","name":"MisterT","default_fill_ml":220,"water_value":4.2,"additive_value":10.6,"standby_color":"#fff","strength":1,"api_enabled":true}
```

Diese Response möchten wir jetzt als JSON verarbeiten um sehr leicht auf die einzelnen Keys und Values zugreifen zu können. Das geht wie bereits erwähnt am einfachsten mit der [ArduinoJSON Library](https://web.archive.org/web/20220627104010/https://my.makesmart.net/topic/141/arduino-ide-arbeiten-mit-json-objekten-für-einsteiger-tutorial).

###  ARBEITEN MIT DER RESPONSE ALS JSON

Zuerst müssen wir in unserem Programm die Arduino JSON Library einbinden:
`#include <ArduinoJson.h>`

Die ganze restliche Magie passiert dann, nachdem wir die Antwort vom Server erhalten haben.
Das JSON-Objekt wird im Code bereits als Variable `payload` gespeichert, dass passiert in der folgenden Zeile:

```cpp
if (httpCode == 200) {
        // Hier wurden die Daten vom Server empfangen
        // String vom Webseiteninhalt speichern
        String payload = sender.getString();
        // Hier kann mit dem Wert weitergearbeitet werden
        // ist aber nicht unbedingt notwendig
        Serial.println(payload);
      }
{
  "adress": "192.168.178.27",
  "name": "MisterT",
  "default_fill_ml": 220,
  "water_value": 4.2,
  "additive_value": 10.6,
  "standby_color": "#fff",
  "strength": 1,
  "api_enabled": true
}
```

Die Variable `payload` kann direkt dafür verwendet werden das JSON Objekt zu deserialisieren.

```cpp
if (httpCode == 200) {
        // Hier wurden die Daten vom Server empfangen
        // String vom Webseiteninhalt speichern
        String payload = sender.getString();

        // Hier wird ein neuer Buffer mit dem Namen response erzeugt, mit dem wir an die Werte kommen
        DynamicJsonDocument response(1024);
        DeserializationError error = deserializeJson(response, payload);
        if (error)
          return;
         
        // Hier lesen wir die Werte über den Buffer aus
        String myName = response["name"];
        int default_fill_ml = response["default_fill_ml"];
        bool api_enabled = response["api_enabled"];
        String standby_color = response["standby_color"];

        // Einfache Ausgabe über den seriellen Monitor
        Serial.println(myName);
        Serial.println(default_fill_ml);
        Serial.println(api_enabled);
        Serial.println(standby_color);
        
}
MisterT
220
1
#fff
```

So einfach ist, es eine REST API mit dem ESP8266 abzufragen. Wenn du dich mehr für das Thema ArduinoJSON interessierst, dann lohnt sich wie gesagt ein Blick in mein Tutorial [Arduino IDE - Arbeiten mit JSON Objekten für Einsteiger](https://web.archive.org/web/20220627104010/https://my.makesmart.net/topic/141/arduino-ide-arbeiten-mit-json-objekten-für-einsteiger-tutorial). Und für alle die auf der Suche nach einer Copy & Paste Lösung sind, hier nochmal der komplette Code für das deserialisiereneiner REST-API Antwort:

```cpp
/*
    HTTPS REST-API GET
    Basic-Code für GET-Request an einen HTTPS-Server
    Created by cooper, 2020
    makesmart.net
*/
 
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
 
HTTPClient sender;
WiFiClientSecure wifiClient;

#include <ArduinoJson.h>
 
// WLAN-Daten
const char* ssid = "WLAN-SSID";
const char* password = "WLAN-PSK";
 
 
void setup() {
  Serial.begin(115200);
  
  WiFi.begin(ssid, password);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(200);
    Serial.print(".");
  }
 
  Serial.println("Mit dem WLAN verbunden!");
  Serial.println();
 
  wifiClient.setInsecure();
  if (sender.begin(wifiClient, "https://rip-my.dev/get/mistert/settings")) {
    // HTTP-Code der Response speichern
    int httpCode = sender.GET(); 
    if (httpCode > 0) {
      // Anfrage wurde gesendet und Server hat geantwortet
      // Info: Der HTTP-Code für 'OK' ist 200
      if (httpCode == 200) {
        // Hier wurden die Daten vom Server empfangen
        // String vom Webseiteninhalt speichern
        String payload = sender.getString();

        // Hier wird ein neuer Buffer mit dem Namen response erzeugt, mit dem wir an die Werte kommen
        DynamicJsonDocument response(1024);
        DeserializationError error = deserializeJson(response, payload);
        if (error)
          return;
         
        // Hier lesen wir die Werte über den Buffer aus
        String myName = response["name"];
        int default_fill_ml = response["default_fill_ml"];
        bool api_enabled = response["api_enabled"];
        String standby_color = response["standby_color"];

        // Einfache Ausgabe über den seriellen Monitor
        Serial.println(myName);
        Serial.println(default_fill_ml);
        Serial.println(api_enabled);
        Serial.println(standby_color);
        
      }else{
        // Falls HTTP-Error
        Serial.print("HTTP-Error: " +  String(httpCode));
      }
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