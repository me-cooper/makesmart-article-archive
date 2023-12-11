# Websockets auf dem ESP8266 nutzen - Template

Hier möchte ich kurz ein Template teilen, mit dem ich mich gerade beschäftigt habe: **Websockets auf dem ESP8266**.

Also Grundlage sollte man wissen, wie man mit HTML Dateien auf dem ESP arbeiten kann. [Hierzu kann dieses Template verwendet werden](https://web.archive.org/web/20230602025543/https://my.makesmart.net/topic/206/).

Außerdem reicht hier nicht nur Basis-Wissen über den ESP und seine Programmierung, sondern hier kommen viele einzelne Bausteine zusammen. Hier arbeiten der Server (ESP8266) und der Client (Javascript) Hand in Hand zusammen, und genau aus dem Grund sollte man auch Javascript zu gewissen Teilen beherrschen, da die Kommunikation immer aus einem Client und einem Server Part besteht. Aber okay. Weiter gehts.

###  WAS MACHT DIESER CODE

Dieser Code dient aktuell in erster Linie als Snippet bzw. Template. Es wurde als nur minimal implementiert und mit einem kleinen Beispiel versehen. Im `loop()` findet man folgenden Part:

```cpp
static bool state = false;
 
if(state != digitalRead(15)){
   state = digitalRead(15);
   Serial.println(state); 
   notifyClients(String(state));
}
```

Als Input-Button wird am **D1 Mini** der Pin **D8** verwendet - also **GPIO15**. Wenn man den Input mit einem Schalter / Taster betätigt, dann wird der Wert dementsprechend auf der Webseite des ESPs aktualisiert. Bei allen Clients in nahezu Echtzeit.

Außerdem bekommt man eine Meldung, wenn ein neuer Nutzer den Server besucht. Dies dient nur als Beispiel.

Anbei noch ein kleines Video, was der folgende Code genau macht, und wie es aussieht:



<iframe src="https://web.archive.org/web/20230602025543if_/https://www.youtube.com/embed/eGPvE-61vwg" data-ruffle-polyfilled="" style="box-sizing: border-box; top: 0px; left: 0px; width: 544px; height: 360px; max-width: 85%; display: inline;"></iframe>



Die Serverseitigen Dateien wurden bearbeitet, um gleich ein praktisches Beispiel zu haben. Die Dateien im Folgendem könnt ihr 1:1 übernehmen und sie auf euren ESP ([wie hier beschrieben](https://web.archive.org/web/20230602025543/https://my.makesmart.net/topic/206/)) hochladen.

###  SCRIPT.JS

```js
$( document ).ready(function() {

    // Sobald das Dokument geladen ist
    console.log("I'm ready to makesmart!");

});


/* Websockets Beispiel */

let socket = new WebSocket(`ws://${window.location.hostname}/ws`);

//  Funktionen die genutzt werden können
//  Nachricht an den Server senden:     socket.send("connection:success"); 

socket.onopen = function(e) {
  console.log("Websockets Verbindung hergstellt!");
  socket.send("connection:new"); 
  console.log();
};

socket.onmessage = function(event) {
    console.log('Nachricht vom Server erhalten:', event.data);

    if(event.data == "1"){
      $("h1#state").hide(200, function() {
        $(this).html("ON").show(200);
        $(this).css("color", "green");
      });
    }

    if(event.data == "0"){
      $("h1#state").hide(200, function() {
        $(this).html("OFF").show(200);
        $(this).css("color", "black");
      })
    }

    if(event.data == "user:welcome"){
      $('#toast-title').html("Willkommen");
      $('#toast-message').html("Willkommen auf dem Server!");
      $('#notify-toast').slideDown(200);
    }
    
    if(event.data == "user:new"){
      // Diese Meldung hier soll nur angezeigt werden, wenn keine andere Meldung angezeigt wird
      // Da sonst auch neue Nutzer diese Meldung bekommen, neue Nutzer sollen aber persönlich begrüßt werden
      if($("#notify-toast").is(":hidden")){
        $('#toast-title').html("Neuer Nutzer");
        $('#toast-message').html("Ein neuer Nutzer ist auf dem Server!");
        $('#notify-toast').slideDown(200);
      }
      
    }

    

};

socket.onclose = function(event) {
  if (event.wasClean) {
    console.log('Verbindung zum Server wurde geschlossen!');
  } else {
    console.log('Verbindung verloren: Timeout!');
  }
};

socket.onerror = function(error) {
  console.log('[error]:', error.message);
};


$('#close-toast').click(function(){
  $('#notify-toast').slideUp(200);
});
```

###  INDEX.HTML

```html
<!doctype html>

<html lang="de">
<head>

  <meta charset="utf-8">

  <title>makesmart HTML 5 Template</title>
  <meta name="description" content="makesmart HTML Template">
  <meta name="author" content="cooper">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-capable" content="yes">

  <!-- Stylesheet-Datei -->
  <link rel="stylesheet" href="assets/css/style.css">

  <!-- FONTAWESOME -->
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" integrity="sha384-wvfXpqpZZVQGK6TAh5PVlGOfQNHSoD2xbE+QkPxCAFlNEevoEH3Sl0sibVcOQVnN" crossorigin="anonymous">

  <!-- jQuery & jQuery UI-->
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
  <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.min.js" integrity="sha256-VazP97ZCwtekAsvgPBSUwPFKdrwD3unUfSGVYrahUqU=" crossorigin="anonymous"></script>

  <!-- Bootstrap -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>


</head>

<body>

  <div class="toast" role="alert" aria-live="assertive" aria-atomic="true" id="notify-toast">
    <div class="toast-header">
      <i class="fa fa-bell"></i>
      <strong class="mr-auto" id="toast-title">Neuer User</strong>
      <small>jetzt</small>

      <button type="button" class="ml-2 mb-1 close" data-dismiss="toast" aria-label="Close"></button>
        <span aria-hidden="true" class="pull-right" id="close-toast" style="cursor: pointer;">&times;</span>
      </button>
      
    </div>
    <div class="toast-body" id="toast-message">
      Ein neuer Nutzer ist beigetreten!
    </div>
  </div>


  <center><h1 id="state">OFF</h1></center>


  <!-- Javascript Datei -->
  <script src="assets/js/script.js"></script>
</body>
</html>
```

###  STYLE.CSS

```css
h1#state{
    font-weight: bolder;
    margin-top: 30px;
}


div.toast{
    background-color: white;
    border: 1px solid gray;
    box-shadow: 5px 5px 5px -5px gray;
    width: 350px;
    margin-top: 10px;
    margin-left: 20px;
    border-radius: 3px;
    display: none;
}

div.toast-header{
    background-color: white;
    border-bottom: 1px solid gray;
    padding: 5px;
    color: gray;
    border-radius: 3px;
    font-size: .9em;
}

div.toast-body{
    background-color: rgb(243, 243, 243);
    padding: 10px 10px;
    font-size: 0.8em;
    border-radius: 3px;
}
```

Nun kommt der eigentlich wichtige Part: Der Programmcode für den ESP. Ich habe alles kommentiert weil ich denke, dass damit alls erläutert wurde.

###  DER PROGRAMMCODE

Auch hier müsst ihr wieder den Hostnamen und die Zugangsdaten vom WLAN anpassen.

```cpp
// WLAN-Zugangsdaten
const char* ssid = "SSID";
const char* password = "PSK";
 
// Hostname des ESPs -> http://spiffinger.local/
const char* espHostname = "spiffinger";
//
// Einfacher Webserver der Dateien aus dem SPIFFS (Filesystem) ausliefert
// CC by cooper @ makesmart.net
// mehr Infos unter: https://my.makesmart.net/topic/206/
//

void setupServerRoutes();
void setupFilePaths();
void onWsEvent();
void setupWebsockets();
void handleWebSocketMessage();
void notifyClients();

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
// Websockets
AsyncWebSocket ws("/ws");

// WLAN-Zugangsdaten
const char* ssid = "SSID";
const char* password = "PSK";

// Hostname des ESPs -> http://spiffinger.local/
const char* espHostname = "spiffinger";

// Was passiert, wenn eine Seite nicht gefunden werden kann
void notFound(AsyncWebServerRequest *request) {
  request->send(404, "text/html", "<center><h1>404 - error</h1></center>");
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
    // Für die Websockets
    setupWebsockets();

    server.begin();
}

void loop() {
  // MDNS
  MDNS.update();
  // Websockets
  ws.cleanupClients();  

  static bool state = false;

  if(state != digitalRead(15)){
    state = digitalRead(15);
    Serial.println(state); 
    notifyClients(String(state));
  }
  
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


void setupWebsockets(){
  ws.onEvent(onWebsocketsEvent);
  server.addHandler(&ws);  
}

void onWebsocketsEvent(AsyncWebSocket * server, AsyncWebSocketClient * client, AwsEventType type, void * arg, uint8_t *data, size_t len){
 
  if(type == WS_EVT_CONNECT){
    //  Eine neue Websockets Verbindung wurde hergestellt
    //  Serial.println("Client Verbinung hergestellt!");
    
    //  Diese nachricht geht nur an den Client
    client->text("user:welcome");
  }
  
  if(type == WS_EVT_DISCONNECT){
    //  Eine Websockets verbindung wurde getrennt
    //  Serial.println("Client Verbindung beendet!");
  }

  if(type == WS_EVT_DATA){
    //  Eine Websocket-Nachricht wurde empfangen
    //  Serial.println("onWebsocketsEvent();");
    handleWebSocketMessage(client, arg, data, len);  
  }
}

void notifyClients(String msg){
  ws.textAll(msg);  
}


//  Die zwei wichtigsten Funktionen um auf eine Nachricht zu antworten:
//  notifyClients("Nachricht an alle Clients");
//  client->text("Nachricht an den Client von dem die Nachricht kommt");

void handleWebSocketMessage(AsyncWebSocketClient * client, void *arg, uint8_t *data, size_t len) {
  Serial.println("handleWebSocketMessage();");
  AwsFrameInfo *info = (AwsFrameInfo*)arg;
  if (info->final && info->index == 0 && info->len == len && info->opcode == WS_TEXT) {
    
    //     message = Inhalt der Nachricht vom Websocket-CLient
    String message = String((char*)data);
    //  Serial.println(message);

    if(message.indexOf("connection:new") >= 0){
      //Serial.println("Ein neuer Client hat sich verbunden");  
      notifyClients("user:new");
    }
    
    
  }
}
```

------

Dieses “Tutorial” dient in erster Linie als Template bzw. Snippet um mit den Websockets zu starten. Ich denke aber, dass ich bei Zeiten das ganze hier mal noch ausbauen werde. Ich möchte dieses Template hier nur mal festhalten, da es sonst wieder in den Tiefen meiner Festplatte verschwindet. ![:heh:](https://web.archive.org/web/20230602025543im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/02344941-c9a5-4a07-9960-141b30a66ac1-DFB6FF81-BD23-47A0-981A-DA48437A27E7.png?v=chnl3kn5hqg)

Natürlich können anstatt eines einfachen Button-Input auch beliebige andere Werte und Daten per Sockets übertragen werden. Zum Beispiel Temperaturwerte, oder es könnten auch Funktionen auf dem ESP in Echzeit ausgeführt werden, etc. etc. etc.