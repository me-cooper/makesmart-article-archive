# Dateien auf dem ESP8266 speichern - Das SPIFFS Dateisystem auf dem ESP



Wenn es darum geht, Inhalte auf dem ESP8266 zu speichern, stolpert man immer wieder über die selben Begriffe. EEPROM, SPIFFS oder RTC Memory. Alle drei Varianten lassen es zu, Daten auf dem ESP zu speichern. Eine Variante schauen wir uns heute genauer an: Das SPIFFS Dateisystem auf dem ESP8266.

------

### Das SPIFFS Dateisystem

Mit dem SPIFFS Dateisystem können wir auf dem Flash-Speicher zugreifen als wäre es ein Dateisystem auf dem Computer. Wir können Dateien anlegen, Dateien lesen und auch Dateien schreiben. Das Beste an der ganzen Sache ist: die gespeicherten Dateien überleben sowohl einen Deep Sleep als auch einen kompletten Reboot.

Die Größe des SPIFF richtet sich nach dem verwendeten ESP8266-Modell. Während ein D1 Mini mit einem SPIFFS von bis zu 3MB daherkommt, schafft es der ESP01-S gerade mal auf bis zu 512KB - immer noch mehr als genug für viele Anwendungen. In diesem Tutorial verwende ich einen ESP8266 D1 Mini.

Die Größe des SPIFFS kann mithilfe der Arduino IDE eingestellt werden. Geht dazu über das Menü auf folgenden Punkt:

```
Arduino IDE
└── Werkzeuge
    └── Flash Size
        ├── 4M (no SPIFFS)
        ├── 4M (1M SPIFFS)
        ├── 4M (2M SPIFFS)
        └── 4M (3m SPIFFS)
```

