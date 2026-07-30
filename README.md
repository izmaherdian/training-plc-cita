# 🏭 PLC Training - CITA ITB (Exercise Directory)

This repository contains a curated collection of 24 **Programmable Logic Controller (PLC)** practice modules and industrial case studies formatted in `.rungs` (YAML Add-On Instruction / AOI Routine) developed for student training in Instrumentation and Control Engineering at ITB (Institut Teknologi Bandung).

---

## 📌 Scope & Topics Covered

The training modules cover fundamental to advanced industrial automation and process control concepts:
1. **Discrete Control Logic**: `XIC` (Normally Open), `XIO` (Normally Closed), and `OTE` (Output Enable) instructions.
2. **Arithmetic & Mathematical Operations**: Scaling calculations (`MUL`, `DIV`), offset addition (`ADD`), and Pythagorean theorem computation (`SQRT`).
3. **Timer Instructions**: *Timer ON Delay* (`TON`), *Retentive Timer* (`RTO`), status bits (`.DN`), and Reset instructions (`RES`).
4. **Counter Instructions**: *Counter Up* (`CTU`), *Counter Down* (`CTD`), and accumulated value comparison (`ACC`).
5. **Analog Signal Scaling & Alarm Conditioning**: ADC raw data acquisition (0–32767 RAW) converted to physical units (°C, bar, Volts) and multi-level alarm indicators (LL, L, H, HH).
6. **Industrial Case Studies**:
   - Automated Parking Capacity Management System
   - Automatic 3-Phase Induction Motor Star-Delta Starter with Electrical Interlock
   - Automated Tank Batch Mixing System (Continuous Loop)
   - Voting Safety Logic System (1o3, 2o3, 3o3)
   - Automatic Entry/Exit Gate Management System with E-Stop and Obstacle Alarm

---

## 🛠️ File Format & How to Use via [Rungs Studio](https://studio.rungs.dev/)

### File Format
Each `.rungs` file is structured in YAML format based on the **Rungs Studio** AOI specification (`version: 4`, `origin: studio`):

```yaml
version: 4
origin: studio
aoi:
  name: Exercise_Name
  description: "Detailed system description..."
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

### How to Open & Simulate Files in Rungs Studio (Web PLC Simulator)
1. **Access the Platform**: Launch your web browser and navigate to **[https://studio.rungs.dev/](https://studio.rungs.dev/)**.
2. **Open / Import `.rungs` Files**:
   - Click the **Open File** or **Import** button on the Rungs Studio interface.
   - Alternatively, drag and drop any `.rungs` file (e.g., `Latihan_1c_Hari_3.rungs`) directly from your file explorer into the web editor.
3. **Ladder Diagram Visualization**:
   - Rungs Studio automatically parses the YAML structure and renders the complete Ladder Diagram including digital logic (`XIC`, `XIO`, `OTE`), timers (`TON`, `RTO`), counters (`CTU`, `CTD`), and math instructions (`MUL`, `DIV`, `ADD`, `SQRT`).
   - Variable tags (`input`, `output`, `local`) and default values are pre-configured automatically.
4. **Interactive Simulation**:
   - Run the simulation mode in Rungs Studio.
   - Toggle input switches (e.g., `BUTTON`, `START_BUTTON`, `V_IN`, `SENSOR_MASUK`) to observe real-time power flow and verify output indicator states.

---

## 📜 System Documentation (24 `.rungs` Files)

---

### 1. `Latihan_1_Hari_1.rungs`
* **Description**: Single-rung simple lamp control system.
* **Tags**:
  * **Input**: `BUTTON` (`BOOL`)
  * **Output**: `LAMPU` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(BUTTON)OTE(LAMPU);
  ```
* **Working Principle**: Evaluates the Normally Open condition of `BUTTON`. When `BUTTON` is pressed (`XIC = TRUE`), the output coil `LAMPU` energizes (`OTE = TRUE`). Releasing the button turns off the lamp.

