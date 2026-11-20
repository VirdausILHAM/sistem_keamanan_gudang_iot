# Sistem Keamanan Pintu & Monitoring Udara Gudang Berbasis IoT (ESP32)

Proyek Internet of Things (IoT) berbasis mikrokontroler **ESP32** yang menggabungkan dua sensor untuk memantau keamanan akses pintu sekaligus mendeteksi potensi kebocoran gas atau asap di dalam ruangan gudang secara *real-time* melalui koneksi WiFi.

---

## A. Deskripsi Setiap Komponen

1. **Mikrokontroler ESP32 DevKit V1**
   * **Fungsi:** Bertindak sebagai pusat kendali utama (*microcontroller*) yang membaca sinyal data dari kedua sensor, memproses logika ambang batas (*threshold*), menghubungkan sistem ke jaringan WiFi (`LabIoTUNISA`), serta menampilkan hasil pemantauan ke Serial Monitor.
2. **Sensor A — Sensor Ultrasonik (HY-SRF05 / HC-SR04)**
   * **Fungsi:** Mengukur jarak objek di depan area pintu gudang menggunakan pantulan gelombang suara ultrasonik melalui pin `Trig` (GPIO 5) dan `Echo` (GPIO 18). Jika terdeteksi objek mendekat pada jarak $\le 15\text{ cm}$, sistem memberikan status peringatan akses pintu.
3. **Sensor B — Sensor Gas & Asap (Seri MQ / Flying-Fish)**
   * **Fungsi:** Mendeteksi keberadaan konsentrasi gas berbahaya maupun asap kebakaran di udara ruangan gudang melalui sinyal analog pada pin `AO` (GPIO 32). Data pembacaan dikonversi dalam rentang nilai ADC `0 – 4095` untuk menentukan status keamanan udara gudang.

---

## B. Penjelasan Alur Kerja Sistem IoT

1. **Inisialisasi & Koneksi WiFi (Wajib):**
   * Saat ESP32 dinyalakan, sistem menginisialisasi komunikasi serial (`115200 baud`), mengatur mode pin sensor, dan menghubungkan perangkat ke jaringan WiFi hingga memperoleh alamat IP lokal (`IP Address`). Sistem juga dilengkapi mekanisme *auto-reconnect* apabila koneksi WiFi terputus.
2. **Pembacaan Data Multi-Sensor:**
   * Pada fungsi `loop()`, ESP32 membaca dua data sensor secara berurutan setiap 1 detik:
     * Mengirim pulsa ultrasonik 10 mikrodetik untuk menghitung jarak objek dalam satuan sentimeter (`cm`).
     * Membaca sinyal tegangan analog dari sensor MQ menggunakan fungsi `analogRead()` untuk memperoleh tingkat konsentrasi gas/asap.
3. **Pemrosesan Logika & Validasi Output:**
   * Nilai jarak dibandingkan dengan batas ambang `15.0 cm` untuk menentukan status pintu (`ADA OBJEK MENDEKAT!` atau `AMAN (Kosong)`).
   * Nilai kadar gas dibandingkan dengan batas ambang `1500` untuk menentukan status udara (`BAHAYA GAS / ASAP!` atau `UDARA NORMAL`).
   * Seluruh status koneksi WiFi dan hasil pembacaan kedua sensor ditampilkan secara terstruktur pada **Serial Monitor**.

---

## C. Konfigurasi Pin (Wiring)

| Komponen | Pin Modul | Pin ESP32 | Keterangan |
| :--- | :--- | :--- | :--- |
| **Ultrasonik** | `VCC` | `VIN` (5V) | Catu daya positif 5V |
| **Ultrasonik** | `GND` | `GND` | Ground |
| **Ultrasonik** | `Trig` | `GPIO 5` (D5) | Sinyal pemicu gelombang |
| **Ultrasonik** | `Echo` | `GPIO 18` (D18) | Sinyal penerima pantulan |
| **Sensor Gas MQ** | `VCC` | `VIN` (5V) | Catu daya positif 5V |
| **Sensor Gas MQ** | `GND` | `GND` | Ground |
| **Sensor Gas MQ** | `AO` | `GPIO 32` (D32) | Output data analog gas/asap |

---

## D. Struktur Repositori
* `sistem_keamanan_gudang_iot.ino` : Kode sumber utama program ESP32 (Arduino IDE).
* `README.md` : Dokumentasi deskripsi komponen, skema pin, dan alur kerja sistem.
