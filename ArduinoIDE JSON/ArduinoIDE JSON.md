# Arduino IDE - Arbeiten mit JSON Objekten für Einsteiger - Tutorial

Die JavaScript Object Notation - JSON - ist ein kompaktes Datenformat in einer einfach lesbaren Textform und dient unter anderem zum Datenaustausche zwischen Anwendungen. Egal ob als Konfigurationsdatei, zum Übertragen von Daten via API oder auch zum Speichern in einer Datenbank wie MongoDB. JSON Objekte sind nicht mehr wegzudenken - und das ist ein guter Grund das ganze auch auf einem Mikrocontroller wie ESP866, D1 Mini, Arduino & Co. zu nutzen.

------

Nehmen wir an, wir programmieren eine kleine Software für eine Lampe mit einer Konfiguration für einen adessierbaren LED-Streifen. Diese Konfiguration möchten wir gerne im JSON-Format abspeichern, sodass wir sie einerseits beispielsweise über das SPIFFS-Dateisystem auslesen können, aber auch, damit wir über eine API die Paramater des JSON-Objekts einfach ändern und anpassen können, wenn wir dies wollten.

Eine ganz einfache JSON Konfiguration könnte so aussehen - zwei Keys und dazugehörige Values:

```json
{ 
   "name": "Lampe",
   "numLEDs": 75  
}
```

------

##  ArduinoJson - Arduino IDE Library hinzufügen

