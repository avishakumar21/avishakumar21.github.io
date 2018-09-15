---
title: ""
image: /assets/LeekColor.png
---

# Milestone 1


**Goal:**
The goal of this lab was to add functionality to enable our robot to follow a line and follow a grid in a figure eight.  

**Materials**
The materials used for this milestone are as follows:
* Pre-assembled robot
* Three line sensors
* Electrical and Masking tape

**Procedure:**


*Part 1: Following A Line*
We began by taping two line sensors to the front of our robot, as close to the ground as possible. We measured the difference between the white and black surfaces using these sensors, and programmed our robot to follow the white line. Our preliminary implementation allowed the robot to generally follow a line, but it easily strayed off course and sometimes missed turns. 
To try to solve this issue, we first added a third sensor, so that the middle sensor would continuously sit on the line, one sensor would sit to the left of the line, and the last sensor would sit to the right of the line. We then removed the tape securing the sensors and mounted the sensors to the front of the robot, still as close to the ground as possible, to ensure consistent readings. We hoped these two modifications would help the robot both stay on the line and consistently turn. This still gave us issues, because it was hard to keep the robot completely straight, and there was not much leniency with the outside sensors staying off of the line.
We readjusted our software to define going straight as having the middle sensor on the white line and the leftmost and rightmost sensors on the black. This allowed us to determine better correction logic because it is easier to detect that the sensors are in a black area because there is more black space. 
We implemented the following state machine:

![alt text](/assets/milestone1/statemachine.png)

In the above state machine, ‘L’ stands for left servo, ‘R’ stands for right servo, and ‘M’ stands for middle servo. Additionally, ‘W’ stands for white and ‘B’ stands for black.
We used the following code to implement the above state machine:

![alt text](/assets/milestone1/statemachinecode.png)

*Part 2: Figure Eight*
To implement the figure eight, we first had to make our robot turn when all three sensors detected white. The turning function first caused the robot to turn some small amount so that its middle sensor was no longer aligned with the line. It then continued to turn until the middle sensor again sensed that it was above the white line.

![alt text](/assets/milestone1/whitelinecode.png)

Once our robot could turn, we coded a loop for it to perform four right turns, followed by four left turns in a row to implement the figure eight.

![alt text](/assets/milestone1/figureeightcode.jpg)

Below is a video of our robot completing a figure eight:

**Video of Figure Eight**  
<a href="https://www.youtube.com/watch?v=l3SK6mzpvlA
" target="_blank"><img src="http://img.youtube.com/vi/l3SK6mzpvlA/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

**Outcome:**
We completed this milestone by adding line following and turning functionality to our robot and programming it to follow a figure eight. This milestone helped us to practice using sensors and and introduced us to the use of state machines to model our robot’s behavior.













