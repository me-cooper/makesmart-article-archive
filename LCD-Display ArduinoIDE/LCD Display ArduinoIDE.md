# LCD Display HD44780 I2C am ESP8266 D1 Mini - Tutorial



Um in einem Projekt Feedback zu geben gibt es veschiedene Möglichkeiten. Eine davon wäre zum Beispiel ein LCD Display - genauer gesagt das HD44780 LCD Display. Das LCD Display gibt es in verschiedenen Ausführungen. Mit blauer und grüner Hintergrundbeleuchtung und mit I2C Bus sowie ohne. In diesem Tutorial behandeln wir die Einrichtung des HD44780 16x2 LCD Displays am ESP8266 D1 Mini und zwar das mit I2C Bus.

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814337188-lcd_hd44780_i2c_modul_makesmart.png)

Die Bezeichnung *16x2* bezieht sich übrigens auf die Ausführung des Displays. 16x2 bedeutet hier so viel wie 16 Zeichen je Zeile, bei zwei Zeilen. Dieses Tutorial kann genauso gut bei einem 20x4 Display angewendet werden.

------

#### LCD LiquidCrystal I2C - Arduino IDE Bibliothek hinzufügen

Wie bei fast allen Modulen muss auch für das HD44780 LCD Display eine Bibliothek in der Arduino IDE installiert werden. Das Installieren einer Bibliothek geht über den Bibliotheksverwalter. Ihr erreicht ihn über das Menü der Arduino IDE. Die Bibliothek trägt den Namen LiquidCrystal I2C und stammt von Frank de Brabander. Eventuell müsst ihr nach der Sucheingabe etwas nach unten scrollen (siehe Bild).

```
Arduino IDE└───Werkzeuge    └───Bibliotheken verwalten...        └───LiquidCrystal I2C
```

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814398466-arduino_ide_lcd_libary.png)

------

#### LCD Display HD44780 I2C - Die Verdrahtung

Die Verdrahtung des HD44780 16x2 LCD Display gestaltet sich dank dem I2C Modul denkbar einfach. Dementsprechend muss auch der I2C Bus am D1 Mini genutzt werden. Ein Blick auf das Datenblatt verrät wo.

![img](https://web.archive.org/web/20230330045930im_/https://makesmart.net/content/images/2020/06/d1_mini_pinout_1.png)

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814426844-esp8266_d1_mini_hd44780_schaltplan.png)

| HD44780 LCD Display I2C Modul | ESP8266 D1 Mini |
| ----------------------------- | --------------- |
| GND                           | G               |
| VCC                           | 5V              |
| SDA                           | D2 (SDA)        |
| SCL                           | D1 (SCL)        |

------

#### LCD Display HD44780 I2C - Der Code

Nachdem die Bibliothek installiert und das LCD Display verdrahtet wurde kommt jetzt der eigentlich wichtige teil: Der Code. Ich starte wie immer direkt mit einem Beispiel welches ihr als Basis für eure LCD-Projekte verwenden könnt. Der Code wurde detailliert kommentiert, sodass die Erklärungen aus dem Code entnommen werden können.

```arduino
/* *  * LCD-Display Beispiel by cooper @ my.makesmart.net * released under CC - CC by cooper @ my.makesmart.net *  */// Benötigte Libary einbinden#include <LiquidCrystal_I2C.h>// LCD-Display Konfiguration// Standard LCD-Adresse ist 0x27 - 16 zeichen - 2 Zeilen LiquidCrystal_I2C lcd(0x27,16,2);void setup(){  // LCD-Display initialisieren  lcd.init();  // Schlatet die Hintergrundbeleuchtung des LCD-Displays an  lcd.backlight();  // würde die Hintergrundbeleuchtung ausschalten  // lcd.noBacklight();  // Startpunkt der Ausgabe setzen. Starte bei Zeichen 0, Zeile 0  lcd.setCursor(0,0);  lcd.print("makesmart");  // Startpunkt der Ausgabe setzen. Starte bei Zeichen 0, Zeile 1  lcd.setCursor(0,1);  lcd.print("cooper");}void loop(){  }
```

Copy

Folgende Zeile bezieht sich auf die Ausführung des LCD Displays. Ich verwende wie bereits erwähnt ein 16x2 LCD Display. Bei der 20x4 Ausführung kann die Zeile einfach angepasst werden.

