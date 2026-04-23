# 🏎️ STM32 Autonomous Line Follower & Collision Avoidance

![Platform](https://img.shields.io/badge/Platform-STM32%20Nucleo-blue)
![Framework](https://img.shields.io/badge/Framework-STM32Cube%20HAL-green)
![Status](https://img.shields.io/badge/Status-Active-success)

## 🌟 Project Overview
This repository contains the firmware for an autonomous line-following robot built on the STM32 platform. The system uses a two-channel digital IR sensor setup for navigation and features a high-priority hardware-interrupt safety loop utilizing an ultrasonic sensor. If an obstacle is detected within 15 cm, the robot halts, triggers visual and audio alarms, and sends an alert via UART.

### 🧠 Core Behaviors
* **Standby Mode:** The car waits for the onboard User Button (`B1`) to be pressed before initiating the main loop (`ready = 1`).
* **Line Following:** Reads two digital line sensors and adjusts L298N motor direction dynamically.
* **Ultrasonic Polling:** Uses Timer 5 (Input Capture) and Timer 4 to accurately measure pulse widths and calculate distance without blocking the main loop.
* **Emergency Override:** If `distance < 15`, the robot stops, activates a buzzer via PWM (`TIM3`), turns on a warning LED (`PA5`), and transmits `"Obstacle detected!!!\r\n"` over UART.
* **Analog Safety Check:** Utilizes ADC1 to ensure a minimum voltage threshold (approx. 2V) before allowing motor movement.

---

## 🛠️ Hardware Architecture & Pinout

### 🔌 Pin Mapping
Based on the internal STM32 CubeIDE configuration, wire your components as follows:

| Component | STM32 Pin | Peripheral / I/O | Function |
| :--- | :--- | :--- | :--- |
| **Start Button** | `B1_Pin` | EXTI | Initiates the start sequence |
| **Line Sensor (Left)** | `PA7` | GPIO Input | Digital line detection |
| **Line Sensor (Right)**| `PB6` | GPIO Input | Digital line detection |
| **Ultrasonic Trigger** | `PA0` | GPIO Output | Triggers HC-SR04 ping |
| **Ultrasonic Echo** | `TIM5_CH2` | Input Capture | Measures pulse width for distance |
| **Motor L (Speed)** | `TIM2_CH2` | PWM Output | ENA on Motor Driver |
| **Motor R (Speed)** | `TIM2_CH3` | PWM Output | ENB on Motor Driver |
| **Motor Driver IN1** | `PB4` | GPIO Output | Left Motor Direction |
| **Motor Driver IN2** | `PB5` | GPIO Output | Left Motor Direction |
| **Motor Driver IN3** | `PB9` | GPIO Output | Right Motor Direction |
| **Motor Driver IN4** | `PB8` | GPIO Output | Right Motor Direction |
| **Buzzer** | `TIM3_CH3` | Timer OC | Audio alarm during obstacle detection |
| **Warning LED** | `PA5` | GPIO Output | Visual alarm during obstacle detection |
| **Telemetry (UART)** | `USART1` | TX/RX | Sends collision alerts to PC/Bluetooth |
| **Analog Sensor/Bat** | `ADC1_IN6` | ADC Input | Safety threshold check |

---

## ⚙️ Software Implementation

### Timer & Interrupt Utilization
This project relies on hardware timers to keep the CPU free for logic processing:
* **`TIM2`**: Generates PWM signals for motor speed control.
* **`TIM3`**: Operates in Output Compare mode to generate the buzzer frequency.
* **`TIM4`**: Timing base to trigger the ultrasonic sensor periodically.
* **`TIM5`**: Input Capture mode specifically configured to measure the microsecond width of the HC-SR04 echo pulse.

---

## 🚀 Getting Started

### Prerequisites
* **STM32CubeIDE**.
* STM32 Nucleo board (configured for HSI internal clock).

---
