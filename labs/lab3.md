---
title: "lab 3"
image: /assets/LeekColor.png
---

# Lab 3

**Goal:**  
The goal of this lab was to integrate everything we previously created into the robot, along with adding a radio to communicate to a GUI, so that the robot can explore a maze successfully.


**Robot Group:**  
Emma & Katarina

*Implementing a Multiplexer:*  
For integrating everything together, our first concern was running out of analog pins. We knew that we wanted the FFTs for the IR and microphone to work on analog rather than digital, so we needed to extend how many pins we had. Rather than use a Schmitt trigger, we decided to use a mux. Below is a schematic of the 8:1 mux that we used.
<img src="/assets/lab3/mux_blank" alt="drawing" height="200px"/>  

We only have 6 analog pins, but we need to include 3 line sensors, 3 wall detection sensors, one IR sensor, and one microphone. We decided to move the wall detection sensors onto the mux because we only need to check for walls at a vertex. This means we can explicitly set the enable bit to low (it is active low, which caused us some confusion at first!) when the robot reaches a vertex. The robot can enable each channel and check for a wall, and then continue based on the logic from which walls are around it.  
<img src="/assets/lab3/our_mux_circuit" alt="drawing" height="200px"/>  

Below is the code for our updated readDistanceSensors() function.  
<img src="/assets/lab3/dist_sensor_code" alt="drawing" height="200px"/>  

Before reading a channel’s output, we must choose the channel. We do this by setting the select bits. The select bits go to three digital pins on the arduino. Below is an example of a chooseChannel() function which sets the select bits to high or low depending on which sensor it should read. This function chooses channel one by setting S0 S1 S2 to 001.  
<img src="/assets/lab3/chn_code" alt="drawing" height="200px"/>  



**Video**
Coming soon!
