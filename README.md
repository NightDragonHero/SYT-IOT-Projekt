# ITP Projekt IoT und ESP-NOW

## Wetterstation

Verfasser: **Niklas Prager, Andjela Zlatkovic**

Datum: **16.05.2026**

## 1. Einführung

Im Bereich des Internets der Dinge (IOT) werden zunehmend Mikrocontroller eingesetzt, um Sensordaten zu erfassen und drahtlos zu übertragen. Eine effiziente und energiearme Kommunikationsmethode stellt dabei ESP-NOW dar, welches eine direkte Kommunikation zwischen ESP-Geräten ohne klassisches WLAN ermöglicht.

In diesem Projekt wird ein System entwickelt, das als eine kleine Wetterstation dienen soll. 


## 2. Projektbeschreibung

Im Rahmen dieses Projekts wurde eine IoT-basierte Wetterstation realisiert. Ein ESP32 misst mithilfe eines Temperratur-Luftdruck-Sensors, eines  Ultraschallsensors, und eines Heligkeitssensor Daten und gibt diese an den zweiten ESP32 weiter. Dieser zeigt diese Messdaten lokal auf einem OLED-Display, sowie über visuelle und akustische Signale an. Schlussendlich werden die Messdaten noch auf einer Website dargestellt.



## 3. Theorie

Das Internet der Dinge (IoT) beschreibt die Vernetzung von Geräten, die über Sensoren verfügen und Daten erfassen, verarbeiten und austauschen können. Mikrocontroller wie der ESP32 spielen dabei eine zentrale Rolle, da sie kostengünstig, energieeffizient und vielseitig einsetzbar sind.

ESP-NOW ist ein Kommunikationsprotokoll, das von Espressif entwickelt wurde. Es ermöglicht die direkte drahtlose Kommunikation zwischen mehreren ESP-Geräten ohne die Notwendigkeit eines WLAN-Routers. Dadurch werden geringe Latenzzeiten und ein niedriger Energieverbrauch erreicht.

Zur Abstandsmessung wird ein Ultraschallsensor verwendet. Dieser sendet Schallwellen aus und misst die Zeit, bis das Echo zurückkommt. Anhand dieser Zeit kann die Entfernung zum Objekt berechnet werden (in cm hier)

Zusätzlich werden Ausgabekomponenten wie ein OLED-Display, eine RGB-LED sowie ein akustischer Signalgeber verwendet, um die gemessenen Daten für den Benutzer verständlich darzustellen

RGB-LED 
HC-SR04 (Ultraschallsensor)
Relais B02
TMB12A05 (Buzzer)
2x ESP 32
DST-015 (Bildschirm)


BMP280:
Der Bosch BMP280 ist ein digitaler Umgebungssensor, der Temperatur (−40 bis +85 °C) und absolute Luftdruck (300–1100 hPa) misst.

### Bauteile (Kurzbeschreibung)

**RGB-LED:** Eine RGB-LED vereint rote, grüne und blaue LEDs in einem Gehäuse. Durch das separate Ansteuern der drei Kanäle (z. B. per PWM) lassen sich verschiedene Farben mischen, wodurch sie sich gut als Status- oder Zustandsanzeige eignet.

**DST-015 (Distanzsensor):** Der DST-015 misst die Entfernung zu einem Objekt und gibt ein entsprechendes Signal aus. Damit lässt sich erkennen, ob sich ein Hindernis im Messbereich befindet und ob Messungen durch nahe Objekte beeinflusst werden könnten.

BMP280:**
Der Bosch BMP280 ist ein digitaler Umgebungssensor, der Temperatur (−40 bis +85 °C) und absolute Luftdruck (300–1100 hPa) misst.

## 4. Arbeitsschritte (Anleitung zur Durchführung)

Im Folgenden werden die Arbeitsschritte beschrieben, um das Projekt (IoT‑Wetterstation mit ESP‑NOW, Anzeige, Weboberfläche und Aktoren) nachzubauen. Das System besteht aus **zwei ESP32**:

- **ESP32 #1 (Sender / Sensor-Node):** misst Temperatur + Luftdruck (BMP280), Distanz (HY‑SRF05) und Tag/Nacht (Lichtsensor) und sendet die Werte per **ESP‑NOW**.
- **ESP32 #2 (Empfänger / Anzeige-Node):** empfängt die Daten, zeigt sie auf einem **OLED** an, erstellt einen **Access Point mit Webserver** und steuert **LEDs** + **Relais**.

---

### 4.1 Benötigte Komponenten

