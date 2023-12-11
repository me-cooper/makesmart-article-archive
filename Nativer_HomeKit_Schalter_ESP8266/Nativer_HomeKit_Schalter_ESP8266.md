# ESP8266 D1 Mini Relais als HomeKit Schalter ohne Bridge - Tutorial

Wenn es bisher darum ging den [ESP8266 D1 Mini](https://web.archive.org/web/20220627125251/https://makesmart.shop/loadBasket/fAK_Si9oESm) in HomeKit einzubinden, bin ich immer den Weg über meine Homebridge auf dem Raspberry Pi gegangen. In der letzten Zeit hat sich da sehr viel getan und mittlerweile gibt es nicht nur eine Lösung das ganze auch ohne Bridge zu erreichen. Wir werden heute einen D1 Mini ohne Bridge oder sonstiges Zubehör direkt und nativ in HomeKit einbinden. Es handelt sich dabei erstmal nur um einen einfachen Schalter der ein Relaismodul betätigt.

------

###  HOMEKIT-ESP8266 - ARDUINO IDE BIBLIOTHEK HINZUFÜGEN

Bevor wir damit beginnen unseren eigenen HomeKit-Schalter einzubinden, muss eine Bibliothek in der Arduino IDE installiert werden. Das geht über den Bibliotheksverwalter. Ihr erreicht ihn über das Menü der Arduino IDE. Die Bibliothek heißt HomeKit-ESP8266 und kann über die Suche des Bibliotheksverwalters gefunden werden.

```
Arduino IDE
└───Werkzeuge
    └───Bibliotheken verwalten...
```

[![HomeKit-ESP8266 - Arduino IDE Bibliothek installieren](https://web.archive.org/web/20220627125251im_/https://my.makesmart.net/assets/uploads/files/1598177591980-homekit-esp8266-bibliothek-installieren.png)](https://web.archive.org/web/20220627125251mp_/https://my.makesmart.net/assets/uploads/files/1598177591980-homekit-esp8266-bibliothek-installieren.png)

------

###  HOMEKIT-ESP8266 - DER CODE

Um den ESP8266 in HomeKit einzubinden, ist mehr als nur eine Datei notwendig. Ihr könnt dem Tutorial weiter folgen und die Dateien selber anlegen, oder die fertigen Dateien herunterladen: [makesmart-esp8266-homekit-relais.zip](https://web.archive.org/web/20220627125251/https://my.makesmart.net/assets/uploads/files/1598180988789-makesmart-esp8266-homekit-relais.zip)

------

Öffnet die Arduino IDE und speichert das Projekt an einem beliebigen Ort. Nachdem das getan ist, müsst ihr zwei weitere Dateien anlegen. Das über die Tastenkombination `Strg + Umschalt + N` oder über den Klick auf den Pfeil in der rechten oberen Ecke.

[![Arduino IDE neue Datei anlegen](https://web.archive.org/web/20220627125251im_/https://my.makesmart.net/assets/uploads/files/1598178175480-arduino_ide_neue_datei_anlegen.png)](https://web.archive.org/web/20220627125251mp_/https://my.makesmart.net/assets/uploads/files/1598178175480-arduino_ide_neue_datei_anlegen.png)

Nennt die erste Datei `my_accessory.c` und die zweite Datei `wifi_info.h`.

------

Jetzt kommen wir zum eigentlichen Code. Den Code könnt ihr 1:1 für die jeweilige Datei kopieren, einfügen und speichern.

####  1. Datei: ***mein_schalter.ino***

```cpp
#include <Arduino.h>
#include <arduino_homekit_server.h>
#include "wifi_info.h"

#define LOG_D(fmt, ...)   printf_P(PSTR(fmt "\n") , ##__VA_ARGS__);

void setup() {
  Serial.begin(115200);
  wifi_connect();
  homekit_storage_reset();
  my_homekit_setup();
}

void loop() {
  my_homekit_loop();
  delay(10);
}

//==============================
// HomeKit setup and loop
//==============================

// Zugriff auf die Definitionen des Accessories in my_accessory.c
extern "C" homekit_server_config_t config;
extern "C" homekit_characteristic_t cha_switch_on;

static uint32_t next_heap_millis = 0;

#define PIN_SWITCH 15

// Diese Funktion wird aufgerufen, wenn der Schalter in HomeKit betätigt wird
void cha_switch_on_setter(const homekit_value_t value) {
  bool on = value.bool_value;
  cha_switch_on.value.bool_value = on;
  LOG_D("Switch: %s", on ? "ON" : "OFF");
  digitalWrite(PIN_SWITCH, on ? HIGH : LOW);
}

void my_homekit_setup() {

  
  pinMode(PIN_SWITCH, OUTPUT);
  digitalWrite(PIN_SWITCH, HIGH);
  cha_switch_on.setter = cha_switch_on_setter;
  arduino_homekit_setup(&config);



  // Das hier kann verwendet werden, um den Status an HomeKit zurückzusenden,
  // falls ein zusätzlicher pysischer Button o.ä verwendet wird.
  
  // bool switch_is_on = true/false;
  // cha_switch_on.value.bool_value = switch_is_on;
  // homekit_characteristic_notify(&cha_switch_on, cha_switch_on.value);
}



void my_homekit_loop() {
  arduino_homekit_loop();
  const uint32_t t = millis();
  if (t > next_heap_millis) {
    // heap-Info alle 30 Sekunden im seriellen Monitor ausgeben
    next_heap_millis = t + 30 * 1000;
    LOG_D("Free heap: %d, HomeKit clients: %d",
        ESP.getFreeHeap(), arduino_homekit_connected_clients_count());

  }
}
```

####  2. Datei: ***my_accessory.c***

```cpp
#include <homekit/homekit.h>
#include <homekit/characteristics.h>

void my_accessory_identify(homekit_value_t _value) {
  printf("accessory identify\n");
}

// Switch (HAP section 8.38)
// required: ON
// optional: NAME

// format: bool; HAP section 9.70; write the .setter function to get the switch-event sent from iOS Home APP.
homekit_characteristic_t cha_switch_on = HOMEKIT_CHARACTERISTIC_(ON, true);

// max. Länge 64
homekit_characteristic_t cha_name = HOMEKIT_CHARACTERISTIC_(NAME, "Schalter");

homekit_accessory_t *accessories[] = {
    HOMEKIT_ACCESSORY(.id=1, .category=homekit_accessory_category_switch, .services=(homekit_service_t*[]) {
        HOMEKIT_SERVICE(ACCESSORY_INFORMATION, .characteristics=(homekit_characteristic_t*[]) {
            HOMEKIT_CHARACTERISTIC(NAME, "Schalter"),
            HOMEKIT_CHARACTERISTIC(MANUFACTURER, "makesmart Community"),
            HOMEKIT_CHARACTERISTIC(SERIAL_NUMBER, "1234567"),
            HOMEKIT_CHARACTERISTIC(MODEL, "ESP8266 D1 Mini"),
            HOMEKIT_CHARACTERISTIC(FIRMWARE_REVISION, "1.0"),
            HOMEKIT_CHARACTERISTIC(IDENTIFY, my_accessory_identify),
            NULL
        }),
    HOMEKIT_SERVICE(SWITCH, .primary=true, .characteristics=(homekit_characteristic_t*[]){
      &cha_switch_on,
      &cha_name,
      NULL
    }),
        NULL
    }),
    NULL
};

homekit_server_config_t config = {
    .accessories = accessories,
    .password = "123-45-678"
};
```

####  3. Datei: ***wifi_info.h***

Im folgenden Code müsst ihr eure WLAN-Zugangsdaten anpassen. Es sind die folgenden beiden Variablen:

```cpp
const char *ssid = "WIFI-SSID";
const char *password = "WIFI-PASSWD";
#ifndef WIFI_INFO_H_
#define WIFI_INFO_H_

#if defined(ESP8266)
#include <ESP8266WiFi.h>
#elif defined(ESP32)
#include <WiFi.h>
#endif

const char *ssid = "WIFI-SSID";
const char *password = "WIFI-PASSWD";

void wifi_connect() {
  WiFi.persistent(false);
  WiFi.mode(WIFI_STA);
  WiFi.setAutoReconnect(true);
  WiFi.begin(ssid, password);
  Serial.println("WiFi connecting...");
  while (!WiFi.isConnected()) {
    delay(100);
    Serial.print(".");
  }
  Serial.print("\n");
  Serial.printf("WiFi connected, IP: %s\n", WiFi.localIP().toString().c_str());
}

#endif
```

Nachdem alle drei Dateien gespeichert wurden, kann das Programm auf den ESP8266 D1 Mini geladen werden. Geht dazu wieder in den Reiter “mein_schalter” und ladet das Programm auf den ESP.

------

###  HOMEKIT-ESP8266 - DIE EINBINDUNG IN HOMEKIT

Die Einbindung des DIY ESP8266 Relais in HomeKit ist ziemlich einfach.Öffnet dazu die Home-App und fügt ein neues Gerät hinzu. Der Schalter sollte in den Geräten in der Nähe auftauchen. Per Tippen auf den Schalter kann dieser hinzugefügt werden. Zum Hinzufügen muss ein Code eingegeben werden, der Code zum Koppeln lautet `123-45-678`.

[![HomeKit neues Gerät hinzufügen](https://web.archive.org/web/20220627125251im_/https://my.makesmart.net/assets/uploads/files/1598180004543-esp8266_homekit_pairing.jpg)](https://web.archive.org/web/20220627125251mp_/https://my.makesmart.net/assets/uploads/files/1598180004543-esp8266_homekit_pairing.jpg)

------

###  HOMEKIT-ESP8266 - DIE VERDRAHTUNG

Der ESP wurde erfolgreich in HomeKit eingebunden und kann von nun an für Automationen und Szenen verwendet werden. Aber eins fehlt noch: Das Relais. Das ist wohl das geringste Übel dieses Tutorials - hier der Schaltplan:

[![ESP8266 D1 Mini Relaiy KY-019 Schaltplan](https://web.archive.org/web/20220627125251im_/https://my.makesmart.net/assets/uploads/files/1598181409142-esp8266_homekit_relais_schaltplan.png)](https://web.archive.org/web/20220627125251mp_/https://my.makesmart.net/assets/uploads/files/1598181409142-esp8266_homekit_relais_schaltplan.png)

| KY-19 Relaismodul | ESP8266 D1 Mini |
| :---------------- | :-------------- |
| S                 | D8              |
| +                 | 5V              |
| -                 | G               |

------

Und fertig ist das HomeKit-Relais mit dem ESP8266 D1 Mini. Nativ. Ohne Bridge. Superschnell und super einfach. ![🙂](https://web.archive.org/web/20220627125251im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

Erfolgreich getestet und im Einsatz hab ich es mit [diesen Produkten](https://web.archive.org/web/20220627125251/https://makesmart.shop/loadBasket/fAK_Si9oESm) vom [makesmart.shop](https://web.archive.org/web/20220627125251/http://makesmart.shop/).

------

Solltest du mit dem Programmieren / Debugging fertig sein, ist noch ein letzter Schritt nötig.

```cpp
homekit_storage_reset();
```

Diese Zeile muss im `setup()` auskommentiert werden. Sonst muss der ESP8266 D1 Mini jedes mal aufs neue mit HomeKit gepaired werden. Wenn die Zeile auskommentiert oder entfernt ist, wird der D1 Mini auch nach dem Abstecken wieder als der gleiche Schalter erkannt und kann eingesetzt werden.

------

###  MODIFIKATIONEN AUS DER COMMUNITY

**Kann ich mehr als nur ein Relais steuern?**
[@Menuhin](https://web.archive.org/web/20220627125251/https://my.makesmart.net/uid/82) hat sich die Arbeit gemacht das Programm anzupassen
[Tutorial: Mehrere Relais an einem D1 Mini](https://web.archive.org/web/20220627125251/https://my.makesmart.net/topic/83/esp8266-d1-mini-relais-als-homekit-schalter-ohne-bridge-tutorial/15)

**Kann ich beim Betätigen des Schalter in HK einen zusätzlichen GPIO kurz triggern lassen?**
Ja, [hier wird beim Betätigen ein zusätzlicher GPIO getriggert](https://web.archive.org/web/20220627125251/https://my.makesmart.net/topic/83/esp8266-d1-mini-relais-als-homekit-schalter-ohne-bridge-tutorial/24).

------

Suchst du gezielt nach weiteren Tutorials mit dieser Library? Dann schau doch mal in der Community unter dem Tag [homekit-esp8266](https://web.archive.org/web/20220627125251/https://my.makesmart.net/tags/homekit-esp8266) vorbei.

*Disclaimer: Ich bin gerade frisch über diese Libary gestoßen und sehe darin (für mich persönlich) sehr viel potenzial. Ich werde vermutlich noch weitere Tutorials über die Libary und das Thema allgemein veröffentlichen. Es soll nicht bei diesem einen Schalter bleiben. Thermostate, Sensoren und so weiter sind ebenfalls möglich.*

\#D1-Mini #ESP8266 #HomeKit #Smarthome