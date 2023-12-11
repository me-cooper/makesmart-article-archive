# Der DS18B20 Temperaturfühler am ESP8266 D1 Mini

Wenn es darum geht an einem Arduino, ESP8266, Raspberry Pi oder D1 Mini die Temperatur zu messen, wird oft ein DS18B20 Sensor verwendet. Die Vorteile liegen klar auf der Hand. Der Sensor ist durch seine kompakten Abmaße sehr gut geeignet, auf kleinem Raum eine zuverlässige Temperaturmessung vorzunehmen. Der DS18B20 ist sehr vielseitig einsetzbar, da es ihn in verschiedenen Ausführungen gibt. Gängig sind Shields für spezifische Boards - genauso gibt es den DS18B20 aber auch als Temperaturfühler, um die Temperatur abseits des Prozessors erfassen zu können, oder als Knopf um ihn auf Platinen zu verwenden.

Egal ob Fühler, Knopf oder Shield - das Auslesen des DS18B20 funktioniert immer auf die gleiche Art und Weise.

------

### Arduino IDE - Libaries installieren

Bevor wir über den OneWire-Bus die Temperatur des Sensors auslesen können, müssen erstmal die passenden Libaries installiert werden. Libaries werden immer gleich installiert. Und zwar über den Bibliotheksverwalter.

```
Arduino IDE
└───Werkzeuge
    └───Bibliotheken verwalten...
```

Für das Auslesen der Temperatur brauchen wir zwei Libaries, einmal die Libary `OneWire` und einmal die Libary `DallasTemperature`. Ihr findet die Libaries, wenn ihr genau nach den beiden Begriffen innerhalb des Bibliothekverwalters sucht.

#### OneWire Libary

