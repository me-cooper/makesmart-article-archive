# Dateitypen in der ArduinoIDE

Datentypen in der Programmierung bestimmen den Wertebereich der definierten Variable oder auch Funktion. In der ArduinoIDE gibt es verschiedene Datentypen die verwendet werden können. Um eine kleine Übersicht darüber zu haben, welche Datentypen zur Verfügung stehen, dachte ich mir, fasse ich das ganze mal in einem kleinen Beitrag zusammen.

Der reservierte Speicherplatz einer Variable oder auch einer Funktion wird unter anderem durch den Datentyp festgelegt.

Bei einer `bool` Variable - die einen Bit `true` oder `false` darstellt, könnte man davon ausgehen, dass nur 1-Bit im Speicher reserviert wird. Das ist aber nicht der Fall. Auch bei einer `bool` Variable werden 8-Bit reserviert, die kleinstmögliche Größe für die Belegung im Arbeitsspeicher.

Aber das ist nur eine Besonderheit im Bezug auf Variablen.

------

###  DATENTYP : `VOID`

Bei dem Datentyp `void` der eigentlich gar kein Datentyp ist, handelt es sich um ein Keyword, dass ausschließlich bei Funktionen zum Einsatz kommt. Funktionen die als `void` initialisiert werden, geben keinen Rückgabewert und führen stumpf etwas aus, ohne das Ergebnis zurückzugeben.

```cpp
void meineFunktion(){
   // do some stuff here an return nothing
}
```

###  DATENTYP : `BOOL` - 1 BIT

Eine Variable vom Typ `bool` bzw. `boolean` kann genau zwei Werte enthalten: `true` oder `false`. Auch wenn dafür im Speicher 8 Bit reserviert werden, kann nur 1 Bit gespeichert werden. Dieser Datentyp ist perfekt dafür geeignet, einen Schalterzustand zu speichern.
*Ist der Schalter betätigt oder nicht?*

```cpp
bool switch_is_on = true;
bool switch_is_on = false;
```

###  DATENTYP : `BYTE` - 8 BIT

Ein Byte kann einen Wert von 0 - 255 speichern. Die meisten PWM Register haben eine Auflösung von 8 Bit bzw. einem Byte. Daraus resultiert zum Beispiel auch der maximale Wert von 255 bei PWM-Signalen.

```cpp
byte value = 33;
```

###  DATENTYP : `CHAR` - 8 BIT

Eine Variable mit dem Typ `char` kann eine Zahl oder einen Buchstaben enthalten. Gespeichert wird der Wert jedoch immer als Buchstabe - also `char` - character. Hier lohnt sich ein Blick in eine sogenannte [ASCII-Tabelle](https://web.archive.org/web/20210922002440/https://tools.piex.at/ascii-tabelle/). Der Speicher der reserviert wird beträgt auch hier 1 Byte - genauer gesagt Werte von -128 - 127. Bei einer ASCII-Tabelle muss dabei immer auf den `Dez` - also den Dezimalwert des jeweiligen Buchstaben geachtet werden.

```cpp
// Bei beiden Definitionen wird der Buchstabe a gespeichert.
char buchstabe_a = ‘a’;
char number_a = 97;
```

###  DATENTYP : `UNSIGNED CHAR` - 8 BIT

Ein ohne Vorzeichen behafteter Datentyp `unsigned` kann ken Vorzeichen enthalten. Also keine negativen Werte. Daraus resultiert ein Wertebereich von 1 Byte, aber nur ins positive: 0 - 255.
Damit haben wir das gleiche wie bei einem `byte`-Datentyp. Um Verwirrung zu vermeiden sollte stattdessen dann auch lieber direkt auf den Datentyp `byte` zurückgegriffen werden.

```cpp
// Würde auch folgendes ASCII-Zeichen repräsentieren: ©
unsigned char myChar = 169;
```

###  DATENTYP : `WORD` - 16 BIT

Auch wenn bei diesem Datentypen kein `unsigned` vorkommt, kann der Wert hier auch nicht negativ sein. Irritierend ist vielleicht die Bezeichnung `word` - es werden nämlich keine Wörter gespeichert, sondern Zahlen mit den Werten 0 - 65535. Dieser Um Verwirrung vorzubeugen, kann stattdessen auch der nachfolgende Datentyp `unsigned int` verwendet werden.

```cpp
word gramms = 45231;
```

###  DATENTYP : `UNSIGNED INT` - 16 BIT

Hierbei handelt es sich um das gleiche wie bei dem Datentyp `word`. Es können 16 Bit positive Zahlen gespeichert werden. Also Zahlen im Wertebereich von 0 - 65535.

```cpp
unsigned int gramms = 45231;
```

###  DATENTYP : `INT` - 16 BIT

Integer sing Ganzzahlen. Also immer Zahlen ohne Komma. Es werden 16 Bit im Speicher reserviert. Die Zahlen können auch negativ sein. Daraus resultiert ein Wertebereich von -32768 - 32767.

```cpp
int year = 2021;
```

###  DATENTYP : `UNSIGNED LONG` - 32 BIT

Bei dem Datentyp `unsigned long` werden 32 Bit im Speicher reserviert. Es werden Zahlen gespeichert, die nicht negativ sein können. Durch das und durch die 32 Bit Speicher die belegt werden, lassen sich sehr große Zahlen speichern. Der Wertebereich einer `unsigned long` Variable geht von 0 - 4294967295.

Meistens wird dieser Datentyp genutzt, um mit der `millis()` Funktion innerhalb der Arduino IDE zu arbeiten.

```cpp
unsigned long last_millis = 3124591539;
```

###  DATENTYP : `LONG` - 32 BIT

Hierbei handelt es sich um einen Datentyp, bei dem 32 Bit im Speicher reserviert werden. Es können sowohl negative als auch positive Zahlen gespeichert werden. Der Wertebereich reicht von -2147483648 - 2147483647.

```cpp
long time_remaining = -12372876  ;
```

###  DATENTYP : `FLOAT` - 32 BIT

Bei dem Datentyp `float` handelt es sich um einen besonderen Datentyp. Denn hier können als einzige Fließkommazahlen gespeichert werden. Aus diesem Grund werden auch 32 Bit im Speicher reserviert. Es ist nicht ratsam grundsätzlich immer mit Float-Variablen zu arbeiten, wenn etwas berechnet werden muss, da diese sehr Rechen und Speicherintensiv sind.
Der Wertebereich einer `float` Variable reicht von -3.4028235E+38 - 3.4028235E+38.

```cpp
float height = 1.75;
```

------

###  DATENTYPEN CASTEN & UMWANDELN

Nicht nur bei Berechnungen mit `int` und `float` Zahlen kann es zu unerwarteten Problemen führen. Auch bei einer Ausgabe oder dem allgemeinen Mischen von verschiedenen Datentypen kommt es immer wieder zu Fehlern. Die Datentypen müssen - bevor sie kombiniert werden können - Umgewandelt bzw. gecastet werden. Wer sich dafür interessiert kann sich gerne mal [diesen Beitrag hier anschauen](https://web.archive.org/web/20210922002440/https://my.makesmart.net/topic/200/arduino-ide-das-umwandeln-und-casten-von-datentypen), in dem ein paar Themen darüber aufgelistet sind.