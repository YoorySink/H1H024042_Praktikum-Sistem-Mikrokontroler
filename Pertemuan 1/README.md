## 1.5.4
1. Pada kondisi apa program masuk ke blok if?
   >Program akan masuk ke dalam blok if ketika nilai variabel timeDelay kurang dari atau sama dengan 100 (timeDelay <= 100)
2. Pada kondisi apa program masuk ke blok else?
   >Program akan masuk ke blok else apabila timeDelay lebih dari 100
3. Apa fungsi dari perintah delay(timeDelay)?
   >Perintah ini berfungsi untuk memberikan jeda pada program. Semakin tinggi angka pada variabel timeDelay, maka jeda waktunya akan semakin lambat.
4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati), ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang → mati dan berikan penjelasan disetiap baris kode
```C++
int ledPin = 6;         // Colokan LED ada di pin 6
int timeDelay = 1000;   // Catatan waktu jeda awal 1000 milidetik (1 detik)
int phase = 0;          // Penanda tugas: 0 artinya makin cepat, 1 artinya makin lambat

void setup() {
  // Bagian persiapan, cuma dijalanin sekali pas Arduino nyala
  pinMode(ledPin, OUTPUT); // Kasih tau Arduino kalau pin 6 itu ngeluarin listrik
}

void loop() {
  // Bagian utama, dijalanin terus-menerus tanpa henti
  
  //Nyalakan dan matikan lampu sesuai timeDelay
  digitalWrite(ledPin, HIGH); // Nyalakan lampu
  delay(timeDelay);           // Tunggu sebentar sesuai waktu timeDelay saat ini

  digitalWrite(ledPin, LOW);  // Matikan lampu
  delay(timeDelay);           // Tunggu sebentar lagi saat lampu mati
  
  // Kalau jeda udah paling cepet (100) dan lagi di fase 0 (makin cepat)
  if (timeDelay <= 100 && phase == 0) {
    phase = 1; // Ubah tugas ke fase 1 (mulai ngerem/makin lambat)
  } 
  // Kalau jeda udah lambat banget (lebih dari 1000) dan lagi di fase 1
  else if (timeDelay > 1000 && phase == 1) {
    delay(3000);        // Istirahat (tahan lampu mati) selama 3 detik
    timeDelay = 1000;   // Balikin nilai timeDelay jadi 1 detik lagi
    phase = 0;          // Kembali ke fase 0 untuk mulai makin cepat lagi
  } 
  // Kalau kondisinya belum nyentuh yang paling cepet atau paling lambat
  else {
    if (phase == 0) {
      // Kalau lagi di fase 0, timeDelay dikurangin 100 biar makin cepat
      timeDelay = timeDelay - 100; 
    } else {
      // Kalau lagi di fase 1, timeDelay ditambahin 200 biar makin lambat
      timeDelay = timeDelay + 200; 
    }
  }
}
```

## 1.6.4
1. Gambarkan rangkaian schematic 5 LED running yang digunakan pada percobaan!
   ><img width="882" height="300" alt="image" src="https://github.com/user-attachments/assets/9288aa77-af91-45b9-928e-eae7aec025fb" />
2. Jelaskan bagaimana program membuat efek LED berjalan dari kiri ke kanan!
   >Program memanfaatkan perulangan for mulai dari pin rendah ke tinggi, yaitu for (int ledPin = 2; ledPin < 8; ledPin++). Di dalam perulangan ini, pin LED dihidupkan dengan perintah digitalWrite(ledPin, HIGH) , ditunda sesaat sesuai variabel timer , kemudian dimatikan dengan digitalWrite(ledPin, LOW)
3. Jelaskan bagaimana program membuat LED kembali dari kanan ke kiri!
   >Program menggunakan perulangan for yang dibalik (decrement), yaitu dari pin tinggi ke rendah dengan sintaks for (int ledPin = 7; ledPin >= 2; ledPin--). Secara berurutan, program menghidupkan pin , menundanya , dan mematikan pin tersebut  sehingga tercipta efek menyala mundur.
4. Buatkan program agar LED menyala tiga LED kanan dan tiga LED kiri secara bergantian dan berikan penjelasan disetiap baris kode
```C
int timer = 500;        // Catatan waktu jeda pergantian lampu (500 milidetik / setengah detik)
unsigned int phase = 0; // Penanda tugas: 0 (kelompok kiri nyala), 1 (kelompok kanan nyala)

void setup() {
  // Bagian persiapan, dijalankan sekali
  // Pakai perulangan (looping) biar nggak capek nulis pinMode satu-satu untuk pin 2 sampai 7
  for (int ledPin = 2; ledPin < 8; ledPin++) {
    pinMode(ledPin, OUTPUT); // Jadikan pin-pin tersebut sebagai tempat keluar listrik
  }
}

void loop() {
  // Bagian utama, dijalankan terus-menerus
  
  // Penjaga gawang: Pastikan nilai phase cuma bolak-balik di angka 0 dan 1.
  // Kalau phase-nya 2, otomatis dikembalikan jadi 0 (sisa bagi dari 2 dibagi 2)
  phase = phase % 2; 

  // Mengatur nyala lampu satu per satu dari pin 2 sampai 7
  for (int ledPin = 2; ledPin < 8; ledPin++) {
    
    // Kalau yang lagi dicek itu pin 2, 3, atau 4 (Kelompok Kiri)
    if (ledPin <= 4) {
      if (phase == 0) {
        digitalWrite(ledPin, HIGH); // Nyalakan saat fase 0
      } else {
        digitalWrite(ledPin, LOW);  // Matikan saat fase 1
      }
    } 
    // Kalau yang dicek selain itu, berarti pin 5, 6, 7 (Kelompok Kanan)
    else {
      if (phase == 0) {
        digitalWrite(ledPin, LOW);  // Matikan saat fase 0 (kebalikannya)
      } else {
        digitalWrite(ledPin, HIGH); // Nyalakan saat fase 1
      }
    }
  }

  // Tunggu sebentar biar mata kita bisa lihat pergantian nyalanya
  delay(timer); 

  // Lanjut ke fase berikutnya biar tugasnya bergantian di putaran selanjutnya
  phase++; 
}
```

siklus tanpa henti , sementara percabangan yang ditempatkan di dalam perulangan tersebut berfungsi menyeleksi kondisi secara instan pada setiap siklus putarannya. Hasilnya, sistem secara dinamis bisa memutuskan untuk mengubah status (misal menyalakan peringatan LED) tepat di saat terjadi perubahan pada input.
