## 3.5.4 Percobaan 3A 
### 1. Jelaskan proses dari input keyboard hingga LED menyala/mati!
> Ketika pengguna mengetik perintah pada keyboard di Serial Monitor, data dikirim ke Arduino melalui komunikasi serial (UART). Arduino kemudian membaca data tersebut menggunakan Serial.read(). Jika data yang diterima adalah '1', maka Arduino memberikan logika HIGH pada pin LED sehingga LED menyala. Jika data '0', maka diberikan logika LOW sehingga LED mati.
### 2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan?
> Serial.available() digunakan untuk mengecek apakah ada data yang tersedia di buffer serial sebelum dibaca. Jika fungsi ini dihilangkan, Arduino tetap mencoba membaca data meskipun tidak ada data yang masuk, sehingga dapat menghasilkan pembacaan data yang tidak valid atau error.
### 3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan berikan penjelasan disetiap baris kode nya
```C
#include <Arduino.h>

const int PIN_LED = 12; // Pin LED terhubung ke pin digital 12

// Variabel untuk sistem blink (millis)
unsigned long prevMillis = 0;   // Menyimpan waktu terakhir LED berubah
const long interval = 500;      // Interval kedip LED (500 ms = 0.5 detik)

// Variabel state
int isBlink = 0;   // 0 = tidak blink, 1 = blink aktif
int ledState = 0;  // Status LED (0 = mati, 1 = nyala)

void setup() {
  Serial.begin(9600); 
  // Memulai komunikasi serial dengan baud rate 9600
  // Artinya kecepatan kirim data antara Arduino dan komputer = 9600 bit/detik

  Serial.println("Ketik '1' ON, '2' BLINK, '0' OFF"); 
  // Menampilkan instruksi ke Serial Monitor

  pinMode(PIN_LED, OUTPUT); 
  // Mengatur pin LED sebagai OUTPUT agar bisa mengontrol nyala/mati LED
}

void loop() {
  // Mengecek apakah ada data yang dikirim dari Serial Monitor
  if (Serial.available() > 0) {

    char input = Serial.read();  
    // Membaca 1 karakter dari input (misalnya '1', '0', atau '2')

    // Jika input '1' → LED menyala
    if (input == '1') {
      isBlink = 0; // Matikan mode blink
      digitalWrite(PIN_LED, HIGH); // Nyalakan LED
      Serial.println("LED ON"); 
    }

    // Jika input '0' → LED mati
    else if (input == '0') {
      isBlink = 0; // Matikan mode blink
      digitalWrite(PIN_LED, LOW); // Matikan LED
      Serial.println("LED OFF"); 
    }

    // Jika input '2' → LED berkedip terus
    else if (input == '2') {
      isBlink = 1; // Aktifkan mode blink
      Serial.println("LED BLINK");
    }

    // Jika input selain 1, 0, 2 (dan bukan enter)
    else if (input != '\n' && input != '\r') {
      Serial.println("Perintah tidak dikenali");
    }
  }
  if (isBlink == 1) { // Jalankan hanya jika mode blink aktif
    unsigned long currentMillis = millis(); 
    // Mengambil waktu sekarang sejak Arduino dinyalakan (dalam milidetik)
    // Cek apakah selisih waktu sudah mencapai interval (500 ms)
    if (currentMillis - prevMillis >= interval) {
      prevMillis = currentMillis; 
      // Simpan waktu sekarang sebagai acuan untuk perhitungan berikutnya
      ledState = !ledState; 
      // Membalik status LED (0 jadi 1, 1 jadi 0)
      digitalWrite(PIN_LED, ledState); 
      // Mengatur LED sesuai state (nyala/mati bergantian)
    }
  }
}
```
### 4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap
sistem
> Pada program ini digunakan fungsi millis() untuk mengatur waktu kedipan LED. Fungsi millis() memungkinkan pembuatan delay secara non-blocking atau interupt sehingga Arduino tetap dapat menjalankan proses lain seperti membaca input dari Serial Monitor saat LED sedang berkedip.

