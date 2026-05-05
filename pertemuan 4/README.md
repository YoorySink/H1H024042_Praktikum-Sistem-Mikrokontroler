# Percobaan 1(4A): Analog to Digital Converter (ADC)
### 1. Apa fungsi perintah analogRead() pada rangkaian praktikum ini?
> analogRead() adalah fungsi pada Arduino yang digunakan untuk membaca tegangan analog dari pin input (A0–A5) dan mengubahnya menjadi nilai digital dengan rentang 0–1023. Pada praktikum ini digunakan untuk membaca input dari potensiometer.
### 2. Mengapa diperlukan fungsi map() dalam program tersebut?
> Fungsi map() digunakan untuk mengubah rentang nilai tertentu ke rentang nilai lain. Pada praktikum ini, map() digunakan untuk mengkonversi nilai ADC (0–1023) menjadi sudut servo (misalnya 0–180 derajat) agar sesuai dengan kebutuhan output.
### 3. Modifikasi program berikut agar servo hanya bergerak dalam rentang 30° hingga 150°, meskipun potensiometer tetap memiliki rentang ADC 0–1023. Jelaskan program pada file README.md
> pada saat potensiometer menghasilkan output maksimal (1023), sudut motor servo hanya akan mencapai batas maksimal 150 derajat, begitu pula pada (0) ke 30 derajat
```C
pos = map(val,
            0,    // min ADC
            1023, // max ADC
            30,    // sudut min
            150   // sudut max
            ); 
```
> code lengkapnya :
```C 
#include <Servo.h> // library untuk servo motor

Servo myservo; // membuat objek servo

// ===================== PIN SETUP =====================
const int potensioPin = A0;   // pin analog input
const int servoPin = 9;       // pin PWM servo

// ===================== VARIABEL =====================
int pos = 0; // sudut servo
int val = 0; // nilai ADC

void setup() {

  // Hubungkan servo ke pin
  myservo.attach(servoPin);

  // Serial monitor
  Serial.begin(9600);

}

void loop() {

  // ===================== PEMBACAAN ADC =====================
  val = analogRead(potensioPin);

  // ===================== KONVERSI DATA =====================
  pos = map(val,
            0,    // min ADC
            1023, // max ADC
            30,    // sudut min
            150   // sudut max
            );

  // ===================== OUTPUT SERVO =====================
  myservo.write(pos);

  // ===================== MONITORING =====================
  Serial.print("ADC Potensio: ");
  Serial.print(val);

  Serial.print(" | Sudut Servo: ");
  Serial.println(pos);

  // ===================== STABILISASI =====================
  delay(100);
}
```

# Percobaan 2(4B): Pulse Width Modulation (PWM)
### Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!
> LED dapat diatur kecerahannya menggunakan fungsi analogWrite() karena Arduino menghasilkan sinyal PWM (Pulse Width Modulation), yaitu sinyal digital yang dinyalakan dan dimatikan dengan cepat. Kecerahan LED ditentukan oleh duty cycle, yaitu perbandingan lama waktu sinyal HIGH terhadap satu periode. Semakin besar nilai PWM, semakin lama sinyal berada pada kondisi HIGH sehingga tegangan rata-rata yang diterima LED meningkat dan LED terlihat lebih terang.
### Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?
> Nilai ADC memiliki rentang 0–1023 karena menggunakan resolusi 10-bit, sedangkan PWM memiliki rentang 0–255 karena menggunakan resolusi 8-bit. Oleh karena itu, diperlukan proses scaling (menggunakan fungsi map()) untuk menyesuaikan nilai ADC agar dapat digunakan sebagai nilai PWM.
### Modifikasilah program berikut agar LED hanya menyala pada rentang kecerahan sedang, yaitu hanya ketika nilai PWM berada pada rentang 50 sampai 200. Jelaskan program pada file README.md.
>
```C
  pwm = map(nilaiADC,
            0,     // ADC min
            1023,  // ADC max
            0,     // PWM min
            255    // PWM max
            );

  // ===================== OUTPUT PWM =====================
  if (pwm >= 50 && pwm <= 200){ //ketika rentan nilai pwm 50-200
  	analogWrite(ledPin, pwm);   // led menyala sesuai nilai pwm
  } else {
	analogWrite(ledPin, 0);       // led mati diluar rentang nilai
  }
```
> kode lengkapnya
```C

// ===================== PIN SETUP =====================
const int potPin = A0;   // pin analog potensiometer
const int ledPin = 9;    // pin PWM LED

// ===================== VARIABEL =====================
int nilaiADC = 0;  // nilai pembacaan ADC
int pwm = 0;       // nilai PWM

void setup() {

  // ===================== OUTPUT SETUP =====================
  pinMode(ledPin, OUTPUT);

  // ===================== SERIAL MONITOR =====================
  Serial.begin(9600);
}

void loop() {

  // ===================== PEMBACAAN SENSOR =====================
  nilaiADC = analogRead(potPin);

  // ===================== PEMROSESAN DATA =====================
  pwm = map(nilaiADC,
            0,     // ADC min
            1023,  // ADC max
            0,     // PWM min
            255    // PWM max
            );

  // ===================== OUTPUT PWM =====================
  if (pwm >= 50 && pwm <= 200){ //ketika rentan nilai pwm 50-200
  	analogWrite(ledPin, pwm);   // led menyala sesuai nilai pwm
  } else {
	analogWrite(ledPin, 0);       // led mati diluar rentang nilai
  }
  // ===================== MONITORING =====================
  Serial.print("ADC: ");
  Serial.print(nilaiADC);

  Serial.print(" | PWM: ");
  Serial.println(pwm);

  // ===================== STABILISASI =====================
  delay(50);
} 
```

# dokumentasi
<p align="center">
  <img width="45%" alt="WhatsApp Image 2026-05-05 at 18 51 57" src="https://github.com/user-attachments/assets/588a518d-5f3e-4d05-8e74-dc6da51b7411" />
  <img width="45%" alt="WhatsApp Image 2026-05-05 at 18 51 58" src="https://github.com/user-attachments/assets/872b795a-9ae6-4dd1-94ba-54367778ce5c" />
</p>

<p align="center">
  <img width="45%" alt="WhatsAppVideo2026-05-05at18 52 05-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/c45a4a22-1d7a-4409-aa05-60fb5df1977d" />
  <img width="45%" alt="WhatsAppVideo2026-05-05at18 52 00-ezgif com-video-to-gif-converter" src="https://github.com/user-attachments/assets/addac8b7-d109-464f-9f00-fc1774a3cff9" />
</p>