---

### 2. `Latihan_1a_Hari_2.rungs`
* **Description**: Independent 4-channel switch-to-lamp mapping.
* **Tags**:
  * **Input**: `SW_0`, `SW_1`, `SW_2`, `SW_3` (`BOOL`)
  * **Output**: `Lamp_0`, `Lamp_1`, `Lamp_2`, `Lamp_3` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(SW_0)OTE(Lamp_0);
  XIC(SW_1)OTE(Lamp_1);
  XIC(SW_2)OTE(Lamp_2);
  XIC(SW_3)OTE(Lamp_3);
  ```
* **Working Principle**: Each switch (`SW_0` to `SW_3`) independently controls its corresponding lamp (`Lamp_0` to `Lamp_3`) without inter-channel dependencies.

---

### 3. `Latihan_1a_Hari_3.rungs`
* **Description**: 5-second Timer ON Delay (TON) lamp activation system.
* **Tags**:
  * **Input**: `TOGGLE_BUTTON_1` (`BOOL`)
  * **Output**: `LAMPU_1` (`BOOL`)
  * **Local**: `TIMER_1` (`TIMER`, `PRE: 5000`)
* **Ladder Logic**:
  ```ld
  XIC(TOGGLE_BUTTON_1)TON(TIMER_1,5000,0);
  XIC(TIMER_1.DN)OTE(LAMPU_1);
  ```
* **Working Principle**: Activating `TOGGLE_BUTTON_1` starts `TIMER_1` counting up to 5000 ms (5 seconds). Once 5 seconds elapse, the status bit `TIMER_1.DN` sets to `TRUE`, energizing `LAMPU_1`.

---

### 4. `Latihan_1b_Hari_2.rungs`
* **Description**: Celsius-to-Reamur temperature conversion using multiplication (`MUL`).
* **Tags**:
  * **Local**: `CELCIUS` (`REAL`, `default: 10`), `GAIN` (`REAL`, `default: 0.8`), `REAMUR` (`REAL`)
* **Ladder Logic**:
  ```ld
  MUL(CELCIUS,GAIN,REAMUR);
  ```
* **Working Principle**: Calculates the Reamur temperature by multiplying `CELCIUS` with the scale factor `GAIN` (0.8): `REAMUR = CELCIUS * 0.8`. An input of 10 °C produces 8 °R.

---

### 5. `Latihan_1b_Hari_3.rungs`
* **Description**: Potentiometer voltage scaling and multi-stage lamp alarm system.
* **Tags**:
  * **Local**: `V_IN` (`REAL`), `ADD_VAL` (`0.1`), `V_TEMP`, `MULT_VAL` (`0.65`), `V_OUT`, `LIMIT_2` (`2.0`), `LIMIT_4` (`4.0`), `LIMIT_5` (`5.0`), `LIMIT_6` (`6.0`)
  * **Output**: `LAMPU_1`, `LAMPU_2`, `LAMPU_3` (`BOOL`)
* **Ladder Logic**:
  ```ld
  ADD(V_IN,ADD_VAL,V_TEMP)MUL(V_TEMP,MULT_VAL,V_OUT);
  GE(V_OUT,LIMIT_2)LT(V_OUT,LIMIT_6)OTE(LAMPU_1);
  GE(V_OUT,LIMIT_4)LT(V_OUT,LIMIT_6)OTE(LAMPU_2);
  GE(V_OUT,LIMIT_5)LT(V_OUT,LIMIT_6)OTE(LAMPU_3);
  ```
* **Working Principle**: Computes scaled voltage `V_OUT = (V_IN + 0.1) * 0.65`. `LAMPU_1` turns ON if 2.0 <= V_OUT < 6.0; `LAMPU_2` turns ON if 4.0 <= V_OUT < 6.0; `LAMPU_3` turns ON if 5.0 <= V_OUT < 6.0. If V_OUT >= 6.0, all lamps turn OFF.

---

### 6. `Latihan_1c_Hari_3.rungs`
* **Description**: Automated parking capacity counter using Counter Up/Down (CTU/CTD).
* **Tags**:
  * **Input**: `SENSOR_MASUK`, `SENSOR_KELUAR` (`BOOL`)
  * **Output**: `LAMPU_PENUH` (`BOOL`)
  * **Local**: `PARKING_COUNTER` (`COUNTER`, `PRE: 10`)
* **Ladder Logic**:
  ```ld
  XIC(SENSOR_MASUK)CTU(PARKING_COUNTER,10,0);
  XIC(SENSOR_KELUAR)CTD(PARKING_COUNTER,10,0);
  XIC(PARKING_COUNTER.DN)OTE(LAMPU_PENUH);
  ```
* **Working Principle**: `SENSOR_MASUK` increments the vehicle count (CTU), while `SENSOR_KELUAR` decrements it (CTD). When the vehicle count reaches maximum capacity 10 (`PRE=10`), the `PARKING_COUNTER.DN` bit energizes `LAMPU_PENUH`.

---

### 7. `Latihan_2a_Hari_2.rungs`
* **Description**: Lamp control with Master Interlock / Emergency Stop (`SW_3`).
* **Tags**:
  * **Input**: `SW_0`, `SW_1`, `SW_2`, `SW_3` (`BOOL`)
  * **Output**: `Lamp_0`, `Lamp_1`, `Lamp_2`, `Lamp_3` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(SW_0)XIO(SW_3)OTE(Lamp_0)OTE(Lamp_1);
  XIC(SW_1)XIO(SW_3)OTE(Lamp_2);
  XIC(SW_2)XIO(SW_3)OTE(Lamp_3);
  ```
