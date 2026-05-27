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




### Bauteile 

## RGB‑LED
Eine RGB‑LED vereint **rote, grüne und blaue Leuchtdioden** in einem Gehäuse. Durch PWM‑Ansteuerung der drei Kanäle lassen sich **beliebige Farben** erzeugen.

---

##BMP280:
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
// ================== BMP280 (Temp/Druck) ==============
TwoWire I2C_BMP = TwoWire(1);
Adafruit_BMP280 bmp(&I2C_BMP);

// =====================================================
// ================== DHT11 (Temp/Feuchte) =============
#define DHT_PIN 32
#define DHT_TYPE DHT11
DHT dht(DHT_PIN, DHT_TYPE);

// =====================================================
// ================== HY-SRF05 (Distanz) ===============
#define TRIG_PIN 5
#define ECHO_PIN 18

// =====================================================
// ================== LICHTSENSOR (HALJIA, DO) =========
#define LICHTSENSOR_PIN 26
const bool DO_HIGH_MEANS_DARK = true; // ggf. false
const int lichtSamples = 6;           // für Mehrheitsentscheidung pro Sekunde (klein halten)
const int lichtDelayMs = 2;
const float nightThreshold = 0.5f;    // >= 50% dunkel => Nacht

// =====================================================
// ================== BUZZER ===========================
#define BUZZER_PIN 27
bool send_ok = true;
unsigned long lastBeep = 0;
const unsigned long beepInterval = 333; // 3x/s

// =====================================================
// ================== BUTTON (SYSTEM SLEEP TOGGLE) =====
#define BUTTON_PIN 25
bool systemSleep = false;

// Entprellung Button
bool lastButtonReading = HIGH;
bool stableButtonState = HIGH;
unsigned long lastDebounceTime = 0;
const unsigned long debounceMs = 40;

// =====================================================
// ================== ESP-NOW ==========================
uint8_t broadcastAddress[] = {0x00, 0x70, 0x07, 0x26, 0xAA, 0x48};

typedef struct struct_message {
  float tempAvg;      // Durchschnitt aus BMP280 + DHT11 (für den 6er-Block)
  float pres;         // Druck-Ø (6er-Block)
  float dist;         // Distanz-Ø (6er-Block)
  bool istNacht;      // Mehrheit (6er-Block)
  bool systemSleep;   // globaler Sleep Toggle
  float humidity;     // Feuchte-Ø (6er-Block)
  float tempBmp;      // BMP Temp-Ø (6er-Block)
  float tempDht;      // DHT Temp-Ø (6er-Block)
} struct_message;

struct_message sensorData;
esp_now_peer_info_t peerInfo;

// =====================================================
// ================== CALLBACK =========================
void OnDataSent(const wifi_tx_info_t *info, esp_now_send_status_t status) {
  send_ok = (status == ESP_NOW_SEND_SUCCESS);
}

// =====================================================
// ================== ZEITSTEUERUNG / SAMPLING =========
const unsigned long sampleIntervalMs = 1000; // 1 Sekunde
unsigned long lastSampleMillis = 0;

const int samplesPerBatch = 6; // 6 Messungen (1x pro Sekunde)
int sampleCount = 0;

// Akkus für Batch-Mittelwerte
float sumBmpTemp = 0;
float sumDhtTemp = 0;
float sumPres = 0;
float sumHum = 0;
float sumDist = 0;
int   countDistValid = 0;

int nightCount = 0;     // Anzahl "Nacht" in der Batch
int nightValid = 0;     // Anzahl gültiger Lichtmessungen (eigentlich = samplesPerBatch)

// DHT: letzte gültige Werte (DHT11 liefert manchmal NaN)
float lastGoodHum = 0;
float lastGoodTempDht = 0;

