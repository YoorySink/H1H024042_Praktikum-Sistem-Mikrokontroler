## Percobaan 3A: UART
### 1. Jelaskan proses dari input keyboard hingga LED menyala/mati!
> Input dari keyboard dikirim melalui Serial Monitor ke Arduino dalam bentuk data serial. Arduino membaca data menggunakan fungsi `Serial.read()`, kemudian memprosesnya dengan kondisi tertentu (misalnya ‘1’ untuk ON, ‘0’ untuk OFF). Berdasarkan kondisi tersebut, Arduino mengatur pin digital sehingga LED menyala atau mati.
### 2. Mengapa digunakan `Serial.available()` sebelum membaca data? Apa yang terjadi jika dihilangkan?
> `Serial.available()` digunakan untuk memastikan bahwa data sudah tersedia sebelum dibaca. Jika dihilangkan, Arduino bisa mencoba membaca data kosong sehingga menghasilkan nilai tidak valid atau error dalam proses pembacaan.
### 3. Delay atau millis()? Jelaskan pengaruhnya terhadap sistem
> `delay()` menghentikan seluruh proses program sementara waktu, sehingga sistem tidak bisa melakukan tugas lain. Sedangkan `millis()` memungkinkan eksekusi non-blocking, sehingga program tetap berjalan dan lebih responsif. Untuk sistem yang kompleks, `millis()` lebih disarankan.
## Percobaan 3B: I2C
### 1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD!
> Arduino bertindak sebagai master dan LCD sebagai slave. Arduino mengirimkan data melalui jalur SDA dan SCL dengan alamat tertentu. LCD yang memiliki alamat tersebut akan menerima dan menampilkan data yang dikirimkan.
### 2. Apakah pin potensiometer harus seperti itu? Apa yang terjadi jika tertukar?
> Ya, konfigurasi pin harus sesuai. Jika pin kiri dan kanan tertukar, nilai pembacaan tetap ada tetapi arah perubahan menjadi terbalik (nilai maksimum menjadi minimum dan sebaliknya).
### 3. Jika UART dan I2C digabung, bagaimana alur kerjanya?
> Data input diterima melalui UART (Serial Monitor), kemudian diproses oleh Arduino. Hasilnya dapat dikirim ke LCD melalui I2C dan juga ditampilkan kembali ke Serial Monitor. Arduino dapat mengelola keduanya karena dijalankan dalam satu program secara berurutan.
## Pertanyaan Umum
### 1. Keuntungan dan kerugian UART dan I2C
> UART: sederhana dan mudah digunakan, tetapi hanya untuk komunikasi dua perangkat.
> I2C: dapat menghubungkan banyak perangkat dengan sedikit pin, namun lebih kompleks dan kecepatan lebih rendah dibanding SPI.
### 2. Peran alamat I2C (misalnya 0x27 vs 0x20)
> Alamat I2C digunakan untuk mengidentifikasi perangkat pada jalur yang sama. Jika alamat berbeda, Arduino dapat memilih perangkat yang dituju. Jika sama, dapat terjadi konflik komunikasi.
### 3. Bagaimana Arduino mengelola dua protokol sekaligus?
> Arduino menjalankan instruksi secara berurutan dalam program. Selama tidak terjadi konflik resource, UART dan I2C dapat digunakan bersamaan dalam satu sistem.

