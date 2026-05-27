# IoT und ESP-NOW

## Wetterstation

Verfasser: **Niklas Prager, Andjela Zlatkovic**

Datum: **27.05.2026**

## 1. Einführung

Im Bereich des Internets der Dinge (IOT) werden zunehmend Mikrocontroller eingesetzt, um Sensordaten zu erfassen und drahtlos zu übertragen. Eine effiziente und energiearme Kommunikationsmethode stellt dabei ESP-NOW dar, welches eine direkte Kommunikation zwischen ESP-Geräten ohne klassisches WLAN ermöglicht.

In diesem Projekt wird ein System entwickelt, das als eine kleine Wetterstation dienen soll. 


## 2. Projektbeschreibung

Im Rahmen dieses Projekts wurde eine IoT-basierte Wetterstation realisiert. Ein ESP32 misst mithilfe eines Temperratur-Luftdruck-Sensors, eines  Ultraschallsensors, und eines Heligkeitssensor Daten und gibt diese an den zweiten ESP32 weiter. Dieser zeigt diese Messdaten lokal auf einem OLED-Display, sowie über visuelle und akustische Signale an. Schlussendlich werden die Messdaten noch auf einer Website dargestellt.



## 3. Theorie

Das Internet der Dinge (IoT) beschreibt die Vernetzung von Geräten, die über Sensoren verfügen und Daten erfassen, verarbeiten und austauschen können. Mikrocontroller wie der ESP32 spielen dabei eine zentrale Rolle, da sie kostengünstig, energieeffizient und vielseitig einsetzbar sind.

ESP-NOW ist ein Kommunikationsprotokoll, das von Espressif entwickelt wurde. Es ermöglicht die direkte drahtlose Kommunikation zwischen mehreren ESP-Geräten ohne die Notwendigkeit eines WLAN-Routers. Dadurch werden geringe Latenzzeiten und ein niedriger Energieverbrauch erreicht.

Zur messung von Daten und ausgeben dieser in verschiedenen Formen sind folgende Bauteile zu benutzen:


### Bauteile 

## RGB‑LED
Eine RGB‑LED vereint **rote, grüne und blaue Leuchtdioden** in einem Gehäuse. Durch PWM‑Ansteuerung der drei Kanäle lassen sich **beliebige Farben** erzeugen.

---

## BMP280:
Der Bosch BMP280 ist ein digitaler Umgebungssensor, der Temperatur (−40 bis +85 °C) und absolute Luftdruck (300–1100 hPa) misst.

---

## HC‑SR04 (Ultraschallsensor)
Der HC‑SR04 misst **Abstände von ca. 2–400 cm** mittels Ultraschall‑Echo. Er sendet einen 40‑kHz‑Impuls aus und berechnet aus der Echo‑Laufzeit die Entfernung.

---

## Relaismodul
Ein Relaismodul ermöglicht das **Schalten von Lasten** über eine **galvanisch getrennte Steuerseite**. Es wird genutzt, um höhere Spannungen oder Ströme sicher zu schalten, hier wird es aber wegen des "klickens" verwendet.

---

## TMB12A05 (Buzzer)
Der TMB12A05 ist ein **aktiver 12‑V‑Buzzer**, der beim Anlegen der Betriebsspannung einen **festen Ton** erzeugt. Er benötigt kein externes Signal und dient als akustischer Alarmgeber.

---

## ESP32 (2×)
Der ESP32 ist ein **leistungsstarker Mikrocontroller** mit **WLAN/Bluetooth**, Dual‑Core‑CPU und vielen GPIO‑Pins. Er eignet sich für IoT‑Projekte, Sensorik und Echtzeit‑Anwendungen.

---

## DST‑015 (Display)
Das DST‑015 ist ein **1,5‑Zoll‑TFT‑Display** mit **128×128 Pixeln**, das über SPI angesteuert wird. Es eignet sich zur Darstellung von Text, Icons und einfachen Ausgaben.

---

## DHT11 (Temperatur- und Feuchtigkeitssensor)
Der DHT11 misst **Temperatur (0–50 °C)** und **relative Luftfeuchtigkeit (20–90 %)**. Er liefert digitale Messwerte und eignet sich für einfache Umgebungsüberwachungen.

---

## Haljia Lichtsensor (Fotowiderstand / LDR-Modul)
Der Haljia‑Lichtsensor nutzt einen **Fotowiderstand (LDR)**, dessen Widerstand sich je nach Umgebungshelligkeit ändert. Er ermöglicht eine einfache **Helligkeitsmessung** über einen analogen Eingang.

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
- Jumper-Kabel, Breadboard, ggf. Widerstände und weiteres
- DHT11 (Luftfeuchtigkeits- und Temoerratur- Sensor)
- Button

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

### (E) Button
- Eingang → **GPIO 25**
- GND → GND

### (F) DHT11
- Data → **GPIO 32**
- VCC → 3.3V/5V (je nach Modul)
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
   - Luftfeuchtigkeit

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

## Mess-ESP-Code

