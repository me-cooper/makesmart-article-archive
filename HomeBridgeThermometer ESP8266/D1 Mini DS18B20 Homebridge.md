# HomeKit Thermometer DIY mit dem ESP8266 D1 Mini & GET-Request - Tutorial

In diesem Beitrag werden wir ein Smarthome-Thermometer basteln. Als Temperaturfühler dient ein DS18B20. Das Gehirn des Projektes ist ein ESP8266 D1 Mini. Inspiriert wurde ich durch den Beitrag von [@Gauweiler42](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/uid/14) in der Community. RE: [Problem mit OneWire DS18B20](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/topic/73/probleme-mit-onewire-ds18b20)

### Funktionbeschreibung

Die Temperatur wird in einem beliebig einstellbaren Intervall abgefragt. Und im seriellen Monitor ausgegeben. Danach findet die Übergabe der Temperatur an die eigene Smarthome-Umgebung statt. Das kann einerseits über MQTT geschehen, über eine GET-Request oder jede beliebige andere Art von Datenübertragung. In diesem Tutorial behandle ich nur die Übertragung via GET-Request.

### Verdrahtung

Die erste Hürde ist das Auslesen der Temperatur am D1 Mini. Hierzu nutze ich wie bereits erwähnt einen DS18B20 Temperaturfühler. Den Fühler gibt es in verschiedenen Ausführungen. Mit Kabel, ohne Kabel, als Stab, als Knopf, … es gibt unterschiedliche Bauformen - dabei wird aber jedes Mal auf den OneWire-Bus zurückgegriffen. Der DS18B20 hat drei Adern. VCC - GND und DQ - wobei DQ die Datenleitung darstellt.

| Beschreibung            | Wert            |
| ----------------------- | --------------- |
| Stromversorgungsbereich | 3,0V bis 5,5V   |
| Messbereich             | -10°C bis +85°C |

Die Verdrahtung für dieses Projekt sieht wie folgt aus:

| D1 Mini | DS18B20 |
| ------- | ------- |
| 5V      | VCC     |
| G       | GND     |
| D4      | DQ      |

![d1_mini_ds18b20.png](https://web.archive.org/web/20220118172814/https://my.makesmart.net/assets/uploads/files/1591952268342-d1_mini_ds18b20.png)

Die Farben der Drähte entsprechen den Farben der Drähte in der Realität. Bei einem DS18B20 in Form eines Fühlers ist die Verdrahtung identisch.

Es gibt neben Fühlern und *Knöpfen* auch sog. Shields als steckbare oder verlötbare Aufsätze für den D1 Mini. Bei einem Shield spart man sich die Verdrahtung, weil man das Shield einfach auf den D1 Mini stecken kann. So bekommt man ein noch kompakteres Thermometer.

Hier ist ein [Warenkorb](https://web.archive.org/web/20220118172814mp_/https://makesmart.shop/loadBasket/kamjqj8gy1R), mit den Produkten die ihr für euer Thermometer verwenden könnt.

### Basic-Code

Folgender Code gibt die Temperatur jede Sekunde über den seriellen Monitor aus:

```c++
#include <OneWire.h>
#include <DallasTemperature.h>
#define ONE_WIRE_BUS 2

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);

//Messintervall in Sekunden
int wait = 5;

//Temperatur
float temperature;


void push(){

  //Hier soll der Wert an die Smarthome-Umgebung übertragen werden

}

void setup(){
  
  Serial.begin(115200);
  wait = wait * 1000;
  sensors.begin();

}

void loop(){

  sensors.requestTemperatures();
  Serial.print(sensors.getTempCByIndex(0));
  Serial.println(" °C");
  temperature = sensors.getTempCByIndex(0);
  push();
  delay(wait);

}
```

Quelle: [makesmart.net](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/topic/73/probleme-mit-onewire-ds18b20/3) [@dieterh](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/uid/27)

Die Ausgabe sieht folgendermaßen aus:

```
22.75 °C
22.75 °C
22.75 °C
22.75 °C
```

Mit der Variable `wait` kann der Messintervall in Sekunden eingestelt werden. Der Sensor gibt den Messwert alle 5 Sekunden im seriellen Monitor zurück.

```c++
//Messintervall in Sekunden
int wait = 5;
```

Dieser Code kann bereits für das eigene Projekt verwendet werden.

------

## Einbidung in das Smarthome - Homebridge

Folgenden Codeschnipsel dient als Ausgangslage zur Einbindung in das eigene Smarthome-System. Ich habe mich für die Variante via [GET-Request](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/topic/67/esp8266-http-get-request) entschieden. Als Smarthome-Plattform nutze ich Homebridge.

### GET-Request

Als Plugin für Homebridge könnte das Plugin [homebridge-http-webhooks](https://web.archive.org/web/20220118172814mp_/https://www.npmjs.com/package/homebridge-http-webhooks) verwendet werden. Die Konfiguration für das Plugin sieht wie folgt aus:

```json
{
	"platform": "HttpWebHooks",
 	"webhook_port": "51828",
 	"sensors": [
  	{
   		"id": "temperatur",
    	"name": "Sensor name 5",
   		"type": "temperature"
  		}
 	]
}
```

Der Code auf dem D1 Mini muss dementsprechend mit einer GET-Request ausgestattet werden:

```c++
/*
    WLAN TERMOMETER GET-Request
    GET-Request an einen HTTP-Server
    Created by cooper, 2020
    makesmart.net
*/

#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <OneWire.h>
#include <DallasTemperature.h>
#define ONE_WIRE_BUS 2

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);

HTTPClient sender;

// WLAN-Daten
const char* ssid = "WLAN_SSID";
const char* password = "WLAN_PASSWD";

//Messintervall in Sekunden
int wait = 5;

//Temperatur
float temperature;


void push(){

  //Hier wird der Wert an die Smarthome-Umgebung übertragen
  
  if (sender.begin("http://makesmart-server:51828/?accessoryId=temperatur&value=" + String(temperature))){

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


void setup() {
  Serial.begin(115200);
  
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(200);
    Serial.print(".");
  }

  Serial.println("Verbunden!");
  wait = wait * 1000;
  sensors.begin();
  
}


void loop() {
  
  sensors.requestTemperatures();
  Serial.print(sensors.getTempCByIndex(0));
  Serial.println(" °C");
  
  temperature = sensors.getTempCByIndex(0);
  push();
  delay(wait);

}
```

Und schon hat man die aktuelle Temperatur in HomeKit eingebunden und kann sie für Automationen oder Benachrichtigungen verwenden. Have Fun!

![76299308-0FFE-4D5C-804D-4EE2A9AFA138.jpeg](https://web.archive.org/web/20220118172814/https://my.makesmart.net/assets/uploads/files/1591952829798-76299308-0ffe-4d5c-804d-4ee2a9afa138-resized.jpeg)

![Bildschirmfoto 2020-06-12 um 10.49.04.png](https://web.archive.org/web/20220118172814/https://my.makesmart.net/assets/uploads/files/1591952421511-bildschirmfoto-2020-06-12-um-10.49.04.png)

### Nützliche Links

[ESP8266 HTTP-GET Request](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/topic/67/esp8266-http-get-request)
[Tutorial: ESP8266 Deep-Sleep](https://web.archive.org/web/20220118172814mp_/https://my.makesmart.net/topic/62/tutorial-esp8266-deep-sleep)