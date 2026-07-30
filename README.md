# 🏭 Training PLC - CITA ITB (Folder Latihan)

Repository ini berisi kumpulan 24 studi kasus dan modul latihan pemrograman **Programmable Logic Controller (PLC)** dalam format `.rungs` (YAML Add-On Instruction / AOI Routine) yang dikembangkan untuk pelatihan mahasiswa Instrumentasi dan Kontrol CITA ITB.

---

## 📌 Topik & Cakupan Latihan

Modul latihan ini mencakup berbagai konsep kontrol otomatisasi industri, meliputi:
1. **Logika Kontrol Diskrit Dasar**: Instruksi `XIC` (Normally Open), `XIO` (Normally Closed), dan `OTE` (Output Enable).
2. **Operasi Aritmatika & Matematika**: Konversi skala (`MUL`, `DIV`), kalkulasi offset (`ADD`), dan kalkulasi Pythagoras (`SQRT`).
3. **Pewaktuan (Timer)**: *Timer ON Delay* (`TON`), *Retentive Timer* (`RTO`), bit status (`.DN`), dan instruksi Reset (`RES`).
4. **Pencacah (Counter)**: *Counter Up* (`CTU`), *Counter Down* (`CTD`), dan komparasi nilai akumulasi (`ACC`).
5. **Penskalaan & Indikator Analog**: Penskalaan data ADC mentah (0–32767 RAW) ke satuan fisik ($^\circ\text{C}$, bar, Volt) dan sistem alarm bertingkat (LL, L, H, HH).
6. **Studi Kasus Sistem Industri**:
   - Sistem Manajemen Capacity Parkir Otomatis
   - Pengasutan Motor Induksi 3-Phase Star-Delta dengan Interlock Elektrik
   - Sistem Pencampuran Tangki Otomatis (*Batch Mixing Loop*)
   - Logika *Voting Safety System* (1o3, 2o3, 3o3)
   - Sistem Akses Gerbang Otomatis dengan E-Stop dan Alarm Rintangan

---

## 🛠️ Format File `.rungs` & Cara Penggunaan via [Rungs Studio](https://studio.rungs.dev/)

### Format File
Setiap file `.rungs` disusun menggunakan format struktural YAML spesifikasi **Rungs Studio** (`version: 4`, `origin: studio`):

```yaml
version: 4
origin: studio
aoi:
  name: Nama_Latihan
  description: "Penjelasan rinci sistem..."
  tags:
    input: [...]
    output: [...]
    local: [...]
  routines:
    Logic:
      type: ld
      content: |-
        XIC(INPUT_TAG)OTE(OUTPUT_TAG);
```

