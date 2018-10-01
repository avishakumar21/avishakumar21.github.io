---
title: ""
image: /assets/LeekColor.png
---

# Lab 2

**Goal:**



**Materials:**
The main portion of this lab required the following materials for each subteam:
* 
*

**Acoustic Team:**  
Liam & Katarina

*Unit Testing:*  
Our first task was to test the arduino to make sure that the serial monitor’s output matches the frequency output from the signal generator. We took the sample code from the FFT library for the serial_adc and ran it with the signal generator outputting 660 Hz. At first, we were having a lot of issues because we could not see anything on the serial output. After a lot of issues with configuration and trying to change the sample code to use the analogread function instead, we discovered with the help of a TA that our version of the Arduino IDE was too new for the FFT library. We had to use an IDE version 1.8.5 or lower in order to run the code. Once we did this, we were able to correctly view the serial output.

*Finding Bins::*  
Once we got a correct output, we took data from multiple readings and put them into an Excel sheet then plotted the data in order to determine the different frequency bins. Because our signal generator produced a 660 Hz signal, we knew that the highest number in one cycle of our data would represent the bin that holds the frequency range that 660 Hz falls into. This is the first graph we got, when we read multiple cycles:
![alt text](/assets/lab2/mult_cycle_readings.png)

 






**Video**  
<a href="https://www.youtube.com/watch?v=bswbdqM8vKw&feature=youtu.be
" target="_blank"><img src="http://img.youtube.com/vi/bswbdqM8vKw/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>
