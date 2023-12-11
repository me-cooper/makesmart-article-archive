# ESP8266 D1 Mini WLAN-Relais mit API

Der ESP8266 D1 Mini erfreut sich vielerlei Anwendungsmöglichkeiten. Viele davon basieren auf dem HTTP-Protokoll. In diesem Artikel bekommt ihr eine kurze Anleitung für ein einfaches WLAN Relais mithilfe eines ESP8266 D1 Mini, einem Relais-Modul und dem HTTP-Protokoll. Durch die verwendete HTTP-API lässt sich das Relais am ESP8266 D1 Mini nicht nur manuell über den Browser steuern, sondern auch in beliebig vielen anderen Projekten ganz leicht einbinden.

makesmart: ESP8266 D1 Mini programmieren - Start mit der Arduino IDE

ESP8266 D1 Mini programmieren mit der kostenlosen Arduino IDE. Einfach und in wenigen Schritten zum ersten Programm. Ganz ohne Vorwissen.

![img](https://web.archive.org/web/20210921192841im_/https://makesmart.net/favicon.png)Pascalmakesmart

![img](https://web.archive.org/web/20210921192841im_/https://makesmart.net/content/images/2020/02/blink_beispiel-1.png)

### Beschreibung

Mit dieser Anleitung erhaltet ihr in wenigen Minuten ein durch den ESP8266 D1 Mini WLAN-fähiges Relais für verschiedene Anwendungen. Das Relais kann über eine HTTP-API angesteuert werden. Die Funktionen sind folgende

| API-Link                       | Beschreibung                                        |
| ------------------------------ | --------------------------------------------------- |
| http://192.168.192.1/relay_on  | Schlatet das Relais an.                             |
| http://192.168.192.1/relay_off | Schlatet das Relais aus.                            |
| http://192.168.192.1/state     | Gibt den Status als "0" oder "1" im Browser zurück. |

Die HTTP-API kann verwendet  werden, um das Relais mithilfe anderer Anwendungen und Plattformen steuern zu können. Das kann zum einen ein anderer ESP8266 sein, der als Fernbedienung fungiert. Die Plattform Homebridge um das Relais in das Smarthome einzubinden. Oder auch jeder andere Dienst, der HTTP-Requests unterstützt.

Möchtest du direkt dein eigenes HTTP WLAN Relais bauen? Ich hab dir [hier](https://web.archive.org/web/20210921192841/https://makesmart.shop/loadBasket/fAK_Si9oESm) einen Warenkorb zusammengestellt mit den Produkten die für dieses Tutorial verwendet wurden.

Anzeige - [makesmart.shop](https://web.archive.org/web/20211216162820/https://makesmart.shop)

[![Relais KY-019](https://web.archive.org/web/20210921192841im_/https://makesmart.shop/media/53/3c/2e/1597161780/relais_jqc3f_5vdc-c_front.png)](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/Relais-KY-019)

[Relais KY-019](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/Relais-KY-019)

Stückpreis

 1,99 €

 

[![D1 Mini Vorderseite](https://web.archive.org/web/20210921192841im_/https://makesmart.shop/media/0e/fb/1d/1597161715/d1_mini_top_2.png)](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/ESP8266-D1-Mini)

[ESP8266 D1 Mini](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/ESP8266-D1-Mini)

Stückpreis

 4,59 €

 

[![Jumper Kabel Female](https://web.archive.org/web/20210921192841im_/https://makesmart.shop/media/c5/19/ab/1597162043/jumper_kabel_female.jpg)](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/Jumper-Kabel-Female-20cm)

[Jumper Kabel Female 20cm](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/Jumper-Kabel-Female-20cm)

Stückpreis

 1,99 €

[**zusammen nur 12,52 €** - zum Warenkorb](https://web.archive.org/web/20210921192841mp_/https://makesmart.shop/loadBasket/fAK_Si9oESm)

## Programmcode

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
ESP8266WebServer server(80);
const char* ssid = "WLAN_SSID";
const char* password = "WLAN_PASSWORD";
//Signal-Pin des Relais: D1
int relay_pin = 5;
void setup()
{
  pinMode(relay_pin, OUTPUT);
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
  server.onNotFound([](){
    server.send(404, "text/plain", "Link wurde nicht gefunden!");  
  });
  server.on("/", []() {
    server.send(200, "text/plain", "Startseite");
  });
  server.on("/relay_on", []() {
    server.send(200, "text/plain", "Relais wurde angeschaltet.");
    relay_on();
  });
  server.on("/relay_off", []() {
    server.send(200, "text/plain", "Relais wurde ausgeschalten.");
    relay_off();
  });
  server.on("/state", []() {
    server.send(200, "text/plain", String(digitalRead(relay_pin)));
  });
  server.begin();
  Serial.println("Webserver gestartet.");
}
void loop() {
  server.handleClient();  
}
void relay_on(){
  digitalWrite(relay_pin, HIGH);  
}
void relay_off(){
  digitalWrite(relay_pin, LOW);    
}
```

Copy

Die beiden folgenden Zeilen müssen angepasst werden, damit sich der ESP8266 mit eurem WLAN-Netzwerk verbinden kann.

```arduino
const char* ssid = "WLAN_SSID";
const char* password = "WLAN_PASSWORD";
```

Copy

## Schaltplan

![ESP8266 D1 Mini: WLAN-Relais mit HTTP-API Schaltplan](https://web.archive.org/web/20210921192841im_/https://makesmart.net/content/images/2020/02/esp8266_d1_mini_relais_schematic_01.png)ESP8266 D1 Mini: WLAN-Relais mit HTTP-API Schaltplan

| ESP8266 D1 Mini | Relais-Modul      |
| --------------- | ----------------- |
| D1              | S - Signalleitung |
| G               | - Leitung         |
| 5V              | + Leitung         |

Wie kann ich ein 2. Relais mit dem D1 Mini anteuern?

Hey, ich versuche aktuell 2 Relais mit dem D1 Mini zu schalten. Der 1. funktioniert auch ohne probleme. Jetzt ist aber noch die frage wie ich den 2. unter einer anderen Port dazu bekomme. Hier der aktueller Code: #include #include #include ESP8266WebSe...

![img](https://web.archive.org/web/20210921192841im_/https://my.makesmart.net/assets/uploads/system/touchicon-192.png)justtn_makesmart

![img](https://web.archive.org/web/20210921192841im_/https://my.makesmart.net/assets/uploads/category/category-10.png)

Frage von justtn_ - Wie kann ich ein zweites Relais mit dem D1 Mini ansteuern?

Interesse daran, das Relais in dein Smarthome einzubinden? Dann schau dir doch mal diesen Artikel an:

makesmart: ESP8266 D1 Mini Relais im Smarthome - Tutorial

Wie cool wäre es, ein Relais am ESP8266 D1 Mini direkt ins SmartHome einzubinden? In diesem Artikel erfährst du, wie du ein ESP8266 D1 Mini Relais in dein SmartHome einbinden kannst und somit alle Funktionen wie Automation und Sprachsteuerung nutzen kannst.

![img](https://web.archive.org/web/20210921192841im_/https://makesmart.net/favicon.png)Pascalmakesmart

![img](https://web.archive.org/web/20210921192841im_/https://makesmart.net/content/images/2020/02/esp8266_relais_http_switch.png)