// =====================================================
// ================== HILFSFUNKTIONEN ==================
void handleButtonToggle() {
  bool reading = digitalRead(BUTTON_PIN);

  if (reading != lastButtonReading) {
    lastDebounceTime = millis();
    lastButtonReading = reading;
  }

  if ((millis() - lastDebounceTime) > debounceMs) {
    if (reading != stableButtonState) {
      stableButtonState = reading;
      if (stableButtonState == LOW) {
        systemSleep = !systemSleep;
        Serial.print("SYSTEM SLEEP TOGGLE: ");
        Serial.println(systemSleep ? "AN" : "AUS");
        digitalWrite(BUZZER_PIN, HIGH); delay(60); digitalWrite(BUZZER_PIN, LOW);
      }
    }
  }
}

bool leseIstNachtSchnell() {
  // kurze Mehrheitsentscheidung über wenige schnelle Reads
  int darkCount = 0;
  for (int i = 0; i < lichtSamples; i++) {
    int raw = digitalRead(LICHTSENSOR_PIN);
    bool isDark = DO_HIGH_MEANS_DARK ? (raw == HIGH) : (raw == LOW);
    if (isDark) darkCount++;
    delay(lichtDelayMs);
  }
  return ((float)darkCount / (float)lichtSamples) >= nightThreshold;
}

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

void resetBatch() {
  sampleCount = 0;

  sumBmpTemp = 0;
  sumDhtTemp = 0;
  sumPres = 0;
  sumHum = 0;
  sumDist = 0;
  countDistValid = 0;

  nightCount = 0;
  nightValid = 0;
}

void sendPacket(float tempAvg, float presAvg, float distAvg, bool istNachtBatch, float humAvg, float bmpAvg, float dhtAvg) {
  sensorData.tempAvg = tempAvg;
  sensorData.pres = presAvg;
  sensorData.dist = distAvg;
  sensorData.istNacht = istNachtBatch;
  sensorData.systemSleep = systemSleep;
  sensorData.humidity = humAvg;
  sensorData.tempBmp = bmpAvg;
  sensorData.tempDht = dhtAvg;

  esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *)&sensorData, sizeof(sensorData));
  if (result != ESP_OK) {
    Serial.println("Senden fehlgeschlagen (esp_now_send Fehler)!");
  }
}

