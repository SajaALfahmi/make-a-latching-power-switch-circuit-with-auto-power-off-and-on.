# 🔌 Smart Latching Power Switch (Auto ON/OFF + Manual Trigger)

This project implements a **latching power switch** using an Arduino, allowing **automatic power cycling** as well as **manual activation via a pushbutton**.

The goal is to build a **power-saving circuit** that:
- Automatically turns **on** and **off** based on a timer.
- Allows **manual activation** using a button.
- Powers itself off after a defined period without needing external intervention.

---

## 🚀 Features

✅ Pressing a button powers the Arduino manually.  
✅ The Arduino powers itself off after 10 seconds.  
✅ After 5 seconds of being off, it powers itself back on.  
✅ No need for holding the button — it "latches" power automatically.  
✅ Smart power control using software and transistor-based switching.

---

## 🧰 Components Used

| Component              | Quantity |
|------------------------|----------|
| Arduino Uno            | 1        |
| Pushbutton             | 1        |
| P-Channel MOSFET (e.g., IRF9540) | 1 |
| NPN Transistor (e.g., 2N2222)    | 1 |
| LED                    | 1        |
| Resistors (10K, 220Ω)  | As needed |
| Breadboard & Wires     | 1 set    |

---

## 🔌 How It Works

- **Manual Start:**  
  When the pushbutton is pressed, a small current activates the NPN transistor which pulls the gate of the pMOS low → powering the Arduino.  
  Once powered, the Arduino immediately sets a pin HIGH to "latch" itself on.

- **Auto OFF:**  
  After 10 seconds, the Arduino stops the latch signal, turning itself off by deactivating the pMOS.

- **Auto ON:**  
  After a 5-second delay, the Arduino reactivates via a timed loop.

---

## 🧠 Logic Flow

1. Arduino is **off**.  
2. **Pushbutton** is pressed → powers on Arduino.  
3. Arduino **keeps itself on** for 10 seconds.  
4. Arduino **powers off** automatically.  
5. Waits 5 seconds → **powers back on**.  
6. Repeat...

---

## 🎬 Simulation Video

A simulation of this latching power switch circuit has been created in **Tinkercad** to demonstrate its operation virtually.  
You can watch the process and verify the logic in the video file: `latching_power_switch.mov`.

---

## 💡 Arduino Code 

```cpp
const int buttonPin = 2;
const int latchPin = 5;
const int pwmPin = 9;

const unsigned long onDuration = 10000;  // milliseconds
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
  unsigned long currentTime = millis();

  // Handle button press to start
  if (digitalRead(buttonPin) == HIGH && currentState == OFF) {
    turnOn();
    currentState = ON;
    stateStartTime = currentTime;
    Serial.println("Power ON by button");
  }

  // Handle auto OFF
  if (currentState == ON && currentTime - stateStartTime >= onDuration) {
    turnOff();
    currentState = OFF;
    stateStartTime = currentTime;
    Serial.println("Power OFF");
  }

  // Handle auto ON after delay
  if (currentState == OFF && currentTime - stateStartTime >= offDuration) {
    turnOn();
    currentState = ON;
    stateStartTime = currentTime;
    Serial.println("Auto Power ON");
  }
}

void turnOn() {
  digitalWrite(latchPin, HIGH);
  analogWrite(pwmPin, 64);
}

void turnOff() {
  digitalWrite(latchPin, LOW);
  analogWrite(pwmPin, 0);
}