**Hardware:**
- 2× ESP32
- BMP280 (I2C) – Temperatur/Luftdruck
- HY‑SRF05 (Ultraschall‑Distanzsensor)
- Lichtsensor-Modul mit Digitalausgang (DO)
- Buzzer
- OLED SSD1306 (I2C, 128×64)
- 3× LEDs (oder RGB-LED-Kanäle) an GPIO 16/17/18 (Empfänger)
- Relaismodul an GPIO 23 (Empfänger)
- Jumper-Kabel, Breadboard, ggf. Widerstände (für LEDs)

**Software / Libraries (Arduino IDE):**
- ESP32 Board Support (Arduino IDE Boardverwalter)
- `Adafruit_BMP280`, `Adafruit_Sensor`
- `Adafruit_GFX`, `Adafruit_SSD1306`
- (optional) `Arduino_JSON`
- `WiFi.h` und `esp_now.h` sind im ESP32 Core enthalten

---

### 4.2 Vorbereitung in der Arduino IDE

1. **ESP32 Boardpaket installieren**  
   Arduino IDE → *Werkzeuge* → *Board* → *Boardverwalter* → „ESP32 by Espressif Systems“.

2. **Bibliotheken installieren**  
   Arduino IDE → *Sketch* → *Bibliothek einbinden* → *Bibliotheken verwalten* → die genannten Libraries installieren.

3. **Zwei Sketches anlegen**  
   - Sketch 1: **Sender (Sensor-Node)**
   - Sketch 2: **Empfänger (Anzeige/Webserver-Node)**

---

### 4.3 Verdrahtung – ESP32 #1 (Sensor-Node / Sender)

#### (A) BMP280 (I2C)
Im Code wird ein eigener I2C-Bus verwendet:
- SDA → **GPIO 21**
- SCL → **GPIO 22**
- VCC → 3.3V
- GND → GND

> Hinweis: Im Sender-Code wird `bmp.begin(0x76)` genutzt. Manche BMP280-Module verwenden `0x77`. Falls der Sensor nicht gefunden wird, muss die Adresse angepasst werden.

#### (B) HY‑SRF05 (Ultraschall)
- TRIG → **GPIO 5**
- ECHO → **GPIO 18**
- VCC → (je nach Modul) 5V
- GND → GND

> Hinweis: Der ECHO-Pin kann bei manchen Modulen 5V ausgeben. Um den ESP32 zu schützen, kann ein Spannungsteiler sinnvoll sein.

#### (C) Lichtsensor (DO)
- DO → **GPIO 26**
- VCC → 3.3V/5V (je nach Modul)
- GND → GND

Im Code gilt: **LOW = dunkel**, daher:
- `istNacht = (digitalRead(LICHTSENSOR_PIN) == LOW);`

#### (D) Buzzer
- Signal → **GPIO 27**
- GND → GND

---

### 4.4 Inbetriebnahme – Sender testen

1. Sender-Sketch auf **ESP32 #1** flashen.
2. Seriellen Monitor öffnen (**115200 Baud**).
3. Prüfen, ob alle 6 Sekunden Messwerte ausgegeben werden:
   - Temperatur (°C)
   - Druck (hPa)
   - Distanz (cm) – bei Timeout `-1.0`
   - Tag/Nacht

**Fehlersuche:**
- Ausgabe „BMP280 nicht gefunden!“ → I2C-Verdrahtung prüfen, Adresse 0x76/0x77 testen.

**Buzzer-Logik:**
- Wenn das ESP‑NOW Senden fehlschlägt, piept der Buzzer ca. **3× pro Sekunde**.

---

### 4.5 Verdrahtung – ESP32 #2 (Empfänger / Anzeige-Node)

#### (A) OLED SSD1306 (I2C)
Im Code:
- `I2C_Display.begin(33, 19);`  
  → SDA = **GPIO 33**, SCL = **GPIO 19**
- Display-Adresse: **0x3C**

Anschluss:
- SDA → **GPIO 33**
- SCL → **GPIO 19**
- VCC → 3.3V
- GND → GND

#### (B) LEDs (Temperatur-Anzeige)
Im Empfänger-Code werden drei Pins genutzt:
- LED 1 → **GPIO 16**
- LED 2 → **GPIO 17**
- LED 3 → **GPIO 18**

> Hinweis: Bitte LEDs mit geeignetem Vorwiderstand (z. B. 220–330 Ω) betreiben.

#### (C) Relais
- IN → **GPIO 23**
- VCC/GND entsprechend Relaismodul

---

### 4.6 Inbetriebnahme – Empfänger (Anzeige/Webserver)