```arduino
// LCD-Display Konfiguration// Standard LCD-Adresse ist 0x27 - 16 zeichen - 2 Zeilen LiquidCrystal_I2C lcd(0x27,20,4);
```

Copy

Nach dem Aufspielen auf den ESP8266 D1 Mini erscheint folgende Ausgabe auf dem LCD Display…

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814563263-hd44780_lcd_example.png)

Um den Text vom LCD-Display zu löschen, kann die Funktion `clear()` verwendet werden.

```arduino
lcd.clear();
```

Copy

------

### Weitere Funktionen der Libary

Neben der Funktion `lcd.print("cooper");` gibt es weitere Funktionen, mit denen man das LCD Display vielseitig verwenden kann.

------

#### scrollDisplayLeft() - scrollDisplayRight()

Mit den beiden Funktionen `scrollDisplayLeft()` und `scrollDisplayRight()` kann man den aktuellen Text auf dem LCD-Display sehr leicht und ohne Aufwand scrollen lassen.

```arduino
void loop(){ lcd.scrollDisplayLeft(); delay(100);}
```

Copy

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814725594-lcd_scroll_left_example.gif)

------

#### blink() - noBlink()

Diesmal keine Led, sondern ein Cursor. Mit den beiden Funktionen `blink()` und `noBlink()` kannst du dir wahsweise einen blinkenden Cursor anzeigen lassen, oder eben nicht.

```arduino
// Zeigt einen blinkenden Cursor am Ende der letzten Textzeile anlcd.blink();// Versteckt den blinkenden Cursor am Ende der letzen Textzeilelcd.noBlink();
```

Copy

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814759626-lcd_cursor.gif)

------

#### display() - noDisplay()

Die Funktion `noDisplay()` versteckt den Text auf dem LCD-Display. Im Gegensatz zu der Funktion `clear()` wird der Text nicht gelöscht, sondern kann einfach wieder mithilfe der Funktion `display()` angezeigt werden.

```arduino
void loop(){   lcd.display();   delay(1000);   lcd.noDisplay();   delay(1000);}
```

Copy

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814794495-lcd_makesmart_cooper.gif)

------

#### Umlaute

Wenn man versucht mithilfe von `lcd.print("Folgt für mehr");` den Text auf das LCD-Display zu kriegen wird man sehen, dass das `ü` fehlerhaft angezeigt wird. Umlaute können wie folgt ausgegeben werden:

```arduino
lcd.print("Folgt f\xF5r mehr");lcd.print("\xE1"); // gibt ein ä auslcd.print("\xEF"); // gibt ein ö auslcd.print("\xF5"); // gibt ein ü auslcd.print("\xE2"); // gibt ein ß aus
```

Copy

------

#### Spezielle (& eigene) Zeichen

Um spezielle Zeichen wie Smileys, Herzen oder auch Glocken anzeigen zu können, müssen diese erst in einem byte Array definiert werden. Eine tolle Seite zum erstellen eigene Zeichen findest zu hier: [LCD Custom Character Generator](https://web.archive.org/web/20230330045930/https://maxpromer.github.io/LCD-Character-Creator/)

Im folgenden Beispiel habe ich mir ein Herz “gezeichnet”.

```arduino
byte heart[] = {  B00000,  B00000,  B01010,  B11111,  B11111,  B01110,  B00100,  B00000};
```

Copy

```arduino
/* *  * LCD-Display Beispiel by cooper @ my.makesmart.net * released under CC - CC by cooper @ my.makesmart.net *  */#include <LiquidCrystal_I2C.h>LiquidCrystal_I2C lcd(0x27,16,2);byte heart[] = {  B00000,  B00000,  B01010,  B11111,  B11111,  B01110,  B00100,  B00000};void setup(){  lcd.init();  lcd.backlight();  lcd.setCursor(7,0);  lcd.print("makesmart");  lcd.setCursor(0,0);  lcd.print("cooper");  // Neues Zeichen erstellen mit der Nummer 5  lcd.createChar(5, heart);}void loop(){    lcd.setCursor(6,0);  // Zeichen an Position ausgeben  lcd.write(5);    delay(1000);    lcd.setCursor(6,0);  // Zeichen an Position wieder löschen  lcd.print(" ");    delay(1000);}
```

Copy

------

![img](https://web.archive.org/web/20230330045930im_/https://my.makesmart.net/assets/uploads/files/1598814837469-lcd_special_chars.gif)