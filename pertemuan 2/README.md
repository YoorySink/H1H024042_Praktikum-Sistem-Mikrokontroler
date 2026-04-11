#2.5.4 Pertanyaan Praktikum
1. Gambarkan rangkaian schematic yang digunakan pada percobaan!
   >coming soon
2. Apa yang terjadi jika nilai num lebih dari 15?
   >Program akan mengalami array out of bounds, yaitu mencoba mengakses memori yang tidak ada atau di luar batas. Akibatnya, Arduino akan mengambil data acak (data sampah) dari memori, sehingga tampilan pada 7-segment akan menjadi ngawur atau tidak beraturan.
3. Apakah program ini menggunakan common cathode atau common anode? Jelaskan
alasanya!
   >pada tipe Anode, semua kutub positif LED sudah tersambung ke 5V, sehingga segmen hanya bisa menyala jika pin Arduino memberikan jalan ke Ground lewat sinyal LOW (0). karena itu pada Program ini menggunakan Common Anode karena adanya simbol ! (NOT) yang membalik logika 1 (nyala) menjadi sinyal LOW.
4. Modifikasi program agar tampilan berjalan dari F ke 0 dan berikan penjelasan disetiap
baris kode nya dalam bentuk README.md!
```C
#include <Arduino.h>
// 7-Segment Common Anode
// Pin mapping segment: a b c d e f g dp 
const int segmentPins[8] = {7, 6, 5, 10, 11, 8, 9, 4};

byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, //0
  {0,1,1,0,0,0,0,0}, //1
  {1,1,0,1,1,0,1,0}, //2
  {1,1,1,1,0,0,1,0}, //3 
  {0,1,1,0,0,1,1,0}, //4
  {1,0,1,1,0,1,1,0}, //5
  {1,0,1,1,1,1,1,0}, //6
  {1,1,1,0,0,0,0,0}, //7
  {1,1,1,1,1,1,1,0}, //8
  {1,1,1,1,0,1,1,0}, //9
  {1,1,1,0,1,1,1,0}, //A
  {0,0,1,1,1,1,1,0}, //b
  {1,0,0,1,1,1,0,0}, //C
  {0,1,1,1,1,0,1,0}, //d
  {1,0,0,1,1,1,1,0}, //E
  {1,0,0,0,1,1,1,0}  //F
}; //Pola Digit (0-F) dalam format Biner

// Fungsi tampil digit (dibalik untuk CA)
void displayDigit(int num)
{
  for(int i=0; i<8; i++)
  { // Jika pola bernilai 1 (nyala), digitalWrite akan mengirim LOW (0) ke LED
    // Karena  pakai Common Anode, LED butuh Ground (0) untuk menyala
    digitalWrite(segmentPins[i], !digitPattern[num][i]);
  }
}

void setup()
{ //inisiasi dari index 0 ke 7
  for(int i=0; i<8; i++)
  {
    pinMode(segmentPins[i], OUTPUT); //set pin segmentpins sebagai output
  }
}

void loop() 
{ // Mulai dari 15 (F) hingga 0, berkurang 1 setiap iterasi
  for(int i=15; i>=0; i-)
  {
    displayDigit(i); //menampilkan digit
    delay(1000); //delay 1 detik tiap digit untuk tampil
  }
}```
   