```c++
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP280.h>
#include <WiFi.h>
#include <esp_now.h>
#include <DHT.h>

// =====================================================
// =============== SENSOR-HARDWARE SETUP ===============
// =====================================================

// BMP280 (I2C, Temperatur + Luftdruck)
TwoWire I2C_BMP = TwoWire(1);
Adafruit_BMP280 bmp(&I2C_BMP);

// DHT11 (Digital, Temperatur + Luftfeuchtigkeit)
#define DHT_PIN 32
#define DHT_TYPE DHT11
DHT dht(DHT_PIN, DHT_TYPE);

// Ultraschall-Sensor HY-SRF05 (Distanz)
#define TRIG_PIN 5
#define ECHO_PIN 18

// Lichtsensor (HALJIA / LM393 DO)
// Hinweis: Manche Module sind invertiert -> ggf. DO_HIGH_MEANS_DARK umstellen.
#define LICHTSENSOR_PIN 26
const bool DO_HIGH_MEANS_DARK = true;
const int lichtSamples = 6;
const int lichtDelayMs = 2;
const float nightThreshold = 0.5f;

// =====================================================
// =================== AKTOREN / UI ====================
// =====================================================

// Buzzer: warnt, wenn ESP-NOW Send nicht erfolgreich ist (nur wenn systemSleep AUS)
#define BUZZER_PIN 27
bool send_ok = true;
unsigned long lastBeep = 0;
const unsigned long beepInterval = 333;

// Button: toggelt System-Sleep für beide ESPs
// Verdrahtung: BUTTON_PIN -> Taster -> GND (INPUT_PULLUP)
#define BUTTON_PIN 25
bool systemSleep = false;

// Entprellung Button
bool lastButtonReading = HIGH;
bool stableButtonState = HIGH;
unsigned long lastDebounceTime = 0;
const unsigned long debounceMs = 40;

// =====================================================
// ===================== ESP-NOW =======================
// =====================================================

// MAC-Adresse vom Empfänger (Anzeige-ESP)
uint8_t broadcastAddress[] = {0x00, 0x70, 0x07, 0x26, 0xAA, 0x48};

// WICHTIG:
// - Muss 1:1 identisch auf Empfänger und Sender sein (Reihenfolge, Typen)
// - uint8_t statt bool für Flags ist robuster bei Struct/Alignment.
typedef struct __attribute__((packed)) struct_message {
  float tempAvg;     // Durchschnitt (BMPavg + DHTavg) / 2
  float pres;        // Luftdruck Ø
  float dist;        // Distanz Ø
  uint8_t istNacht;  // 1 = Nacht, 0 = Tag
  uint8_t systemSleep; // 1 = Sleep an, 0 = Sleep aus
  float humidity;    // Luftfeuchte Ø
  float tempBmp;     // BMP Temperatur Ø
  float tempDht;     // DHT Temperatur Ø
} struct_message;

struct_message sensorData;
esp_now_peer_info_t peerInfo;

// Send Callback: hier merken wir uns, ob der Send erfolgreich war
void OnDataSent(const wifi_tx_info_t *info, esp_now_send_status_t status) {
  send_ok = (status == ESP_NOW_SEND_SUCCESS);
}

// =====================================================
// ============ MESS-LOGIK: 6x messen -> Ø =============
// =====================================================

// Jede Sekunde ein Sample:
const unsigned long sampleIntervalMs = 1000;
unsigned long lastSampleMillis = 0;

// Nach 6 Samples wird ein Paket geschickt (ca. alle 6 Sekunden)
const int samplesPerBatch = 6;
int sampleCount = 0;

// Summen für Mittelwerte
float sumBmpTemp = 0, sumDhtTemp = 0, sumPres = 0, sumHum = 0, sumDist = 0;
int countDistValid = 0;

// Tag/Nacht Mehrheit
int nightCount = 0, nightValid = 0;

// DHT11 liefert manchmal NaN -> wir merken uns den letzten gültigen Wert
float lastGoodHum = 0, lastGoodTempDht = 0;

// ---------------- Button Toggle ----------------
void handleButtonToggle() {
  bool reading = digitalRead(BUTTON_PIN);

  if (reading != lastButtonReading) {
    lastDebounceTime = millis();
    lastButtonReading = reading;
  }

  if ((millis() - lastDebounceTime) > debounceMs) {
    if (reading != stableButtonState) {
      stableButtonState = reading;

      // FALLING edge: Button gedrückt (HIGH -> LOW)
      if (stableButtonState == LOW) {
        systemSleep = !systemSleep;
        Serial.print("SYSTEM SLEEP TOGGLE: ");
        Serial.println(systemSleep ? "AN" : "AUS");

        // kurzer Feedback-Beep
        digitalWrite(BUZZER_PIN, HIGH);
        delay(60);
        digitalWrite(BUZZER_PIN, LOW);
      }
    }
  }
}

// ---------------- Lichtmessung (schnell) ----------------
bool leseIstNachtSchnell() {
  int darkCount = 0;
  for (int i = 0; i < lichtSamples; i++) {
    int raw = digitalRead(LICHTSENSOR_PIN);
    bool isDark = DO_HIGH_MEANS_DARK ? (raw == HIGH) : (raw == LOW);
    if (isDark) darkCount++;
    delay(lichtDelayMs);
  }
  return ((float)darkCount / (float)lichtSamples) >= nightThreshold;
}

// ---------------- Distanzmessung ----------------
float leseDistanzCm() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long dauer = pulseIn(ECHO_PIN, HIGH, 30000);
  if (dauer <= 0) return -1.0f;
  return (dauer * 0.0343f / 2.0f);
}

// ---------------- Batch Reset ----------------
void resetBatch() {
  sampleCount = 0;
  sumBmpTemp = sumDhtTemp = sumPres = sumHum = sumDist = 0;
  countDistValid = 0;
  nightCount = nightValid = 0;
}

// ---------------- ESP-NOW Paket senden ----------------
void sendPacket(float tempAvg, float presAvg, float distAvg, bool istNachtBatch, float humAvg, float bmpAvg, float dhtAvg) {
  sensorData.tempAvg = tempAvg;
  sensorData.pres = presAvg;
  sensorData.dist = distAvg;
  sensorData.istNacht = istNachtBatch ? 1 : 0;
  sensorData.systemSleep = systemSleep ? 1 : 0;
  sensorData.humidity = humAvg;
  sensorData.tempBmp = bmpAvg;
  sensorData.tempDht = dhtAvg;

  esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *)&sensorData, sizeof(sensorData));
  if (result != ESP_OK) Serial.println("Senden fehlgeschlagen (esp_now_send)!");
}

// =====================================================
// ======================== SETUP ======================
// =====================================================
void setup() {
  Serial.begin(115200);
  delay(500);

  // Pins
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(LICHTSENSOR_PIN, INPUT_PULLUP);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  // Sensoren starten
  dht.begin();

  I2C_BMP.begin(21, 22);
  if (!bmp.begin(0x76)) {
    Serial.println("BMP280 nicht gefunden!");
    while (1) {}
  }

  // ESP-NOW starten
  WiFi.mode(WIFI_STA);
  if (esp_now_init() != ESP_OK) {
    Serial.println("ESP-NOW Init fehlgeschlagen.");
    return;
  }
  esp_now_register_send_cb(OnDataSent);

  // Peer (Empfänger) registrieren
  memcpy(peerInfo.peer_addr, broadcastAddress, 6);
  peerInfo.channel = 0;
  peerInfo.encrypt = false;
  if (esp_now_add_peer(&peerInfo) != ESP_OK) {
    Serial.println("Peer hinzufügen fehlgeschlagen.");
    return;
  }

  resetBatch();
  lastSampleMillis = millis();

  Serial.println("Sender gestartet: 6x Messen (1/s) -> Mittelwerte senden.");
}

// =====================================================
// ========================= LOOP =======================
// =====================================================
void loop() {
  unsigned long now = millis();

  // Button immer prüfen (auch im Sleep)
  handleButtonToggle();

  // Buzzer-Alarm bei Send-Problemen (nur wenn Sleep AUS)
  if (!systemSleep) {
    if (!send_ok && (now - lastBeep >= beepInterval)) {
      lastBeep = now;
      digitalWrite(BUZZER_PIN, HIGH); delay(80); digitalWrite(BUZZER_PIN, LOW);
    }
    if (send_ok) digitalWrite(BUZZER_PIN, LOW);
  } else {
    digitalWrite(BUZZER_PIN, LOW);
  }

  // Jede Sekunde 1 Sample
  if (now - lastSampleMillis < sampleIntervalMs) return;
  lastSampleMillis += sampleIntervalMs;

  // Wenn SystemSleep aktiv: nur alle 6 Sekunden ein Statuspaket senden,
  // damit der Empfänger im Sleep bleibt und später wieder sofort aufwachen kann.
  if (systemSleep) {
    sampleCount++;
    if (sampleCount >= samplesPerBatch) {
      Serial.println("SystemSleep AN -> sende Statuspaket.");
      sendPacket(0, 0, -1, false, 0, 0, 0);
      resetBatch();
    }
    return;
  }

  // -------- 1 Sample messen --------
  float bmpTemp = bmp.readTemperature();
  float pres = bmp.readPressure() / 100.0f;

  float dhtTemp = dht.readTemperature();
  float hum = dht.readHumidity();

  // DHT Fehler abfangen
  if (!isnan(dhtTemp)) lastGoodTempDht = dhtTemp;
  if (!isnan(hum)) lastGoodHum = hum;
  dhtTemp = lastGoodTempDht;
  hum = lastGoodHum;

  float dist = leseDistanzCm();
  bool nacht = leseIstNachtSchnell();

  // -------- Werte aufsummieren --------
  sumBmpTemp += bmpTemp;
  sumDhtTemp += dhtTemp;
  sumPres += pres;
  sumHum += hum;

  if (dist > 0) { sumDist += dist; countDistValid++; }

  if (nacht) nightCount++;
  nightValid++;

  sampleCount++;

  // -------- Nach 6 Samples: Mittelwerte senden --------
  if (sampleCount >= samplesPerBatch) {
    float bmpAvg = sumBmpTemp / samplesPerBatch;
    float dhtAvg = sumDhtTemp / samplesPerBatch;
    float presAvg = sumPres / samplesPerBatch;
    float humAvg  = sumHum  / samplesPerBatch;

    float distAvg = (countDistValid > 0) ? (sumDist / countDistValid) : -1.0f;
    float tempAvg = (bmpAvg + dhtAvg) / 2.0f;
    bool istNachtBatch = (nightCount >= (nightValid / 2 + 1));

    Serial.print("SEND | Temperatur(Ø)="); Serial.print(tempAvg, 1);
    Serial.print(" C | Feuchte(Ø)="); Serial.print(humAvg, 0);
    Serial.print(" % | Druck(Ø)="); Serial.print(presAvg, 0);
    Serial.print(" hPa | Distanz(Ø)="); Serial.print(distAvg, 1);
    Serial.print(" cm | Licht="); Serial.println(istNachtBatch ? "NACHT" : "TAG");

    sendPacket(tempAvg, presAvg, distAvg, istNachtBatch, humAvg, bmpAvg, dhtAvg);
    resetBatch();
  }
}
```