![Arduino IDE OneWire Libary makesmart](https://web.archive.org/web/20230330040334im_/https://my.makesmart.net/assets/uploads/files/1602410979654-arduino_ide_one_wire_libary_makesmart.png)

#### DallasTemperature Libary

![Arduino IDE DallasTemperature Libary makesmart](https://web.archive.org/web/20230330040334im_/https://my.makesmart.net/assets/uploads/files/1602411039427-arduino_ide_dallastemperature_libary_makesmart.png)

### Das DS18B20 Shield ESP8266 D1 Mini

Die wohl einfachste Möglichkeit einen DS18B20 am D1 Mini zu verwenden, ist das DS18B20 Shield. Das Shield lässt sich einfach auf den D1 Mini stecken - es ist also eigentlich keinerlei Verdrahtung notwendig. Das DS18B20 Shield findet ihr [hier](https://web.archive.org/web/20230330040334/https://makesmart.shop/loadBasket/kamjqj8gy1R). Ein Schaltplan zum Verdrahten des DS18B20 am D1 Mini ohne Shield findet ihr weiter unten nach dem Code-Beispiel.

Anzeige - [makesmart.shop](https://web.archive.org/web/20230313074226/https://makesmart.shop)

[![D1 Mini Vorderseite](https://web.archive.org/web/20230330040334im_/https://makesmart.shop/media/0e/fb/1d/1597161715/d1_mini_top_2.png)](https://web.archive.org/web/20230330040334mp_/https://makesmart.shop/ESP8266-D1-Mini)

[ESP8266 D1 Mini](https://web.archive.org/web/20230330040334mp_/https://makesmart.shop/ESP8266-D1-Mini)

Stückpreis

 5,79 €

 

[![img](https://web.archive.org/web/20230330040334im_/https://makesmart.shop/media/9a/e7/32/1601910162/ds18b20_d1_mini_shield_1.png)](https://web.archive.org/web/20230330040334mp_/https://makesmart.shop/DS18B20-Shield-fuer-D1-Mini-Temperatur-Sensor)

[DS18B20 Shield für D1 Mini - Temperatur ...](https://web.archive.org/web/20230330040334mp_/https://makesmart.shop/DS18B20-Shield-fuer-D1-Mini-Temperatur-Sensor)

Stückpreis

 4,99 €

[**zusammen nur 14,73 €** - zum Warenkorb](https://web.archive.org/web/20230330040334mp_/https://makesmart.shop/loadBasket/kamjqj8gy1R)

### Der Code

Der Code für das Auslesen der Temperatur mit dem DS18B20 Shield sieht folgendermaßen aus:

```arduino
/*
    DS18B20 Basic Code
    Temperatur auslesen mit einem DS18B20 Temperaturfühlers
    Created by cooper, 2020
    my.makesmart.net/user/cooper
*/

#include <OneWire.h>
#include <DallasTemperature.h>

// Der PIN D2 (GPIO 4) wird als BUS-Pin verwendet
#define ONE_WIRE_BUS 4

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature DS18B20(&oneWire);

// In dieser Variable wird die Temperatur gespeichert
float temperature;


void setup(){
  Serial.begin(115200);

  // DS18B20 initialisieren
  DS18B20.begin();
}

void loop(){
  
  DS18B20.requestTemperatures();
  temperature = DS18B20.getTempCByIndex(0);

  // Ausgabe im seriellen Monitor
  Serial.println(String(temperature) + " °C");

  // 5 Sekunden warten
  delay(5000);

}
```

Copy

Das Ergebnis ist die aktuelle Temperatur im seriellen Monitor - und zwar alle 5 Sekunden.

```
12:03:46.730 -> 20.81 °C
12:03:52.493 -> 20.81 °C
12:03:58.270 -> 20.81 °C
12:04:04.070 -> 20.81 °C
```

------

### Die Verdrahtung des DS18B20 ohne Shield

Du hast kein Shield oder möchtest eine andere Bauart des DS18B20 verwenden? Dann hast du zwei Optionen.

### Pin D4 als Datenleitung

Die erste und einfachere Option ist die Verwendung des Pins `D4` als Datenleitung. Hier kannst du den DS18B20 direkt und ohne weitere Hardware anschließen um die Temperatur auszulesen.

![DS18B20 ESP8266 D1 Mini Schaltplan](https://web.archive.org/web/20230330040334im_/https://my.makesmart.net/assets/uploads/files/1591952268342-d1_mini_ds18b20.png)

Du kannst den oben genannten Code 1:1 übernehmen. Du musst lediglich die Definition des Datenpins ändern.

```arduino
// Der PIN D4 (GPIO 2) wird als BUS-Pin verwendet
#define ONE_WIRE_BUS 0
```

Copy

So erhältst du die gleiche Ausgabe im seriellen Monitor.

```
12:03:46.730 -> 20.81 °C
12:03:52.493 -> 20.81 °C
12:03:58.270 -> 20.81 °C
12:04:04.070 -> 20.81 °C
```

### Andere Pins als Datenleitung

Solltest du den Pin `D4` bereits in Verwendung haben, dann bleibt dir nun noch eine weitere Möglichkeit den DS18B20 zu verwenden. Dafür benötigst du aber einen Widerstand. Ich empfehle dir einen 2 - 5 kΩ Widerstand.

Der Widerstand muss zwischen der Datenleitung DQ (Gelb) und der positiven Leitung VCC (Rot) angebracht werden. Wenn du das getan hast, kannst du *fast* jeden Pin am D1 Mini zum Auslesen der Temperatur verwenden. Auch hier gilt:

Du musst lediglich wieder eine Zeile im Programm anpassen.

```ardino
// Der PIN D7 (GPIO 13) wird als BUS-Pin verwendet
#define ONE_WIRE_BUS 13
```

Copy

Als Pins für die Datenleitung eignen sich folgende Pins:

| D1   | D2   | D3   | D4   | D5   | D6   | D7   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 🗸    | 🗸    | 🗸    | 🗸    | 🗸    | 🗸    | 🗸    |

Ein Überblick der Pins und den dazugehörigen Pin-Definitionen findet ihr im [Datenblatt des D1 Minis](https://web.archive.org/web/20230330040334/https://makesmart.net/esp8266-d1-mini-datenblatt/).