1. Empfänger-Sketch auf **ESP32 #2** flashen.
2. Der ESP32 startet als **Access Point**:
   - SSID: `ESP32_Anzeige`
   - Passwort: `12345678`
3. Mit Handy/Laptop verbinden.
4. Im Browser die Weboberfläche aufrufen (typisch):
   - `http://192.168.4.1/`

Auf dem OLED werden angezeigt:
- Temperatur, Druck, Distanz
- Lichtstatus (TAG/NACHT)
- LED-Modus (AUTO/AUS)
- Relais-System (AKTIV/AUS)

---

### 4.7 ESP‑NOW Datenübertragung prüfen

1. Sender und Empfänger einschalten.
2. Prüfen, ob am Empfänger Live-Daten ankommen (Display oder Webinterface).
3. Falls keine Daten ankommen: MAC-Adresse prüfen.

> Wichtig: Im Sender ist `broadcastAddress[]` fest vorgegeben. Diese Adresse muss zum Empfänger passen (oder korrekt als Broadcast genutzt werden).

---

### 4.8 Funktionslogik (Software)

**Sendeintervall:** alle **6 Sekunden** werden neue Sensordaten vom Sender übertragen.

**LED-Automatik (Empfänger, nur wenn `ledAutomatik == true`):**
- Temperatur > 25°C → Pin 16 an (z. B. „warm“)
- Temperatur < 15°C → Pin 17 an (z. B. „kalt“)
- sonst → Pin 18 an (z. B. „normal“)

**Relais-System (Empfänger, nur wenn `relaisSystemAktiv == true`):**
- Objekt näher als 50 cm → schnelles Schalten (200 ms)
- Objekt weiter weg → langsames Schalten (1000 ms)

**Weboberfläche:**
- Zeigt Live-Daten und Status an.
- Buttons schalten:
  - LED Automatik EIN / Alle LEDs AUS
  - Relais-System EIN / AUS
- Die Seite aktualisiert ca. alle 6 Sekunden über `/chart-data`.

---

### 4.9 Abschluss / Checkliste

- [ ] BMP280 liefert plausible Werte
- [ ] Distanzsensor liefert Werte (nicht dauerhaft `-1.0`)
- [ ] Lichtsensor schaltet auf „NACHT“, wenn abgedeckt
- [ ] Empfänger zeigt Werte auf OLED an
- [ ] Webinterface erreichbar über AP (`ESP32_Anzeige`)
- [ ] LED-Automatik reagiert auf Temperaturbereiche
- [ ] Relais reagiert auf Distanz und lässt sich per Web deaktivieren
- [ ] Buzzer piept nur bei ESP‑NOW Sendefehlern


### Code

Sehr wichtig ist es, den verwendeten Code zu dokumentieren.

```c++
unsigned long myTime;

void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.print("Time: ");
  myTime = millis();
  Serial.println(myTime); // Gibt die Zeit seit dem Programmstart aus
  delay(1000);            // Eine Sekunde warten, um keine riesigen Datenmengen zu senden
}
```

Dieser Code muss natürlich auch beschrieben und kommentiert werden.

### Bilder und Schaltungen

<img src="https://i.pinimg.com/originals/22/99/fd/2299fd6a63810585136801a720768cde.jpg" alt="10 beste lustige Tierfotos für Freitag" style="zoom:33%;" />

Bilder können URLs aus dem Internet enthalten oder auch lokale Pfade einbinden. Der Lesende muss aber Zugriff auf das Verzeichnis haben, sonst wird nichts angezeigt.

![richtige Schaltung](data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEA...)