## 3.6.4 Percobaan 3B
### 1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD!
> Pada komunikasi I2C, Arduino bertindak sebagai master dan LCD sebagai slave. Arduino mengirimkan data melalui jalur SDA dan sinyal clock melalui SCL. Setiap perangkat memiliki alamat unik, sehingga Arduino dapat mengirim data ke LCD berdasarkan alamat tersebut (misalnya 0x27).
### 2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan pin kanan tertukar
> boleh ditukar. Jika pin kiri dan kanan tertukar, maka arah perubahan nilai akan terbalik, tetapi masih dapat terbaca oleh Arduino.
### 3. Modifikasi program (gabungan UART + I2C output)
```C
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
// ganti alamat jika perlu (0x27 / 0x20)
// Inisialisasi LCD alamat 0x27 ukuran 16 kolom 2 baris
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Pin input potensiometer
const int pinPot = A0;

void setup() {
  Serial.begin(9600);   // Mulai komunikasi serial (UART)

  lcd.init();           // Inisialisasi LCD
  lcd.backlight();      // Nyalakan lampu LCD
}

void loop() {
  int nilai = analogRead(pinPot);  // Baca nilai ADC (0–1023)

  int persen = (float)nilai / 1023 * 100;   // Konversi ke persen (0–100%)
  float volt = nilai * (5.0 / 1023.0);      // Konversi ke tegangan (0–5V)

  // Mapping ke bar (0-16)
  int panjangBar = map(nilai, 0, 1023, 0, 16); // Mapping ke panjang bar LCD

  // OUTPUT KE SERIAL
  Serial.print("ADC: ");
  Serial.print(nilai);
  Serial.print("\tVolt: ");
  Serial.print(volt, 2); // tampilkan 2 angka desimal
  Serial.print(" V");
  Serial.print("\tPersen: ");
  Serial.print(persen);
  Serial.println("%");

  // OUTPUT KE LCD 
  lcd.clear(); // Bersihkan layar sebelum update

  // Baris 1: Nilai adc dan persenannya
  lcd.setCursor(0, 0);
  lcd.print("ADC:");
  lcd.print(nilai);
  lcd.print("  "); //clear sisa

  lcd.setCursor(10, 0);
  lcd.print(persen);
  lcd.print("%");

  // Baris 2: bar
  // pengulangan untuk membuat bar
  lcd.setCursor(0, 1);
  for (int i = 0; i < 16; i++) {
    if (i < panjangBar) {
      lcd.print((char)255);
    } else {
      lcd.print(" ");
    }
  }

  delay(300); // Delay agar tampilan tidak terlalu cepat (lebih stabil)
}
```
### 4. Lengkapi table berikut berdasarkan pengamatan pada Serial Monitor
| ADC | Volt (V) | Persen (%) |
| :---: | :---: | :---: |
| 1 | 0.00 | 0% |
| 21 | 0.10 | 2% |
| 49 | 0.23 | 4% |
| 74 | 0.35 | 7% |
| 96 | 0.46 | 9% |

# Dokumentasi
<table>
  <tr>
    <td> <img width="1280" height="720" alt="WhatsApp Image 2026-04-15 at 11 29 15" src="https://github.com/user-attachments/assets/00bf0c3a-1c33-47a5-ae3d-019ca147aa06" />
    </td>
    <td> <img width="1280" height="720" alt="WhatsApp Image 2026-04-15 at 11 29 15" src="https://github.com/user-attachments/assets/70e1915f-1e06-4aad-801f-b6df8eb94ede" />
    </td>
  </tr>
  <tr>
    <td><img width="567" height="320" alt="WhatsAppVideo2026-04-15at11 58 42-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/7fdb38af-d09e-42a5-ab5f-34c843ac4c6e" />
    </td>
    <td><img width="567" height="320" alt="WhatsAppVideo2026-04-15at11 29 20-ezgif com-video-to-gif-converter (1)" src="https://github.com/user-attachments/assets/0ed1e52f-199e-456c-8e29-648b07fec55a" />
    </td>
  </tr>
</table>



