# ⚖️ Beam Balance System

### Ball-and-Beam Position Control using Arduino Uno, Ultrasonic Sensor & Servo Motor

**Course Lab Project**

**Developed by:** **Lalan Kumar Das**
**Under the Guidance of:** **Dr. Chayan Bhawal**

---

## 📸 Project Preview

> **Add your best and clearest photograph of the complete working prototype here.**
> This should be the first image visitors see when they open the repository.

![Beam Balance System](images/beam-balance-main.jpg)

---

## 📌 About the Project

The **Beam Balance System**, also known as a **Ball-and-Beam System**, is a practical control-system project developed as part of a course laboratory.

The objective of this project is to control the position of a ball placed on a beam by automatically adjusting the **angle of the beam using a servo motor**.

An **HC-SR04 ultrasonic sensor** measures the position of the ball. The measured position is sent to an **Arduino Uno**, which calculates the error between the desired position and the actual position. Based on this error, a control signal is generated for the servo motor.

The servo changes the beam angle, causing the ball to move toward the desired position.

The system therefore demonstrates the fundamental concept of a **closed-loop feedback control system**.

---

## 🎯 Objectives

The main objectives of this project are:

* To understand the working principle of a **ball-and-beam control system**.
* To measure the position of a ball using an **ultrasonic sensor**.
* To control the beam angle using a **servo motor**.
* To implement a feedback-based control system using **Arduino Uno**.
* To understand the practical implementation of a **PID controller**.
* To study the effect of controller parameters on system response.
* To gain hands-on experience in integrating **sensors, actuators, embedded systems, and control algorithms**.

---

## 🧠 System Concept

The project follows a closed-loop control structure:

```text
             Desired Position
                    │
                    ▼
              ┌───────────┐
              │  Compare  │
              └─────┬─────┘
                    │
                  Error
                    │
                    ▼
              ┌───────────┐
              │ Controller│
              │ PID Logic │
              └─────┬─────┘
                    │
              Control Signal
                    │
                    ▼
              ┌───────────┐
              │   Servo   │
              │   Motor   │
              └─────┬─────┘
                    │
              Beam Angle
                    │
                    ▼
              ┌───────────┐
              │    Ball   │
              │ Position  │
              └─────┬─────┘
                    │
                    ▼
              ┌───────────┐
              │ HC-SR04   │
              │  Sensor   │
              └─────┬─────┘
                    │
                    └──────────► Feedback
```

---

# ⚙️ How the System Works

The complete operation can be understood in five basic steps.

### 1. Ball Position Measurement

The **HC-SR04 ultrasonic sensor** measures the distance between the sensor and the ball.

The sensor sends an ultrasonic pulse and measures the time taken for the reflected pulse to return.

The Arduino calculates the approximate distance using the echo time.

In the program:

```cpp
long t = pulseIn(echo, HIGH);
long cm = t / 29 / 2;
```

The calculated distance is then used as the feedback signal.

---

### 2. Setpoint

A desired ball position is defined in the program:

```cpp
int setP = 15;
```

Therefore, the current program uses **15 cm as the target distance from the ultrasonic sensor**.

> **Important:** This means `15 cm` represents the sensor-to-ball distance, not necessarily the physical center of the beam.

---

### 3. Error Calculation

The controller calculates the difference between the desired position and the measured position.

```text
Error = Setpoint − Measured Position
```

In the Arduino code:

```cpp
double error = setP - dis;
```

If the ball moves away from the desired position, the error changes and the controller responds accordingly.

---

### 4. Control Action

The calculated error is processed by the controller.

The project contains the basic structure of a PID controller:

```text
PID Output = P + I + D
```

The output is then converted into a suitable servo angle.

---

### 5. Beam Adjustment

The servo motor changes the angle of the beam.

When the beam angle changes, gravity causes the ball to roll along the beam.

The ultrasonic sensor measures the new position again, and the process repeats.

This continuous measurement and correction creates the **feedback loop**.

---

# 🔄 Closed-Loop Feedback

The complete feedback process is:

```text
Ball Position
     ↓
Ultrasonic Measurement
     ↓
Arduino
     ↓
Error Calculation
     ↓
Controller
     ↓
Servo Motor
     ↓
Beam Angle
     ↓
Ball Movement
     ↓
New Ball Position
     ↓
Ultrasonic Measurement
     ↺
```

This is the fundamental idea behind many real-world automatic control systems.

---

# 🧰 Hardware Components