### Cara Menggunakan & Membuka File di Rungs Studio (Web PLC Simulator)
1. **Akses Platform**: Buka browser dan kunjungi **[https://studio.rungs.dev/](https://studio.rungs.dev/)**.
2. **Buka / Impor File `.rungs`**:
   - Klik tombol **Open File** / **Import** pada antarmuka Rungs Studio.
   - Atau cukup *drag & drop* (seret dan lepas) file `.rungs` (misalnya `Latihan_1c_Hari_3.rungs`) dari komputer ke halaman web Rungs Studio.
3. **Visualisasi Ladder Diagram**:
   - Rungs Studio secara otomatis memparsing file YAML dan memvisualisasikan diagram rungs Ladder lengkap beserta instruksi digital (`XIC`, `XIO`, `OTE`), timer (`TON`, `RTO`), counter (`CTU`, `CTD`), dan fungsi matematika (`MUL`, `DIV`, `ADD`, `SQRT`).
   - Variabel `input`, `output`, dan `local` akan terkonfigurasi secara otomatis sesuai definisi pada file.
4. **Simulasi Interaktif**:
   - Jalankan mode simulasi pada Rungs Studio.
   - Klik/toggle sakelar input (seperti `BUTTON`, `START_BUTTON`, `V_IN`, `SENSOR_MASUK`) untuk menguji alur daya listrik (*power flow*) dan verifikasi status output secara real-time.

---

## 📜 Dokumentasi & Penjelasan Sistem (24 File `.rungs`)

---

### 1. `Latihan_1_Hari_1.rungs`
* **Deskripsi**: Kontrol lampu sederhana berbasis 1 Rung.
* **Tags**:
  * **Input**: `BUTTON` (`BOOL`)
  * **Output**: `LAMPU` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(BUTTON)OTE(LAMPU);
  ```
* **Cara Kerja**: Memeriksa kondisi Normally Open dari `BUTTON`. Saat `BUTTON` ditekan (`XIC = TRUE`), koil output `LAMPU` aktif (`OTE = TRUE`). Saat tombol dilepas, lampu padam.

---

### 2. `Latihan_1a_Hari_2.rungs`
* **Deskripsi**: Pemetaan sakelar ke lampu 4-channel secara independen.
* **Tags**:
  * **Input**: `SW_0`, `SW_1`, `SW_2`, `SW_3` (`BOOL`)
  * **Output**: `Lamp_0`, `Lamp_1`, `Lamp_2`, `Lamp_3` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(SW_0)OTE(Lamp_0);
  XIC(SW_1)OTE(Lamp_1);
  XIC(SW_2)OTE(Lamp_2);
  XIC(SW_3)OTE(Lamp_3);
  ```
* **Cara Kerja**: Tiap sakelar (`SW_0` s.d. `SW_3`) mengontrol lampu yang sesuai (`Lamp_0` s.d. `Lamp_3`) secara mandiri tanpa saling mempengaruhi.

---

### 3. `Latihan_1a_Hari_3.rungs`
* **Deskripsi**: Penundaan waktu menyala (*Timer ON Delay* - TON) 5 detik.
* **Tags**:
  * **Input**: `TOGGLE_BUTTON_1` (`BOOL`)
  * **Output**: `LAMPU_1` (`BOOL`)
  * **Local**: `TIMER_1` (`TIMER`, `PRE: 5000`)
* **Logika Ladder**:
  ```ld
  XIC(TOGGLE_BUTTON_1)TON(TIMER_1,5000,0);
  XIC(TIMER_1.DN)OTE(LAMPU_1);
  ```
* **Cara Kerja**: Saat `TOGGLE_BUTTON_1` diaktifkan, `TIMER_1` menghitung hingga 5000 ms (5 detik). Setelah 5 detik berlalu, bit status `TIMER_1.DN` bernilai `TRUE` dan menyalakan `LAMPU_1`.

---

### 4. `Latihan_1b_Hari_2.rungs`
* **Deskripsi**: Konversi suhu Celcius ke Reamur berbasis perkalian (`MUL`).
* **Tags**:
  * **Local**: `CELCIUS` (`REAL`, `default: 10`), `GAIN` (`REAL`, `default: 0.8`), `REAMUR` (`REAL`)
* **Logika Ladder**:
  ```ld
  MUL(CELCIUS,GAIN,REAMUR);
  ```
* **Cara Kerja**: Menghitung Reamur dengan mengalikan nilai `CELCIUS` dengan faktor skala `GAIN` (0.8): $\text{REAMUR} = \text{CELCIUS} \times 0.8$. Untuk input 10 $\text{}^\circ\text{C}$, dihasilkan 8 $\text{}^\circ\text{R}$.

---

### 5. `Latihan_1b_Hari_3.rungs`
* **Deskripsi**: Penskalaan potensiometer dan pengondisian alarm lampu bertingkat.
* **Tags**:
  * **Local**: `V_IN` (`REAL`), `ADD_VAL` (`0.1`), `V_TEMP`, `MULT_VAL` (`0.65`), `V_OUT`, `LIMIT_2` (`2.0`), `LIMIT_4` (`4.0`), `LIMIT_5` (`5.0`), `LIMIT_6` (`6.0`)
  * **Output**: `LAMPU_1`, `LAMPU_2`, `LAMPU_3` (`BOOL`)
* **Logika Ladder**:
  ```ld
  ADD(V_IN,ADD_VAL,V_TEMP)MUL(V_TEMP,MULT_VAL,V_OUT);
  GE(V_OUT,LIMIT_2)LT(V_OUT,LIMIT_6)OTE(LAMPU_1);
  GE(V_OUT,LIMIT_4)LT(V_OUT,LIMIT_6)OTE(LAMPU_2);
  GE(V_OUT,LIMIT_5)LT(V_OUT,LIMIT_6)OTE(LAMPU_3);
  ```
* **Cara Kerja**: Tegangan $V_{OUT} = (V_{IN} + 0.1) \times 0.65$. `LAMPU_1` aktif jika $2.0 \le V_{OUT} < 6.0$; `LAMPU_2` aktif jika $4.0 \le V_{OUT} < 6.0$; `LAMPU_3` aktif jika $5.0 \le V_{OUT} < 6.0$. Bila $V_{OUT} \ge 6.0$, seluruh lampu padam.

---

### 6. `Latihan_1c_Hari_3.rungs`
* **Deskripsi**: Sistem penghitung kapasitas parkir otomatis berbasis Counter Up/Down (CTU/CTD).
* **Tags**:
  * **Input**: `SENSOR_MASUK`, `SENSOR_KELUAR` (`BOOL`)
  * **Output**: `LAMPU_PENUH` (`BOOL`)
  * **Local**: `PARKING_COUNTER` (`COUNTER`, `PRE: 10`)
* **Logika Ladder**:
  ```ld
  XIC(SENSOR_MASUK)CTU(PARKING_COUNTER,10,0);
  XIC(SENSOR_KELUAR)CTD(PARKING_COUNTER,10,0);
  XIC(PARKING_COUNTER.DN)OTE(LAMPU_PENUH);
  ```
* **Cara Kerja**: `SENSOR_MASUK` menambah jumlah kendaraan (CTU), `SENSOR_KELUAR` menguranginya (CTD). Ketika jumlah kendaraan mencapai 10 mobil (`PRE=10`), bit `PARKING_COUNTER.DN` aktif dan menyalakan `LAMPU_PENUH`.

---

### 7. `Latihan_2a_Hari_2.rungs`
* **Deskripsi**: Kontrol lampu dengan Master Interlock / Emergency Stop (`SW_3`).
* **Tags**:
  * **Input**: `SW_0`, `SW_1`, `SW_2`, `SW_3` (`BOOL`)
  * **Output**: `Lamp_0`, `Lamp_1`, `Lamp_2`, `Lamp_3` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(SW_0)XIO(SW_3)OTE(Lamp_0)OTE(Lamp_1);
  XIC(SW_1)XIO(SW_3)OTE(Lamp_2);
  XIC(SW_2)XIO(SW_3)OTE(Lamp_3);
  ```
* **Cara Kerja**: `SW_0` menyalakan `Lamp_0` & `Lamp_1`, `SW_1` menyalakan `Lamp_2`, `SW_2` menyalakan `Lamp_3`. Sakelar `SW_3` berfungsi sebagai *Master Emergency Interlock*; bila `SW_3` aktif, seluruh jalur terputus dan semua lampu mati.

---

### 8. `Latihan_2a_Hari_3.rungs`
* **Deskripsi**: Akumulasi waktu dengan Retentive Timer (RTO) 10 detik dan Reset manual.
* **Tags**:
  * **Input**: `TOGGLE_BUTTON_1`, `TOGGLE_BUTTON_2` (`BOOL`)
  * **Output**: `LAMPU_1` (`BOOL`)
  * **Local**: `TIMER_1` (`TIMER`, `PRE: 10000`)
* **Logika Ladder**:
  ```ld
  XIC(TOGGLE_BUTTON_1)RTO(TIMER_1,10000,0);
  XIC(TIMER_1.DN)OTE(LAMPU_1);
  XIC(TOGGLE_BUTTON_2)RES(TIMER_1);
  ```
* **Cara Kerja**: RTO menyimpan akumulasi waktu (`ACC`). Ketika total durasi `TOGGLE_BUTTON_1` ditekan mencapai 10 detik, `LAMPU_1` menyala. Penekanan `TOGGLE_BUTTON_2` mereset timer (`RES`).

---

### 9. `Latihan_2b_Hari_2.rungs`
* **Deskripsi**: Kalkulasi matematika Teorema Pythagoras.
* **Tags**:
  * **Local**: `A` (`3`), `B` (`4`), `C` (`21`), `AA`, `BB`, `HASIL` (`REAL`)
* **Logika Ladder**:
  ```ld
  MUL(A,A,AA)MUL(B,B,BB)ADD(AA,BB,C)SQRT(C,HASIL);
  ```
* **Cara Kerja**: Menghitung $\text{HASIL} = \sqrt{A^2 + B^2}$. Untuk $A=3$ dan $B=4$, diperoleh $AA=9$, $BB=16$, $C=25$, dan $\text{HASIL}=5.0$.

---

### 10. `Latihan_2b_Hari_3.rungs`
* **Deskripsi**: Sekuensial pencampuran zat kimia via sensor perbedaan tekanan (DP) dan timer.
* **Tags**:
  * **Local**: `DP_A` (`5`), `DP_B` (`5`), `LIMIT_5` (`5`), `TIMER_1` (`5000ms`), `TIMER_2` (`5000ms`)
  * **Output**: `VALVE_A`, `VALVE_B`, `VALVE_C` (`BOOL`)
* **Logika Ladder**:
  ```ld
  LT(DP_A,LIMIT_5)OTE(VALVE_A);
  GE(DP_A,LIMIT_5)LT(DP_B,LIMIT_5)OTE(VALVE_B);
  GE(DP_A,LIMIT_5)GE(DP_B,LIMIT_5)TON(TIMER_1,5000,0);
  XIC(TIMER_1.DN)TON(TIMER_2,5000,0);
  XIC(TIMER_1.DN)XIO(TIMER_2.DN)OTE(VALVE_C);
  ```
* **Cara Kerja**: Jika $DP_A < 5\text{V}$, `VALVE_A` membuka. Jika $DP_A \ge 5\text{V}$ dan $DP_B < 5\text{V}$, `VALVE_B` membuka. Jika kedua $DP \ge 5\text{V}$, `TIMER_1` berjalan 5 detik, kemudian `VALVE_C` membuka selama 5 detik (`TIMER_2`), lalu sistem mati otomatis.

---

### 11. `Latihan_2c_Hari_3.rungs`
* **Deskripsi**: Pengasutan Motor Induksi 3-Phase Star-Delta otomatis dengan latching & interlock.
* **Tags**:
  * **Input**: `START_BUTTON`, `STOP_BUTTON` (`BOOL`)
  * **Output**: `K1_MAIN`, `K2_STAR`, `K3_DELTA` (`BOOL`)
  * **Local**: `TIMER_STAR_DELTA` (`TIMER`, `PRE: 5000`)
* **Logika Ladder**:
  ```ld
  [XIC(START_BUTTON),XIC(K1_MAIN)]XIO(STOP_BUTTON)OTE(K1_MAIN)TON(TIMER_STAR_DELTA,5000,0);
  XIC(K1_MAIN)XIO(TIMER_STAR_DELTA.DN)XIO(K3_DELTA)OTE(K2_STAR);
  XIC(K1_MAIN)XIC(TIMER_STAR_DELTA.DN)XIO(K2_STAR)OTE(K3_DELTA);
  ```
* **Cara Kerja**: Tekan `START_BUTTON` mengunci (`latch`) Kontaktor Utama `K1_MAIN` dan mengaktifkan Kontaktor Star `K2_STAR` selama 5 detik pertama. Setelah 5 detik, timer memutus `K2_STAR` dan mengaktifkan Kontaktor Delta `K3_DELTA`. Dilengkapi *interlock* silang elektrik antara Star dan Delta.

---

### 12. `Latihan_3a_Hari_2.rungs`
* **Deskripsi**: Deteksi bahaya bertingkat (*Multi-stage Safety System*).
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `SIRINE`, `LAMPU`, `SPRINKLER` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(SENS_1)OTE(SIRINE);
  XIC(SENS_1)XIC(SENS_2)OTE(LAMPU);
  XIC(SENS_1)XIC(SENS_2)XIC(SENS_3)OTE(SPRINKLER);
  ```
* **Cara Kerja**:
  - Level 1 (`SENS_1`) $\rightarrow$ `SIRINE` menyala.
  - Level 2 (`SENS_1` AND `SENS_2`) $\rightarrow$ `SIRINE` & `LAMPU` menyala.
  - Level 3 (`SENS_1`, `SENS_2`, `SENS_3` aktif bersamaan) $\rightarrow$ `SIRINE`, `LAMPU`, & `SPRINKLER` aktif.

---

### 13. `Latihan_3a_Hari_3.rungs`
* **Deskripsi**: Aktivasi lampu setelah pencapaian 5 kali ketukan tombol (`CTU`).
* **Tags**:
  * **Input**: `START_PUSH_BUTTON`, `STOP_PUSH_BUTTON` (`BOOL`)
  * **Output**: `START_LAMP` (`BOOL`)
  * **Local**: `COUNTER_1` (`COUNTER`, `PRE: 5`)
* **Logika Ladder**:
  ```ld
  XIC(START_PUSH_BUTTON)CTU(COUNTER_1,5,0);
  XIC(COUNTER_1.DN)OTE(START_LAMP);
  XIC(STOP_PUSH_BUTTON)RES(COUNTER_1);
  ```
* **Cara Kerja**: Setiap ketukan `START_PUSH_BUTTON` menambah hitungan. Setelah 5 kali ditekan (`ACC=5`), bit `COUNTER_1.DN` menyalakan `START_LAMP`. Tombol `STOP_PUSH_BUTTON` mereset hitungan (`RES`).

---

### 14. `Latihan_3b_Hari_2.rungs`
* **Deskripsi**: Penskalaan linier sensor Pressure Transmitter (0–32767 RAW ke 0–25 bar).
* **Tags**:
  * **Local**: `RAW_VAL` (`16383.5`), `MAX_BAR` (`25`), `MAX_RAW` (`32767`), `TEMP`, `TEKANAN` (`REAL`)
* **Logika Ladder**:
  ```ld
  MUL(RAW_VAL,MAX_BAR,TEMP)DIV(TEMP,MAX_RAW,TEKANAN);
  ```
* **Cara Kerja**: Mengonversi nilai mentah ADC ke satuan bar: $\text{TEKANAN} = (\text{RAW\_VAL} \times \text{MAX\_BAR}) / \text{MAX\_RAW}$. Untuk `RAW_VAL` = 16383.5, dihasilkan `TEKANAN` = 12.5 bar.

---

### 15. `Latihan_3b_Hari_3.rungs`
* **Deskripsi**: Indikator alarm analog multi-level dengan Trip/Reset atas (7V).
* **Tags**:
  * **Local**: `V_IN` (`5.5`), `LIMIT_6` (`6.0`), `LIMIT_5` (`5.0`), `LIMIT_3` (`3.0`), `LIMIT_2` (`2.0`), `LIMIT_7` (`7.0`)
  * **Output**: `LAMPU_1` (HH), `LAMPU_2` (H), `LAMPU_3` (L), `LAMPU_4` (LL) (`BOOL`)
* **Logika Ladder**:
  ```ld
  GE(V_IN,LIMIT_6)LT(V_IN,LIMIT_7)OTE(LAMPU_1);
  GE(V_IN,LIMIT_5)LT(V_IN,LIMIT_7)OTE(LAMPU_2);
  GE(V_IN,LIMIT_3)LT(V_IN,LIMIT_7)OTE(LAMPU_3);
  GE(V_IN,LIMIT_2)LT(V_IN,LIMIT_7)OTE(LAMPU_4);
  ```
* **Cara Kerja**: `LAMPU_4` aktif bila $V_{IN} \ge 2\text{V}$, `LAMPU_3` bila $V_{IN} \ge 3\text{V}$, `LAMPU_2` bila $V_{IN} \ge 5\text{V}$, dan `LAMPU_1` bila $V_{IN} \ge 6\text{V}$. Semua alarm padam jika $V_{IN} \ge 7\text{V}$.

---

### 16. `Latihan_3c_Hari_3.rungs`
* **Deskripsi**: Otomatisasi pencampuran tangki (*Batch Mixing System*) berulang (*looping*).
* **Tags**:
  * **Input**: `START_BUTTON`, `STOP_BUTTON` (`BOOL`)
  * **Output**: `POMPA_1`, `POMPA_2`, `MOTOR_MIXER`, `POMPA_3` (`BOOL`)
  * **Local**: `RUN` (`BOOL`), `TIMER_1` (5s), `TIMER_2` (5s), `TIMER_3` (7s), `TIMER_4` (10s)
* **Logika Ladder**:
  ```ld
  [XIC(START_BUTTON),XIC(RUN)]XIO(STOP_BUTTON)OTE(RUN);
  XIC(RUN)XIO(TIMER_4.DN)TON(TIMER_1,5000,0);
  XIC(TIMER_1.DN)TON(TIMER_2,5000,0);
  XIC(TIMER_2.DN)TON(TIMER_3,7000,0);
  XIC(TIMER_3.DN)TON(TIMER_4,10000,0);
  XIC(RUN)XIO(TIMER_1.DN)OTE(POMPA_1);
  XIC(TIMER_1.DN)XIO(TIMER_2.DN)OTE(POMPA_2);
  XIC(TIMER_2.DN)XIO(TIMER_3.DN)OTE(MOTOR_MIXER);
  XIC(TIMER_3.DN)XIO(TIMER_4.DN)OTE(POMPA_3);
  ```
* **Cara Kerja**: `POMPA_1` menyuplay 5s $\rightarrow$ `POMPA_2` menyuplay 5s $\rightarrow$ `MOTOR_MIXER` mengaduk 7s $\rightarrow$ `POMPA_3` menguras 10s. Setelah `TIMER_4.DN` aktif, siklus otomatis mengulang dari awal hingga `STOP_BUTTON` ditekan.

---

### 17. `Latihan_4a_Hari_2.rungs`
* **Deskripsi**: Logika *Voting Safety System* (1o3, 2o3, 3o3).
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `LAMPU`, `SIRINE`, `SPRINKLER` (`BOOL`)
* **Logika Ladder**:
  ```ld
  [XIC(SENS_1),[XIC(SENS_2),XIC(SENS_3)]]OTE(LAMPU);
  [XIC(SENS_1)XIC(SENS_2),[XIC(SENS_2)XIC(SENS_3),XIC(SENS_1)XIC(SENS_3)]]OTE(SIRINE);
  XIC(SENS_1)XIC(SENS_2)XIC(SENS_3)OTE(SPRINKLER);
  ```
* **Cara Kerja**: `LAMPU` menyala bila min. 1 sensor aktif (1o3). `SIRINE` menyala bila min. 2 sensor aktif (2o3). `SPRINKLER` menyala hanya bila ketiga sensor aktif simultan (3o3).

---

### 18. `Latihan_4a_Hari_3.rungs`
* **Deskripsi**: Pembatas operasi lampu (Normally-ON, padam saat counter $> 5$).
* **Tags**:
  * **Input**: `START_PUSH_BUTTON`, `STOP_PUSH_BUTTON` (`BOOL`)
  * **Output**: `START_LAMP` (`BOOL`)
  * **Local**: `COUNTER_1` (`COUNTER`, `PRE: 6`)
* **Logika Ladder**:
  ```ld
  XIC(START_PUSH_BUTTON)CTU(COUNTER_1,6,0);
  XIO(COUNTER_1.DN)OTE(START_LAMP);
  XIC(STOP_PUSH_BUTTON)RES(COUNTER_1);
  ```
* **Cara Kerja**: `START_LAMP` menyala secara standar. Setiap penekanan `START_PUSH_BUTTON` menambah counter. Jika ditekan $> 5$ kali (mencapai preset 6), bit `.DN` aktif dan memadamkan `START_LAMP`. `STOP_PUSH_BUTTON` mereset hitungan.

---

### 19. `Latihan_4b_Hari_2.rungs`
* **Deskripsi**: Penjumlahan offset analog & komparasi nilai ambang batas (*Threshold Alarm*).
* **Tags**:
  * **Local**: `DATA_1` (`2500`), `ADD_VAL` (`3000`), `HASIL`, `LIMIT_VAL` (`5000`)
  * **Output**: `LAMPU` (`BOOL`)
* **Logika Ladder**:
  ```ld
  ADD(DATA_1,ADD_VAL,HASIL);
  GT(HASIL,LIMIT_VAL)OTE(LAMPU);
  ```
* **Cara Kerja**: Menambahkan offset $+3000$ ke `DATA_1`. Jika `HASIL` $> 5000$ ($2500+3000=5500$), `LAMPU` menyala.

---

### 20. `Latihan_5a_Hari_2.rungs`
* **Deskripsi**: Alarm proteksi kebakaran berbasis kombinasi kondisi sensor.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `LAMPU`, `SIRINE`, `SPRINKLER` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(SENS_1)XIC(SENS_2)OTE(LAMPU);
  XIC(SENS_3)OTE(LAMPU)OTE(SIRINE);
  XIC(SENS_3)[XIC(SENS_2),XIC(SENS_1)]OTE(SPRINKLER);
  ```
* **Cara Kerja**: `LAMPU` menyala jika (`SENS_1` AND `SENS_2`) OR `SENS_3`. `SIRINE` menyala jika `SENS_3`. `SPRINKLER` menyala jika `SENS_3` AND (`SENS_1` OR `SENS_2`).

---

### 21. `Latihan_5a_Hari_3.rungs`
* **Deskripsi**: Kontrol lampu berbasis komparasi akumulasi counter (`LT`).
* **Tags**:
  * **Input**: `START_PUSH_BUTTON`, `STOP_PUSH_BUTTON` (`BOOL`)
  * **Output**: `START_LAMP` (`BOOL`)
  * **Local**: `COUNTER_1` (`COUNTER`, `PRE: 10`), `LIMIT_5` (`DINT`, `5`)
* **Logika Ladder**:
  ```ld
  XIC(START_PUSH_BUTTON)CTU(COUNTER_1,10,0);
  LT(COUNTER_1.ACC,LIMIT_5)OTE(START_LAMP);
  XIC(STOP_PUSH_BUTTON)RES(COUNTER_1);
  ```
* **Cara Kerja**: `START_LAMP` menyala selama hitungan `COUNTER_1.ACC` $< 5$. Saat hitungan menyentuh 5 atau lebih, lampu padam. Reset via `STOP_PUSH_BUTTON` mengembalikan lampu menyala.

---

### 22. `Latihan_5b_Hari_2.rungs`
* **Deskripsi**: Penskalaan suhu Boiler & pengawasan alarm batas suhu.
* **Tags**:
  * **Local**: `RAW_VAL` (`21298.55`), `MAX_TEMP` (`100`), `MAX_RAW` (`32767`), `TEMPERATUR`, `LIMIT_80` (`80`), `LIMIT_60` (`60`), `LIMIT_45` (`45`)
  * **Output**: `BOILER`, `SIRENE`, `LAMPU_PERINGATAN` (`BOOL`)
* **Logika Ladder**:
  ```ld
  MUL(RAW_VAL,MAX_TEMP,TEMP_CALC)DIV(TEMP_CALC,MAX_RAW,TEMPERATUR);
  LT(TEMPERATUR,LIMIT_80)OTE(BOILER);
  GE(TEMPERATUR,LIMIT_60)OTE(SIRENE);
  GE(TEMPERATUR,LIMIT_45)OTE(LAMPU_PERINGATAN);
  ```
* **Cara Kerja**: Penskalaan 0–100 $\text{}^\circ\text{C}$. `BOILER` aktif saat suhu $< 80\text{}^\circ\text{C}$. `LAMPU_PERINGATAN` aktif saat suhu $\ge 45\text{}^\circ\text{C}$. `SIRENE` alarm aktif saat suhu $\ge 60\text{}^\circ\text{C}$.

---

### 23. `Latihan_6a_Hari_2.rungs`
* **Deskripsi**: Kontrol palang pintu lalu lintas & indikator merah/hijau.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `PALANG`, `LAMPU_MERAH`, `LAMPU_HIJAU` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(SENS_1)XIO(SENS_2)OTE(PALANG)OTE(LAMPU_HIJAU);
  XIC(SENS_2)OTE(LAMPU_MERAH);
  ```
* **Cara Kerja**: `SENS_1` terdeteksi $\rightarrow$ `PALANG` terbuka dan `LAMPU_HIJAU` menyala (syarat `SENS_2` bebas). Jika sensor bahaya `SENS_2` aktif $\rightarrow$ `PALANG` tertutup dan `LAMPU_MERAH` menyala.

---

### 24. `Latihan_7a_Hari_2.rungs`
* **Deskripsi**: Sistem gerbang masuk/keluar otomatis dengan E-Stop dan Alarm Kendala.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3`, `STOP` (`BOOL`)
  * **Output**: `PALANG_MASUK`, `LAMPU_MERAH`, `LAMPU_HIJAU`, `PALANG_KELUAR`, `ALARM` (`BOOL`)
* **Logika Ladder**:
  ```ld
  XIC(SENS_1)XIO(SENS_2)XIO(STOP)OTE(PALANG_MASUK);
  XIC(SENS_3)XIO(STOP)OTE(PALANG_KELUAR);
  XIC(SENS_2)XIO(STOP)OTE(LAMPU_MERAH)OTE(ALARM);
  ```
* **Cara Kerja**: `PALANG_MASUK` terbuka bila `SENS_1` aktif, `SENS_2` bebas, dan `STOP` tidak aktif. `PALANG_KELUAR` terbuka bila `SENS_3` aktif dan `STOP` tidak aktif. Bila `SENS_2` terdeteksi, `LAMPU_MERAH` dan `ALARM` aktif.

---

## 👨‍💻 Penulis & Lisensi

**Laboratorium PTIO / CITA ITB**  
Program Magister Instrumentasi dan Kontrol - Institut Teknologi Bandung
