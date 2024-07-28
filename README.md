# Final Proyek Mikrokontroller
<ul>
  <li>Mata Kuliah: Sistem Mikrokontroller</li>
  <li>Dosen Pengampu: <a href="https://github.com/Muhammad-Ikhwan-Fathulloh">Muhammad Ikhwan Fathulloh</a></li>
</ul>

## Kelompok
<ul>
  <li>Kelompok: 12</li>
  <li>Proyek: Smarthome</li>
  <li>Anggota: </li>
  <ul>
    <li>Anggota 1: <a href="">Luqman</a></li>
    <li>Anggota 2: <a href="">Ages</a></li>
    <li>Anggota 3: <a href="">Agus</a></li>
  </ul>
</ul>

## Judul Proyek 1
<p>Smarthome dengan Platform Blynk</p>

## Penjelasan
<p>Membuat proyek Smarthome dengan ESP32 yang terhubung ke platform Blynk. Proyek ini mencakup kontrol relay untuk menyalakan LED, pengaturan kecerahan LED dengan potensiometer, pembacaan suhu dan kelembapan dengan DHT22, serta prediksi kondisi berdasarkan suhu dan kelembapan.<p>

## Alat-alat
## Spesifikasi Proyek
1. Platform Blynk
2. Relay 2 Channel untuk menyalakan LED
3. Potensiometer untuk mengatur kecerahan LED dengan PWM
4. DHT22 untuk mengukur suhu dan kelembapan
5. Prediksi kondisi berdasarkan suhu dan kelembapan
6. Restful API untuk integrasi Smarthome
  
## Komponen yang Diperlukan
1. ESP32
2. Relay 2 Channel
3. LED
4. Potensiometer
5. DHT22
6. Platform Blynk

## Dokumentasi Wokwi
```
#define BLYNK_TEMPLATE_ID "YourTemplateID"
#define BLYNK_DEVICE_NAME "YourDeviceName"
#define BLYNK_AUTH_TOKEN "YourAuthToken"

#include <WiFi.h>
#include <BlynkSimpleEsp32.h>
#include <DHT.h>
```
- Library WiFi.h: Digunakan untuk menghubungkan ESP32 ke jaringan WiFi.
- Library BlynkSimpleEsp32.h: Digunakan untuk menghubungkan ESP32 ke platform Blynk.
- Library DHT.h: Digunakan untuk membaca data dari sensor DHT22.
- BLYNK_TEMPLATE_ID, BLYNK_DEVICE_NAME, BLYNK_AUTH_TOKEN: Konstanta ini digunakan untuk menghubungkan perangkat ke Blynk.

```
char auth[] = BLYNK_AUTH_TOKEN;
char ssid[] = "YourNetworkName";
char pass[] = "YourPassword";

#define DHTPIN 4
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

#define RELAY1_PIN 12
#define RELAY2_PIN 13
#define POT_PIN 34
#define LED_PIN 2

float temperature;
float humidity;

BlynkTimer timer;
```
- auth, ssid, pass: Variabel ini menyimpan token autentikasi Blynk, nama SSID, dan kata sandi WiFi.
- DHTPIN, DHTTYPE, DHT dht: Pin dan tipe sensor DHT22 serta inisialisasi sensor.
- RELAY1_PIN, RELAY2_PIN, POT_PIN, LED_PIN: Pin yang digunakan untuk relay, potensiometer, dan LED.
- temperature, humidity: Variabel untuk menyimpan data suhu dan kelembapan.
- BlynkTimer timer: Timer yang digunakan untuk menjadwalkan fungsi tertentu.

```
void setup() {
  Serial.begin(115200);
  Blynk.begin(auth, ssid, pass);
  dht.begin();
  pinMode(RELAY1_PIN, OUTPUT);
  pinMode(RELAY2_PIN, OUTPUT);
  pinMode(LED_PIN, OUTPUT);
  timer.setInterval(1000L, sendSensorData);
}
```
- Serial.begin(115200): Inisialisasi komunikasi serial dengan baud rate 115200.
- Blynk.begin(auth, ssid, pass): Menghubungkan ESP32 ke Blynk menggunakan token autentikasi, SSID, dan kata sandi WiFi.
- dht.begin(): Inisialisasi sensor DHT22.
- pinMode: Menentukan mode pin sebagai OUTPUT untuk relay dan LED.
- timer.setInterval(1000L, sendSensorData): Menjadwalkan fungsi sendSensorData untuk dijalankan setiap 1000 milidetik (1 detik).