Es können auch [Base64 Encodierte Bilder](https://www.base64-image.de/) verwendet werden. Dies hat den Vorteil, dass Bilder direkt in Markdown gespeichert werden.

![schlechtes Beispiel](data:image/jpeg;base64,/9j/4AAQS.....)

Bildausschnitte sollten mit Bedacht gewählt werden. Schaltungen sollten ansprechend gezeichnet und verständlich dargestellt werden. Leitungsfarben können angepasst werden. Die Aussage des Bildes sollte immer auch besprochen werden.

### Tabellen

| Variable                             | n     | M     | SD   | 1      | 2      | 3     | 4     | 5    | 6     | 7    |
| ------------------------------------ | ----- | ----- | ---- | ------ | ------ | ----- | ----- | ---- | ----- | ---- |
| 1. Internal–external status a        | 3,697 | 0.43  | 0.49 | —      |        |       |       |      |       |      |
| 2. Manager job performance           | 2,134 | 3.14  | 0.62 | −.08** | —      |       |       |      |       |      |
| 3. Starting salary b                 | 3,697 | 1.01  | 0.27 | .45**  | −.01   | —     |       |      |       |      |
| 4. Subsequent promotion              | 3,697 | 0.33  | 0.47 | .08**  | .07**  | .04*  | —     |      |       |      |
| 5. Organizational tenure             | 3,697 | 6.45  | 6.62 | −.29** | .09**  | .01   | .09** | —    |       |      |
| 6. Unit service performance c        | 3,505 | 85.00 | 6.98 | −.25** | −.39** | .24** | .08** | .01  | —     |      |
| 7. Unit financial performance c      | 694   | 42.61 | 5.86 | .00    | −.03   | .12*  | −.07  | −.02 | .16** | —    |

Auch die Aussage der Tabelle muss ausformuliert werden.

### Text

*"Eine **Technische Dokumentation** (auch Technikdokumentation oder Produktdokumentation) umfasst alle Informationsprodukte, die ein technisches Erzeugnis beschreiben und zu seiner Nutzung, Wartung oder Reparatur anleiten. Sie bereitet die Informationen systematisch auf und strukturiert sie so, dass der jeweilige Zweck vollständig erfüllt wird."* [5]

Text aus anderen Quellen kann gerne verwendet werden, muss aber immer richtig zitiert werden, da das sonst als Plagiat gewertet wird. Die Aussage des zitierten Textausschnitts sollte auch immer zusätzlich besprochen werden.

## 5. Zusammenfassung

Das Projekt soll hier in wenigen Sätzen zusammengefasst werden. Auch Schwierigkeiten und Fehler bei der Durchführung sind wichtig zu dokumentieren. Wenn es gröbere Probleme gab, sollten diese schon vorher (zum Beispiel in den Arbeitsschritten), kleinere in der Zusammenfassung beschrieben werden.

Im nächsten Abschnitt sind noch die Quellen anzugeben. Alles, was nicht vom Autor selbst erzeugt wurde, ist in einer Dokumentation zu zitieren und in den Quellen anzugeben. Es ist wichtig, immer festzuhalten, woher eine Information stammt, um diese gegebenenfalls nachprüfen zu können. Die meisten Informationen stammen in dieser Zeit aus dem Internet. Hier reicht es aber nicht nur, die URL anzugeben. Titel, Autor und vor allem das Datum, wann die URL das letzte Mal überprüft wurde, sind wichtig anzugeben (da sich Informationen im Internet auch ändern können).

Es gibt viele Standards, richtig zu zitieren; in der Technik wird jedoch der [IEEE Standard](https://ieeeauthorcenter.ieee.org/wp-content/uploads/IEEE-Reference-Guide.pdf) [6] bevorzugt. Wie hier sichtbar ist, wird dazu im Text eine eckige Klammer gesetzt, welche zu den Quellen am Ende führt. Der IEEE Standard ist sehr ausführlich, hier gibt es ein paar [Beispiele](https://pitt.libguides.com/citationhelp/ieee) [7]. Wir können uns das Leben aber mit diesem [Citation Generator](https://www.citethisforme.com/) [8] leichter machen.

## 6. Quellen

[1] Adam-P, “Markdown cheatsheet · Adam-P/markdown-here wiki,” *GitHub*. [Online]. Available: <https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet>. [Accessed: 05-Feb-2022].

[2] GitHub Docs, *Basic writing and formatting syntax - GitHub Docs*. [Online]. Available: <https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-syntax>. [Accessed: 05-Feb-2022].

[3] Markdowntutorial.com, *Markdown Tutorial*. [Online]. Available: <https://www.markdowntutorial.com/>. [Accessed: 05-Feb-2022].

[4] Markdownguide.org, *Markdown Guide*. [Online]. Available: <https://www.markdownguide.org/>. [Accessed: 05-Feb-2022].

[5] De.wikipedia.org, *Technische Dokumentation – Wikipedia*. [Online]. Available: <https://de.wikipedia.org/wiki/Technische_Dokumentation>. [Accessed: 05-Feb-2022].

[6] Ieeeauthorcenter.ieee.org, *IEEE Reference Guide*. [Online]. Available: <https://ieeeauthorcenter.ieee.org/wp-content/uploads/IEEE-Reference-Guide.pdf>. [Accessed: 05-Feb-2022].

[7] Pitt.libguides.com, *Citation Styles: IEEE Style*. [Online]. Available: <https://pitt.libguides.com/citationhelp/ieee>. [Accessed: 05-Feb-2022].

[8] Cite This For Me, *Save Time and Improve your Marks with CiteThisForMe*. [Online]. Available: <https://www.citethisforme.com/>. [Accessed: 05-Feb-2022].
