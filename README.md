Beam Balance Using PID Controller

Arduino Uno • HC-SR04 • Servo Motor • Closed-Loop Control

A practical Beam Balance (Ball-and-Beam) control system developed as a course laboratory project at IIT Guwahati.

Author: Lalan Kumar Das
Roll No.: 230108028
Guide: Dr. Chayan Bhawal
Institute: Indian Institute of Technology Guwahati
Department: Electrical and Electronics Engineering

📌 Project Overview

The objective of this project is to demonstrate a real-time closed-loop position control system using a physical beam-and-ball mechanism.

An HC-SR04 ultrasonic sensor measures the position of the ball. The Arduino Uno compares this measured position with the desired reference position and calculates the control action using a PID-control structure. A servo motor then changes the inclination of the beam, causing the ball to roll toward the desired position.

The complete loop is:

Measure → Calculate Error → PID Control → Servo Actuation → Beam Motion → Ball Position → Feedback

This project connects control-system theory with practical embedded hardware and mechanical prototyping.

🎯 Objectives

Build a practical beam-and-ball control prototype.

Measure ball position using an HC-SR04 ultrasonic sensor.

Use Arduino Uno for real-time control computation.

Control beam inclination using a servo motor.

Implement the P, I and D control structure in Arduino.

Understand the role of feedback, error and controller output.

Study practical issues such as sensor noise, mechanical vibration and actuator limitations.

Provide a foundation for experimental PID tuning.

⚙️ System Architecture

The system is designed as a closed-loop feedback control system.

flowchart LR
    R["Reference<br/>Setpoint"] --> E["Error<br/>e = r - y"]
    E --> PID["PID Controller<br/>P + I + D"]
    PID --> S["Servo Motor<br/>Actuator"]
    S --> P["Beam + Ball<br/>Physical Plant"]
    P --> H["HC-SR04<br/>Position Measurement"]
    H --> E

Control principle

For the documented implementation, the reference position is:

Setpoint = 15 cm

The Arduino calculates:

[
e(t) = r(t) - y(t)
]

where:

r(t) = desired ball position

y(t) = measured ball position

e(t) = position error

The controller structure is:

[
u(t) = K_p e(t) + K_i \int e(t),dt + K_d\frac{de(t)}{dt}
]

The resulting controller output is mapped to a suitable servo command.

🔄 Working Principle

The user-defined reference position is set to 15 cm.

The HC-SR04 sends an ultrasonic pulse toward the ball.

The echo time is measured by the Arduino.

The echo duration is converted into distance in centimetres.

The Arduino calculates the position error:
error = setpoint - measured_position

The P, I and D terms are calculated.

The combined controller output is converted into a servo position.

The servo changes the beam inclination through the mechanical linkage.

Gravity causes the ball to roll along the beam.

The sensor measures the new position and the cycle repeats.

The objective is to continuously reduce the position error and keep the ball near the desired location.

🧰 Hardware Components

Component

Function

Arduino Uno

Main controller; reads the sensor and generates the servo command

HC-SR04

Measures the ball position using ultrasonic echo time

Servo Motor

Changes the beam inclination

Beam + Support

Mechanical plant and ball path

Lightweight Ball

Controlled object

Jumper Wires / Breadboard

Electrical connections

Mechanical Linkage

Transfers servo motion to the beam

🔌 Pin Configuration

Device

Pin

Arduino Uno

HC-SR04

VCC

5V

HC-SR04

GND

GND

HC-SR04

TRIG

D2

HC-SR04

ECHO

D3

Servo

Signal

D5

Servo

VCC

5V / suitable external supply

Servo

GND

Common GND

Important: If an external supply is used for the servo, its ground should be connected to the Arduino ground.

🧠 PID Controller Implementation

The Arduino program maintains the previous error and accumulated error to form the three controller actions.

Proportional term

P = Kp × error

The proportional action responds to the present error.

Integral term

I = Ki × accumulated_error

The integral action accounts for the accumulated error over time.

Derivative term

D = Kd × change_in_error

The derivative action responds to the change in error.

Total controller output

PID output = P + I + D

The output is then mapped to a servo angle and limited to the safe range used by the implementation.

💻 Arduino Uno Code

The complete Arduino implementation is available in:

src/beam_balance_pid.ino

The core implementation follows this feedback sequence:

#include <Servo.h>

Servo servo;

#define trig 2
#define echo 3

#define kp 0
#define ki 0
#define kd 0

double priError = 0;
double toError = 0;

void setup() {
    pinMode(trig, OUTPUT);
    pinMode(echo, INPUT);

    servo.attach(5);

    Serial.begin(9600);
    servo.write(50);
}

