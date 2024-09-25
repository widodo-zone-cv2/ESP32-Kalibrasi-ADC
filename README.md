Repositori ini berisi kode untuk membuat tabel lookup (LUT) yang digunakan untuk mengoreksi masalah non-linearitas ADC. Contoh juga disertakan mengenai cara menggunakan LUT dalam program Anda.

Kode ini berdasarkan karya dari [Helmut Weber](https://github.com/MacLeod-D/ESP32-ADC), yang pertama kali dijelaskan di [forum diskusi ESP32]((https://esp32.com/viewtopic.php?f=19&t=2881&start=30#p47663)), namun telah modifikasi dengan perbaikan bug oleh Henry Cheung.

dan pada repository ini sedikit telah saya modifikasi, untuk beberapa kode program pada pembacaan ADC setelah dilakukan kalibrasi.

### Masalah Linearitas pada ESP32

Untuk mengatasi masalah non-linearitas ADC pada ESP32, digunakan tabel lookup (LUT) untuk mengoreksi ketidakakuratan tersebut. Anda mungkin perlu membuat tabel lookup sendiri karena nilai tersebut bisa berbeda pada setiap perangkat, tergantung pada variasi tegangan referensi internal ESP32.

[![ESP32 ADC linearity](https://github.com/e-tinkers/esp32-adc-calibrate/blob/master/images/esp32_ADClinearity.png)](https://github.com/e-tinkers/esp32-adc-calibrate/blob/master/images/esp32_ADClinearity.png)

### Hal yang Perlu Diketahui Sebelum Menggunakan LUT

Sebaiknya Anda membaca [ESP32 Analog to Digital Converter](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/peripherals/adc.html) sebelum menggunakan LUT untuk mengatasi masalah linearitas pada ESP32. Menurut dokumentasi, chip ESP32 (ESP32-D0WD dan ESP32-D0WDQ6) yang diproduksi setelah minggu pertama tahun 2018 sudah diukur secara individual dan dibakar dengan nilai Vref eFuse ke dalam chip. Nilai Vref eFuse ini bisa dibaca menggunakan fungsi `read_efuse_vref(void)`, yang kodenya dapat ditemukan di [esp_adc_cal.c](https://github.com/espressif/esp-idf/blob/f91080637c054fa2b4107192719075d237ecc3ec/components/esp_adc_cal/esp_adc_cal.c#L153).

Ada juga fungsi[`calculate_voltage_linear()`](https://github.com/espressif/esp-idf/blob/f91080637c054fa2b4107192719075d237ecc3ec/components/esp_adc_cal/esp_adc_cal.c#L246)  di perpustakaan yang sama, yang menggunakan rumus polinomial untuk mengoreksi linearitas.

Anda bisa memilih antara metode polinomial atau LUT untuk mengatasi masalah linearitas ADC pada ESP32. LUT memakan banyak memori (namun tidak menjadi masalah besar bagi ESP32), tetapi lebih cepat dan lebih akurat dibandingkan metode polinomial.

### Bagaimana Cara Membuat LUT?
Program ini menggunakan DAC ESP32 untuk menghasilkan nilai sebagai referensi dan memasukkannya ke ADC untuk kalibrasi. Jangan lupa untuk menggunakan kabel jumper pendek untuk menghubungkan keluaran DAC (GPIO 25) ke Channel ADC 1 ADC7 (GPIO 35) pada ESP32 Anda.

Secara default, program akan menghasilkan `float ADC_LUT`, yang memakan lebih banyak memori tetapi memberikan presisi yang lebih baik. Jika Anda ingin menggunakan tabel `int ADC_LUT`, komentar baris `#define FLOAT_LUT` untuk mendapatkan tabel dalam bentuk integer.

1. Hubungkan kabel jumper antara GPIO 25 dan GPIO 35 (yaitu A7);
2. Pilih tabel yang ingin Anda buat (float atau integer);
3. Jalankan sketsa `main.cpp` dari Arduino IDE;
Setelah program berhenti, salin dan tempel seluruh tabel dari Serial Monitor ke sketsa Anda untuk digunakan.
Bagaimana Cara Menggunakan LUT?
Lihat sketsa di direktori contoh untuk mengetahui cara menerapkan LUT dalam program Anda. Jalankan sketsa dan lihat hasilnya di Serial Plotter.

Berikut adalah hasil perbandingan antara pembacaan mentah dari ADC ESP32 dengan pembacaan yang telah dikalibrasi menggunakan LUT.

[![rawReading versus calbratedReading](https://github.com/e-tinkers/esp32-adc-calibrate/blob/master/images/rawReading_versus_calibratedReading.png)](https://github.com/e-tinkers/esp32-adc-calibrate/blob/master/images/rawReading_versus_calibratedReading.png)

Dan berikut adalah keluaran DAC dibandingkan dengan pembacaan ADC yang telah dikalibrasi.

[![DAC output versus ADC calbratedReading](https://github.com/e-tinkers/esp32-adc-calibrate/blob/master/images/DAC_output_versus_adcCalibratedReading.png)](https://github.com/e-tinkers/esp32-adc-calibrate/blob/master/images/DAC_output_versus_adcCalibratedReading.png)
