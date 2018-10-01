---
title: ""
image: /assets/LeekColor.png
---

# Lab 2

**Goal:**



**Materials:**
The main portion of this lab required the following materials for each subteam:
* 

**Acoustic Team:**  
Liam & Katarina

*Unit Testing:*  
Our first task was to test the arduino to make sure that the serial monitor’s output matches the frequency output from the signal generator. We took the sample code from the FFT library for the serial_adc and ran it with the signal generator outputting 660 Hz. At first, we were having a lot of issues because we could not see anything on the serial output. After a lot of issues with configuration and trying to change the sample code to use the analogread function instead, we discovered with the help of a TA that our version of the Arduino IDE was too new for the FFT library. We had to use an IDE version 1.8.5 or lower in order to run the code. Once we did this, we were able to correctly view the serial output.

*Finding Bins:*  
Once we got a correct output, we took data from multiple readings and put them into an Excel sheet then plotted the data in order to determine the different frequency bins. Because our signal generator produced a 660 Hz signal, we knew that the highest number in one cycle of our data would represent the bin that holds the frequency range that 660 Hz falls into. This is the first graph we got, when we read multiple cycles:
![alt text](/assets/lab2/mult_cycle_readings.png)
We can observe the different peaks for each cycle, noticing that the peaks tend to stay toward the beginning of each cycle. We determined that the first two bins always had the highest numbers, and can discard this data as noise. Beyond this, for 660 Hz we found that the fifth bin always held the highest number. This can be more easily observed in a graph of one cycle:
![alt text](/assets/lab2/one_cycle_reading.png)
[alt text](/assets/lab2/excel_bins.png)
As we can see above, the fifth bin holds the highest value, and this pattern continued over multiple cycles. 
Looking at team Alpha’s calculation, we can determine how many frequencies are represented by one bin:
ADC sampling frequency = 38kHz = 16MHz/ 32 prescalar / 13 clock cycles
We know one cycle takes 256 samples, so by taking this sampling frequency divided by the number of samples, we can get the number of frequencies per bin:
38kHz / 256 =148.4 Hz pr bin.
Now we can add the bin values to the excel sheet to determine the range of the fifth bin:


 






**Video**  