void loop() {
    PID();
}

long distance() {
    digitalWrite(trig, LOW);
    delayMicroseconds(4);

    digitalWrite(trig, HIGH);
    delayMicroseconds(10);

    digitalWrite(trig, LOW);

    long t = pulseIn(echo, HIGH);
    long cm = t / 29 / 2;

    return cm;
}

void PID() {
    int dis = distance();

    int setP = 15;
    double error = setP - dis;

    double Pvalue = error * kp;
    double Ivalue = toError * ki;
    double Dvalue = (error - priError) * kd;

    double PIDvalue = Pvalue + Ivalue + Dvalue;

    priError = error;
    toError += error;

    Serial.println(PIDvalue);

    int Fvalue = (int)PIDvalue;

    Fvalue = map(Fvalue, -135, 135, 135, 0);

    if (Fvalue < 0)
        Fvalue = 0;

    if (Fvalue > 135)
        Fvalue = 135;

    servo.write(Fvalue);
}

⚠️ Note about PID tuning

The documented code currently contains:

#define kp 0
#define ki 0
#define kd 0

These values are intentionally documented as the starting framework, not as final optimized tuning parameters.

For actual closed-loop performance, Kp, Ki and Kd should be tuned experimentally based on the mechanical setup and measured response.

📏 Sensor Measurement

The HC-SR04 determines distance from the duration of the echo pulse.

The implementation uses:

long cm = t / 29 / 2;

The measured distance is then used as the feedback signal for the controller.

Because ultrasonic measurements can fluctuate, practical improvements may include averaging or filtering the sensor readings.

🏗️ Physical Prototype

The theoretical ball-and-beam system was converted into a practical prototype using a lightweight mechanical structure.

The setup consists of:

A beam supported around a pivot

A servo-based beam adjustment mechanism

HC-SR04 sensor for position measurement

Arduino Uno as the controller

A lightweight ball as the controlled object

📷 Prototype Image

Upload the project photograph to: images/beam-balance-prototype.jpg



Figure: Practical Beam Balance prototype.

📊 Experimental Observation

During operation, the main quantities of interest are:

Desired ball position

Measured ball position

Position error

PID controller output

Servo angle

Response stability

Settling behaviour

Sensor fluctuations

The Arduino Serial Monitor/Serial Plotter can be used to observe controller behaviour during testing.

Suggested result plot

Upload your Serial Plotter/result graph to: images/pid-response.png



Figure: Experimental position/controller response.

⚠️ Practical Challenges

1. Ultrasonic sensor noise

HC-SR04 measurements may fluctuate due to sensing conditions and ball geometry.

Possible improvement: moving-average or other filtering.

2. Mechanical vibration

A flexible beam or loose linkage can introduce unwanted movement.

Possible improvement: use a more rigid beam and stronger mechanical support.

3. Sensor alignment

Incorrect sensor alignment can produce inconsistent position measurements.

Possible improvement: mount and align the sensor carefully with the ball path.

4. Servo limitations

The servo has limited speed, torque and angular range.

Possible improvement: use an appropriate servo and stable power supply.

5. PID tuning

The controller gains strongly affect stability, response speed and overshoot.

Possible improvement: tune Kp, then Ki and Kd experimentally.

🚀 Future Improvements

Experimentally tune Kp, Ki and Kd.

Add ultrasonic measurement filtering.

Log measured position and controller output.

Generate quantitative response plots.

Improve beam rigidity and pivot construction.

Improve servo-to-beam mechanical linkage.

Optimize sensor placement and alignment.

Use a dedicated/stable servo power supply.

Add better protection for sensor noise and abnormal readings.

Compare P, PI and PID responses experimentally.

🎥 Project Demonstration

Upload your demonstration video and replace the link below.

Video: ▶ Watch Project Demonstration

The video should demonstrate:

Complete hardware setup

Ball movement on the beam

Ultrasonic position measurement

Servo-based beam adjustment

Closed-loop control behaviour

📄 Project Report / PDF

The detailed project report is available here:

📘 View Project Report

The report contains the system description, objectives, hardware design, Arduino implementation, control methodology, prototype discussion, conclusion and future improvements.


🎓 Academic Context

This work was completed as a course laboratory project at IIT Guwahati under the guidance of Dr. Chayan Bhawal.

The project provided practical exposure to:

Feedback control systems

PID controllers

Sensor interfacing

Arduino programming

Servo actuation

Closed-loop control

Embedded systems

Mechanical prototyping

Experimental controller tuning

👨‍💻 Author

Lalan Kumar Das
Roll No.: 230108028
Electrical and Electronics Engineering
Indian Institute of Technology Guwahati