### Der Anzeige ESP

```c++
#include <WiFi.h>
#include <esp_now.h>
#include <WebServer.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// =====================================================
// ====================== DISPLAY ======================
// =====================================================
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
TwoWire I2C_Display = TwoWire(0);
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &I2C_Display, -1);

// =====================================================
// ====================== AKTOREN ======================
// =====================================================
#define RELAY_PIN 23
#define LED_RED 16
#define LED_GREEN 17
#define LED_BLUE 18

// =====================================================
// ================== VERLAUF / HISTORY ================
// =====================================================
const int anzahlDatenpunkte = 10;
float temperaturHistorie[anzahlDatenpunkte];
float druckHistorie[anzahlDatenpunkte];
float feuchteHistorie[anzahlDatenpunkte];
int datenIndex = 0;
bool pufferVoll = false;

// =====================================================
// ====================== STATUS =======================
// =====================================================
bool ledAutomatik = true;
bool relaisSystemAktiv = true;

bool nightStatus = false;
bool systemSleepRx = false;

// Live-Werte (werden nach jedem ESP-NOW Empfang aktualisiert)
float webTempAvg = 0;
float webDruck = 0;
float webDistanz = 0;
float webHumidity = 0;

// =====================================================
// ================== ESP-NOW DATENFORMAT ==============
// =====================================================
// WICHTIG: Muss 1:1 zum Sender passen!
typedef struct __attribute__((packed)) struct_message {
  float tempAvg;
  float pres;
  float dist;
  uint8_t istNacht;
  uint8_t systemSleep;
  float humidity;
  float tempBmp;
  float tempDht;
} struct_message;

struct_message incomingData;

// =====================================================
// ========== ZEIT: seit erster Übertragung ============
// =====================================================
unsigned long firstRxMillis = 0; // wird beim ersten Empfang gesetzt
String lastTimestamp = "--:--";  // mm:ss

String formatElapsedMMSS(unsigned long elapsedMs) {
  unsigned long totalSeconds = elapsedMs / 1000UL;
  unsigned long minutes = totalSeconds / 60UL;
  unsigned long seconds = totalSeconds % 60UL;
  char buf[8];
  snprintf(buf, sizeof(buf), "%02lu:%02lu", minutes, seconds);
  return String(buf);
}

void updateRunningTimestamp() {
  if (firstRxMillis == 0) { lastTimestamp = "--:--"; return; }
  lastTimestamp = formatElapsedMMSS(millis() - firstRxMillis);
}

// =====================================================
// ================== WLAN / WEBSERVER =================
// =====================================================
const char* ap_ssid = "ESP32_Anzeige";
const char* ap_password = "12345678";
WebServer server(80);

// =====================================================
// ================== STANDBY / SLEEPMODE ==============
// =====================================================
// - Timeout: wenn lange kein Sender-Paket kommt -> Standby
// - SystemSleep: wenn Sender Button drückt -> Standby
const unsigned long timeoutMs = 15000;
unsigned long lastDataMillis = 0;
bool standbyMode = false;

void alleAus() {
  // Alles aus, damit Standby wirklich "ruhig" ist
  digitalWrite(LED_RED, LOW);
  digitalWrite(LED_GREEN, LOW);
  digitalWrite(LED_BLUE, LOW);
  digitalWrite(RELAY_PIN, LOW);
}

void enterStandby(const char* reason) {
  if (standbyMode) return;
  standbyMode = true;

  alleAus();

  // OLED aus
  display.clearDisplay();
  display.display();
  display.ssd1306_command(SSD1306_DISPLAYOFF);

  Serial.print("STANDBY EIN: ");
  Serial.println(reason);
}

void exitStandbyIfAllowed() {
  // Aufwachen nur, wenn:
  // - systemSleepRx ist AUS
  // - und wir haben "frische" Daten innerhalb Timeout
  unsigned long now = millis();
  bool rxOk = (lastDataMillis != 0 && (now - lastDataMillis <= timeoutMs));

  if (standbyMode && !systemSleepRx && rxOk) {
    standbyMode = false;
    display.ssd1306_command(SSD1306_DISPLAYON);
    Serial.println("WAKE: Standby AUS");
  }
}

void standbyCheckTimeout() {
  // Button-Sleep hat Vorrang
  if (systemSleepRx) {
    enterStandby("SYSTEM-SLEEP aktiv (Button am Sender)");
    return;
  }

  // Kein Empfang -> Standby
  unsigned long now = millis();
  if (!standbyMode && (lastDataMillis == 0 || (now - lastDataMillis > timeoutMs))) {
    enterStandby("TIMEOUT (kein Sender/kein Empfang)");
  }

  // Falls wieder Empfang da ist, kann man wieder aufwachen
  exitStandbyIfAllowed();
}

// =====================================================
// ======================= WEBSITE =====================
// =====================================================
const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE html><html><head><title>ESP32 Messwerte</title>
<style>
body { font-family: sans-serif; display:flex; flex-direction:column; align-items:center; }
.container { border:1px solid #ccc; padding:15px; margin-bottom:20px; width:80%; max-width:800px; border-radius:8px; }
.chart-box { border:2px solid #444; padding:10px; margin-top:15px; border-radius:6px; }
canvas { width:100%; height:220px; background:#fff; }
button { padding:10px; margin:5px; font-size:16px; border-radius:5px; border:none; color:white; cursor:pointer; }
button.ein { background:#28a745; } button.aus { background:#dc3545; }
</style></head><body>
<h1>ESP32 Messwerte & Steuerung</h1>

<div class="container">
  <h2>Live-Daten</h2>
  <p>Zeit seit erster Uebertragung: <span id="ts">--:--</span></p>
  <p>Temperatur (Ø): <span id="temp">--</span> &deg;C</p>
  <p>Feuchte: <span id="hum">--</span> %</p>
  <p>Druck: <span id="pres">--</span> hPa</p>
  <p>Distanz: <span id="dist">--</span> cm</p>
  <p>Helligkeit: <span id="light">--</span></p>
  <p>Status: <span id="rx">--</span></p>
</div>

<div class="container">
  <h2>Daten der letzten 1 Minute</h2>
  <div class="chart-box"><p><b>Temperatur (Ø)</b></p><canvas id="tempChart" width="600" height="220"></canvas></div>
  <div class="chart-box"><p><b>Luftdruck</b></p><canvas id="presChart" width="600" height="220"></canvas></div>
  <div class="chart-box"><p><b>Luftfeuchtigkeit</b></p><canvas id="humChart" width="600" height="220"></canvas></div>
</div>

<div class="container">
  <h2>LED Steuerung</h2>
  <p>Status: <span id="ledStatus">--</span></p>
  <form action="/ledAuto" method="POST" style="display:inline-block;"><button type="submit" class="ein">Automatik EIN</button></form>
  <form action="/ledAus" method="POST" style="display:inline-block;"><button type="submit" class="aus">Alle LEDs AUS</button></form>
</div>

<div class="container">
  <h2>Relais Steuerung</h2>
  <p>Status: <span id="relaisStatus">--</span></p>
  <form action="/relaisEin" method="POST" style="display:inline-block;"><button type="submit" class="ein">Relais-System EIN</button></form>
  <form action="/relaisAus" method="POST" style="display:inline-block;"><button type="submit" class="aus">Relais-System AUS</button></form>
</div>

<script>
function drawGraph(canvasId, values, color, yLabel, unit, yMin, yMax) {
  const canvas = document.getElementById(canvasId);
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0,0,canvas.width,canvas.height);
  if(!values || values.length === 0) return;
  const w=canvas.width, h=canvas.height, pad=45;

  ctx.strokeStyle="#000"; ctx.lineWidth=2;
  ctx.beginPath(); ctx.moveTo(pad,10); ctx.lineTo(pad,h-pad); ctx.lineTo(w-10,h-pad); ctx.stroke();

  ctx.fillStyle="#000"; ctx.font="12px Arial";
  ctx.fillText("Zeit", w/2-20, h-10);
  ctx.fillText(yLabel, 5, 12);

  for(let t=0;t<=60;t+=6){
    const x=pad+(t/60)*(w-2*pad);
    ctx.beginPath(); ctx.moveTo(x,h-pad); ctx.lineTo(x,h-pad+5); ctx.stroke();
    const label=(t===60)?t+unit.x:t;
    ctx.fillText(label.toString(), x-6, h-pad+18);
  }

  const ySteps=5;
  for(let i=0;i<=ySteps;i++){
    const val=yMin+(i*(yMax-yMin)/ySteps);
    const y=h-pad-(i/ySteps)*(h-2*pad);
    ctx.beginPath(); ctx.moveTo(pad-5,y); ctx.lineTo(pad,y); ctx.stroke();
    const label=(i===ySteps)?val.toFixed(1)+unit.y:val.toFixed(1);
    ctx.fillText(label, 5, y+4);
  }

  ctx.strokeStyle=color; ctx.lineWidth=2;
  ctx.beginPath();
  values.forEach((v,i)=>{
    const time=i*6;
    const x=pad+(time/60)*(w-2*pad);
    const y=h-pad-((v-yMin)/(yMax-yMin))*(h-2*pad);
    if(i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
  });
  ctx.stroke();
}

async function fetchData(){
  try{
    const r = await fetch('/chart-data');
    const d = await r.json();

    document.getElementById('ts').innerText = d.live.ts;
    document.getElementById('temp').innerText = d.live.temp.toFixed(1);
    document.getElementById('hum').innerText = d.live.hum.toFixed(0);
    document.getElementById('pres').innerText = d.live.pres.toFixed(0);
    document.getElementById('dist').innerText = d.live.dist.toFixed(1);
    document.getElementById('light').innerText = d.status.night ? 'NACHT':'TAG';

    document.getElementById('ledStatus').innerText = d.status.ledAutomatik ? "Automatisch":"Manuell Aus";
    document.getElementById('relaisStatus').innerText = d.status.relaisSystemAktiv ? "System AKTIV":"System AUS";

    if (d.status.systemSleep) document.getElementById('rx').innerText = 'SYSTEM-SLEEP (Button)';
    else if (d.status.standby) document.getElementById('rx').innerText = 'STANDBY (kein Empfang)';
    else document.getElementById('rx').innerText = 'OK';

    drawGraph("tempChart", d.history.temp, "#ff6384", "Temperatur", {x:"s", y:"°C"}, 10, 35);
    drawGraph("presChart", d.history.pres, "#36a2eb", "Luftdruck", {x:"s", y:"hPa"}, 950, 1050);
    drawGraph("humChart", d.history.hum, "#4bc0c0", "Feuchte", {x:"s", y:"%"}, 0, 100);
  } catch(e){ console.error(e); }
}
setInterval(fetchData, 6000);
window.onload = fetchData;
</script>
</body></html>
)rawliteral";

// =====================================================
// =================== ESP-NOW RECEIVE =================
// =====================================================
void OnDataRecv(const esp_now_recv_info_t *info, const uint8_t *incomingDataBytes, int len) {
  // Sicherheitscheck: Länge muss exakt passen
  if (len != sizeof(struct_message)) {
    Serial.print("RX len mismatch: ");
    Serial.println(len);
    return;
  }

  memcpy(&incomingData, incomingDataBytes, sizeof(incomingData));

  // Flags übernehmen
  systemSleepRx = incomingData.systemSleep ? true : false;
  nightStatus   = incomingData.istNacht ? true : false;

  // Empfangszeit merken (für Timeout-Standby)
  lastDataMillis = millis();

  // Startzeit für Timer setzen (nur beim ersten Paket)
  if (firstRxMillis == 0) firstRxMillis = lastDataMillis;

  // Wenn Sender "SystemSleep" fordert -> sofort Standby
  if (systemSleepRx) {
    enterStandby("SYSTEM-SLEEP vom Sender");
    return;
  }

  // Messwerte übernehmen
  webTempAvg  = incomingData.tempAvg;
  webDruck    = incomingData.pres;
  webDistanz  = incomingData.dist;
  webHumidity = incomingData.humidity;

  // Verlaufspuffer füllen (für Diagramme)
  temperaturHistorie[datenIndex] = webTempAvg;
  druckHistorie[datenIndex]      = webDruck;
  feuchteHistorie[datenIndex]    = webHumidity;

  datenIndex = (datenIndex + 1) % anzahlDatenpunkte;
  if (!pufferVoll && datenIndex == 0) pufferVoll = true;

  // Debug-Ausgabe
  updateRunningTimestamp();
  Serial.print("RX +"); Serial.print(lastTimestamp);
  Serial.print(" | Temperatur(Ø)="); Serial.print(webTempAvg, 1);
  Serial.print(" C | Feuchte="); Serial.print(webHumidity, 0);
  Serial.print(" % | Druck="); Serial.print(webDruck, 0);
  Serial.print(" hPa | Distanz="); Serial.print(webDistanz, 1);
  Serial.print(" cm | Licht="); Serial.println(nightStatus ? "NACHT" : "TAG");

  // Falls wir wegen Timeout im Standby waren -> wecken
  exitStandbyIfAllowed();
}

// =====================================================
// =================== JSON FÜR WEBSITE =================
// =====================================================
String erstelleChartDatenAlsJson() {
  updateRunningTimestamp();

  int count = pufferVoll ? anzahlDatenpunkte : datenIndex;

  String json = "{";
  json += "\"history\":{";

  // Temperatur Verlauf
  json += "\"temp\":[";
  for (int i = 0; i < count; i++) {
    int ring = (datenIndex - count + i + anzahlDatenpunkte) % anzahlDatenpunkte;
    json += String(temperaturHistorie[ring], 2);
    if (i < count - 1) json += ",";
  }
  json += "],";

  // Druck Verlauf
  json += "\"pres\":[";
  for (int i = 0; i < count; i++) {
    int ring = (datenIndex - count + i + anzahlDatenpunkte) % anzahlDatenpunkte;
    json += String(druckHistorie[ring], 2);
    if (i < count - 1) json += ",";
  }
  json += "],";

  // Feuchte Verlauf
  json += "\"hum\":[";
  for (int i = 0; i < count; i++) {
    int ring = (datenIndex - count + i + anzahlDatenpunkte) % anzahlDatenpunkte;
    json += String(feuchteHistorie[ring], 2);
    if (i < count - 1) json += ",";
  }
  json += "]";

  json += "},";

  // Live Werte
  json += "\"live\":{";
  json += "\"ts\":\"" + lastTimestamp + "\",";
  json += "\"temp\":" + String(webTempAvg, 2) + ",";
  json += "\"hum\":" + String(webHumidity, 2) + ",";
  json += "\"pres\":" + String(webDruck, 2) + ",";
  json += "\"dist\":" + String(webDistanz, 2);
  json += "},";

  // Status
  json += "\"status\":{";
  json += "\"ledAutomatik\":" + String(ledAutomatik ? "true" : "false") + ",";
  json += "\"relaisSystemAktiv\":" + String(relaisSystemAktiv ? "true" : "false") + ",";
  json += "\"night\":" + String(nightStatus ? "true" : "false") + ",";
  json += "\"standby\":" + String(standbyMode ? "true" : "false") + ",";
  json += "\"systemSleep\":" + String(systemSleepRx ? "true" : "false");
  json += "}";

  json += "}";
  return json;
}

// =====================================================
// ======================= SETUP =======================
// =====================================================
void setup() {
  Serial.begin(115200);

  // History initialisieren
  for (int i = 0; i < anzahlDatenpunkte; i++) {
    temperaturHistorie[i] = 0;
    druckHistorie[i] = 0;
    feuchteHistorie[i] = 0;
  }

  // Pins initialisieren
  pinMode(LED_RED, OUTPUT);
  pinMode(LED_GREEN, OUTPUT);
  pinMode(LED_BLUE, OUTPUT);
  pinMode(RELAY_PIN, OUTPUT);
  alleAus();

  // OLED initialisieren
  I2C_Display.begin(33, 19);
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) while (true);
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.ssd1306_command(SSD1306_DISPLAYON);

  // AP + ESP-NOW
  WiFi.mode(WIFI_AP_STA);
  WiFi.softAP(ap_ssid, ap_password);

  if (esp_now_init() != ESP_OK) {
    Serial.println("ESP-NOW init failed!");
    while (true);
  }
  esp_now_register_recv_cb(OnDataRecv);

  // Webserver Routen
  server.on("/", HTTP_GET, []() { server.send(200, "text/html", index_html); });
  server.on("/chart-data", HTTP_GET, []() { server.send(200, "application/json", erstelleChartDatenAlsJson()); });

  server.on("/relaisEin", HTTP_POST, []() { relaisSystemAktiv = true; server.sendHeader("Location", "/"); server.send(302); });
  server.on("/relaisAus", HTTP_POST, []() { relaisSystemAktiv = false; server.sendHeader("Location", "/"); server.send(302); });
  server.on("/ledAuto",   HTTP_POST, []() { ledAutomatik = true; server.sendHeader("Location", "/"); server.send(302); });
  server.on("/ledAus",    HTTP_POST, []() { ledAutomatik = false; server.sendHeader("Location", "/"); server.send(302); });

  server.begin();
}

// =====================================================
// ======================== LOOP =======================
// =====================================================
void loop() {
  // Zeit läuft auch ohne neue Pakete weiter
  updateRunningTimestamp();

  // Sleep/Standby prüfen
  standbyCheckTimeout();

  // Im Standby: Webserver nicht bedienen, Display bleibt aus, alles bleibt aus
  if (standbyMode) {
    delay(50);
    return;
  }

  // Webserver bedienen (nur wenn nicht Standby)
  server.handleClient();

  // ===================================================
  // OLED Anzeige (mit vollen Texten)
  // ===================================================
  display.clearDisplay();
  display.setCursor(0, 0);

  display.print("Zeit: ");
  display.println(lastTimestamp);

  display.print("Temperatur: ");
  display.print(webTempAvg, 1);
  display.println(" C");

  display.print("Feuchte: ");
  display.print(webHumidity, 0);
  display.println(" %");

  display.print("Druck: ");
  display.print(webDruck, 0);
  display.println(" hPa");

  display.print("Distanz: ");
  display.print(webDistanz, 1);
  display.println(" cm");

  display.print("Licht: ");
  display.println(nightStatus ? "NACHT" : "TAG");

  display.display();

  // ===================================================
  // LED Automatik
  // ===================================================
  if (ledAutomatik) {
    if (webTempAvg > 25) {
      digitalWrite(LED_RED, HIGH);
      digitalWrite(LED_GREEN, LOW);
      digitalWrite(LED_BLUE, LOW);
    } else if (webTempAvg < 15) {
      digitalWrite(LED_GREEN, HIGH);
      digitalWrite(LED_RED, LOW);
      digitalWrite(LED_BLUE, LOW);
    } else {
      digitalWrite(LED_BLUE, HIGH);
      digitalWrite(LED_RED, LOW);
      digitalWrite(LED_GREEN, LOW);
    }
  } else {
    digitalWrite(LED_RED, LOW);
    digitalWrite(LED_GREEN, LOW);
    digitalWrite(LED_BLUE, LOW);
  }

  // ===================================================
  // Relais-Logik
  // ===================================================
  if (relaisSystemAktiv) {
    bool objektNahe = (webDistanz > 0 && webDistanz < 50);
    if (objektNahe) {
      digitalWrite(RELAY_PIN, HIGH); delay(200);
      digitalWrite(RELAY_PIN, LOW);  delay(200);
    } else {
      digitalWrite(RELAY_PIN, HIGH); delay(1000);
      digitalWrite(RELAY_PIN, LOW);  delay(1000);
    }
  } else {
    digitalWrite(RELAY_PIN, LOW);
    delay(500);
  }
}
```




