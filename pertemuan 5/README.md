# 5.5.4 Percobaan 5A
### 1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
> Task terlihat berjalan bersamaan, tetapi sebenarnya dijalankan bergantian oleh scheduler FreeRTOS dengan sangat cepat. Ketiga task memiliki prioritas yang sama sehingga scheduler menggunakan time slicing, dan saat task menjalankan vTaskDelay(), CPU akan digunakan oleh task lain
### 2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!
> Task keempat dapat ditambahkan dengan membuat fungsi task baru, kemudian menambahkan xTaskCreate() pada setup(). Setelah itu, task baru akan dijalankan oleh scheduler bersama task lainnya.
```C
void TaskBaru(void *pvParameters)
{
  while(1)
  {
    Serial.println("Task4");
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}
```
```C
xTaskCreate(
  TaskBaru,
  "task4",
  128,
  NULL,
  1,
  NULL );
```
### 3. Modifikasilah program dengan menambah sensor (misalnya potensiometer), lalu gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya? Jelaskan program pada file README.md.
```C
#include <Arduino_FreeRTOS.h> // library FreeRTOS untuk multitasking

void TaskBlink1(void *pvParameters); // deklarasi task LED 1
void TaskBlink2(void *pvParameters); // deklarasi task LED 2
void Taskprint(void *pvParameters);  // deklarasi task print serial
void TaskPot(void *pvParameters);    // deklarasi task potensiometer

int potValue = 0;   // variabel untuk nilai analog potensiometer
int delayTime = 200; // delay default LED

void setup() {
  Serial.begin(9600); // inisialisasi komunikasi serial

  // membuat task LED 1
  xTaskCreate(TaskBlink1, "task1", 128, NULL, 1, NULL);

  // membuat task LED 2
  xTaskCreate(TaskBlink2, "task2", 128, NULL, 1, NULL);

  // membuat task print serial
  xTaskCreate(Taskprint, "task3", 128, NULL, 1, NULL);

  // membuat task pembaca potensiometer
  xTaskCreate(TaskPot, "task4", 128, NULL, 1, NULL);

  vTaskStartScheduler(); // menjalankan scheduler RTOS
}

void loop() {} // tidak digunakan karena RTOS berjalan di task

void TaskBlink1(void *pvParameters) {
  pinMode(8, OUTPUT); // set pin 8 sebagai output LED
  while(1) {
    Serial.println("Task1"); // debug task 1
    digitalWrite(8, HIGH); // LED ON
    vTaskDelay(delayTime / portTICK_PERIOD_MS); // delay sesuai potensiometer
    digitalWrite(8, LOW); // LED OFF
    vTaskDelay(delayTime / portTICK_PERIOD_MS); // delay sesuai potensiometer
  }
}

void TaskBlink2(void *pvParameters) {
  pinMode(7, OUTPUT); // set pin 7 sebagai output LED
  while(1) {
    Serial.println("Task2"); // debug task 2
    digitalWrite(7, HIGH); // LED ON
    vTaskDelay(2 * delayTime / portTICK_PERIOD_MS); // delay lebih lama
    digitalWrite(7, LOW); // LED OFF
    vTaskDelay(2 * delayTime / portTICK_PERIOD_MS); // delay lebih lama
  }
}

void Taskprint(void *pvParameters) {
  int counter = 0; // variabel penghitung

  while(1) {
    counter++; // increment counter
    Serial.println(counter);   // tampilkan counter
    Serial.println(delayTime); // tampilkan delay saat ini
    vTaskDelay(500 / portTICK_PERIOD_MS); // delay print 500ms
  }
}

void TaskPot(void *pvParameters) {
  while(1) {
    potValue = analogRead(A0); // baca nilai potensiometer
    delayTime = map(potValue, 0, 1023, 100, 1000); // ubah ke range delay LED
    vTaskDelay(100 / portTICK_PERIOD_MS); // update setiap 100ms
  }
}
```
> Pada program LED, hasilnya kecepatan kedip LED akan mengikuti nilai potensiometer, yaitu berkisar antara 100ms sampai 1000ms. LED tetap menyala sesuai pola yang sama, tetapi cepat-lambatnya kedipan berubah sesuai putaran potensiometer secara real-time.
> <img width="20%" alt="VID_20260506_112931-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/a694c478-12f8-46b5-a9a0-88ff421cc7b0" />