* **Working Principle**: `SW_0` illuminates `Lamp_0` & `Lamp_1`, `SW_1` illuminates `Lamp_2`, and `SW_2` illuminates `Lamp_3`. Switch `SW_3` acts as a Master Emergency Interlock; when `SW_3` is activated, all ladder rungs open and all lamps immediately turn OFF.

---

### 8. `Latihan_2a_Hari_3.rungs`
* **Description**: Accumulated time tracking using Retentive Timer (RTO) 10s with manual Reset.
* **Tags**:
  * **Input**: `TOGGLE_BUTTON_1`, `TOGGLE_BUTTON_2` (`BOOL`)
  * **Output**: `LAMPU_1` (`BOOL`)
  * **Local**: `TIMER_1` (`TIMER`, `PRE: 10000`)
* **Ladder Logic**:
  ```ld
  XIC(TOGGLE_BUTTON_1)RTO(TIMER_1,10000,0);
  XIC(TIMER_1.DN)OTE(LAMPU_1);
  XIC(TOGGLE_BUTTON_2)RES(TIMER_1);
  ```
* **Working Principle**: The RTO retains accumulated time (`ACC`) across button releases. When total cumulative time reaches 10 seconds (10000 ms), `LAMPU_1` turns ON. Activating `TOGGLE_BUTTON_2` executes a timer reset (`RES`).

---

### 9. `Latihan_2b_Hari_2.rungs`
* **Description**: Pythagorean Theorem hypotenuse mathematical computation.
* **Tags**:
  * **Local**: `A` (`3`), `B` (`4`), `C` (`21`), `AA`, `BB`, `HASIL` (`REAL`)
* **Ladder Logic**:
  ```ld
  MUL(A,A,AA)MUL(B,B,BB)ADD(AA,BB,C)SQRT(C,HASIL);
  ```
* **Working Principle**: Computes `HASIL = sqrt(A^2 + B^2)`. Given defaults A=3 and B=4, it calculates AA=9, BB=16, C=25, resulting in HASIL=5.0.

---

