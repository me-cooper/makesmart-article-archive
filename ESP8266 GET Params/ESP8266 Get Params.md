# ESP8266 GET-Variablen auslesen

GET-Variablen sind Variablen, die über die URL übergeben werden. Das kann einerseits manuell durch eintippen der URL im Browser geschehen, oder durch andere Programme oder Geräte. Grundlegend lässt sich sagen, dass GET-Variablen im Webbrowser immer an einem `?` erkennbar sind. Anschließend folgen die Variablen mit ihren Inhalten.

Wenn man die URL [http://example.com?variable=wert](https://web.archive.org/web/20221202005451/http://example.com/?variable=wert) aufruft, wird die Variable `variable` mit dem Inhalt `wert` übergeben. Man kann auch mehrere Variablen übergeben, diese werden dann mit einem `&` voneinander getrennt.

[http://example.com/?name=cooper&alias=brain](https://web.archive.org/web/20221202005451/http://example.com/?name=cooper&alias=brain)
Variable: `name = cooper`
Variable: `alias = brain`

GET-Variablen kann man immer und überall übergeben - macht meistens aber keinen Sinn. **Erst wenn der Server dementsprechend programmiert ist, werden die übergebenen Daten verarbeitet.** Und genau das macht dieser Code.

------

##  Beispiel

Auf dem [ESP8266](https://web.archive.org/web/20221202005451/https://makesmart.shop/loadBasket/5uu5PEm-mLA) läuft ein Webserver. Die IP-Adresse meines ESPs lautet `192.168.178.47`.

Beim Aufruf der URL des ESPs wird die Variable `name` übergeben. Der Inhalt der Variable wird vom ESP ausgelesen und kann dann verarbeitet werden. Im Beispiel ist die Verarbeitung die einfache Ausgabe im Webbrowser und im seriellen Monitor.

[http://192.168.178.47/?name=cooper](https://web.archive.org/web/20221202005451/http://192.168.178.47/?name=cooper)
Output: `Name: cooper`

[http://192.168.178.47/?name=makesmart](https://web.archive.org/web/20221202005451/http://192.168.178.47/?name=makesmart)
Output: `Name: makesmart`

[http://192.168.178.47/?name=gehtdichgarnixan](https://web.archive.org/web/20221202005451/http://192.168.178.47/?name=gehtdichgarnixan)
Output: `Name: gehtdichgarnixan`

##  Code

```arduino
/*
    HTTP-GET Request verarbeiten
    Basic-Code für GET-Request Verarbeitung auf dem ESP8266
    Created by cooper, 2020
    makesmart.net
*/

#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
#include <ESP8266mDNS.h>

ESP8266WebServer server(80);

const char* ssid = "WLAN_SSID";
const char* password = "WLAN_PASS";


void setup()
{

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

    // Es wird keine Seite definiert, sodass bei jeder URL IMMER die Funktion
    // get_hook() aufgerufen wird
    
    get_hook();
    
  });


  server.begin();
  
  Serial.println("Webserver gestartet.");
}



void loop() {
  server.handleClient();  
}


void get_hook() {

    if(server.hasArg("name")){

      // Variable "name" wird übergeben
      Serial.println("Variable 'name' übergeben!");

      // Anschließend die Prüfung, ob die Variable 'name' leer ist:
      
      if(server.arg("name") != ""){
        
        // Falls Variable 'name' nicht leer ist:
        Serial.println(server.arg("name"));
        
        // Ausgabe im Webbrowser HTTP-Code 200: Ok
        server.send(200, "text/plain", "Name: " + server.arg("name"));

        
      }else{
        
        // Wenn Variable die 'name' übergeben wurde, aber leer ist:
        // Ausgabe im Webbrowser HTTP-Code 200: Ok
        server.send(200, "text/plain","Name ist da! .. Aber ohne Inhalt.");  
        
      }
      


    
    }else{
      //Wenn gar keine Variablen übergeben wurden
      server.send(200, "text/plain", "Es wurden keine Variablen übergeben"); 
    }



}
```

##  Einstellungen

Das übliche. Wie immer die WLAN-Daten:

```c++
const char* ssid = "SSID";
const char* password = "PSK";
```

Es wurde keine spezielle Seite definiert. Es wird immer die Funktion get_hook() aufgerufen:

```c++
server.onNotFound([](){
    
    get_hook();
    
});
```

Natürlich kann der Webserver auch nur bei bestimmten Links die Funktion aufrufen:

```c++
server.on("/set_data", []() {
    get_hook();
});
```

[http://192.168.178.47/set_data?name=cooper](https://web.archive.org/web/20221202005451/http://192.168.178.47/set_data?name=cooper)
Output: `Name: cooper`

------

##  Anwendung

GET-Variablen könnten z.B Variablen auf dem ESP überschreiben, um Parameter im laufenden Betrieb zu ändern. Mit dem Link

[http://192.168.178.47/?delay=5000](https://web.archive.org/web/20221202005451/http://192.168.178.47/?delay=5000)

könnte zum Beispiel der `delay()` des Blink-Programmes geändert werden.

------

[In einem Projekt von mir](https://web.archive.org/web/20221202005451/https://my.makesmart.net/topic/30/mister-t-zitronentee-spender-diy) habe ich GET-Variablen verwendet, um Zeiten und Abläufe in einem Programm im laufenden Betrieb zu ändern.

------

GET-Variablen können auch dazu dienen, Funktionen aufzurufen um I/O-Pins zu schalten oder andere Prozesse & Abläufe einzuleiten.

[http://192.168.178.47/?timer=50](https://web.archive.org/web/20221202005451/http://192.168.178.47/?timer=50)
[http://192.168.178.47/?delay=300](https://web.archive.org/web/20221202005451/http://192.168.178.47/?delay=300)
[http://192.168.178.47/?sensor=active](https://web.archive.org/web/20221202005451/http://192.168.178.47/?sensor=active)

------

Mit so ziemlich jeder Programmiersprache kann man HTTP-GET Requests senden. Unter anderem [auch mit einem ESP](https://web.archive.org/web/20221202005451/https://my.makesmart.net/topic/67/esp8266-http-get-request).

**Diese Methode ist eine sehr einfache Möglichkeit, den ESP mit anderer Software & auch Hardware zu verbinden um mit variablen Werten zu arbeiten.**

\#ESP8266 #HTTP #Request #D1-Mini #Arduino-IDE

------

Viel Spaß mit euren Projekten,

*cooper*