### Bilder und Schaltungen

<img src="https://github.com/NightDragonHero/SYT-IOT-Projekt/blob/main/Schaltplan_Wetterstation.png" alt="10 beste lustige Tierfotos für Freitag" style="zoom:33%;" />

### Tabellen

| Komponente | Funktion |
|---|---|
| ESP32 (Sender / Sensor-Node) | Liest Sensordaten ein (BMP280: Temperatur/Luftdruck, DHT11: Temperatur/Luftfeuchte, HY‑SRF05: Distanz, Lichtsensor: Tag/Nacht), bildet Mittelwerte (6 Samples) und sendet sie per ESP‑NOW. |
| ESP32 (Empfänger / Anzeige-Node) | Empfängt ESP‑NOW Daten, zeigt sie lokal am OLED an, erstellt einen Access Point mit Webserver/Webinterface und steuert LEDs + Relais abhängig von Modus und Messwerten. |
| BMP280 (I2C) | Misst Temperatur und Luftdruck (wird im Sender gelesen und übertragen). |
| DHT11 | Misst Temperatur und Luftfeuchtigkeit (Sender; Werte werden für Durchschnitt/Anzeige übertragen). |
| HY‑SRF05 (Ultraschallsensor) | Misst Distanz per Echo-Laufzeit (Sender; Distanz wird übertragen und am Empfänger für Relais-Logik genutzt). |
| Lichtsensor-Modul (Digitalausgang/DO) | Ermittelt Tag/Nacht (Sender; Status wird übertragen und im Webinterface/OLED als „TAG/NACHT“ angezeigt). |
| Buzzer | Akustisches Signal am Sender bei ESP‑NOW Sendefehlern; zusätzlich kurzer Feedback-Beep beim Umschalten von „SystemSleep“ per Button. |
| Button | Schaltet am Sender den „SystemSleep“-Status um; dieser wird per ESP‑NOW übertragen und versetzt den Empfänger in Standby (Display aus, LEDs/Relais aus). |
| OLED SSD1306 (I2C, 128×64) | Lokale Anzeige am Empfänger (Zeit seit erster Übertragung, Temperatur, Feuchte, Druck, Distanz, Lichtstatus). |
| LEDs (GPIO 16/17/18 am Empfänger) | Temperatur-Ampel im Automatikmodus: >25 °C rot, <15 °C grün, sonst blau; bei „LED AUS“ sind alle aus. |
| Relaismodul (GPIO 23 am Empfänger) | Schaltet im aktiven Relais-System abhängig von Distanz (unter 50 cm schnell, sonst langsam); kann über Webinterface ein/aus geschaltet werden. |
| Weboberfläche (Access Point + WebServer) | Zeigt Live-Daten + Status, bietet Buttons für LED-Automatik/LED-Aus und Relais-System Ein/Aus, liefert JSON über `/chart-data` und zeichnet Diagramme (Temp/Druck/Feuchte) im Browser. |
| Standby-/Timeout-Logik (Empfänger) | Geht in Standby bei Empfangs-Timeout (~15 s) oder wenn „SystemSleep“ vom Sender aktiv ist; wacht wieder auf, sobald frische Daten kommen und SystemSleep aus ist. |