### 10. `Latihan_2b_Hari_3.rungs`
* **Description**: Sequential chemical mixing process based on differential pressure (DP) sensors and timers.
* **Tags**:
  * **Local**: `DP_A` (`5`), `DP_B` (`5`), `LIMIT_5` (`5`), `TIMER_1` (`5000ms`), `TIMER_2` (`5000ms`)
  * **Output**: `VALVE_A`, `VALVE_B`, `VALVE_C` (`BOOL`)
* **Ladder Logic**:
  ```ld
  LT(DP_A,LIMIT_5)OTE(VALVE_A);
  GE(DP_A,LIMIT_5)LT(DP_B,LIMIT_5)OTE(VALVE_B);
  GE(DP_A,LIMIT_5)GE(DP_B,LIMIT_5)TON(TIMER_1,5000,0);
  XIC(TIMER_1.DN)TON(TIMER_2,5000,0);
  XIC(TIMER_1.DN)XIO(TIMER_2.DN)OTE(VALVE_C);
  ```
* **Working Principle**: If DP_A < 5V, `VALVE_A` opens. If DP_A >= 5V and DP_B < 5V, `VALVE_B` opens. When both DP >= 5V, `TIMER_1` runs for 5 seconds, after which `VALVE_C` opens for 5 seconds (`TIMER_2`), followed by an automatic system shutdown.

---

### 11. `Latihan_2c_Hari_3.rungs`
* **Description**: Automatic 3-Phase Star-Delta Induction Motor Starter with latching & interlocks.
* **Tags**:
  * **Input**: `START_BUTTON`, `STOP_BUTTON` (`BOOL`)
  * **Output**: `K1_MAIN`, `K2_STAR`, `K3_DELTA` (`BOOL`)
  * **Local**: `TIMER_STAR_DELTA` (`TIMER`, `PRE: 5000`)
* **Ladder Logic**:
  ```ld
  [XIC(START_BUTTON),XIC(K1_MAIN)]XIO(STOP_BUTTON)OTE(K1_MAIN)TON(TIMER_STAR_DELTA,5000,0);
  XIC(K1_MAIN)XIO(TIMER_STAR_DELTA.DN)XIO(K3_DELTA)OTE(K2_STAR);
  XIC(K1_MAIN)XIC(TIMER_STAR_DELTA.DN)XIO(K2_STAR)OTE(K3_DELTA);
  ```
* **Working Principle**: Pressing `START_BUTTON` latches Main Contactor `K1_MAIN` and energizes Star Contactor `K2_STAR` for the initial 5 seconds. After 5 seconds, the transition timer de-energizes `K2_STAR` and activates Delta Contactor `K3_DELTA`. Includes cross-electrical interlocks between Star and Delta contactors.

---