// =====================================================
// ================== SETUP ============================
void setup() {
  Serial.begin(115200);
  delay(1000);

  pinMode(BUZZER_PIN, OUTPUT);
  digitalWrite(BUZZER_PIN, LOW);

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(LICHTSENSOR_PIN, INPUT_PULLUP);

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  dht.begin();

  I2C_BMP.begin(21, 22);
  if (!bmp.begin(0x76)) {
    Serial.println("BMP280 nicht gefunden!");
    while (1) {}
  }

  WiFi.mode(WIFI_STA);

  if (esp_now_init() != ESP_OK) {
    Serial.println("ESP-NOW Init fehlgeschlagen.");
    return;
  }

  esp_now_register_send_cb(OnDataSent);

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
// ================== LOOP =============================
void loop() {
  unsigned long now = millis();

  // Button immer prüfen
  handleButtonToggle();

  // Buzzer bei "kein Empfänger" nur wenn systemSleep AUS
  if (!systemSleep) {
    if (!send_ok) {
      if (now - lastBeep >= beepInterval) {
        lastBeep = now;
        digitalWrite(BUZZER_PIN, HIGH);
        delay(80);
        digitalWrite(BUZZER_PIN, LOW);
      }
    } else {
      digitalWrite(BUZZER_PIN, LOW);
    }
  } else {
    digitalWrite(BUZZER_PIN, LOW);
  }

  // Sampling jede Sekunde
  if (now - lastSampleMillis < sampleIntervalMs) return;
  lastSampleMillis += sampleIntervalMs;

  // Wenn systemSleep AN: nur Statuspakete senden, aber weiterhin jede Sekunde
  // zählen, damit der Empfänger schnell umschalten kann.
  if (systemSleep) {
    sampleCount++;
    if (sampleCount >= samplesPerBatch) {
      // Statuspaket ohne Messwerte
      Serial.println("SystemSleep AN -> sende Statuspaket (Batch).");
      sendPacket(0, 0, -1, false, 0, 0, 0);
      resetBatch();
    }
    return;
  }

  // ======= 1 Messung pro Sekunde =======
  float bmpTemp = bmp.readTemperature();
  float pres = bmp.readPressure() / 100.0f;

  float dhtTemp = dht.readTemperature();
  float hum = dht.readHumidity();

  if (!isnan(dhtTemp)) lastGoodTempDht = dhtTemp;
  if (!isnan(hum)) lastGoodHum = hum;
  dhtTemp = lastGoodTempDht;
  hum = lastGoodHum;

  float dist = leseDistanzCm();
  bool nacht = leseIstNachtSchnell();

  // Akkumulieren
  sumBmpTemp += bmpTemp;
  sumDhtTemp += dhtTemp;
  sumPres += pres;
  sumHum += hum;

  if (dist > 0) {
    sumDist += dist;
    countDistValid++;
  }

  if (nacht) nightCount++;
  nightValid++;

  sampleCount++;

  Serial.print("Sample "); Serial.print(sampleCount);
  Serial.print("/"); Serial.print(samplesPerBatch);
  Serial.print(" | BMP="); Serial.print(bmpTemp, 1);
  Serial.print(" DHT="); Serial.print(dhtTemp, 1);
  Serial.print(" H="); Serial.print(hum, 0);
  Serial.print(" P="); Serial.print(pres, 0);
  Serial.print(" D="); Serial.print(dist, 1);
  Serial.print(" | "); Serial.println(nacht ? "NACHT" : "TAG");

  // ======= nach 6 Messungen: Mittelwerte bilden & senden =======
  if (sampleCount >= samplesPerBatch) {
    float bmpAvg = sumBmpTemp / samplesPerBatch;
    float dhtAvg = sumDhtTemp / samplesPerBatch;
    float presAvg = sumPres / samplesPerBatch;
    float humAvg = sumHum / samplesPerBatch;

    float distAvg = (countDistValid > 0) ? (sumDist / countDistValid) : -1.0f;

    // Durchschnitt aus beiden Temperatur-Sensoren
    float tempAvg = (bmpAvg + dhtAvg) / 2.0f;

    // Mehrheit Tag/Nacht
    bool istNachtBatch = (nightCount >= (nightValid / 2 + 1)); // Mehrheit

    Serial.println("===== BATCH SEND =====");
    Serial.print("BMP Avg:   "); Serial.println(bmpAvg, 1);
    Serial.print("DHT Avg:   "); Serial.println(dhtAvg, 1);
    Serial.print("Temp Avg:  "); Serial.println(tempAvg, 1);
    Serial.print("Hum Avg:   "); Serial.println(humAvg, 0);
    Serial.print("Pres Avg:  "); Serial.println(presAvg, 0);
    Serial.print("Dist Avg:  "); Serial.println(distAvg, 1);
    Serial.print("Light:     "); Serial.println(istNachtBatch ? "NACHT" : "TAG");
    Serial.println("=======================");

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
#include <Arduino_JSON.h>

// ================== DISPLAY ==================
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

TwoWire I2C_Display = TwoWire(0);

Adafruit_SSD1306 display(
  SCREEN_WIDTH,
  SCREEN_HEIGHT,
  &I2C_Display,
  -1
);

#define RELAY_PIN 23

// ================== GRAPH DATEN ==================
const int anzahlDatenpunkte = 10;

float temperaturHistorie[anzahlDatenpunkte];
float druckHistorie[anzahlDatenpunkte];
float feuchteHistorie[anzahlDatenpunkte];

int datenIndex = 0;
bool pufferVoll = false;

// ================== STATUS ==================
bool ledAutomatik = true;
bool relaisSystemAktiv = true;

bool nightStatus = false;

// ================== ESP NOW ==================
typedef struct {

  float temp;
  float pres;
  float dist;
  float hum;

  bool istNacht;

} struct_message;

struct_message incomingData;

// ================== LIVE DATEN ==================
float webTemperatur = 0;
float webDruck = 0;
float webDistanz = 0;
float webFeuchte = 0;

// ================== WLAN ==================
const char* ap_ssid = "ESP32_Anzeige";
const char* ap_password = "12345678";

WebServer server(80);

// ================== HTML ==================
const char index_html[] PROGMEM = R"rawliteral(

<!DOCTYPE html>
<html>

<head>

<title>ESP32 Steuerung</title>

<style>

body {
  font-family: sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.container {
  border: 1px solid #ccc;
  padding: 15px;
  margin-bottom: 20px;
  width: 80%;
  max-width: 800px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.chart-box {
  border: 2px solid #444;
  padding: 10px;
  margin-top: 15px;
  border-radius: 6px;
}

canvas {
  width: 100%;
  height: 220px;
  background: #fff;
}

h1, h2 {
  color: #333;
}

button {
  padding: 10px;
  margin: 5px;
  font-size: 16px;
  border-radius: 5px;
  border: none;
  color: white;
  cursor: pointer;
}

button.ein {
  background-color: #28a745;
}

button.aus {
  background-color: #dc3545;
}

</style>

</head>

<body>

<h1>ESP32 Messwerte & Steuerung</h1>

<div class="container">

  <h2>Live-Daten</h2>

  <p>
    Temperatur:
    <span id="temp">--</span>
    &deg;C
  </p>

  <p>
    Feuchtigkeit:
    <span id="hum">--</span>
    %
  </p>

  <p>
    Druck:
    <span id="pres">--</span>
    hPa
  </p>

  <p>
    Distanz:
    <span id="dist">--</span>
    cm
  </p>

  <p>
    Helligkeit:
    <span id="light">--</span>
  </p>

</div>

<div class="container">

  <h2>Daten der letzten 1 Minute</h2>

  <div class="chart-box">

    <p><b>Temperatur</b></p>

    <canvas
      id="tempChart"
      width="600"
      height="220">
    </canvas>

  </div>

  <div class="chart-box">

    <p><b>Luftdruck</b></p>

    <canvas
      id="presChart"
      width="600"
      height="220">
    </canvas>

  </div>

  <div class="chart-box">

    <p><b>Luftfeuchtigkeit</b></p>

    <canvas
      id="humChart"
      width="600"
      height="220">
    </canvas>

  </div>

</div>

<div class="container">

  <h2>LED Steuerung</h2>

  <p>
    Status:
    <span id="ledStatus">--</span>
  </p>

  <form
    action="/ledAuto"
    method="POST"
    style="display:inline-block;">

    <button
      type="submit"
      class="ein">

      Automatik EIN

    </button>

  </form>

  <form
    action="/ledAus"
    method="POST"
    style="display:inline-block;">

    <button
      type="submit"
      class="aus">

      Alle LEDs AUS

    </button>

  </form>

</div>

<div class="container">

  <h2>Relais Steuerung</h2>

  <p>
    Status:
    <span id="relaisStatus">--</span>
  </p>

  <form
    action="/relaisEin"
    method="POST"
    style="display:inline-block;">

    <button
      type="submit"
      class="ein">

      Relais-System EIN

    </button>

  </form>

  <form
    action="/relaisAus"
    method="POST"
    style="display:inline-block;">

    <button
      type="submit"
      class="aus">

      Relais-System AUS

    </button>

  </form>

</div>

<script>

function drawGraph(
  canvasId,
  values,
  color,
  yLabel,
  unit,
  yMin,
  yMax
) {

  const canvas =
    document.getElementById(canvasId);

  const ctx =
    canvas.getContext("2d");

  ctx.clearRect(
    0,
    0,
    canvas.width,
    canvas.height
  );

  if(values.length === 0) return;

  const w = canvas.width;
  const h = canvas.height;

  const pad = 45;

  ctx.strokeStyle = "#000";
  ctx.lineWidth = 2;

  ctx.beginPath();

  ctx.moveTo(pad, 10);
  ctx.lineTo(pad, h - pad);
  ctx.lineTo(w - 10, h - pad);

  ctx.stroke();

  ctx.fillStyle = "#000";
  ctx.font = "12px Arial";

  ctx.fillText(
    "Zeit",
    w/2 - 20,
    h - 10
  );

  ctx.fillText(
    yLabel,
    5,
    12
  );

  for(let t=0; t<=60; t+=6){

    const x =
      pad +
      (t/60)*(w-2*pad);

    ctx.beginPath();

    ctx.moveTo(x, h - pad);
    ctx.lineTo(x, h - pad + 5);

    ctx.stroke();

    const label =
      (t===60)
      ? t + unit.x
      : t;

    ctx.fillText(
      label.toString(),
      x-6,
      h - pad + 18
    );
  }

  const ySteps = 5;

  for(let i=0; i<=ySteps; i++){

    const val =
      yMin +
      (
        i*(yMax-yMin)/ySteps
      );

    const y =
      h - pad -
      (
        i/ySteps
      ) *
      (h-2*pad);

    ctx.beginPath();

    ctx.moveTo(pad-5, y);
    ctx.lineTo(pad, y);

    ctx.stroke();

    const label =
      (i===ySteps)
      ? val.toFixed(1) + unit.y
      : val.toFixed(1);

    ctx.fillText(
      label,
      5,
      y+4
    );
  }

  ctx.strokeStyle = color;
  ctx.lineWidth = 2;

  ctx.beginPath();

  values.forEach((v,i)=>{

    const time = i*6;

    const x =
      pad +
      (time/60)*(w-2*pad);

    const y =
      h - pad -
      (
        (v-yMin)/(yMax-yMin)
      ) *
      (h-2*pad);

    if(i===0) {
      ctx.moveTo(x,y);
    }
    else {
      ctx.lineTo(x,y);
    }
  });

  ctx.stroke();
}

async function fetchData(){
  try{
    const response = await fetch('/chart-data');
    const data = await response.json();

    document.getElementById('temp').innerText = data.live.temp.toFixed(1);
    document.getElementById('hum').innerText  = data.live.hum.toFixed(0);
    document.getElementById('pres').innerText = data.live.pres.toFixed(0);
    document.getElementById('dist').innerText = data.live.dist.toFixed(1);
    document.getElementById('ledStatus').innerText = data.status.ledAutomatik ? "Automatisch" : "Manuell Aus";
    document.getElementById('relaisStatus').innerText = data.status.relaisSystemAktiv ? "System AKTIV" : "System AUS";
    document.getElementById('light').innerText = data.status.night ? 'NACHT' : 'TAG';

    // ---- Diagramme zeichnen:
    drawGraph(
      "tempChart",
      data.history.temp,
      "#ff6384",
      "Temperatur",
      {x:"s", y:"°C"},
      10, 35
    );
    drawGraph(
      "presChart",
      data.history.pres,
      "#36a2eb",
      "Luftdruck",
      {x:"s", y:"hPa"},
      950, 1050
    );
    drawGraph(
      "humChart",
      data.history.hum,
      "#4bc0c0",
      "Feuchtigkeit",
      {x:"s", y:"%"},
      0, 100
    );
  } catch(e){
    console.error(e);
  }
}

setInterval(fetchData, 6000);

window.onload = fetchData;

</script>

</body>
</html>

)rawliteral";

// ================== RECEIVE CALLBACK ==================
void OnDataRecv(
  const esp_now_recv_info_t *info,
  const uint8_t *incomingDataBytes,
  int len
) {

  if (len != sizeof(struct_message)) return;

  memcpy(
    &incomingData,
    incomingDataBytes,
    sizeof(incomingData)
  );

  webTemperatur = incomingData.temp;
  webDruck      = incomingData.pres;
  webDistanz    = incomingData.dist;
  webFeuchte    = incomingData.hum;

  nightStatus   = incomingData.istNacht;

  temperaturHistorie[datenIndex] =
    webTemperatur;

  druckHistorie[datenIndex] =
    webDruck;

  feuchteHistorie[datenIndex] =
    webFeuchte;

  datenIndex =
    (datenIndex + 1)
    % anzahlDatenpunkte;

  if (!pufferVoll && datenIndex == 0) {
    pufferVoll = true;
  }
}

// ================== JSON ==================
String erstelleChartDatenAlsJson() {

  int count =
    pufferVoll
    ? anzahlDatenpunkte
    : datenIndex;

  String json = "{";

  json += "\"history\":{";

  // TEMP
  json += "\"temp\":[";

  for (int i = 0; i < count; i++) {

    int ringIndex =
      (datenIndex - count + i + anzahlDatenpunkte)
      % anzahlDatenpunkte;

    json +=
      String(
        temperaturHistorie[ringIndex],
        2
      );

    if (i < count - 1) {
      json += ",";
    }
  }

  json += "],";

  // DRUCK
  json += "\"pres\":[";

  for (int i = 0; i < count; i++) {

    int ringIndex =
      (datenIndex - count + i + anzahlDatenpunkte)
      % anzahlDatenpunkte;

    json +=
      String(
        druckHistorie[ringIndex],
        2
      );

    if (i < count - 1) {
      json += ",";
    }
  }

  json += "],";

  // FEUCHTE
  json += "\"hum\":[";

  for (int i = 0; i < count; i++) {

    int ringIndex =
      (datenIndex - count + i + anzahlDatenpunkte)
      % anzahlDatenpunkte;

    json +=
      String(
        feuchteHistorie[ringIndex],
        2
      );

    if (i < count - 1) {
      json += ",";
    }
  }

  json += "]";

  json += "},";

  // LIVE
  json += "\"live\":{";

  json += "\"temp\":";
  json += String(webTemperatur, 2);
  json += ",";

  json += "\"hum\":";
  json += String(webFeuchte, 2);
  json += ",";

  json += "\"pres\":";
  json += String(webDruck, 2);
  json += ",";

  json += "\"dist\":";
  json += String(webDistanz, 2);

  json += "},";

  // STATUS
  json += "\"status\":{";

  json += "\"ledAutomatik\":";
  json += String(
    ledAutomatik
    ? "true"
    : "false"
  );

  json += ",";

  json += "\"relaisSystemAktiv\":";
  json += String(
    relaisSystemAktiv
    ? "true"
    : "false"
  );

  json += ",";

  json += "\"night\":";
  json += String(
    nightStatus
    ? "true"
    : "false"
  );

  json += "}";

  json += "}";

  return json;
}

// ================== SETUP ==================
void setup() {

  Serial.begin(115200);

  for (int i=0; i<anzahlDatenpunkte; i++) {

    temperaturHistorie[i] = 0;
    druckHistorie[i] = 0;
    feuchteHistorie[i] = 0;
  }

  pinMode(16, OUTPUT);
  pinMode(17, OUTPUT);
  pinMode(18, OUTPUT);

  pinMode(RELAY_PIN, OUTPUT);

  I2C_Display.begin(33,19);

  if(!display.begin(
      SSD1306_SWITCHCAPVCC,
      0x3C
    )) {

    while(true);
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);

  WiFi.mode(WIFI_AP_STA);

  WiFi.softAP(
    ap_ssid,
    ap_password
  );

  esp_now_init();

  esp_now_register_recv_cb(
    OnDataRecv
  );

  server.on("/", HTTP_GET, []() {

    server.send(
      200,
      "text/html",
      index_html
    );
  });

  server.on("/chart-data", HTTP_GET, []() {

    server.send(
      200,
      "application/json",
      erstelleChartDatenAlsJson()
    );
  });

  server.on("/relaisEin", HTTP_POST, []() {

    relaisSystemAktiv = true;

    server.sendHeader("Location", "/");
    server.send(302);
  });

  server.on("/relaisAus", HTTP_POST, []() {

    relaisSystemAktiv = false;

    server.sendHeader("Location", "/");
    server.send(302);
  });

  server.on("/ledAuto", HTTP_POST, []() {

    ledAutomatik = true;

    server.sendHeader("Location", "/");
    server.send(302);
  });

  server.on("/ledAus", HTTP_POST, []() {

    ledAutomatik = false;

    server.sendHeader("Location", "/");
    server.send(302);
  });

  server.begin();
}

// ================== LOOP ==================
void loop() {

  server.handleClient();

  display.clearDisplay();

  display.setCursor(0,0);

  display.print("Temp: ");
  display.print(webTemperatur, 1);
  display.println(" C");

  display.print("Feuchte: ");
  display.print(webFeuchte, 0);
  display.println(" %");

  display.print("Druck: ");
  display.print(webDruck, 0);
  display.println(" hPa");

  display.print("Dist: ");
  display.print(webDistanz, 1);
  display.println(" cm");

  display.print("Licht: ");

  display.println(
    nightStatus
    ? "NACHT"
    : "TAG"
  );

  display.print("-------------------\n");

  display.print("LEDs: ");

  display.println(
    ledAutomatik
    ? "AUTO"
    : "AUS"
  );

  display.print("Relais: ");

  display.println(
    relaisSystemAktiv
    ? "AKTIV"
    : "AUS"
  );

  display.display();

  Serial.println(
    nightStatus
    ? "Lichtstatus: NACHT"
    : "Lichtstatus: TAG"
  );

  // LED SYSTEM
  if (ledAutomatik) {

    if (webTemperatur > 25) {

      digitalWrite(16, HIGH);
      digitalWrite(17, LOW);
      digitalWrite(18, LOW);
    }

    else if (webTemperatur < 15) {

      digitalWrite(17, HIGH);
      digitalWrite(16, LOW);
      digitalWrite(18, LOW);
    }

    else {

      digitalWrite(18, HIGH);
      digitalWrite(16, LOW);
      digitalWrite(17, LOW);
    }
  }

  else {

    digitalWrite(16, LOW);
    digitalWrite(17, LOW);
    digitalWrite(18, LOW);
  }

  // RELAIS
  if (relaisSystemAktiv) {

    bool objektNahe =
      (
        webDistanz > 0
        &&
        webDistanz < 50
      );

    if (objektNahe) {

      digitalWrite(RELAY_PIN, HIGH);
      delay(200);

      digitalWrite(RELAY_PIN, LOW);
      delay(200);
    }

    else {

      digitalWrite(RELAY_PIN, HIGH);
      delay(1000);

      digitalWrite(RELAY_PIN, LOW);
      delay(1000);
    }
  }

  else {

    digitalWrite(RELAY_PIN, LOW);
    delay(500);
  }
}
```




### Bilder und Schaltungen

<img src="https://github.com/NightDragonHero/SYT-IOT-Projekt/blob/main/Schaltplan_Wetterstation.png" alt="10 beste lustige Tierfotos für Freitag" style="zoom:33%;" />

### Tabellen

| Variable                             | n     | M     | SD   | 1      | 2      | 3     | 4     | 5    | 6     | 7    |
| ------------------------------------ | ----- | ----- | ---- | ------ | ------ | ----- | ----- | ---- | ----- | ---- |
| 1. Ultraschallsensor       | 3,697 | 0.43  | 0.49 | —      |        |       |       |      |       |      |
| 2. OLED-Display           | 2,134 | 3.14  | 0.62 | −.08** | —      |       |       |      |       |      |
| 3. RGB-LED                | 3,697 | 1.01  | 0.27 | .45**  | −.01   | —     |       |      |       |      |
| 4. Grafik mit Access Point              | 3,697 | 0.33  | 0.47 | .08**  | .07**  | .04*  | —     |      |       |      |
| 5. Buzzer            | 3,697 | 6.45  | 6.62 | −.29** | .09**  | .01   | .09** | —    |       |      |
| 6. Relais       | 3,505 | 85.00 | 6.98 | −.25** | −.39** | .24** | .08** | .01  | —     |      |
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