## 5. Zusammenfassung

In diesem Projekt wurde eine IoT-basierte Wetterstation mit zwei ESP32 realisiert. Der Sender erfasst Messwerte (Temperatur/Luftdruck über BMP280, Temperatur/Luftfeuchte über DHT11, Distanz über HY‑SRF05 sowie Tag/Nacht über einen Lichtsensor) und überträgt die gemittelten Daten in festen Intervallen per ESP‑NOW an den Empfänger.

Der Empfänger stellt die Werte lokal auf einem OLED dar und bietet zusätzlich eine Weboberfläche über einen eigenen Access Point. Über diese Oberfläche lassen sich die LED‑Automatik sowie das Relais-System steuern. Eine besondere Herausforderung war die zuverlässige Datenübertragung (korrekte MAC-Adresse/Strukturgröße) und die robuste Betriebslogik mit Standby bei fehlendem Empfang bzw. per „SystemSleep“-Umschaltung. Insgesamt zeigt das Projekt anschaulich, wie sich Sensorik, drahtlose Kommunikation und einfache Aktorik mit dem ESP32 zu einem funktionsfähigen IoT-System kombinieren lassen.

## 6. Quellen

[1] Arduino. „ESP-NOW“. Zugegriffen: 27. Mai 2026. [Online]. Verfügbar unter: https://docs.espressif.com/projects/arduino-esp32/en/latest/api/espnow.html

