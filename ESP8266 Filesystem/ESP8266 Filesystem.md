# ESP8266 Webserver echte HTML Dateien ausliefern - Tutorial

### VORBEREITUNGEN

1. [ESPAsyncWebserver installieren](https://web.archive.org/web/20220627131818/https://github.com/me-no-dev/ESPAsyncWebServer)
2. [makesmart HTML-Template herunterladen](https://web.archive.org/web/20220627131818/https://my.makesmart.net/topic/116/)
3. Die Dateien entpacken und in einen Order names `data` packen. Sieht dann ungefähr so aus:

```
/
├── data
│   ├── assets
│   │   ├── css
│   │   │   └── style.css
│   │   ├── img
│   │   └── js
│   │       └── script.js
│   └── index.html
└── webserver_filesystem.ino
```

1. Den Order Data via Arduino IDE hochladen
   `Werkzeuge -> ESP8266 Sketch Data Upload`
   ![Bildschirmfoto 2021-05-21 um 22.30.38.png](https://web.archive.org/web/20220627131818im_/https://my.makesmart.net/assets/uploads/files/1621629079675-bildschirmfoto-2021-05-21-um-22.30.38.png)
   ***Note:** Der Upload funktioniert nur, wenn der serielle Monitor geschlossen ist. ![:heh:](https://web.archive.org/web/20220627131818im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/02344941-c9a5-4a07-9960-141b30a66ac1-DFB6FF81-BD23-47A0-981A-DA48437A27E7.png?v=chnl3kn5hqg)*

*Der Uploader **sollte** bei neueren Version der Arduino IDE schon standardmäßig vorhanden sein, [ansonsten muss die Erweiterung manuell installiert werden](https://web.archive.org/web/20220627131818/https://github.com/esp8266/arduino-esp8266fs-plugin).*

------

###  DER PROGRAMMCODE

WLAN-Daten und Hostname bearbeiten:

```cpp
// WLAN-Zugangsdaten
const char* ssid = "SSID";
const char* password = "PSK";

// Hostname des ESPs -> http://spiffinger.local/
const char* espHostname = "spiffinger";
```

Sketch hochladen:

```cpp
//
// Einfacher Webserver der Dateien aus dem SPIFFS (Filesystem) ausliefert
// CC by cooper @ makesmart.net
// mehr Infos unter: https://my.makesmart.net/topic/206/
//

void setupServerRoutes();
void setupFilePaths();

// WLAN
#include <ESP8266WiFi.h>
#include <ESP8266mDNS.h>

// Webserver 
#include <ESPAsyncTCP.h>
#include <ESPAsyncWebServer.h>

// Filesystem
#include <FS.h>

// Standard HTTP-Port 80
AsyncWebServer server(80);

// WLAN-Zugangsdaten
const char* ssid = "SSID";
const char* password = "PSK";

// Hostname des ESPs -> http://spiffinger.local/
const char* espHostname = "spiffinger";

// Was passiert, wenn eine Seite nicht gefunden werden kann
void notFound(AsyncWebServerRequest *request) {
    request->send(404, "text/html", "<center><h1>404 error - Seite nicht gefunden</h1></center>");
}

void setup() {
    delay(500);
    Serial.begin(115200);
    Serial.println();
    delay(500);

    // Dateisystem initialisieren
    if(SPIFFS.begin()){
      Serial.println("Dateisystem: initialisiert");
    }else{
      Serial.println("Dateisystem: Fehler beim initialisieren");
    }
    
    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid, password);
    if (WiFi.waitForConnectResult() != WL_CONNECTED) {
        Serial.println("WLAN Verbindung fehlgeschlagen");
        return;
    }

    Serial.print("Verbunden! IP-Adresse: "); Serial.println(WiFi.localIP());

    // Starten des mDNS-Servers
    if (!MDNS.begin(espHostname)) {             
      Serial.println("Fehler beim Staren des mDNS-Servers!");
    }

    // Server Routen werden in einer eigenen Funktion definiert
    setupServerRoutes();
    // Datei Routen werden in einer eigenen Funktion definiert
    setupFilePaths();

    server.begin();
}

void loop() {
  MDNS.update();
  
}


void setupServerRoutes(){
  // Als Document-Root wird der Ordner "data"
  // Alle Pfade müssen relativ vom Ordner "data" definiert werden

  // Datei index.html wird ausgeliefert
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
        request->send(SPIFFS, "/index.html");
  });

  server.onNotFound(notFound);
  
}

void setupFilePaths(){
  // In der index.html Datei wird eine .js und eine .css Datei verlinkt
  // diese können ohne die folgenden Zeilen nicht gefunden werden

  // Der  erste Pfad bezieht sich auf die .html-Datei
  // dort ist als Pfad zur CSS-Datei "assets/css/style.css" hinterlegt
  server.on("/assets/css/style.css", HTTP_GET, [](AsyncWebServerRequest *request){
    // also soll auch die Datei aus dem SPIFFS dementsprechend ausgeliefert werden
    request->send(SPIFFS, "/assets/css/style.css", "text/css");
  });

  server.on("/assets/js/script.js", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/assets/js/script.js", "text/javascript");
  });

  // Gleiches kann man auch mit Bildern anderen Dateien machen
  
}
```

------

###  WEBSERVER MIT HTML-DATEIEN FEIERN

Ausgabe im seriellen Monitor beachten:

```
22:21:26.396 -> Dateisystem: initialisiert
22:21:27.490 -> Verbunden! IP-Adresse: 192.168.178.89
```

Webseite öffnen und freuen, dass die HTML-Datei aus dem Dateisystem angezeigt wird.

![Bildschirmfoto 2021-05-21 um 22.28.09.png](https://web.archive.org/web/20220627131818im_/https://my.makesmart.net/assets/uploads/files/1621628903016-bildschirmfoto-2021-05-21-um-22.28.09.png)

------

Die HTML-, CSS- und Javascript-Dateien können jetzt mit einem beliebigem Editor bearbeitet und im Browser gedebugged / designed und anschließend wieder mit **Punkt 4** hochgeladen werden. Der Sketch muss dafür nicht immer wieder neu hochgeladen werden. ![👍](https://web.archive.org/web/20220627131818im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f44d.png?v=chnl3kn5hqg) Freuen dass jetzt auch echte HTML-Dateien mit CSS und Javascript über den Webserver auf dem ESP angezeigt werden können. ![:programmingparrot:](https://web.archive.org/web/20220627131818im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/85c54979-2b42-4d6a-8357-458d97c45f28-programmingParrot.gif?v=chnl3kn5hqg)

------

**Wird noch ausgearbeitet und dient aktuell als Entwurf ![:heh:](https://web.archive.org/web/20220627131818im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/02344941-c9a5-4a07-9960-141b30a66ac1-DFB6FF81-BD23-47A0-981A-DA48437A27E7.png?v=chnl3kn5hqg)**