![Arduino IDE ESP8266 SPIFFS Einstellung](https://web.archive.org/web/20210415173620im_/https://my.makesmart.net/assets/uploads/files/1598293445757-arduino_ide_edp8266_spiffs.jpg)

------

### SPIFFS - Die Libary

Um auf die Funktionen des SPIFFS zugreifen zu können, wird eine Bibliothek benötigt. Die Bibliothek ist bereits standardmäßig installiert und kann sofort verwendet werden.

Ich möchte an dieser Stelle direkt mit einem Beispiel starten. Die Erklärung einzelner Bestandteile kommt weiter unten im Text.

```
// Code by cooper@my.makesmart.net
// Released under CC - CC by cooper@my.makesmart.net

// Filesystem-Libary einbinden
#include <FS.h>

// Name der Datei die wir speichern und auslesen möchten
const char* datei = "meine.txt";

void setup() {
  delay(1000);
  Serial.begin(115200);
  Serial.println();
  Serial.println("ESP gestartet");

  // Dateisystem initialisieren
  if(SPIFFS.begin()){
    Serial.println("Dateisystem: initialisiert");
  }else{
    Serial.println("Dateisystem: Fehler beim initialisieren");
  }

  // Dateisystem formatieren
  //SPIFFS.format();

  // Öffnen der von uns definierten Datei nur mit Leserechten
  // r - Read - nur Leserechte
  // w - Write - Schreibrechte
  
  File file = SPIFFS.open(datei, "r");
  
  if(!file){
    // Die Datei ist nicht vorhanden
    Serial.println("Die Datei " + String(datei) + " existiert nicht!");

    // Datei mit Schreibrechten öffnen, wird erstellt wenn nicht vorhanden
    File file = SPIFFS.open(datei, "w");
    Serial.println("Datei " + String(datei) + " wurde erstellt!");

    // Daten in die Datei schreiben
    file.print("Der Inhalt meiner Datei.");

    // Schließen der Datei
    file.close();
    
  }else{

     // Die Datei ist vorhanden
     Serial.println("Die Datei " + String(datei) + " wurde geöffnet!");
     
     // Inhalt ausgeben oder verarbeiten
     int i;
     Serial.println();
     for(i=0;i<file.size();i++){
        
      Serial.print((char)file.read());
        
     }
      
     file.close();
      
  }
  

}

void loop() {

  // Hier wird einfach nur der Inhalt der Datei in Dauerschleife ausgegeben
  File file = SPIFFS.open(datei, "r");
  
  int i;
  Serial.println();
  for(i=0;i<file.size();i++){
        
   //Serial.print((char)file.read());
   Serial.print((char)file.read());
        
  }
      
  file.close();

      
  delay(20000);

}
```

Die Ausgabe im seriellen Monitor sieht wie folgt aus:

```
ESP gestartet
Dateisystem: initialisiert
Die Datei meine.txt existiert nicht!
Datei meine.txt wurde erstellt!

Der Inhalt meiner Datei.
```

------

### Erläuterung des Codes

Zu Beginn **muss** das Dateisystem initialisiert werden. Ansonsten kann weder Lese- noch Schreibzugriff erfolgen.

```
// Dateisystem initialisieren
if(SPIFFS.begin()){
  Serial.println("Dateisystem: initialisiert");
}else{
  Serial.println("Dateisystem: Fehler beim initialisieren");
}
```

In der folgenden Variable wird der Name der Datei gespeichert, auf die wir zugreifen wollen. Mithilfe dieser Variable erstellen wir ein neues File-Objekt mit dem Namen `file`. Über dieses Objekt greifen wir von dort an auf die einzelnen Funktionen der Bibliothek zu.

Es wird also die Datei `meine.txt` geöffnet.

```
const char* datei = "meine.txt";

// Öffnen der von uns definierten Datei
// r - Read - nur Leserechte
// w - Write - Schreibrechte
  
File file = SPIFFS.open(datei, "r");
```

Die Datei ist (noch) nicht vorhanden. Ob eine Datei vorhanden ist, kann man mit dem gerade eben erstellen Objekt `file` prüfen.

```
if(!file){
  Serial.println("Die Datei " + String(datei) + " existiert nicht!");

  // Datei mit Schreibrechten öffnen / Datei wird erstellt wenn nicht vorhanden
  File file = SPIFFS.open(datei, "w");
  Serial.println("Datei " + String(datei) + " wurde erstellt!")
  
  // Daten in die Datei schreiben
  file.print("Der Inhalt meiner Datei.");

  // Schließen der Datei
  file.close();
    
  }
```

Es wird also die Datei `meine.txt` erstellt, da sie noch nicht vorhanden ist. Gleichzeit wird der Text `Der Inhalt meiner Datei.` in der Datei gespeichert.

Beim Neustarten des ESP wird der oben aufgeführte Code nicht mehr ausgeführt, da die Datei dann bereits existiert und die If-Anweisung deshalb übersprungen wird.

Um jetzt mit dem Inhalt der Datei arbeiten zu können, kann der folgende Code verwendet werden. Bei mir ist dieser Block im Setup sowie im Loop.

```
File file = SPIFFS.open(datei, "r");
  
  int i;
  Serial.println();
  for(i=0;i<file.size();i++){
        
   Serial.print((char)file.read());
        
  }
      
  file.close();

      
  delay(20000);
```

Alle 20 Sekunden wird der Inhalt der Datei ausgegeben. Der serielle Monitor zeigt folgendes an:

```
ESP gestartet
Dateisystem: initialisiert
Die Datei meine.txt existiert nicht!
Datei meine.txt wurde erstellt!

Der Inhalt meiner Datei.
Der Inhalt meiner Datei.
Der Inhalt meiner Datei.
```

------

### Vorteile des SPIFFS

Das SPIFFS bietet mehrere Vorteile. Es können zum einen Konfigurationen gespeichert werden die entweder direkt auf dem ESP liegen oder auch nachträglich über ein Webinterface o.Ä geändert wurden. Sie gehen nicht mehr verloren.

Wie man sieht ist das SPIFFS, verglichen zu anderen Möglichkeiten, sehr einfach zu verwenden.

Auch für den Batteriebetrieb hat das SPIFFS Vorteile. Anstatt bei jedem Messen der Temperatur oder der Luftfeuchtigkeit die Daten über das WLAN zu senden, können die Messwerte zwischengespeichert und nur jedes 10x an die Zentrale geschickt werden. Das spart ordentlich Energie.

Allgemein ermöglicht das SPIFFS mehr Flexibilität bei Projekten. Ich werde demnächst eine Steuerung für ein Förderband programmieren. Parallel laufen noch anderes Prozesse. Ich habe keine Möglichkeit über Zeit oder über WLAN zu programmieren, also werde ich SPIFFS nutzen. Bei jedem Deep Sleep wird eine Variable um +1 Punkt erhöht. Wenn ein gewisser Schwellenwert erreicht ist, läuft die Steuerung an und setzt den Counter zurück.

Das SPIFFS bietet viele neue Möglichkeiten der Programmierung.