[2] Espressif Systems. „ESP-NOW (Espressif Documentation)“. Zugegriffen: 27. Mai 2026. [Online]. Verfügbar unter: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/network/esp_now.html

[3] S. Santos, „ESP32 with DHT11/DHT22 Temperature and Humidity Sensor using Arduino IDE | Random Nerd Tutorials“. Zugegriffen: 18. Februar 2025. [Online]. Verfügbar unter: https://randomnerdtutorials.com/esp32-dht11-dht22-temperature-humidity-sensor-arduino-ide/

[4] „Arduino - DHT11 | Arduino Tutorial“, Arduino Getting Started. Zugegriffen: 18. Februar 2025. [Online]. Verfügbar unter: https://arduinogetstarted.com/tutorials/arduino-dht11

[5] Adafruit. „Adafruit BMP280 Library“. Zugegriffen: 27. Mai 2026. [Online]. Verfügbar unter: https://github.com/adafruit/Adafruit_BMP280_Library

[6] Adafruit. „Adafruit SSD1306“. Zugegriffen: 27. Mai 2026. [Online]. Verfügbar unter: https://github.com/adafruit/Adafruit_SSD1306

[7] Adafruit. „Adafruit GFX Library“. Zugegriffen: 27. Mai 2026. [Online]. Verfügbar unter: https://github.com/adafruit/Adafruit-GFX-Library

[8] „ESP32 - OLED“, ESP32 Tutorial. Zugegriffen: 18. Februar 2025. [Online]. Verfügbar unter: https://esp32io.com/tutorials/esp32-oled
