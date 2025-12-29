# 📡 Oscilloscope Signal Analysis 

오실로스코프를 활용하여  
**UART / Ultrasonic Sensor / DHT11 / I2C / LCD1602 / DMA** 등  
임베디드 시스템에서 사용되는 주요 통신 및 하드웨어 동작을  
**파형 레벨에서 분석하고 검증한 학습**입니다.

단순히 코드가 “동작한다”에서 끝나는 것이 아니라,  
**데이터시트 ↔ 코드 ↔ 실제 신호 파형**의 관계를 직접 확인하고  
디버깅을 할 수 있는 능력을 키우는것을 목표로 하였습니다.

---

## 🧩 Project Overview

- **Category**: Embedded Systems / Firmware / Signal Analysis  
- **Tools**: Oscilloscope 
- **Platform**: STM32CubeIDE (STM32411RE)
- **Focus**:
  - 프로토콜 타이밍 분석
  - 센서 신호 해석 및 검증
  - 이론과 실제 동작 간 차이 이해

---

## 🎯 Analysis Objectives

- MCU 내부 동작을 **파형 레벨에서 이해**
- 데이터시트의 타이밍 조건을 **실제 신호로 검증**
- 코드 실행 결과와 하드웨어 신호의 연관성 분석
- 임베디드 디버깅 역량 강화

---

## 🔍 Analysis Topics

---

### 1️⃣ UART Protocol Analysis

#### ✔ Analysis Points
- Start Bit / Data Bit / Stop Bit 구조 확인
- **LSB First** 전송 방식 검증
- Baud Rate 기준 비트 주기 측정
- Idle 상태(High) 확인
- 문자 전송시 ASCII로 변환된것을 확인
  
#### ✔ Key Observations
- Start Bit는 항상 **Low**
- 데이터는 **LSB → MSB 순서로 전송**
- Baudrate에 따라 비트 폭이 결정됨

#### 📷 Oscilloscope Capture
<img width="759" height="235" alt="uart_scope" src="https://github.com/user-attachments/assets/806e9002-5c79-46a4-aa1c-c1854a01fcef" />

---

### 2️⃣ Ultrasonic Sensor (HC-SR04) Signal Analysis

#### ✔ Analysis Points
- Trigger 신호 폭 측정
- Echo 신호 High 유지 시간 측정
- 거리 계산 공식 검증

#### ✔ Distance Calculation
- 거리 = 속도 X 시간
- Echo High Time = 300 µs  
→ Distance ≈ **5.1 cm**

#### 📷 Oscilloscope Capture
<img width="467" height="710" alt="ultrasonic_scope_1" src="https://github.com/user-attachments/assets/e6578dc1-758e-4eed-a2ea-5e58830d6d8e" />
<img width="393" height="705" alt="ultrasonic_scope_2" src="https://github.com/user-attachments/assets/bbafbd41-f924-4661-9068-fb1fbb4467a5" />

---

### 3️⃣ DHT11 Temperature & Humidity Protocol Analysis

#### ✔ Analysis Points
- MCU → DHT11 **Start Signal 타이밍 분석**
- DHT11 Response 신호 확인
- 데이터 비트(0 / 1)를 **High 펄스 길이 차이**로 구분
- 총 **40-bit 데이터 프레임 구조** 분석

#### ✔ DHT11 Communication Timing
- MCU Start Signal  
  - Low ≈ **18 ms**  
  - High ≈ **20~40 µs**
- DHT11 Response  
  - Low → High 응답 신호 발생
- Data Bit Encoding  
  - `0` : High ≈ **26~28 µs**  
  - `1` : High ≈ **70 µs**

#### ✔ Key Observations
- DHT11은 ADC가 아닌 **내부 MCU에서 디지털 신호 생성**
- 데이터는 전압이 아닌 **시간 길이로 0/1 표현**
- 정확한 타이머/딜레이 제어가 필수적임

#### 📷 Oscilloscope Capture
<img width="469" height="345" alt="dht11_scope_1" src="https://github.com/user-attachments/assets/c3717ac3-79e9-40ea-b1bb-290b57bb131d" />
<img width="449" height="315" alt="dht11_scope_2" src="https://github.com/user-attachments/assets/c85dafcb-0e1e-497f-b408-5fcae75993bf" />

---

### 4️⃣ I2C Communication Analysis (LCD1602)

#### ✔ Analysis Points
- SDA / SCL 신호 관계 분석
- Start / Stop Condition 확인
- ACK Bit 동작 확인
- 동기식 통신 구조 검증
- Enable 신호 타이밍 확인
- Command / Data 전송 시 파형 비교
  
#### ✔ Key Observations
- SCL High 상태에서 SDA 변화 → Start / Stop
- 8bit 슬레이브 주소/ 8-bit 데이터 전송 후 ACK Bit 존재 확인
- Master-Slave 구조 확인

#### 📷 Oscilloscope Capture
<img width="908" height="477" alt="i2c_lcd_scope_1" src="https://github.com/user-attachments/assets/e888be84-ea79-4160-a90e-bd33af32062b" />
<img width="790" height="370" alt="i2c_lcd_scope_2" src="https://github.com/user-attachments/assets/40db406d-1f53-4ef7-9ca8-451ac89bd32a" />
<img width="683" height="332" alt="i2c_lcd_scope_3" src="https://github.com/user-attachments/assets/d8566083-c6ca-4326-9c85-0e21c1444926" />
<img width="753" height="347" alt="i2c_lcd_scope_4" src="https://github.com/user-attachments/assets/eff3c0a4-c7f1-4012-a80b-f0a9585e42a1" />

---

### 5️⃣ DMA Operation Concept 

#### ✔ Analysis Points
- CPU 개입 없이 데이터 전송 구조 이해
- 인터럽트 기반 처리와 DMA 방식 비교
- 시스템 성능 관점에서 DMA 필요성 정리

#### ✔ Key Observations
- DMA 사용 시 CPU 부하 감소
- 실시간 시스템에서 데이터 처리 효율 향상

---

## 🧠 What I Learned

- 코드가 정상이어도 **파형이 틀리면 시스템은 오동작할 수 있음**
- 데이터시트 타이밍 조건은 **실측으로 반드시 검증해야 함**
- 타이밍 기반 센서(DHT11)는 **소프트웨어 지연 오차에 매우 민감**
- Timer / Input Capture 기반 측정의 중요성 체감
- 오실로스코프는 임베디드 디버깅의 핵심 도구임

---

## 📌 Conclusion

본 학습을 통해  
임베디드 개발자는 장치가 정상적으로 동작하지 않을떄
코드에서만 오류를 확인 하는것이 아닌
스코프를 통해 H/W디버깅 할 수 있는 능력이 중요하다는 것을 알았고 
이 능력을 습득하여 좋은 경험이였다고 생각합니다.


---




