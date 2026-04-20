## Percobaan 3A: UART
### 1. Jelaskan proses dari input keyboard hingga LED menyala/mati!
> Ketika pengguna mengetik perintah pada keyboard di Serial Monitor, data dikirim ke Arduino melalui komunikasi serial (UART). Arduino kemudian membaca data tersebut menggunakan Serial.read(). Jika data yang diterima adalah '1', maka Arduino memberikan logika HIGH pada pin LED sehingga LED menyala. Jika data '0', maka diberikan logika LOW sehingga LED mati.
### 2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan?
> Serial.available() digunakan untuk mengecek apakah ada data yang tersedia di buffer serial sebelum dibaca. Jika fungsi ini dihilangkan, Arduino tetap mencoba membaca data meskipun tidak ada data yang masuk, sehingga dapat menghasilkan pembacaan data yang tidak valid atau error.
### 3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi
jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan
berikan penjelasan disetiap baris kode nya
```C
#include <Arduino.h>        // Library utama Arduino

const int PIN_LED = 12;     // Menentukan pin LED di pin 12
bool blinkMode = false;     // Variabel untuk menyimpan mode blink (true/false)

void setup() {
  Serial.begin(9600);      // Memulai komunikasi serial dengan baud rate 9600
  Serial.println("Ketik '1' ON, '0' OFF, '2' BLINK"); // Instruksi ke user
  pinMode(PIN_LED, OUTPUT); // Mengatur pin LED sebagai output
}

void loop() {
  // Mengecek apakah ada data yang masuk dari Serial Monitor
  if (Serial.available() > 0) {
    char data = Serial.read(); // Membaca data 1 karakter dari serial

    if (data == '1') {         // Jika input '1'
      blinkMode = false;       // Matikan mode blink
      digitalWrite(PIN_LED, HIGH); // Nyalakan LED
      Serial.println("LED ON");    // Tampilkan status
    } 
    else if (data == '0') {    // Jika input '0'
      blinkMode = false;       
      digitalWrite(PIN_LED, LOW);  // Matikan LED
      Serial.println("LED OFF");
    } 
    else if (data == '2') {    // Jika input '2'
      blinkMode = true;        // Aktifkan mode blink
      Serial.println("LED BLINK");
    }
  }

  // Jika mode blink aktif
  if (blinkMode) {
    digitalWrite(PIN_LED, HIGH); // LED nyala
    delay(500);                  // Tunggu 500 ms
    digitalWrite(PIN_LED, LOW);  // LED mati
    delay(500);                  // Tunggu 500 ms
  }
}
```
### 4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap
sistem
> Pada program ini digunakan delay() karena lebih sederhana untuk membuat efek kedip. Namun, delay() bersifat blocking sehingga Arduino tidak dapat melakukan proses lain selama delay berlangsung. Jika menggunakan millis(), program menjadi non-blocking sehingga lebih efisien dan dapat menangani banyak proses sekaligus.

## Percobaan 3B: I2C
### 1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD!
> Pada komunikasi I2C, Arduino bertindak sebagai master dan LCD sebagai slave. Arduino mengirimkan data melalui jalur SDA dan sinyal clock melalui SCL. Setiap perangkat memiliki alamat unik, sehingga Arduino dapat mengirim data ke LCD berdasarkan alamat tersebut (misalnya 0x27).
### 2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan
pin kanan tertukar
> Ya. Konfigurasi pin potensiometer harus sesuai agar menghasilkan pembacaan yang benar. Jika pin kiri dan kanan tertukar, maka arah perubahan nilai akan terbalik, tetapi masih dapat terbaca oleh Arduino.
### 3. Modifikasi program (gabungan UART + I2C output)
```
#include <Wire.h>                // Library komunikasi I2C
#include <LiquidCrystal_I2C.h>   // Library LCD I2C
#include <Arduino.h>             // Library utama Arduino

LiquidCrystal_I2C lcd(0x27, 16, 2); // Inisialisasi LCD (alamat 0x27, 16x2)

const int pinPot = A0;           // Pin analog untuk potensiometer

void setup() {
  Serial.begin(9600);           // Memulai komunikasi serial
  lcd.init();                   // Inisialisasi LCD
  lcd.backlight();              // Mengaktifkan lampu latar LCD
}

void loop() {
  int nilai = analogRead(pinPot); // Membaca nilai analog (0-1023)

  float volt = nilai * (5.0 / 1023.0); // Konversi ke tegangan (Volt)
  int persen = map(nilai, 0, 1023, 0, 100); // Konversi ke persen (0-100%)

  // Menampilkan data ke Serial Monitor (UART)
  Serial.print("ADC: ");
  Serial.print(nilai);
  Serial.print(" Volt: ");
  Serial.print(volt);
  Serial.print(" V Persen: ");
  Serial.print(persen);
  Serial.println("%");

  // Menampilkan data ke LCD baris pertama
  lcd.setCursor(0, 0);          // Set posisi kolom 0, baris 0
  lcd.print("ADC:");
  lcd.print(nilai);             
  lcd.print("   ");             // Menghapus sisa karakter lama

  // Membuat progress bar di baris kedua
  lcd.setCursor(0, 1);          // Pindah ke baris kedua
  int panjangBar = map(nilai, 0, 1023, 0, 16); // Mapping ke panjang bar

  for (int i = 0; i < 16; i++) { // Loop sepanjang 16 kolom LCD
    if (i < panjangBar) {
      lcd.print((char)255);     // Karakter blok penuh
    } else {
      lcd.print(" ");           // Kosong
    }
  }

  delay(200);                   // Delay agar tampilan stabil
}
```

#Dokumentasi


