# 🔌 Latching Power Switch with Auto ON/OFF using Arduino

This project demonstrates a **latching power switch circuit** that allows an Arduino to **turn itself on and off**, either through a pushbutton or automatically based on timer logic.

The design improves power efficiency by disconnecting the main power rail using a **P-Channel MOSFET** and controlling it through a **NPN transistor**.

---

## 🚀 Features

- Pressing a button turns on the Arduino and an LED.
- The Arduino remains powered for 10 seconds.
- After 10 seconds, it powers itself off.
- After 5 seconds, it powers back on automatically.
- The LED brightness is controlled using PWM.

---

## 🧰 Components Used

| Component         | Quantity |
|------------------|----------|
| Arduino Uno       | 1        |
| Pushbutton        | 1        |
| P-Channel MOSFET  | 1        |
| NPN Transistor (e.g., 2N2222) | 1        |
| Resistors (220Ω, 10KΩ) | Several |
| LED               | 1        |
| Breadboard & Wires | 1        |

---

## 🔌 Circuit Overview

- The **pushbutton** connects to pin `D2` (INPUT).
- The **MOSFET Gate** is controlled via `D5` (OUTPUT).
- The **PWM pin `D9`** controls the LED brightness.
- A **NPN transistor** acts as an interface between Arduino and the MOSFET gate to latch the power.
- The circuit powers OFF by pulling the MOSFET gate HIGH via the transistor.

---

## 🧠 How It Works

1. Initially, the Arduino is off.
2. Pressing the button briefly powers the board through the transistor → the Arduino boots up.
3. Arduino sets `D5 HIGH`, keeping itself powered.
4. After 10 seconds, the Arduino sets `D5 LOW`, cutting its own power.
5. After a 5-second delay, the button logic restarts the board automatically.

---

## 💡 Arduino Code (Short Version)

```cpp
const int buttonPin = 2;
const int latchPin = 5;
const int pwmPin = 9;

const unsigned long onDuration = 10000;
const unsigned long offDuration = 5000;

enum PowerState { OFF, ON };
PowerState currentState = OFF;

unsigned long stateStartTime = 0;

void setup() {
  pinMode(buttonPin, INPUT);
  pinMode(latchPin, OUTPUT);
  pinMode(pwmPin, OUTPUT);
  digitalWrite(latchPin, LOW);
  analogWrite(pwmPin, 0);
  Serial.begin(9600);
}

void loop() {
  unsigned long now = millis();

  if (digitalRead(buttonPin) == HIGH && currentState == OFF) {
    turnOn();
    currentState = ON;
    stateStartTime = now;
    Serial.println("Power ON by button");
  }

  if (currentState == ON && now - stateStartTime >= onDuration) {
    turnOff();
    currentState = OFF;
    stateStartTime = now;
    Serial.println("Power OFF");
  }

  if (currentState == OFF && now - stateStartTime >= offDuration) {
    turnOn();
    currentState = ON;
    stateStartTime = now;
    Serial.println("Auto Power ON");
  }
}

void turnOn() {
  digitalWrite(latchPin, HIGH);
  analogWrite(pwmPin, 64); // 25% brightness
}

void turnOff() {
  digitalWrite(latchPin, LOW);
  analogWrite(pwmPin, 0);
}
