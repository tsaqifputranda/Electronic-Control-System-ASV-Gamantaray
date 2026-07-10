# 📋 Spesifikasi Sistem Elektronika Kontrol ASV

Dokumen ini mencakup batasan, parameter kerja, dan komponen yang digunakan pada sistem kontrol elektronik ASV Gamantaray.

### 1. Spesifikasi Tegangan & Daya
* **Baterai Utama:** LiPo 4S (14.8V) / Min. 5000 mAh.
* **Tegangan Sistem Kontrol:** 5V DC (Sistem Mikrokontroler & Sensor).
* **Tegangan Sistem Aktuator:** 12V - 14.8V DC (ESC & Motor Penggerak).

### 2. Kebutuhan Komponen Utama
| Fungsi | Komponen | Protokol Komunikasi |
| :--- | :--- | :--- |
| **Main Controller** | Pixhawk 4 / Raspberry Pi 4 | MAVLink / ROS |
| **Telemetry Module** | Holybro 915MHz | UART / Serial |
| **GPS & Compas** | Ublox Neo-M8N | I2C & UART |
| **Sensor Jarak** | Ultrasonic Waterproof / Lidar | GPIO / PWM |
