#1.5.4
1. Pada kondisi apa program masuk ke blok if?
2. Pada kondisi apa program masuk ke blok else?
3. Apa fungsi dari perintah delay(timeDelay)?
4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati), ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang → mati dan berikan penjelasan disetiap baris kode
---
1. Program akan masuk ke dalam blok if ketika nilai variabel timeDelay kurang dari atau sama dengan 100 (timeDelay <= 100)
2. Program akan masuk ke blok else apabila timeDelay lebih dari 100
3. Perintah ini berfungsi untuk memberikan jeda pada program. Semakin tinggi angka pada variabel timeDelay, maka jeda waktunya akan semakin lambat.
```C++
const int ledPin = 6;
int timeDelay = 1000;
int fase = 0; // 0 = Makin Cepat, 1 = Sedang, 2 = Mati

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {
  if (fase == 0) {
    // Nyalakan LED (Fase Lambat -> Cepat)
    digitalWrite(ledPin, HIGH);
    delay(timeDelay);
    digitalWrite(ledPin, LOW);
    delay(timeDelay);

    if (timeDelay <= 100) {
      fase = 1; // Pindah ke fase sedang
    } else {
      timeDelay -= 100; // Percepatan bertahap
    }
  } 
  else if (fase == 1) {
    // Fase Sedang
    timeDelay = 500;
    digitalWrite(ledPin, HIGH);
    delay(timeDelay);
    digitalWrite(ledPin, LOW);
    delay(timeDelay);
    fase = 2; // Pindah ke fase mati
  } 
  else if (fase == 2) {
    // Fase Mati (Reset ke awal setelah jeda)
    delay(3000); 
    timeDelay = 1000; // Reset ke nilai awal
    fase = 0; // Mulai siklus lagi
  }
}```

https://media1.tenor.com/m/K8A8OIcNpQEAAAAd/grandma-grandma-sad.gif
