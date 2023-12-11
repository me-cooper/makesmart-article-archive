# MFRC522 RFID Reader am ESP8266 D1 Mini

Der MFRC522 RFID Reader ist ein beliebtes Modul wenn es darum geht, RFID Tags am Raspberry Pi, Arduino, ESP8266 und co. auszulesen. Und genau darum soll es heute gehen. In diesem Tutorial werden wir gemeinsam einen MFRC522 RFID Reader am ESP8266 D1 Mini anschließen und programmieren. Das Programmieren beschränkt sich darauf, die UID des Chips auszulesen. Wir werden keine Daten auf den Chip speichern oder auslesen. Es geht lediglich darum zu unterscheiden, ob Chip A oder Chip B aufgelegt wurde. Aber was das genau bedeutet, erkläre ich im Laufe dieses Tutorials.

### MFRC522 RFID Reader - Verkabelung

Bevor wir mit der Verkabelung starten, werfen wir einen Blick auf das Pinout des ESP8266 D1 Minis.

------

![ESP8266 D1 Mini Pinout - Pinbelegung](https://web.archive.org/web/20220118154851im_/https://makesmart.net/content/images/2020/06/d1_mini_pinout_1.png)

Heute arbeiten wir das erste mal mit dem SPI-Interface des D1 Minis. SPI bedeutet so viel wie **Serial Peripheral Interface** und ist ein Bus-System welches der MFRC522 benötigt um zu funktionieren. Hier einmal die Verdrahtung des RFID Readers mit dem ESP8266 D1 Mini.

![MFRC522 - ESP8266 D1 Mini Schaltplan](https://web.archive.org/web/20220118154851im_/https://my.makesmart.net/assets/uploads/files/1598018501573-mfrc522_esp8266_d1_mini_schaltplan.png)

Die Pin-Bezeichnungen auf dem RFID-Reader stimmen überwiegend mit den Bezeichnung auf Pinout des D1 Minis überein. Hier anbei nochmal eine Tabelle, die das Verdrahten der Schaltung erleichtern sollte.

| MFRC522 RFID Reader | ESP8266 D1 Mini   |
| ------------------- | ----------------- |
| **RST**             | **D3**            |
| **SDA**             | **D8 (SPI SS)**   |
| **MOSI**            | **D7 (SPI MOSI)** |
| **MISO**            | **D6 (SPI MISO)** |
| **SCK**             | **D5 (SPI SCK)**  |
| **3.3V**            | **3V3**           |
| **GND**             | **G**             |

Nachdem die Verdrahtung abgeschlossen ist, können wir mit der Einrichtung der Arduino IDE weitermachen. In der IDE muss nämlich eine Bibliothek installiert werden, damit der MFRC522 läuft.

------

### MFRC522 RFID Reader - Arduino IDE Bibliothek hinzufügen

Um auf Beispiele der Community und Funktionen des RFID-Readers zugreifen zu können, muss die Arduio Bibliothek MFRC522 installiert werden. Startet dazu die Arduino IDE und öffnet über die Werkzeuge im Menü den Bibliotheksverwalter.

```text
Arduino IDE
└───Werkzeuge
    └───Bibliotheken verwalten
```

![MFRC522 Bibliothek - Arduino IDE](https://web.archive.org/web/20220118154851im_/https://my.makesmart.net/assets/uploads/files/1598019128592-bibliothek_mfrc522_rfid_arduino_ide.png)

Sucht im Bibliotheksverwalter nach dem Begriff *MFRC522* und installiert das Paket der GithubCommunity.

------

### MFRC522 RFID Reader - Beispiele

Wie bei fast jedem anderen Modul gibt es auch für den RFID-Reader einige Beispiele, in die man reinschnuppern kann. Die Beispiele befinden sich unter folgendem Menüpunkt der IDE.

```text
Arduino IDE
└───Datei
    └───Beispiele
        └───MFRC522
```

![MFRC522 Beispiele - Arduino IDE](https://web.archive.org/web/20220118154851im_/https://my.makesmart.net/assets/uploads/files/1598019453597-mfrc522_rfid_beispiele_arduino_ide.png)

Die Beispiele könnt ihr gerne auf eigene Faust erkunden und durchstöbern. Unser Ziel ist jetzt erstmal den RFID-Reader in der Basic-Form zum Laufen zu bekommen. Anbei habe ich einen Code vorbereitet, den ihr 1:1 kopieren und als Ausgangslage für euer RFID-Projekt verwenden könnt.

### MFRC522 RFID Reader - Code

```arduino
#include <SPI.h>
#include <MFRC522.h>

#define SS_PIN          15         // Pin: D8
#define RST_PIN         0          // Pin: D3

long chipID;


// MFRC522-Instanz erstellen
MFRC522 mfrc522(SS_PIN, RST_PIN);

void setup() {
	Serial.begin(115200);

  // SPI-Bus initialisieren
	SPI.begin();

  // MFRC522 initialisieren
	mfrc522.PCD_Init();

  //Kurze Pause nach dem Initialisieren   
	delay(10);
  Serial.println("");

  // Details vom MFRC522 RFID READER / WRITER ausgeben
	mfrc522.PCD_DumpVersionToSerial();	

  
	Serial.println("RFID-Chip auflegen, um UID anzuzeigen...");
}

void loop() {
  
	// Sobald ein Chip aufgelegt wird startet diese Abfrage
	if (mfrc522.PICC_IsNewCardPresent()){
  
    //CardID resetten
    chipID = 0;
    
    mfrc522.PICC_ReadCardSerial();

    // Hier wird die ID des Chips in die Variable chipID geladen
    for (byte i = 0; i < mfrc522.uid.size; i++){
      chipID=((chipID+mfrc522.uid.uidByte[i])*10);
    }

    //... und anschließend ausgegeben
    Serial.println(chipID);

    // Danach 5 Sekunden pausieren, um mehrfaches lesen /ausführen zu verhindern
    delay(5000);
      
	}

	

}
```

Nachdem das Programm hochgeladen wurde, erscheint im seriellen Monitor folgende Ausgabe:

```
Firmware Version: 0x88 = (clone)
RFID-Chip auflegen, um UID anzuzeigen...
```

Ich lege halte nun mal drei verschiedene Chips an den Reader. Die Ausgabe sieht wie folgt aus:

```
Firmware Version: 0x88 = (clone)
RFID-Chip auflegen, um UID anzuzeigen...
2384830
2680600
1799100
```

Jeder Chip hat seine eigene ID und kann entsprechend zu anderen Chips unterschieden werden.

```arduino
if(chipID == 2680600){
      Serial.println("Hallo cooper!");
    }

    if(chipID == 2384830){
      Serial.println("Hallo Fremder!");
    }

    if(chipID == 1799100){
      Serial.println("Für Erzfeinde ist der Zugriff gesperrt!");
    }
2384830
Hallo Fremder!
1799100
Für Erzfeinde ist der Zugriff gesperrt!
2680600
Hallo cooper!
```

#### Disclaimer

**Diese Art und Weise einer Erkennung für Zugriff durchzuführen ist alles andere als sicher! Seid euch dessen bewusst und nutzt diesen RFID-Reader nur für Projekte, die mit keinen Sicherheitsrelevanten Einrichtungen zu tun haben (Haustüre, Safe, …). Ein Umgehen der Sicherheit durch Personen die Ahnung von der Materie haben \*könnte\* möglich sein.**