# 5.6.4 Percobaan 5B
### 1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
> Kedua task terlihat berjalan bersamaan, tetapi sebenarnya dijalankan secara bergantian oleh scheduler FreeRTOS dengan sangat cepat. Task read_data mengirim data ke queue menggunakan xQueueSend(), sedangkan task display menerima data menggunakan xQueueReceive(). Saat suatu task menjalankan vTaskDelay() atau menunggu queue, scheduler akan memberikan CPU kepada task lain sehingga komunikasi data terlihat berjalan bersamaan.
### 2. Apakah program ini berpotensi mengalami race condition? Jelaskan!
> Program memiliki kemungkinan race condition yang sangat kecil karena komunikasi data menggunakan queue FreeRTOS. Fungsi xQueueSend() digunakan oleh task read_data untuk mengirim data ke queue, sedangkan xQueueReceive() digunakan oleh task display untuk menerima data dari queue. Queue FreeRTOS sudah memiliki mekanisme pengaturan akses data sehingga data yang dikirim akan disalin terlebih dahulu sebelum diterima task lain, sehingga pertukaran data antar task menjadi lebih aman dan teratur.
### 3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis. Bagaimana hasilnya? Jelaskan program pada file README.md
```C
#include <Arduino_FreeRTOS.h> // library RTOS
#include <queue.h>            // library queue untuk komunikasi antar task
#include <DHT.h>              // library sensor DHT

#define DHTPIN 2   // pin data sensor DHT
#define DHTTYPE DHT11 // tipe sensor DHT11

DHT dht(DHTPIN, DHTTYPE); // inisialisasi sensor DHT

struct readings { // struktur data untuk suhu & kelembaban
  float temp; // variabel suhu
  float hum;  // variabel kelembaban
};

QueueHandle_t my_queue; // deklarasi queue RTOS

void read_data(void *pvParameters);    // task baca sensor
void display_data(void *pvParameters); // task tampilkan data
void setup() {
  Serial.begin(9600); // serial monitor
  dht.begin(); // mulai sensor DHT
  my_queue = xQueueCreate(5, sizeof(struct readings)); // buat queue kapasitas 5
  xTaskCreate(read_data, "read", 128, NULL, 1, NULL);     // task baca sensor
  xTaskCreate(display_data, "display", 128, NULL, 1, NULL); // task tampil data
}

void loop() {} // tidak dipakai karena RTOS

void read_data(void *pvParameters) {
  struct readings data; // variabel penyimpan data sensor
  while(1) {
    data.temp = dht.readTemperature(); // baca suhu
    data.hum = dht.readHumidity();      // baca kelembaban
    xQueueSend(my_queue, &data, portMAX_DELAY); // kirim ke queue
    vTaskDelay(2000 / portTICK_PERIOD_MS); // delay 2 detik (sesuai DHT)
  }
}

void display_data(void *pvParameters) {
  struct readings data; // variabel penerima data
  while(1) {
    if(xQueueReceive(my_queue, &data, portMAX_DELAY) == pdPASS) { // ambil data dari queue
      Serial.print("Suhu : "); // tampilkan teks suhu
      Serial.print(data.temp);  // tampilkan nilai suhu
      Serial.println(" C");     // satuan

      Serial.print("Kelembaban : "); // tampilkan teks kelembaban
      Serial.print(data.hum);        // tampilkan nilai kelembaban
      Serial.println(" %");          // satuan
    }
  }
}
```
> Hasilnya, pembacaan sensor DHT11 akan menampilkan suhu dan kelembaban secara dinamis sesuai kondisi lingkungan secara real-time. Data dikirim melalui queue antar task, sehingga Task read akan terus memperbarui nilai dan Task display akan menampilkannya di Serial Monitor setiap 2 detik.

# Dokumentasi
<p align="center">
  <img width="45%" alt="dokumentasi pecobaan5A" src="https://github.com/user-attachments/assets/340c2f80-20f6-4ba1-a941-88b0828fd5e1" />
  <img width="45%" alt="dokumentasivideopercobaan5A-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/1c603a16-f116-431d-a569-704d65627605" />
</p>

<p align="center">
  <img wwidth="25%" alt="dokumentasi percobaan5B" src="https://github.com/user-attachments/assets/b9195ee6-c003-4e3c-80b4-c779f695b597" />
  <img width="45%" alt="dokumentasivideopercobaan5B-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/53f8f8ff-ec13-48a4-8478-d6d09e61e639" />
</p>
