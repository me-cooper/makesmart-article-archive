# Ultraschallsensoren HC-SR04 und JSN-SR04T am ESP8266 D1 Mini betreiben

Heute schauen wir uns an, wie man verschiedene Ultraschallsensoren an einem ESP8266 D1 Mini betreiben kann - Das wohl bekannteste Modul ist das **HC-SR04** was sich für vielerlei Projekte durchaus gut eignet. Ich persönlich bevorzuge aber den eleganteren **JSN-SR04T**, bei dem der Sender und Empfänger in einem Bauteil vereint sind.

Zusätzlich zum schlanken Sensor, hat man dabei auch noch ein Kabel in ausreichender Länge angebunden, um die Elektronik und den eigentlichen Sensor dezentral per Steckverfahren montieren zu können.

Der **JSN-SR04T** ist also nicht nur praktischer in der Anwendung, sondern auch sicherer im Betrieb mit Flüssigkeiten.

![HC-SR04-JSN-SR04T-vergleich.png](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/assets/uploads/files/1619019013668-hc-sr04-jsn-sr04t-vergleich.png)

[Die Produkte in diesem Tutorial habe ich in diesem Warenkorb hinterlegt.](https://web.archive.org/web/20230602010707/https://makesmart.shop/loadBasket/axfFKXmo1Vi)

------

Kommen wir kurz zum Funktionsprinzip dieser beiden Sensoren, das trotz unterschiedlicher Bauweise identisch ist. Durch Ultrashall-Wellen im Frequenzbereich von 40 kHz, die durch den Sender ausgestrahlt und den Empfänger ausgelesen werden, kann durch Berücksichtung der Zeit eine Entfernung berechnet werden.
Das geschieht mithilfe der Trigger-Pulsweite, die 10µs beträgt.

Auf Grundlager der berechneten Entfernung kann man verschiedene Projekte realisieren, das wären zum Beispiel eine Füllstandsanzeige für einen Öl- oder Wassertank oder einer Zysterne. Oder auch eine einfache Abstandsnessung, wie sie in Autos beim Einparken zum Einsatz kommt.

Auch diese Sensoren funktionieren nach dem selben Prinzip.

------

###  DIE VERDRAHTUNG

Die Verdrahtung sieht wie folgt aus, und ist für beide Sensor-Varianten identisch. ![🎉](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f389.png?v=chnl3kn5hqg)

![esp8266_d1_mini_HC-SR04_schaltplan.png](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/assets/uploads/files/1619018869203-esp8266_d1_mini_hc-sr04_schaltplan.png)

| D1 Mini | HC-SR04 / JSN-SR04T |
| :------ | :------------------ |
| 5V      | VCC                 |
| G       | GND                 |
| D0      | TRIG                |
| D1      | ECHO                |

------

Sollest du mehrere Sensoren an einem ESP8266 D1 Mini oder Arduino betreiben wollen, kannst du den **TRIG** Pin an allen Modulen teilen, nur der **ECHO**-Pin muss dann zum Auslesen je einen eigenen Eingang bekommen. So kannst du ohne Probleme mehrere Sensoren mit der minimalen Anzahl an Pins an einem Board verwenden. ![💪](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f4aa.png?v=chnl3kn5hqg)

------

###  DAS PROGRAMM

Das Programm für einen Sensor sieht wie folgt aus und passt zur eben genannten Verdrahtung und auch für beide Varianten des Moduls.

```c++
/*
  Ultraschall-Sensoren 
  HC-SR04 & JSN-SR04T
  ESP8266 D1 Mini
  Tutorial by cooper @ makesmart.net
  cc 2021
*/

// ESP8266 D1 Mini
#define ECHO 5    // D1
#define TRIG 16   // D0

long duration;  // Variable um die Zeit der Ultraschall-Wellen zu speichern
float distance; // Variable um die Entfernung zu berechnen

void setup() {
    
  Serial.begin(115200);   // Baudrate: 115200
  pinMode(TRIG, OUTPUT);  // TRIG-Pin: Output
  pinMode(ECHO, INPUT);   // ECHO-Pin: Input
  
}


void loop() {
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);

  // TRIG-Pin ist HIGH für 10 Microsekunden
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);
  
  // Liest das ECHO aus und speichert die Zeit von Senden / Empfangen in Microsekunden
  duration = pulseIn(ECHO, HIGH);

  
  // Berechnung der Entfernung
  // Da der Weg doppel ist: Hinweg - Rückweg, muss der Wert durch 2 geteilt werden
  distance = duration * 0.034 / 2;
  
  // Anzeige der Entfernung im seriellen Monitor
  Serial.print("Entfernung: " + String(distance) + "cm");
  
}
```

Die Ausgabe im seriellen Monitor sieht wie folgt aus - Die Differenz kommt daher, weil ich den Sensor während der Ausgabe vom Ziel entfernt habe.

```
Entfernung: 52.43cm
Entfernung: 52.55cm
Entfernung: 53.06cm
Entfernung: 52.80cm
Entfernung: 52.92cm
[...]
```

Trotz der bezeichnung *Ultraschall-Sensor* nehme ich während der Messung ein leichtes Summen wahr - wenn es außenrum still ist.

Ein Grund für mich, den Sensor nicht in unmittelbarere Nähe meines Ohres zu platzieren. Es ist also nicht zu empfehlen, mit dem Sensor den Abstand von Gesicht bis zum Fernseher zu messen. ![:heh:](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/customizations/02344941-c9a5-4a07-9960-141b30a66ac1-DFB6FF81-BD23-47A0-981A-DA48437A27E7.png?v=chnl3kn5hqg)

------

###  DIE ENTFERNUNG IST BERECHNET, WAS NUN?

Wenn man die Entfernung des Gegenstandes oder der Oberflächen erstmal erfasst hat, kann man nun weiter gehen und diverse andere Sachen berechnen. Dazu zählt, wie vorhin bereits erwähnt, zum Beispiel das Berechnen eines Füllstandes.

Dazu verwendet man das Gesamtvolumen des Behälters mit Abzug des Volumens das leer ist - mit der Hilfe des gemessenen Abstandes…
Aber das ist eher ein Thema der Mathematik und kein Thama der Inbetriebnahme dieser beiden Module. ![🙂](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

------

Wenn du mehr zum Thema #ESP8266, #Arduino-IDE und Co. erfahren möchstest, kannst du gerne die [makesmart Community](https://web.archive.org/web/20230602010707/https://my.makesmart.net/) dazu nutzen. ![🙂](https://web.archive.org/web/20230602010707im_/https://my.makesmart.net/plugins/nodebb-plugin-emoji/emoji/apple/1f642.png?v=chnl3kn5hqg)

[Die Produkte aus diesem Tutorial findet ihr hier.](https://web.archive.org/web/20230602010707/https://makesmart.shop/loadBasket/5AgoDTEJPSL)