Bevor wir damit anfangen, mit einem JSON Objekt zu arbeiten, muss zuerst eine passenden Library installiert werden. Die Library heißt **[ArduinoJson](https://web.archive.org/web/20230602014446/https://arduinojson.org/)**. Libraries werden immer gleich installiert. Und zwar über den Bibliotheksverwalter innerhalb der Arduino IDE.

```
Arduino IDE
└───Werkzeuge
    └───Bibliotheken verwalten...
```

Die Library kann über die Eingabe in der Suche gefunden werden

[![makesmart arduino json verarbeiten](https://web.archive.org/web/20230602014446im_/http://i.epvpimg.com/trFKbab.png)](https://web.archive.org/web/20230602014446mp_/http://i.epvpimg.com/trFKbab.png)

Mit der Installation der Library können wir auf alle Klassen, Objekte und Methoden dieser zugreifen und starten, JSON Objekte innerhalb der Arduino IDE zu erstellen, zu bearbeiten und auszulesen.

##  Arduino IDE - JSON Objekt erstellen

Wir starten mit einem leeren Programm, in dem die Library `ArduinoJson` bereits eingebunden wurde.

```cpp
#include <ArduinoJson.h>

void setup() {
  // Setup läuft einmal
  Serial.begin(115200);
  Serial.println("makesmart ESP8266");
} 

void loop() {
  // Loop läuft in Endlosschleife
}
```

Mit dem Import der Library sind wir auch sofort in der Lage ein JSON-Objekt zu erstellen. Und zwar mit der Klasse `DynamicJsonDocument`.

```cpp
void setup() {
  // Setup läuft einmal
  Serial.begin(115200);
  Serial.println("makesmart ESP8266");

  // Eine kleine Pause für uns
  delay(1000);

  // Hier wird das JSON-Objekt erstellt
  DynamicJsonDocument configJSON(1024);
  configJSON["name"] = "Lampe";
  configJSON["numLEDs"] = 75;
  
  serializeJson(configJSON, Serial); 
} 
```

Die Ausgabe im seriellen Monitor sieht nach dem Hochladen wie folgt aus:

```json
{"name":"Lampe","numLEDs":24}
```

Mithilfe des Webservices [JsonLint](https://web.archive.org/web/20230602014446/https://jsonlint.com/) lässt sich das JSON-Objekt leicht lesbar formatieren:

```json
{
   "name": "Lampe",
   "numLEDs": 24
}
```

Das hinzufügen von weiteren Key- und Value-Paaren ist genauso simple, wie das eben erstellte JSON-Objekt:

```cpp
// Hier wird das JSON-Objekt erstellt
DynamicJsonDocument configJSON(1024);
configJSON["name"] = "Lampe";
configJSON["numLEDs"] = 75;
configJSON["endpoint"] = "/api/lamp";
  
serializeJson(configJSON, Serial);
{
   "name": "Lampe",
   "numLEDs": 24,
   "endpoint": "/api/lamp"
}
```

------

##  ArduinoJSON - erstellen von Arrays

####  Einfaches Array als Liste mit Werten

Arrays sind Listen, Listen mit Werten. Lasst uns annehmen: Unsere Lampe hat verschiedene Animationen. Diese Animationen werden je druch eine eigene Bezeichnung definiert. Das Array mit den Bezeichnungen der Animationen könnte so aussehen:

```cpp
String animations[] = {"demo", "fire", "ocean", "fade"};
["demo", "fire", "ocean", "fade"]
```

Um dieses Array in unser JSON Objekt zu bekommen, können wir eine nützliche Funktion aus der Library verwenden - `add()`.

Dafür erstellen für einen Loop, der jeden Eintrag im Array `String animations[]` druchläuft, und den aktuellen Wert per `add()` zu unserem JSON Objekt hinzufügt:

```cpp
String animations[] = {"demo", "fire", "ocean", "fade"};

// for-Schleife, die das Array 'animations[]' durchläuft
for (byte i = 0; i < (sizeof(animations)/sizeof(animations[0])); i++) {
   // animations[i] enthält den aktuellen Wert vom Array und wird per 'add()' zum Objekt hinzugefügt
   configJSON["animations"].add(animations[i]);
}
```

Das Objekt sieht anschließend folgendermaßen aus:

```json
{
   "name": "Lampe",
   "numLEDs": 24,
   "endpoint": "/api/lamp",
   "animations": ["demo", "fire", "ocean", "fade"]
}
```

####  Arrays mit Objeken

Was wäre denn, wenn wir nicht nur einen Endpoint haben möchten, sondern vielleicht zwei? Diese beiden Endpoints können wir ebenfalls in einem Array speichern. Das Array enthält dabei nicht nur zwei Werte, sondern zwei Objekte - unserer beiden Endpoints.

Wir ersetzen den Key `endpoint` in unserem Objekt durch ein Array mit dem Key `endpoints`.
In diesem Array speichern wir zwei Objekte:

```cpp
configJSON["endpoints"][0]["switchOn"] = "/api/lamp/switchOn";
configJSON["endpoints"][1]["switchOff"] = "/api/lamp/switchOff";
  
serializeJson(configJSON, Serial);
{
	"name": "Lampe",
	"numLEDs": 24,
	"endpoints": [
           {
              "switchOn": "/api/lamp/switchOn"
           },
           {
               "switchOff": "/api/lamp/switchOff"
           }
	],
	"animations": ["demo", "fire", "ocean", "fade"]
}
```

Wir sehen, dass wir ein Array `endpoints[]` erhalten und darin sind zwei Objekte. Einmal mit dem Key `switchOn` und einmal mit dem Key `switchOff`. Natürlich können wir auch mehrere Key- und Value-Paare in einem Objekt innerhalb eines Arrays speichern. Dazu muss lediglich an der gleichen Index-Position des Arrays ein weiteres Paar hinzugefügt werden:

```cpp
configJSON["endpoints"][0]["switchOn"] = "/api/lamp/switchOn";
configJSON["endpoints"][0]["desc"] = "Turn the lights on!";
  
configJSON["endpoints"][1]["switchOff"] = "/api/lamp/switchOff";
configJSON["endpoints"][1]["desc"] = "Turn the lights off!";
  
serializeJson(configJSON, Serial);
{
	"name": "Lampe",
	"numLEDs": 24,
	"endpoints": [
           {
	      "switchOn": "/api/lamp/switchOn",
	      "desc": "Turn the lights on!"
	   },
  	   {
	      "switchOff": "/api/lamp/switchOff",
	      "desc": "Turn the lights off!"
	   }
  ],
  "animations": ["demo", "fire", "ocean", "fade"]
}
```

####  Objekte im Objekt

Das mit den gerade eben aufgeführten Objekten in Arrays ist auch so ähnlich gültig für Objekte in Objekten.

```json
{
   {
      // OBJ 1
   }
   {
      // OBJ 2
   }
   {
      // OBJ 2
   }
}
```

Wir erstellen ein neues Objekt `ledSettings`. Dieses Objekt erhält von uns vier verschiedene Key- Value-Paare:

```cpp
configJSON["ledSettings"]["chip"] = "WS2812B";
configJSON["ledSettings"]["colorOrder"] = "GRB";
configJSON["ledSettings"]["framerate"] = "60";
configJSON["ledSettings"]["brightness"] = "100";
```

Wir fügen in unsere `configJson` also ein Objekt ein mit der Bezeichnung `ledSettings`.
Dieses Objekt erhält von uns vier Eigenschaften: `chip`, `colorOrder`, `framerate`, `brightness` mit den dazugehörigen Values:

```json
{
   "name": "Lampe",
   "numLEDs": 24,
   "endpoints": [
      {
          "switchOn": "/api/lamp/switchOn",
          "desc": "Turn the lights on!"
      },
      {
         "switchOff": "/api/lamp/switchOff",
         "desc": "Turn the lights off!"
       }
  ],
  "animations": ["demo", "fire", "ocean", "fade"],
  "ledSettings": {
      "chip": "WS2812B",
      "colorOrder": "GRB",
      "framerate": "60",
      "brightness": "100"
   }
}
```

------

Das ganze Spielchen mit Objekten, Listen, Listen mit Objekten und Objekte mit Listen lässt sich beliebig oft wiederholen und ist sehr flexibel und vor allem auch sehr verschatelt anwendbar. Auch wenn es im ersten Moment *kompliziert* aussieht, ist es eigentlich doch ziemlich selbsterklärend, vor allem wenn man das Prinzip einer Objektorientierten Datenerfassung / Verarbeitung verstanden hat. Hier nochmal der bisherige Code:

```cpp
void setup() {
  // Setup läuft einmal
  Serial.begin(115200);
  Serial.println("makesmart ESP8266");

  // Eine kleine Pause für uns
  delay(1000);

  // Hier wird das JSON-Objekt erstellt
  DynamicJsonDocument configJSON(1024);
  
  configJSON["name"] = "Lampe";
  configJSON["numLEDs"] = 75;
  
  configJSON["endpoints"][0]["switchOn"] = "/api/lamp/switchOn";
  configJSON["endpoints"][0]["desc"] = "Turn the lights on!";
  
  configJSON["endpoints"][1]["switchOff"] = "/api/lamp/switchOff";
  configJSON["endpoints"][1]["desc"] = "Turn the lights off!";
  
  // String array[] welches unserer Werte enthält
  String animations[] = {"demo", "fire", "ocean", "fade"};

  // for-Schleife, die das Array 'animations[]' durchläuft
  for (byte i = 0; i < (sizeof(animations)/sizeof(animations[0])); i++) {
    // animations[i] enthält den aktuellen Wert vom Array und wird per 'add()' zum Objekt hinzugefügt
    configJSON["animations"].add(animations[i]);
  }
  
  // Hier wird ein Objekt 'ledSettings' innerhalb von dem Objekt 'configJSON' erstellt 
  configJSON["ledSettings"]["chip"] = "WS2812B";
  configJSON["ledSettings"]["colorOrder"] = "GRB";
  configJSON["ledSettings"]["framerate"] = "60";
  configJSON["ledSettings"]["brightness"] = "100";

  serializeJson(configJSON, Serial);
} 
```

------

Die Ausgabe des Objektes erscheint jetzt nur im seriellen Monitor. Grund dafür ist die folgende Zeile:

```cpp
serializeJson(configJSON, Serial);
```

Um das Objekt in einer Variable zu speichern, kann man folgendes tun:

```cpp
// Varbiable anlegen
String meineJson;
// configJSON in meineJSON speichern
serializeJson(configJSON, meineJson);

// meineJSON auf dem seriellen Monitor ausgeben
Serial.println(meineJson);
```

Die Variable `meineJson` kann jetzt verwendet werden, um das Objekt in einer Datei zu speichern. Dazu kann ich dir [mein Tutorial zum SPIFFS-Dateisystem auf dem ESP8266](https://web.archive.org/web/20230602014446/https://makesmart.net/esp8266-spiffs-dateisystem/) empfehlen. Außerdem könnte es so auch 1:1 an eine API gesendet werden, über die die Daten dann auf eine andere Art und Weise weiterverarbeitet werden können.

So viel mal zum erstellen von JSON-Dateien und Objekten innerhalb der Arduino IDE. Wie sieht es aber mit dem Auslesen aus?

##  Werte aus einem JSON Objekt auslesen

Das Objekt wurde erstellt, die Konfiguration wurde gespeichert und wir sind bereit damit zu arbeiten. Aber wie komme ich jetzt an die Werte einer JSON-Datei? Zuerst möchte ich erwähnen, dass es hier jetzt keinen Unterschied macht, ob man das Objekt selbst erstellt hat, oder ob man es von einer API verarbeitet. JSON ist JSON und die Anwendung bleibt die gleiche, egal woher das JSON kommt.

Gehen wir einfach mal davon aus, dass sich das JSON-Objekt in der Variable `meineJson` befindet.
Diese Variable enthält das JSON Objekt ausgelesen via SPIFFS, API oder was auch immer als `String`.

```cpp
String meineJson = "{....}";
```

Um an die Werte zu kommen starten wir mit diesem Snippet:

```cpp
String meineJson = "{....}";

DynamicJsonDocument config(1024);
DeserializationError error = deserializeJson(config, meineJson);
if (error)
  return;
```

Für das JSON `meineJson` wird im ersten Schritt ein neuer JSON Buffer angelegt: `DynamicJsonDocument config(1024);`.

Ausgelesen bzw. deserialisiert wird das Objekt dann final mit `deserializeJson(config, meineJson);` wobei `meineJson` unser JSON-Objekt als String enthält und `config` die Bezeichnung unserers gerade erstellen Buffers ist.

------

Die Values der einzelnen Keys können wir dann ziemlich einfach in Variablen speichern:

```cpp
// Diese Variabe enthält ein JSON-Objekt
String meineJson = "{....}";

// Hier wird ein neuer Buffer erzeugt, mit dem wir an die Werte kommen
DynamicJsonDocument config(1024);
DeserializationError error = deserializeJson(config, meineJson);
if (error)
  return;

// Hier lesen wir die Werte über den Buffer aus
String name = config["name"];
Serial.println(name);
```

Output:

```console
Lampe
```

------

Tatsächlich funktioniert es fast 1:1 wie beim Speichern eines Objektes. Also sollte auch das Auslesen von Objekten oder Werten einzelner Keys im Objekt kein Problem sein. Ich möchte jetzt an die Einstellung der Helligkeit kommen:

```cpp
int brightness = config["ledSettings"]["brightness"];
Serial.println(brightness);
```

Output:

```console
100
```

------

Ihr seht: es ist gar nicht kompliziert auch auf einem Mikrocontroller den Komfort von JSON-Objekten zu nutzen. So wird die Kommunikaton via API wesentlich leichter und auch die Einrichtung einer Konfiruation mittels Spiffs ist in Zukunft kein Problem mehr.

Das war nur ein kleiner Einblick in die sehr umfangreiche Library [ArduinoJSON](https://web.archive.org/web/20230602014446/https://arduinojson.org/). Es gibt noch viel mehr Möglichkeiten das ganze umzusetzen. Außerdem gibt es noch eine ganze Reihe an nützlichen Funktionen die ebenfalls in dieser Library enthalten sind. Aber das würde den Rahmen hier sprengen: Außerdem gibt es ja genau für sowas die [Dokumentation](https://web.archive.org/web/20230602014446/https://arduinojson.org/v6/doc/).

\#API #Arduino-IDE #Arduino-JSON #D1-Mini #ESP8266 #JSON #SPIFFS