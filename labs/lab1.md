---
title: ""
image: /assets/LeekColor.png
---

# Lab 1

**Goal:**
The goal of this lab was to understand how to program an Arduino to do different tasks so that eventually we would be able to program it to make the robot do an autonomous task.

**Materials:**
The main portion of this lab required the following materials for each subteam:
* Arduino Uno
* USB A/B Cable
* Continuous Rotation Servo
* LED
* Potentiometer
* Resistors
* 1 Solderless Breadboard  

The preliminary construction of our robot required:
* Chassis
* Screws
* 9V Battery with Clip
* Ball Bearings

**Procedure:**

*Part 1: Blinking LED*  
We first got the LED on the board to blink, then hooked up an external LED to the board as shown below. It is important to note that a series resistor of at least 300 ohm was added to this setup. Throughout this lab, series resistors were added to all components connected to pins to mediate the risk of burning out any of the components or pins.

**External Blink Code**
![alt text](/assets/lab1/externalblink.png)


*Part 2: Exploring Analog Output*  
We then connected a potentiometer to the circuit again adding a series resistor. We first read the values from the potentiometer through an analog input pin, and then modified our code to output that value to the LED to control its brightness. 

*Part 3: Learning to Use Servos*  
We then hooked up a PWM signal to control a servo motor. The value read from the potentiometer is written to the servo in order to control the movement of the servo. We used the Arduino Servo library to power the servo and experiment with its speed and rotational direction. We figured out that writing a 0 would give a full speed rotation clockwise, 90 would stop the motor, and writing 180 would give a full speed rotation counter-clockwise. One important note about this part of the lab was that we had to adjust the motor so that at 90 the motor would stop. This was done by using a screw to adjust the motor.

**PWM Code**
![alt text](/assets/lab1/pwmcode.png)

*Part 4: Robot Assembly*  
We then assembled our robot by hooking up 2 motors to the Arduino. We played around a lot with the timing and speed needed for each motor so that they could act together to perform a task. At first we got the robot to just turn right, and then played around with getting it to go in a square. One of our motors kept getting stuck in the turn, so we will have to adjust this. Getting the robot to do more complicated patterns will be easier when the movement is based on a sensor and not based on timing.


**Outcome:**  
By the end of this lab, we had a robot that could generally move in a square shape. We also had a better understanding of both the Arduino IDE and how to connect and control various components with our Arduino Uno.


**Video**  
<a href="https://www.youtube.com/watch?v=bswbdqM8vKw&feature=youtu.be
" target="_blank"><img src="http://img.youtube.com/vi/bswbdqM8vKw/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>