### 12. `Latihan_3a_Hari_2.rungs`
* **Description**: Multi-stage hazard warning and protection system.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `SIRINE`, `LAMPU`, `SPRINKLER` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(SENS_1)OTE(SIRINE);
  XIC(SENS_1)XIC(SENS_2)OTE(LAMPU);
  XIC(SENS_1)XIC(SENS_2)XIC(SENS_3)OTE(SPRINKLER);
  ```
* **Working Principle**:
  - Level 1 (`SENS_1`) -> Activates `SIRINE`.
  - Level 2 (`SENS_1` AND `SENS_2`) -> Activates `SIRINE` & `LAMPU`.
  - Level 3 (All 3 sensors active simultaneously) -> Activates `SIRINE`, `LAMPU`, & fire `SPRINKLER`.

---

### 13. `Latihan_3a_Hari_3.rungs`
* **Description**: Lamp activation after 5 button press counts (`CTU`).
* **Tags**:
  * **Input**: `START_PUSH_BUTTON`, `STOP_PUSH_BUTTON` (`BOOL`)
  * **Output**: `START_LAMP` (`BOOL`)
  * **Local**: `COUNTER_1` (`COUNTER`, `PRE: 5`)
* **Ladder Logic**:
  ```ld
  XIC(START_PUSH_BUTTON)CTU(COUNTER_1,5,0);
  XIC(COUNTER_1.DN)OTE(START_LAMP);
  XIC(STOP_PUSH_BUTTON)RES(COUNTER_1);
  ```
* **Working Principle**: Each press of `START_PUSH_BUTTON` increments the counter. Upon reaching 5 counts (`ACC=5`), `COUNTER_1.DN` energizes `START_LAMP`. Pressing `STOP_PUSH_BUTTON` resets the counter (`RES`).

---

### 14. `Latihan_3b_Hari_2.rungs`
* **Description**: Linear pressure transmitter scaling (0–32767 RAW to 0–25 bar).
* **Tags**:
  * **Local**: `RAW_VAL` (`16383.5`), `MAX_BAR` (`25`), `MAX_RAW` (`32767`), `TEMP`, `TEKANAN` (`REAL`)
* **Ladder Logic**:
  ```ld
  MUL(RAW_VAL,MAX_BAR,TEMP)DIV(TEMP,MAX_RAW,TEKANAN);
  ```
* **Working Principle**: Converts raw ADC count into engineering bar units: `TEKANAN = (RAW_VAL * MAX_BAR) / MAX_RAW`. For `RAW_VAL` = 16383.5, it calculates `TEKANAN` = 12.5 bar.

---

### 15. `Latihan_3b_Hari_3.rungs`
* **Description**: Multi-level analog alarm indicator with High-Trip/Reset (7V).
* **Tags**:
  * **Local**: `V_IN` (`5.5`), `LIMIT_6` (`6.0`), `LIMIT_5` (`5.0`), `LIMIT_3` (`3.0`), `LIMIT_2` (`2.0`), `LIMIT_7` (`7.0`)
  * **Output**: `LAMPU_1` (HH), `LAMPU_2` (H), `LAMPU_3` (L), `LAMPU_4` (LL) (`BOOL`)
* **Ladder Logic**:
  ```ld
  GE(V_IN,LIMIT_6)LT(V_IN,LIMIT_7)OTE(LAMPU_1);
  GE(V_IN,LIMIT_5)LT(V_IN,LIMIT_7)OTE(LAMPU_2);
  GE(V_IN,LIMIT_3)LT(V_IN,LIMIT_7)OTE(LAMPU_3);
  GE(V_IN,LIMIT_2)LT(V_IN,LIMIT_7)OTE(LAMPU_4);
  ```
* **Working Principle**: `LAMPU_4` energizes when V_IN >= 2V, `LAMPU_3` when V_IN >= 3V, `LAMPU_2` when V_IN >= 5V, and `LAMPU_1` when V_IN >= 6V. All alarms turn OFF if V_IN >= 7V.

---

### 16. `Latihan_3c_Hari_3.rungs`
* **Description**: Automated tank batch mixing system with continuous sequence looping.
* **Tags**:
  * **Input**: `START_BUTTON`, `STOP_BUTTON` (`BOOL`)
  * **Output**: `POMPA_1`, `POMPA_2`, `MOTOR_MIXER`, `POMPA_3` (`BOOL`)
  * **Local**: `RUN` (`BOOL`), `TIMER_1` (5s), `TIMER_2` (5s), `TIMER_3` (7s), `TIMER_4` (10s)
* **Ladder Logic**:
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
* **Working Principle**: Stage 1: `POMPA_1` fills fluid for 5s -> Stage 2: `POMPA_2` fills additive for 5s -> Stage 3: `MOTOR_MIXER` agitates for 7s -> Stage 4: `POMPA_3` drains tank for 10s. When `TIMER_4.DN` turns ON, the batch cycle automatically resets and loops from Stage 1 until `STOP_BUTTON` is pressed.

---

### 17. `Latihan_4a_Hari_2.rungs`
* **Description**: Voting Safety Logic System (1o3, 2o3, 3o3).
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `LAMPU`, `SIRINE`, `SPRINKLER` (`BOOL`)
* **Ladder Logic**:
  ```ld
  [XIC(SENS_1),[XIC(SENS_2),XIC(SENS_3)]]OTE(LAMPU);
  [XIC(SENS_1)XIC(SENS_2),[XIC(SENS_2)XIC(SENS_3),XIC(SENS_1)XIC(SENS_3)]]OTE(SIRINE);
  XIC(SENS_1)XIC(SENS_2)XIC(SENS_3)OTE(SPRINKLER);
  ```
* **Working Principle**: `LAMPU` illuminates if at least 1 sensor triggers (1o3). `SIRINE` activates if at least 2 sensors trigger simultaneously (2o3). `SPRINKLER` activates only if all 3 sensors trigger together (3o3).

---

### 18. `Latihan_4a_Hari_3.rungs`
* **Description**: Normally-ON operation limit system (de-energizes when count > 5).
* **Tags**:
  * **Input**: `START_PUSH_BUTTON`, `STOP_PUSH_BUTTON` (`BOOL`)
  * **Output**: `START_LAMP` (`BOOL`)
  * **Local**: `COUNTER_1` (`COUNTER`, `PRE: 6`)
* **Ladder Logic**:
  ```ld
  XIC(START_PUSH_BUTTON)CTU(COUNTER_1,6,0);
  XIO(COUNTER_1.DN)OTE(START_LAMP);
  XIC(STOP_PUSH_BUTTON)RES(COUNTER_1);
  ```
* **Working Principle**: `START_LAMP` is ON by default. Each press of `START_PUSH_BUTTON` increments `COUNTER_1`. When pressed more than 5 times (reaching preset 6), `COUNTER_1.DN` triggers, opening the Normally Closed contact and turning OFF `START_LAMP`. `STOP_PUSH_BUTTON` resets the count (`RES`).

---

### 19. `Latihan_4b_Hari_2.rungs`
* **Description**: Analog data offset addition & threshold comparison alarm.
* **Tags**:
  * **Local**: `DATA_1` (`2500`), `ADD_VAL` (`3000`), `HASIL`, `LIMIT_VAL` (`5000`)
  * **Output**: `LAMPU` (`BOOL`)
* **Ladder Logic**:
  ```ld
  ADD(DATA_1,ADD_VAL,HASIL);
  GT(HASIL,LIMIT_VAL)OTE(LAMPU);
  ```
* **Working Principle**: Adds offset +3000 to `DATA_1`. If `HASIL` > 5000 (2500 + 3000 = 5500), `LAMPU` energizes.

---

### 20. `Latihan_5a_Hari_2.rungs`
* **Description**: Fire protection alarm system based on combined sensor conditions.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `LAMPU`, `SIRINE`, `SPRINKLER` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(SENS_1)XIC(SENS_2)OTE(LAMPU);
  XIC(SENS_3)OTE(LAMPU)OTE(SIRINE);
  XIC(SENS_3)[XIC(SENS_2),XIC(SENS_1)]OTE(SPRINKLER);
  ```