```
void sendSensorData() {
  temperature = dht.readTemperature();
  humidity = dht.readHumidity();

  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  Blynk.virtualWrite(V5, temperature);
  Blynk.virtualWrite(V6, humidity);

  String condition = predictCondition(temperature, humidity);
  Blynk.virtualWrite(V7, condition);
}
```
- temperature = dht.readTemperature(): Membaca suhu dari sensor DHT22.
- humidity = dht.readHumidity(): Membaca kelembapan dari sensor DHT22.
- isnan(temperature) || isnan(humidity): Memeriksa apakah pembacaan sensor gagal (jika nilai tidak valid).
- Blynk.virtualWrite(V5, temperature): Mengirim data suhu ke widget di aplikasi Blynk.
- Blynk.virtualWrite(V6, humidity): Mengirim data kelembapan ke widget di aplikasi Blynk.
- predictCondition(temperature, humidity): Memanggil fungsi untuk memprediksi kondisi berdasarkan suhu dan kelembapan.
- Blynk.virtualWrite(V7, condition): Mengirim hasil prediksi ke widget di aplikasi Blynk.

```
String predictCondition(float temp, float hum) {
  if (temp > 30 && hum > 70) {
    return "Hot and Humid";
  } else if (temp > 30) {
    return "Hot";
  } else if (hum > 70) {
    return "Humid";
  } else {
    return "Comfortable";
  }
}
```
- predictCondition(float temp, float hum): Fungsi ini mengembalikan string yang menunjukkan kondisi berdasarkan suhu dan kelembapan:
A. Hot and Humid: Jika suhu > 30°C dan kelembapan > 70%.
B. Hot: Jika suhu > 30°C.
C. Humid: Jika kelembapan > 70%.
D. Comfortable: Jika tidak ada kondisi di atas yang terpenuhi.

```
BLYNK_WRITE(V1) {
  int relay1State = param.asInt();
  digitalWrite(RELAY1_PIN, relay1State);
}

BLYNK_WRITE(V2) {
  int relay2State = param.asInt();
  digitalWrite(RELAY2_PIN, relay2State);
}

BLYNK_WRITE(V3) {
  int potValue = param.asInt();
  analogWrite(LED_PIN, potValue);
}
```
- BLYNK_WRITE(V1): Fungsi ini dipanggil ketika nilai virtual pin V1 di Blynk berubah. Mengubah status relay 1.
- BLYNK_WRITE(V2): Fungsi ini dipanggil ketika nilai virtual pin V2 di Blynk berubah. Mengubah status relay 2.
- BLYNK_WRITE(V3): Fungsi ini dipanggil ketika nilai virtual pin V3 di Blynk berubah. Mengubah nilai PWM pada pin LED sesuai dengan nilai dari potensiometer.

```
void loop() {
  Blynk.run();
  timer.run();
}
```
- Blynk.run(): Menjaga koneksi Blynk tetap aktif dan memproses event.
- timer.run(): Menjalankan fungsi timer yang telah dijadwalkan.


```
#include <WiFi.h>
#include <WebServer.h>

// Create an instance of the server
WebServer server(80);

// Rest API endpoint handlers
void handleRoot() {
  server.send(200, "text/plain", "Welcome to Smarthome API");
}

void handleGetStatus() {
  String message = "Temperature: " + String(temperature) + "\n";
  message += "Humidity: " + String(humidity) + "\n";
  message += "Relay 1: " + String(digitalRead(RELAY1_PIN)) + "\n";
  message += "Relay 2: " + String(digitalRead(RELAY2_PIN)) + "\n";
  server.send(200, "text/plain", message);
}

void setup() {
  // Existing setup code...
  
  // Start the server
  server.begin();
  
  // Define API endpoints
  server.on("/", handleRoot);
  server.on("/status", handleGetStatus);
  
  // Initialize other endpoints as needed
}

void loop() {
  Blynk.run();
  timer.run();
  server.handleClient();
}
```
Untuk menambahkan Restful API.
- WebServer server(80): Membuat instance server pada port 80.
- server.begin(): Memulai server.
- server.on("/", handleRoot): Menangani permintaan ke root URL dengan fungsi handleRoot.
- server.on("/status", handleGetStatus): Menangani permintaan ke URL /status dengan fungsi handleGetStatus.

### 
<ul>
    <li><a href="https://wokwi.com/projects/404664348824905729">Wokwi</a></li>
  <li><a href="https://github.com/26babyL/22552012046">Github</a></li>
</ul>
