# 🛠️ Dokumentasi Desain Perangkat Keras (Hardware Design)

Dokumen ini memuat spesifikasi teknis, skematik, dan detail interkoneksi dari 2 unit PCB custom yang dirancang menggunakan **KiCad E.D.A. 9.0.4** untuk sistem kendali otonom ASV Gamantaray Safinah One 2026.

---

## 1. Board 1: Power Distribution Board (PDB) Control System
[PDB Control System Schematic](PDBControl_Schematic.pdf)
* **Nama File Skematik:** `Power Distribution Board.kicad_sch`
* **Fungsi Utama:** Menerima daya dari baterai utama, menyediakan fitur proteksi, dan mendistribusikan tegangan (baik langsung maupun regulasi) ke seluruh sub-sistem kapal.

### A. Fitur Proteksi & Input Utama
* **Power Input (Battery):** Menggunakan konektor **XT60-M** menerima tegangan baterai LiPo 4S (+14.8V nominal, terlabel `+14VB`).
* **Reverse Polarity Protection (Proteksi Terbalik Kutub):** Menggunakan P-Channel MOSFET **IRF4905 (Q1)**, Zener Diode 18V (D2) untuk membatasi $V_{gs}$, dan resistor pembagi tegangan 100kΩ (R2). Fitur ini melindungi seluruh sistem jika baterai tidak sengaja terpasang terbalik.
* **Bulk Filtering:** Dilengkapi dengan kapasitor *decoupling* dan *bulk* C1 (100nF 50V) serta C2 (470uF 35V) untuk meredam lonjakan tegangan (*voltage spike*).

### B. Daftar Jalur Distribusi Daya (Power Rails)

| Jalur Output | Konektor | Modul Regulator / Komponen | Komponen Tujuan | Fitur Indikator / Filter |
| :---: | :---: | :--- | :--- | :--- |
| **+14.8V (Direct)** | XT60-F (J2) | Direct Battery | PC Intel NUC (Menuju Boost Converter internal) | LED D3 (Resistor 3.3kΩ) |
| **+14.8V (Direct)** | XT60-F (J3) | Direct Battery | Pixhawk 6C (Menuju PM02 Power Module) | LED D4 (Resistor 3.3kΩ) |
| **+14.8V (Direct)** | XT30-F (J5) | Direct Battery | **PCB Carrier Board** (Menuju UBEC 5V) | LED D6, Kapasitor C5 (100nF) |
| **+24V (Regulated)**| XT30-F (J4) | **XL6009 Boost Converter** (U1) | Router TP-Link EAP110 | LED D5 (Resistor 6.8kΩ), C3 (100µF 50V) |
| **+12V (Regulated)**| XT30-F (J6) | **Mini560 Buck Converter** (U3) | Router TP-Link Archer-MR400 | LED D7 (Resistor 2.2kΩ), C6 (100µF 35V) |
| **+12V (Regulated)**| 4x Conn 2-Pin (J7-J10) | Pararel dari output Mini560 | 4x Cooling DC Fan 12V | Jalur pararel langsung untuk kipas pendingin |

---

## 2. Board 2: PCB Carrier Board (Sensor & Microcontroller Interface)
* **Nama File Skematik:** `PCB Carrier.kicad_sch`
* **Fungsi Utama:** Sebagai *breakout board* pusat untuk mikrokontroler STM32 Black Pill, manajemen sensor jarak (TOF), dan interkoneksi data ke Raspberry Pi serta aktuator indikator.

### A. Spesifikasi Power Input Board
* **Power Input:** Menggunakan konektor **XT30-M (J1)** yang menerima suplai tegangan **+5V DC** stabil yang diturunkan oleh UBEC dari PDB. 
* Terdapat LED Indikator Daya Utama (D1) dengan resistor pembatas arus 470Ω (R1).

### B. Konfigurasi Mikrokontroler & Perifer (STM32 Black Pill)
Board ini mengintegrasikan **STM32F4x1Cx Black Pill (U1)** dengan pemetaan pinout sebagai berikut:

#### 1. Jalur Komunikasi I2C (Dual TOF Sensor)
Menggunakan dua sensor Time-of-Flight (TOF) berbasis JST-XH 5-Pin. Jalur bus I2C (SCL & SDA) ditarik ke atas (*pulled-up*) menggunakan resistor **2.2kΩ (R3 & R4)** ke jalur `+3V3` untuk stabilitas data.
* **Konektor TOF 1 (J2):** 
  * Pinout: Pin 1 (`PA0-XSHUT1`), Pin 2 (`PB7-SDA`), Pin 3 (`PB6-SCL`), Pin 4 (`GND`), Pin 5 (`+3V3`).
  * Filter: Kapasitor C1 (10µF) & C2 (100nF).
* **Konektor TOF 2 (J3):** 
  * Pinout: Pin 1 (`PA1-XSHUT2`), Pin 2 (`PB7-SDA`), Pin 3 (`PB6-SCL`), Pin 4 (`GND`), Pin 5 (`+3V3`).
  * Filter: Kapasitor C4 (10µF) & C3 (100nF).

#### 2. Jalur Komunikasi Serial (COM - Raspberry Pi)
Komunikasi data *low-level* ke *high-level computer* dijembatani via konektor **JST-XH 3-Pin (J4)** menggunakan protokol UART/Serial:
* Pin 1: `PA9-TX` (Mengirim data dari STM32 ke RX Raspberry Pi)
* Pin 2: `PA10-RX` (Menerima data di STM32 dari TX Raspberry Pi)
* Pin 3: `GND` (Grounding bersama)

#### 3. Indikator LED State (WS2812B)
Output sinyal kontrol untuk lampu indikator status kapal dihubungkan via konektor **JST-XH 3-Pin (J5)**:
* Pin 1: `GND`
* Pin 2: `PA2-LED STATE` (Pin kontrol data addressable LED)
* Pin 3: `+5V` (Suplai daya LED strip)

### C. Fitur Mekanik
* Kedua PCB dilengkapi dengan **4x Mounting Hole Pad (H1 - H4)** berukuran standar untuk pemasangan baut spacer kokoh di dalam box komponen ASV.
