# Der MSP-107 Helligkeitssensor am ESP8266 D1 Mini - Tutorial

Der MSP-107 ist ein Helligkeitssensor der für Arduino, ESP8266, Raspberry Pi und Co. verwendet werden kann. Das Besondere am MSP-107 ist, dass er als analoger sowie digitaler Sensor eingesetzt werden kann. Das bedeutet, dass er als digital Sensor unterscheiden kann, ob es hell oder dunkel ist - als analoger Sensor kann er nicht nur unterscheiden ob hell oder dunkel, sondern kann zusätzlich auch noch bestimmen, wie hell oder dunkel es gerade ist. In diesem Tutorial werden wir beide Varianten besprechen und bearbeiten. So sieht der MSP-107 im Details aus.

![img](https://web.archive.org/web/20230610140246im_/https://makesmart.shop/media/ae/aa/30/1601911701/phosotsensor_2.png)

Den MSP-107 welchen ich für dieses Tutorial findet ihr [hier](https://web.archive.org/web/20230610140246/https://makesmart.shop/loadBasket/VkEkRfGjfPs).

Anzeige - [makesmart.shop](https://web.archive.org/web/20230605150036/https://makesmart.shop)

[![Jumper Kabel Female](https://web.archive.org/web/20230610140246im_/https://makesmart.shop/media/c5/19/ab/1597162043/jumper_kabel_female.jpg)](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/Jumper-Kabel-Female-20cm)

[Jumper Kabel Female 20cm](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/Jumper-Kabel-Female-20cm)

Stückpreis

 1,99 €

 

[![img](https://web.archive.org/web/20230610140246im_/https://makesmart.shop/media/ae/aa/30/1601911701/phosotsensor_2.png)](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/MSP-107-Lichtsensor-Helligkeitssensor-Digital-Analog-Sensor)

[MSP-107 - Lichtsensor Helligkeitssensor ...](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/MSP-107-Lichtsensor-Helligkeitssensor-Digital-Analog-Sensor)

Stückpreis

 3,49 €

 

[![D1 Mini Vorderseite](https://web.archive.org/web/20230610140246im_/https://makesmart.shop/media/0e/fb/1d/1597161715/d1_mini_top_2.png)](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/ESP8266-D1-Mini)

[ESP8266 D1 Mini](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/ESP8266-D1-Mini)

Stückpreis

 5,79 €

[**zusammen nur 15,22 €** - zum Warenkorb](https://web.archive.org/web/20230610140246mp_/https://makesmart.shop/loadBasket/VkEkRfGjfPs)

------

### MSP-107 als digitaler Helligkeitssensor

Wie bei digitalen Signalen üblich, gibt es nur zwei Zustände: **an oder aus**. Auch bei dem MSP-107 als digitaler Sensor gibt es nur zwei Optionen: **dunkel oder hell**. Der Sensor gibt beim Zustand **hell** eine **0** aus - wenn es **dunkel** ist, gibt er eine **1** zurück.

------

Die Verdrahtung als digitaler Sensor ist wie folgt:

| ESP8266 D1 Mini | MSP-107 Sensor |
| --------------- | -------------- |
| D6              | DO             |
| 3.3V / 5V       | VCC            |
| G               | GND            |

Außer den Pins `D8`,`D4` und `D3` kann jeder Pin am D1 Mini als Input für den Sensor verwendet werden. Hier im Beispiel verwende ich den Pin `D6` als Input-Pin.

```arduino
/*

	ESP8266 D1 Mini MSP-107 Sensor digital
	Basic Code by cooper
	@my.makesmart.net/user/cooper

*/

// MSP-107 Helligkeitssensor - PIN D6  
int photo_sensor = 12; // D6

// Hell: 0 - Dunkel: 1
bool night_state;

void setup() {

  Serial.begin(115200);
  delay(500);

  pinMode(photo_sensor, INPUT);
  
}

void loop() {

   night_state = digitalRead(photo_sensor);
   
   if(night_state == 1){
      Serial.println("Es ist dunkel!");
   }
   
   if(night_state == 0){
      Serial.println("Es ist hell!");
   }
   
   delay(5000);

  
}
```

Copy

Jenachdem ob es hell oder dunkel ist, sieht die Ausgabe im seriellen Monitor wie folgt aus:

```
Es ist dunkel!
Es ist hell!
```

Wann es für den MSP-107 dunkel bzw. hell ist, kann am Potentiometer druch drehen eingestellt werden.

------

### MSP-107 als analoger Helligkeitssensor

Wenn es dir nicht reicht zu wissen ob es dunkel oder hell ist, sondern du lieber wissen möchtest, wie hell bzw dunkel es ist, kannst du den MSP-107 als analogen Sensor verwenden. Die Verdrahtung sieht dabei wie folgt aus:

| ESP8266 D1 Mini | MSP-107 Sensor |
| --------------- | -------------- |
| A0              | AO             |
| 3.3V            | VCC            |
| G               | GND            |

Der einzige analoge Pin am D1 Mini ist der Pin `A0` - also ist das auch der einzige Pin den wir für den analogen Sensor verwenden können. Wichtig: **Der analoge Input am D1 Mini verträgt maximal 3.3V!**

Der Code für den analogen Sensor sieht wie folgt aus:

```arduino
/*

	ESP8266 D1 Mini MSP-107 Sensor analog
	Basic Code by cooper
	@my.makesmart.net/user/cooper

*/

// MSP-107 Helligkeitssensor - PIN A0 
int photo_sensor = A0; // A0

// Variable zum Speichern des Helligkeitswertes
int light_value;

void setup() {

  Serial.begin(115200);
  delay(500);

  pinMode(photo_sensor, INPUT);
  
}

void loop() {

   light_value = analogRead(photo_sensor);
   
   Serial.println(String(light_value));
   
   delay(5000);

}
```

Copy

Jenachdem wie hell es ist, wird im seriellen Monitor ein Wert zwischen `0` und `1023` ausgegeben. Diesen Wert kann man weiterverarbeiten. Außerdem muss man manuell festlegen, welcher Wert als hell oder dunkel definiert wird.

------

Was anhand des Wertes passieren soll, kann man zum Beispiel mit einer if-Abfrage festlegen.

```
if(light_value >= 510){
	// mach was
}else{
	// mach was anderes
}
```

#### 