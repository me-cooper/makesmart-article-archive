# ArduinoIDE - Umwandeln von Datentypen

Wer sich mit der Arduino IDE und Modulen wie ESP8266, ESP32 oder auch dem Arduino selber beschäftigt, wird früher oder später mal über das Problem stolpern, verschiedene Datentypen zu kombinieren zu wollen, oder auch Datentypen im Allgemeinen umwandeln zu wollen, um die Daten dann in anderen Operationen nutzen zu können. Ein gängiger *cast* wäre zum Beispiel eine `int` Variable in eine `float` Variable umzuwandeln, um damit genauer rechnen zu können.

Leider gib es keine Methode oder Funktion, die universell einsetzbar ist. Es kommt immer ganz drauf an, welchen Datentypen ihr umwandeln wollt, und vor allem welcher Datentyp am Ende rauskommen soll. Hier versuche ich mal alle Gängigen Datentyp-Konvertierungen aufzulisten - gruppiert nach dem gewünschten End-Datentyp.

Habt ihr weitere Vorschläge und Hinweise bzw. Snippets, dann würde ich mich freuen, wenn ihr sie unter diesem Beitrag hier teilen würdet.

------

##  **Datentyp** : String

**`Integer`**

```cpp
int i = 25;
String convertedString = String(i);
// "25"
```

**`Float`**

```cpp
float i = 22.5367;
String convertedString = String(i, 3);
// "22.536"
```

**`Bool`**

```cpp
bool state = false;
String convertedString = String(state );
// "0"
```

------

##  **Datentyp** : Int

**`String`**

```cpp
String number = "255";
int i = number.toInt();
// 255
```

**`Float`**

```cpp
float number = "123.232";
int i = (int)number;
// 123
```

**`Char`**

```cpp
char myChar[] = "147";
int i= atoi(myChar);
// 147
```

------

##  **Datentyp** : Float

**`Int`**

```cpp
int i = 187;
float myFloat= float(i);
// 187.00
```

**`String`**

```cpp
String number = "14.2653";
float i = number.toFloat();
// 14.2653
```

 