* **Working Principle**: `LAMPU` activates if (`SENS_1` AND `SENS_2`) OR `SENS_3`. `SIRINE` activates if `SENS_3` triggers. `SPRINKLER` activates if `SENS_3` triggers AND at least one of (`SENS_1` OR `SENS_2`) triggers.

---

### 21. `Latihan_5a_Hari_3.rungs`
* **Description**: Counter accumulation comparison lamp control (`LT`).
* **Tags**:
  * **Input**: `START_PUSH_BUTTON`, `STOP_PUSH_BUTTON` (`BOOL`)
  * **Output**: `START_LAMP` (`BOOL`)
  * **Local**: `COUNTER_1` (`COUNTER`, `PRE: 10`), `LIMIT_5` (`DINT`, `5`)
* **Ladder Logic**:
  ```ld
  XIC(START_PUSH_BUTTON)CTU(COUNTER_1,10,0);
  LT(COUNTER_1.ACC,LIMIT_5)OTE(START_LAMP);
  XIC(STOP_PUSH_BUTTON)RES(COUNTER_1);
  ```
* **Working Principle**: `START_LAMP` remains ON as long as `COUNTER_1.ACC` count is less than 5. Once the count reaches 5 or more, the lamp turns OFF. Resetting via `STOP_PUSH_BUTTON` restores the lamp to ON.

