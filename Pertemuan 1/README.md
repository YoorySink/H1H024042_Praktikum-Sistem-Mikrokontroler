## 1.5.4
1. Pada kondisi apa program masuk ke blok if?
2. Pada kondisi apa program masuk ke blok else?
3. Apa fungsi dari perintah delay(timeDelay)?
4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati), ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang → mati dan berikan penjelasan disetiap baris kode
---
1. Program akan masuk ke dalam blok if ketika nilai variabel timeDelay kurang dari atau sama dengan 100 (timeDelay <= 100)
2. Program akan masuk ke blok else apabila timeDelay lebih dari 100
3. Perintah ini berfungsi untuk memberikan jeda pada program. Semakin tinggi angka pada variabel timeDelay, maka jeda waktunya akan semakin lambat.

```C++
const int ledPin = 6; // Nomor pin LED yang dipakai
int timeDelay = 1000; // Variabel penampung waktu delay (kecepatan awal)
unsigned int phase = 0; // Variabel penampung fase (0 = lambat -> cepat, 1 = cepat -> sedang -> mati/reset)

void setup() { 
    // Fungsi yang akan dijalankan sekali saat Arduino menyala
    pinMode(ledPin, OUTPUT); // Menginisiasi pin LED sebagai output
}

void loop() { 
    // Fungsi yang akan dijalankan terus-menerus oleh Arduino
    digitalWrite(ledPin, HIGH); // Menyalakan LED
    delay(timeDelay); // Jeda, menentukan berapa lama LED menyala

    digitalWrite(ledPin, LOW); // Mematikan LED
    delay(timeDelay); // Jeda, menentukan berapa lama LED mati

    // Percabangan untuk mengatur fase dan waktu delay
    if (timeDelay <= 100 && phase == 0) { 
        // Mengecek apakah timeDelay sudah maksimal cepat (<= 100) dan sedang di fase 0
        phase = 1; // Mengubah ke fase kebalikannya (melambat menuju mati)
    } 
    else if (timeDelay > 1000 && phase == 1) { 
        // Mengecek apakah timeDelay sudah melambat melebihi 1000 dan sedang di fase 1
        delay(3000); // Memberikan jeda mati selama 3 detik sebelum siklus baru (reset)
        timeDelay = 1000; // Mengembalikan nilai timeDelay ke 1000 (lambat)
        phase = 0; // Mengembalikan nilai phase ke 0 (memulai siklus percepatan lagi)
    } 
    else { 
        // Jika kedua kondisi batas di atas belum terpenuhi, atur perubahan kecepatannya
        if (phase == 0) {
            timeDelay -= 100; // Jika sedang di fase 0, kurangi jeda waktu agar makin cepat
        } else {
            timeDelay += 200; // Jika di fase 1, tambah jeda waktu agar makin lambat
        }
    }
}
```

## 1.6.4
1. Gambarkan rangkaian schematic 5 LED running yang digunakan pada percobaan!
   ><img width="882" height="300" alt="image" src="https://github.com/user-attachments/assets/9288aa77-af91-45b9-928e-eae7aec025fb" />

3. Jelaskan bagaimana program membuat efek LED berjalan dari kiri ke kanan!
4. Jelaskan bagaimana program membuat LED kembali dari kanan ke kiri!
5. Buatkan program agar LED menyala tiga LED kanan dan tiga LED kiri secara bergantian dan berikan penjelasan disetiap baris kode
---
1. 
2. Program memanfaatkan perulangan for mulai dari pin rendah ke tinggi, yaitu for (int ledPin = 2; ledPin < 8; ledPin++). Di dalam perulangan ini, pin LED dihidupkan dengan perintah digitalWrite(ledPin, HIGH) , ditunda sesaat sesuai variabel timer , kemudian dimatikan dengan digitalWrite(ledPin, LOW)
3. Program menggunakan perulangan for yang dibalik (decrement), yaitu dari pin tinggi ke rendah dengan sintaks for (int ledPin = 7; ledPin >= 2; ledPin--). Secara berurutan, program menghidupkan pin , menundanya , dan mematikan pin tersebut  sehingga tercipta efek menyala mundur.
4. 

## 1.7
1. Uraikan hasil tugas pada praktikum yang telah dilakukan pada setiap percobaan!
2. Bagaimana pengaruh penggunaan struktur perulangan (seperti for dan while) terhadap jalannya program pada Arduino?
3. Bagaimana cara kerja percabangan (if-else) dalam menentukan kondisi LED menyala atau mati berdasarkan input yang diberikan?
4. Bagaimana kombinasi antara perulangan dan percabangan dapat digunakan untuk membuat sistem Arduino yang responsif terhadap perubahan kondisi lingkungan?
---
1.
   - Percobaan 1A: Hasilnya menunjukkan penggunaan logika percabangan if-else. LED dapat berkedip dengan jeda yang makin pendek (makin cepat) akibat variabel yang dikurangi dalam blok else , dan program mengatur kembali kondisinya saat nilai jeda menyentuh ambang batas tertentu di dalam blok if.
   - Percobaan 2A: Hasilnya menghasilkan efek LED running. Melalui penggunaan perulangan for maju dan mundur , LED menyala secara berurutan dari kiri ke kanan, dan berbalik arah dari kanan ke kiri secara konsisten berulang kali.
2. Penggunaan perulangan memungkinkan Arduino mengeksekusi blok kode secara berulang kali sehingga sangat menghemat penulisan kode. Perulangan for membuat penambahan atau pengurangan berulang kali secara rapi asalkan syaratnya terpenuhi , sedangkan while akan terus mengulang selama kondisi di dalamnya bernilai benar yang mana proses pengecekannya mirip dengan perintah IF.
3. Percabangan if-else bekerja dengan menyeleksi sebuah kondisi tunggal. Jika kondisi dalam blok if terpenuhi, maka perintah di dalamnya akan diproses untuk menentukan nyala atau mati LED. Jika kondisi tersebut tidak sesuai, maka Arduino mengeksekusi perintah yang ada di dalam blok else sebagai alternatif pengambilan keputusan. Hal ini membuat program mampu memilih jalur eksekusi dinamis.
4. Kombinasi keduanya dapat digunakan untuk membuat sistem kontrol perangkat keras yang adaptif. Perulangan memastikan Arduino terus menerus memantau keadaan lingkungan dan menjalankan siklus tanpa henti , sementara percabangan yang ditempatkan di dalam perulangan tersebut berfungsi menyeleksi kondisi secara instan pada setiap siklus putarannya. Hasilnya, sistem secara dinamis bisa memutuskan untuk mengubah status (misal menyalakan peringatan LED) tepat di saat terjadi perubahan pada input.
