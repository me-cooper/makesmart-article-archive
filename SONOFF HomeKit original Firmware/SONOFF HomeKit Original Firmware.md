# SONOFF Geräte mit der original Firmware via HomeKit steuern - Tutorial

Wenn es darum geht, ein SONOFF Gerät in HomeKit oder mit einer anderen Smarthome-Plattform außerhalb der eWeLink-App zu steuern, wird oftmals auf die custom Firmware Tasmota zurückgegriffen. Dabei gibt es aber mehrere Hürden: Das Flashen der Firmware unterscheidet sich von Modell zu Modell und gestaltet sich allgemein immer noch schwieriger. Damit ist jetzt Schluss - denn ich zeige euch eine Möglichkeit, wie ihr SONOFF Geräte trotz original-Firmware mit HomeKit steuern könnt.

###  DIE HOMEBRIDGE AUF DEM MAKESMART-SERVER

Realisieren werde ich das ganze mithilfe der Homebridge auf meinem makesmart-server. Ich nutze schon seit gut zwei Jahren Homebridge als Zentrale, um meine Geräte in HomeKit einzubinden. Für die Homebridge gibt es ganz frisch ein Plugin namens [homebridge-ewelink](https://web.archive.org/web/20211202132838/https://www.npmjs.com/package/homebridge-ewelink).

Wie der Name schon erahnen lässt, lassen sich mithilfe des Plugins die SONOFF Geräte auch mit der original Firmware in HomeKit einbinden.

Dafür muss das Plugin zuerst installiert werden, ich installiere es über die homebridge-config-ui-x indem ich nach dem Begriff `ewelink` suche.

[![homebridge-ewelink Plugin installieren](https://web.archive.org/web/20211202132838im_/https://my.makesmart.net/assets/uploads/files/1600452266196-homebridge-ewelink-plugin-installieren.png)](https://web.archive.org/web/20211202132838mp_/https://my.makesmart.net/assets/uploads/files/1600452266196-homebridge-ewelink-plugin-installieren.png)

###  HOMEBRIDGE-EWELINK KONFIGURATION

Nach dem Installieren taucht ein Pop-Up auf, in dem ihr einmal den Ländercode (49) und die Zugangsdaten zu eurem eWeLink Account eingeben müsst.

[![homebridge-ewewlink Plugin Einstellungen](https://web.archive.org/web/20211202132838im_/https://my.makesmart.net/assets/uploads/files/1600452326429-homebridge-ewewlink-plugin-einstellungen.png)](https://web.archive.org/web/20211202132838mp_/https://my.makesmart.net/assets/uploads/files/1600452326429-homebridge-ewewlink-plugin-einstellungen.png)

Die Konfiguration für das Plugin wird danach automatisch erstellt. Die eingegeben Daten werden dazu verwendet, die Konfiguration des Plugins in die config.json einzutragen. Ein Blick in die config.json verrät, was eingetragen wurde:

```json
{
            "name": "eWeLink",
            "countryCode": "49",
            "username": "deine@mail.de",
            "password": "deinpasswort",
            "debug": false,
            "debugReqRes": false,
            "sensorTimeLength": 60,
            "sensorTimeDifference": 120,
            "valveTimeLength": 20,
            "hideTHSwitch": false,
            "hideZBLDPress": false,
            "groups": [
                {
                    "type": "null",
                    "setup": "null",
                    "operationTime": 100
                }
            ],
            "bridgeSensors": [
                {
                    "type": "motion"
                }
            ],
            "platform": "eWeLink"
        }
```

------

Einfacher geht es wohl kaum - zumindest hab ich das selten erlebt.

Als nächstes muss die Homebridge einmal neugestartet werden.

###  SONOFF GERÄTE IN HOMEKIT EINGEBUNDEN

Nach dem Neustarten verrät die Ausgabe der Homebridge-Konsole, dass die SONOFF-Geräte in der eWeLink-App automatisch erkannt und erfolgreich über die lokale IP-Adresse in Homebridge eingebunden wurden.

```shell
[9/18/2020, 18:33:48] [eWeLink] [0] eWeLink devices were loaded from the Homebridge cache.
[9/18/2020, 18:33:48] [eWeLink] [1] primary devices were loaded from your eWeLink account.
[9/18/2020, 18:33:48] [eWeLink] [1] primary devices were discovered on your local network.
[9/18/2020, 18:33:48] [eWeLink] [Gerätd1f401] has been added to Homebridge.
[9/18/2020, 18:33:48] [eWeLink] [Gerätd1f401] found in eWeLink and locally with IP [192.168.178.98].
```

Nach einem kurzen Moment taucht mein SONOFF Basic dann auch als Schalter in der Home App auf. Mein Gerät trägt den Namen **Gerätd1f401**. Dieser wurde aus der eWeLink App übernommen. Der Name kann beliebig direkt in der Home App angepasst werden.

Ich kann ihn nun per Siri steuern, in Automationen einbinden oder für Szenen verwenden.

[![SONOFF Basic HomeKit Schalter](https://web.archive.org/web/20211202132838im_/https://my.makesmart.net/assets/uploads/files/1600452902868-sonoff_basic_homekit_schalter.png)](https://web.archive.org/web/20211202132838mp_/https://my.makesmart.net/assets/uploads/files/1600452902868-sonoff_basic_homekit_schalter.png)