| Component                  |    Quantity | Purpose                       |
| -------------------------- | ----------: | ----------------------------- |
| Arduino Uno                |           1 | Main controller               |
| HC-SR04 Ultrasonic Sensor  |           1 | Ball position measurement     |
| SG90 Servo Motor           |           1 | Beam angle control            |
| Ball                       |           1 | Controlled object             |
| Breadboard                 |           1 | Circuit prototyping           |
| Jumper Wires               | As required | Electrical connections        |
| Cardboard / Beam Structure |           1 | Mechanical structure          |
| USB Cable                  |           1 | Programming and communication |

---

# 🔌 Pin Configuration

The current implementation uses the following Arduino connections:

| Component   | Pin    | Arduino Uno |
| ----------- | ------ | ----------- |
| HC-SR04     | TRIG   | D2          |
| HC-SR04     | ECHO   | D3          |
| Servo Motor | Signal | D5          |
| HC-SR04     | VCC    | 5V          |
| HC-SR04     | GND    | GND         |
| Servo Motor | VCC    | 5V*         |
| Servo Motor | GND    | GND         |

### Power Note

For a more reliable hardware implementation, the servo should preferably be supplied by a suitable external **5V supply**, especially if the servo causes voltage fluctuations.

The **Arduino ground and external servo-supply ground must be connected together**.

---

# 🖼️ Circuit Diagram

The following circuit diagram represents the connection between the Arduino Uno, HC-SR04 ultrasonic sensor, and servo motor.

![Beam Balance Circuit Diagram](images/circuit-diagram.png)

> **Add the final circuit diagram to the `images` folder using the filename `circuit-diagram.png`.**

---

# 🧮 PID Controller

The project uses a PID-based control structure.

PID stands for:

* **P — Proportional**
* **I — Integral**
* **D — Derivative**

The general PID equation is:

```text
u(t) = Kp·e(t) + Ki∫e(t)dt + Kd·de(t)/dt
```

where:

* `e(t)` = error
* `Kp` = proportional gain
* `Ki` = integral gain
* `Kd` = derivative gain
* `u(t)` = controller output

### Proportional Action

The proportional term responds to the **present error**.

```text
P = Kp × Error
```

A larger error produces a larger proportional response.

---

### Integral Action

The integral term considers the **accumulated error over time**.

```text
I = Ki × Accumulated Error
```

It can help reduce steady-state error.

---

### Derivative Action

The derivative term considers the **change in error**.

```text
D = Kd × Change in Error
```

It can help reduce excessive oscillation and improve the response of the system.

---

# 💻 PID Implementation in Arduino

The PID calculation is implemented in the program as:

```cpp
double Pvalue = error * kp;
double Ivalue = toError * ki;
double Dvalue = (error - priError) * kd;

double PIDvalue = Pvalue + Ivalue + Dvalue;
```

The previous error is stored using:

```cpp
priError = error;
```

and the accumulated error is updated using:

```cpp
toError += error;
```

The controller output is then converted into a servo command.

---

## ⚠️ Current PID Status

The current code contains:

```cpp
#define kp 0
#define ki 0
#define kd 0
```

Therefore, **the PID calculation framework is implemented, but the PID gains are currently set to zero**.

This means the current version should be considered a **PID-based control prototype**, with proper gain tuning remaining as an important next step.

The values of `Kp`, `Ki`, and `Kd` can be experimentally adjusted to achieve:

* Better stability
* Lower steady-state error
* Reduced oscillation
* Faster response
* Better ball positioning

> This distinction is intentionally mentioned so that the repository accurately represents the current implementation.

---

# 🎛️ Servo Control

The calculated controller output is mapped to a servo operating range:

```cpp
Fvalue = map(Fvalue, -135, 135, 135, 0);
```

The value is then restricted to the allowed range:

```cpp
if (Fvalue < 0) {
  Fvalue = 0;
}

if (Fvalue > 135) {
  Fvalue = 135;
}
```

Finally, the servo is commanded using:

```cpp
servo.write(Fvalue);
```

The servo movement changes the beam inclination and consequently changes the position of the ball.

---

# 🖥️ Serial Monitoring

The controller output is sent to the Serial Monitor at:

```cpp
Serial.begin(9600);
```

The PID output can therefore be observed during experimentation.

This can be useful while tuning the controller and studying system response.

---

# 📷 Hardware Prototype

## Complete Prototype

![Complete Beam Balance Prototype](images/beam-balance-main.jpg)

## Front View

![Front View](images/beam-balance-front.jpg)

## Top View

![Top View](images/beam-balance-top.jpg)

## Electronics and Arduino

![Electronics Setup](images/beam-balance-electronics.jpg)

## Mechanical Structure

![Mechanical Structure](images/beam-balance-mechanical.jpg)

> **Recommended:** The most important image here is the **complete working prototype**.
> A clear photograph showing the beam, ball, ultrasonic sensor, servo, and Arduino together makes the project much easier to understand.

