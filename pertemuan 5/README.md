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
#include <Arduino_FreeRTOS.h>

void TaskBlink1(void *pvParameters);
void TaskBlink2(void *pvParameters);
void Taskprint(void *pvParameters);
void TaskPot(void *pvParameters);

int potValue = 0;
int delayTime = 200;

void setup() {
  Serial.begin(9600);

  xTaskCreate(
    TaskBlink1,
    "task1",
    128,
    NULL,
    1,
    NULL );

  xTaskCreate(
    TaskBlink2,
    "task2",
    128,
    NULL,
    1,
    NULL );

  xTaskCreate(
    Taskprint,
    "task3",
    128,
    NULL,
    1,
    NULL );

  xTaskCreate(
    TaskPot,
    "task4",
    128,
    NULL,
    1,
    NULL );

  vTaskStartScheduler();
}

void loop()
{
}

void TaskBlink1(void *pvParameters) {
  pinMode(8, OUTPUT);

  while(1)
  {
    Serial.println("Task1");

    digitalWrite(8, HIGH);
    vTaskDelay(delayTime / portTICK_PERIOD_MS);

    digitalWrite(8, LOW);
    vTaskDelay(delayTime / portTICK_PERIOD_MS);
  }
}

void TaskBlink2(void *pvParameters)
{
  pinMode(7, OUTPUT);

  while(1)
  {
    Serial.println("Task2");

    digitalWrite(7, HIGH);
    vTaskDelay(2 * delayTime / portTICK_PERIOD_MS);

    digitalWrite(7, LOW);
    vTaskDelay(2 * delayTime / portTICK_PERIOD_MS);
  }
}

void Taskprint(void *pvParameters) {
  int counter = 0;

  while(1)
  {
    counter++;

    Serial.println(counter);
    Serial.println(delayTime);

    vTaskDelay(500 / portTICK_PERIOD_MS);
  }
}

void TaskPot(void *pvParameters)
{
  while(1)
  {
    potValue = analogRead(A0);

    delayTime = map(potValue, 0, 1023, 100, 1000);

    vTaskDelay(100 / portTICK_PERIOD_MS);
  }
}
```
> <img width="20%" alt="VID_20260506_112931-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/a694c478-12f8-46b5-a9a0-88ff421cc7b0" />


# 5.6.4 Percobaan 5B
### 1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
> Kedua task terlihat berjalan bersamaan, tetapi sebenarnya dijalankan secara bergantian oleh scheduler FreeRTOS dengan sangat cepat. Task read_data mengirim data ke queue menggunakan xQueueSend(), sedangkan task display menerima data menggunakan xQueueReceive(). Saat suatu task menjalankan vTaskDelay() atau menunggu queue, scheduler akan memberikan CPU kepada task lain sehingga komunikasi data terlihat berjalan bersamaan.
### 2. Apakah program ini berpotensi mengalami race condition? Jelaskan!
> Program memiliki kemungkinan race condition yang sangat kecil karena komunikasi data menggunakan queue FreeRTOS. Fungsi xQueueSend() digunakan oleh task read_data untuk mengirim data ke queue, sedangkan xQueueReceive() digunakan oleh task display untuk menerima data dari queue. Queue FreeRTOS sudah memiliki mekanisme pengaturan akses data sehingga data yang dikirim akan disalin terlebih dahulu sebelum diterima task lain, sehingga pertukaran data antar task menjadi lebih aman dan teratur.
### 3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis. Bagaimana hasilnya? Jelaskan program pada file README.md
```C
#include <Arduino_FreeRTOS.h>
#include <queue.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

struct readings {
  float temp;
  float hum;
};

QueueHandle_t my_queue;

void read_data(void *pvParameters);
void display_data(void *pvParameters);

void setup() {
  Serial.begin(9600);

  dht.begin();

  my_queue = xQueueCreate(5, sizeof(struct readings));

  xTaskCreate(read_data, "read", 128, NULL, 1, NULL);
  xTaskCreate(display_data, "display", 128, NULL, 1, NULL);
}

void loop() {
}

void read_data(void *pvParameters) {
  struct readings data;

  while(1) {
    data.temp = dht.readTemperature();
    data.hum = dht.readHumidity();

    xQueueSend(my_queue, &data, portMAX_DELAY);

    vTaskDelay(2000 / portTICK_PERIOD_MS);
  }
}

void display_data(void *pvParameters) {
  struct readings data;

  while(1) {
    if(xQueueReceive(my_queue, &data, portMAX_DELAY) == pdPASS) {

      Serial.print("Suhu : ");
      Serial.print(data.temp);
      Serial.println(" C");

      Serial.print("Kelembaban : ");
      Serial.print(data.hum);
      Serial.println(" %");
    }
  }
}
```

# Dokumentasi
<p align="center">
  <img width="45%" alt="dokumentasi pecobaan5A" src="https://github.com/user-attachments/assets/340c2f80-20f6-4ba1-a941-88b0828fd5e1" />
  <img width="45%" alt="dokumentasivideopercobaan5A-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/1c603a16-f116-431d-a569-704d65627605" />
</p>

<p align="center">
  <img wwidth="45%" alt="dokumentasi percobaan5B" src="https://github.com/user-attachments/assets/b9195ee6-c003-4e3c-80b4-c779f695b597" />
  <img width="45%" alt="dokumentasivideopercobaan5B-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/53f8f8ff-ec13-48a4-8478-d6d09e61e639" />
</p>