---

### 22. `Latihan_5b_Hari_2.rungs`
* **Description**: Boiler temperature scaling & multi-threshold monitoring.
* **Tags**:
  * **Local**: `RAW_VAL` (`21298.55`), `MAX_TEMP` (`100`), `MAX_RAW` (`32767`), `TEMPERATUR`, `LIMIT_80` (`80`), `LIMIT_60` (`60`), `LIMIT_45` (`45`)
  * **Output**: `BOILER`, `SIRENE`, `LAMPU_PERINGATAN` (`BOOL`)
* **Ladder Logic**:
  ```ld
  MUL(RAW_VAL,MAX_TEMP,TEMP_CALC)DIV(TEMP_CALC,MAX_RAW,TEMPERATUR);
  LT(TEMPERATUR,LIMIT_80)OTE(BOILER);
  GE(TEMPERATUR,LIMIT_60)OTE(SIRENE);
  GE(TEMPERATUR,LIMIT_45)OTE(LAMPU_PERINGATAN);
  ```
* **Working Principle**: Scales raw ADC input to 0–100 °C. `BOILER` heater energizes only when temperature < 80 °C (shuts OFF at >= 80 °C). `LAMPU_PERINGATAN` activates when temperature >= 45 °C. Alarm `SIRENE` activates when temperature >= 60 °C.

---

### 23. `Latihan_6a_Hari_2.rungs`
* **Description**: Simple traffic barrier gate & red/green indicator control.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3` (`BOOL`)
  * **Output**: `PALANG`, `LAMPU_MERAH`, `LAMPU_HIJAU` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(SENS_1)XIO(SENS_2)OTE(PALANG)OTE(LAMPU_HIJAU);
  XIC(SENS_2)OTE(LAMPU_MERAH);
  ```
* **Working Principle**: `SENS_1` detects approaching vehicle -> opens `PALANG` gate and illuminates `LAMPU_HIJAU` (provided obstacle sensor `SENS_2` is clear). If obstacle sensor `SENS_2` is active -> gate remains closed and `LAMPU_MERAH` illuminates.

---

### 24. `Latihan_7a_Hari_2.rungs`
* **Description**: Bi-directional automated gate management with E-Stop & Obstacle Alarm.
* **Tags**:
  * **Input**: `SENS_1`, `SENS_2`, `SENS_3`, `STOP` (`BOOL`)
  * **Output**: `PALANG_MASUK`, `LAMPU_MERAH`, `LAMPU_HIJAU`, `PALANG_KELUAR`, `ALARM` (`BOOL`)
* **Ladder Logic**:
  ```ld
  XIC(SENS_1)XIO(SENS_2)XIO(STOP)OTE(PALANG_MASUK);
  XIC(SENS_3)XIO(STOP)OTE(PALANG_KELUAR);
  XIC(SENS_2)XIO(STOP)OTE(LAMPU_MERAH)OTE(ALARM);
  ```
* **Working Principle**: `PALANG_MASUK` (Entry Gate) opens if `SENS_1` is active, `SENS_2` (obstacle) is clear, and `STOP` switch is inactive. `PALANG_KELUAR` (Exit Gate) opens if `SENS_3` is active and `STOP` is inactive. If `SENS_2` detects an obstacle, both `LAMPU_MERAH` and `ALARM` activate simultaneously.

---

## 👨‍💻 Author & License

**PTIO / CITA ITB Laboratory**  
Master Program in Instrumentation and Control - Institut Teknologi Bandung