---

# 🎥 Demonstration Video

A demonstration of the working prototype will be provided here.

### ▶️ Beam Balance Demonstration

**[Watch the Project Demonstration Video](video/beam-balance-demo.mp4)**

> Upload the final demonstration video to the `video` folder, or replace the link above with your YouTube/Google Drive video link.

### What the video should ideally show

1. The complete hardware setup.
2. The ball placed on the beam.
3. The ultrasonic sensor measuring the ball position.
4. Servo movement.
5. Beam movement.
6. Ball movement toward the desired position.
7. Arduino Serial Monitor, if useful.

---

# 📄 Project Report

The complete project report/documentation will be added here:

### 📘 [View / Download Project Report](docs/Beam-Balance-Project-Report.pdf)

The report contains detailed information about:

* Project introduction
* Objectives
* System design
* Hardware components
* Circuit diagram
* Working principle
* Control-system concept
* PID controller
* Arduino implementation
* Experimental observations
* Limitations
* Future improvements
* Conclusion

> **Upload your final PDF to `docs/Beam-Balance-Project-Report.pdf`.**

---


# 🚀 How to Run

### 1. Hardware Setup

Connect the components according to the pin configuration and circuit diagram.

### 2. Open Arduino IDE

Open:

```text
Beam_Balance.ino
```

### 3. Select Board

Select:

```text
Arduino Uno
```

and choose the correct COM port.

### 4. Upload

Upload the program to the Arduino Uno.

### 5. Place the Ball

Place the ball on the beam within the sensing range of the ultrasonic sensor.

### 6. Observe the System

Open the Serial Monitor at:

```text
9600 baud
```

Observe the controller output and servo response.

### 7. PID Tuning

If PID control is being experimentally tuned, adjust:

```cpp
#define kp ...
#define ki ...
#define kd ...
```

and observe the effect on the ball position and system stability.

---

# 📊 System Parameters

| Parameter           | Current Value |
| ------------------- | ------------- |
| Microcontroller     | Arduino Uno   |
| Distance Sensor     | HC-SR04       |
| Actuator            | SG90 Servo    |
| TRIG Pin            | D2            |
| ECHO Pin            | D3            |
| Servo Signal        | D5            |
| Setpoint            | 15 cm         |
| Serial Baud Rate    | 9600          |
| Servo Command Range | 0°–135°       |
| Control Structure   | PID-based     |
| Current `Kp`        | 0             |
| Current `Ki`        | 0             |
| Current `Kd`        | 0             |

---

# ⚠️ Limitations

As a low-cost laboratory prototype, the current system has several practical limitations:

* The mechanical beam is constructed from cardboard.
* The mechanical structure is less rigid than an industrial setup.
* Ultrasonic sensors can be affected by measurement noise and object orientation.
* Servo motors have limited speed, torque, and angular resolution.
* Mechanical backlash can affect positioning accuracy.
* The PID parameters require proper experimental tuning.
* The current PID gains are set to zero.
* Servo power requirements can affect Arduino stability if the power source is inadequate.

---

# 🔧 Future Improvements

The project can be further improved by:

* Experimentally tuning `Kp`, `Ki`, and `Kd`.
* Using a stronger and more rigid beam.
* Improving the servo-to-beam mechanical linkage.
* Adding filtering to reduce ultrasonic sensor noise.
* Using a dedicated power supply for the servo.
* Adding an LCD/OLED display for real-time measurements.
* Recording position and control-output data.
* Plotting the system response.
* Measuring **rise time, settling time, overshoot, and steady-state error**.
* Comparing different controller settings.
* Using a more precise position sensor for improved accuracy.
* Developing a more robust mechanical structure for long-term operation.

---


# 👨‍💻 Project Information

### Student

**Lalan Kumar Das**
** 230108028   IITG EEE **

### Project Type

**Course Lab Project**

### Guided By

**Dr. Chayan Bhawal**

### Project

**Beam Balance / Ball-and-Beam Control System**

---

# ⭐ Project Status

**Prototype Completed**

The physical Beam Balance prototype has been constructed and tested as a course-lab project.

The current implementation establishes the sensor–controller–actuator feedback structure and includes a PID calculation framework. Further PID tuning and mechanical optimization can be performed to improve stability and positioning accuracy.

---

## 🙌 Acknowledgement

I would like to express my sincere gratitude to **Dr. Chayan Bhawal** for his guidance and support during the development of this course-lab project.

This project provided valuable hands-on experience in applying control-system concepts to a real physical system and helped bridge the gap between theoretical concepts and practical implementation.

---

<p align="center">
  <b>⚙️ Built to understand Control Systems through a real physical prototype.</b>
</p>
