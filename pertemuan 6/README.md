# 6.5.4 Percobaan 6A
### 1. Jelaskan proses bagaimana tombol dapat mengubah kondisi LED menggunakan
interrupt!
> Ketika tombol ditekan, pin interrupt pada Arduino mendeteksi perubahan sinyal sehingga interrupt akan aktif dan menghentikan sementara program utama. Setelah itu, Arduino menjalankan ISR (tombolInterrupt()) untuk mengubah nilai variabel ledState, kemudian program utama kembali berjalan dan LED berubah kondisi menjadi ON atau OFF sesuai nilai variabel tersebut.
### 2. Apa fungsi attachInterrupt() pada program tersebut?
> Fungsi attachInterrupt() digunakan untuk menghubungkan pin interrupt dengan fungsi ISR yang akan dijalankan ketika kondisi interrupt terjadi. Pada program, fungsi ini digunakan agar Arduino dapat menjalankan fungsi tombolInterrupt() ketika tombol ditekan menggunakan mode FALLING.
### 3. Mengapa pada ISR tidak disarankan menggunakan delay() dan Serial.print()?
> Penggunaan delay() dan Serial.print() di dalam ISR tidak disarankan karena dapat membuat proses interrupt menjadi lambat dan menghambat jalannya program utama. ISR sebaiknya dibuat singkat agar sistem tetap responsif dan interrupt lain tidak tertunda.
### 4. Apa fungsi keyword volatile pada variabel ledState?
> Keyword volatile digunakan agar compiler selalu membaca nilai terbaru dari variabel karena nilainya dapat berubah sewaktu-waktu di dalam ISR. Dengan menggunakan volatile, sinkronisasi data antara ISR dan program utama dapat berjalan dengan benar.
### 5. Pada percobaan digunakan mode interrupt FALLING. Modifikasikan program menggunakan mode interrupt lain (RISING, CHANGE, atau LOW) kemudian:
> - Jelaskan perbedaan cara kerja masing-masing mode interrupt tersebut
> - Analisis perubahan perilaku LED yang terjadi pada setiap mode
> - Sertakan source code dan penjelasan program dalam bentuk README.md
```C
#include <Arduino.h> 
// Memanggil library utama Arduino
volatile bool ledState = false; 
// Variabel untuk menyimpan status LED
// volatile digunakan karena variabel diubah di dalam ISR
void tombolInterrupt() { 
// Fungsi ISR (Interrupt Service Routine)
// Akan dijalankan saat interrupt terjadi
 ledState = !ledState; 
 // Mengubah kondisi LED
 // Jika sebelumnya OFF menjadi ON, dan sebaliknya
}

void setup() {
 pinMode(13, OUTPUT); 
 // Mengatur pin 13 sebagai output untuk LED
 pinMode(2, INPUT_PULLUP); 
 // Mengatur pin 2 sebagai input dengan pull-up internal
 // Tombol akan aktif saat ditekan
 attachInterrupt(
  digitalPinToInterrupt(2), 
  // Mengubah pin 2 menjadi pin interrupt
  tombolInterrupt, 
  // Fungsi ISR yang akan dijalankan
  CHANGE 
  // Interrupt aktif setiap ada perubahan sinyal
  // LOW ke HIGH atau HIGH ke LOW
 );
}
void loop() {
 digitalWrite(13, ledState); 
 // Menyalakan atau mematikan LED
 // sesuai nilai variabel ledState
}
```
> Pada mode CHANGE, interrupt akan aktif setiap terjadi perubahan sinyal pada pin input, baik saat tombol ditekan maupun dilepas. Akibatnya LED dapat berubah kondisi dua kali lebih cepat dibanding mode FALLING



# 6.6.4 Percobaan 6B
### 1. Jelaskan bagaimana fungsi millis() bekerja pada program tersebut!
>  Fungsi millis() bekerja dengan menghitung waktu sejak Arduino pertama kali dinyalakan dalam satuan milidetik. Pada program, nilai millis() dibandingkan dengan waktu sebelumnya (previousMillis) untuk menentukan apakah interval waktu tertentu telah tercapai sehingga LED dapat diubah kondisinya secara periodik tanpa menggunakan delay()
### 2. Apa perbedaan utama antara delay() dan millis()?
> delay() menghentikan sementara seluruh proses program selama waktu yang ditentukan sehingga mikrokontroler tidak dapat menjalankan tugas lain. Sedangkan millis() menggunakan metode perhitungan waktu tanpa menghentikan program utama sehingga sistem tetap dapat menjalankan proses lain secara bersamaan.
### 3. Mengapa metode millis() disebut non-blocking
> Metode millis() disebut non-blocking karena program utama tetap berjalan selama proses perhitungan waktu berlangsung. Dengan metode ini, mikrokontroler masih dapat membaca input, menjalankan task lain, dan merespon interrupt tanpa harus menunggu jeda waktu selesai.
### 4. Modifikasi program agar:
> - LED pertama berkedip setiap 1 detik
> - LED kedua berkedip setiap 500 ms
> - Tanpa menggunakan delay()
```C
#include <Arduino.h>
// Memanggil library utama Arduino
unsigned long previousMillis1 = 0;
// Menyimpan waktu terakhir LED 1 berubah
unsigned long previousMillis2 = 0;
// Menyimpan waktu terakhir LED 2 berubah
const long interval1 = 1000;
// Interval LED 1 = 1000 ms (1 detik)
const long interval2 = 500;
// Interval LED 2 = 500 ms
bool ledState1 = false;
// Status LED 1
bool ledState2 = false;
// Status LED 2

void setup() {
 pinMode(13, OUTPUT);
 // Mengatur pin 13 sebagai output LED 1
 pinMode(12, OUTPUT);
 // Mengatur pin 12 sebagai output LED 2
}

void loop() {
 unsigned long currentMillis = millis();
 // Mengambil waktu saat ini dari millis()

 if(currentMillis - previousMillis1 >= interval1) {
 // Mengecek apakah interval LED 1 sudah tercapai
  previousMillis1 = currentMillis;
  // Memperbarui waktu terakhir LED 1
  ledState1 = !ledState1;
  // Mengubah kondisi LED 1
  digitalWrite(13, ledState1);
  // Menulis kondisi LED 1 ke pin 13
 }

 if(currentMillis - previousMillis2 >= interval2) {
 // Mengecek apakah interval LED 2 sudah tercapai
  previousMillis2 = currentMillis;
  // Memperbarui waktu terakhir LED 2
  ledState2 = !ledState2;
  // Mengubah kondisi LED 2
  digitalWrite(12, ledState2);
  // Menulis kondisi LED 2 ke pin 12
 }
}
```


# dokumentasi
<p align="center">
 <img width="45%" alt="dokumentasi percobaan6A" src="https://github.com/user-attachments/assets/cfce665e-9a50-4061-b5fe-c06040a7c1f3" />
 <img width="45%" alt="dokumentasi perocbaan6B" src="https://github.com/user-attachments/assets/5f4a8c72-754d-4417-a775-aade10444955" />
</p>
<p align="center">
<img wwidth="90%" alt="dokumentasi video percobaan6A" src="https://github.com/user-attachments/assets/c3498e4e-19a3-4afc-9c17-9aa303924b91" />
<img wwidth="90%" alt="dokumentasi video percobaan6B" src="https://github.com/user-attachments/assets/6a7421ba-d8cc-429d-9aed-56ebab2a2085" />
</p>
