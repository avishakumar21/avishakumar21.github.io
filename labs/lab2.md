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
Once we got a correct output, we took data from multiple readings and put them into an Excel sheet then plotted the data in order to determine the different frequency bins. Because our signal generator produced a 660 Hz signal, we knew that the highest number in one cycle of our data would represent the bin that holds the frequency range that 660 Hz falls into.

We determined that the first two bins always had the highest numbers, and can discard this data as noise. Beyond this, for 660 Hz we found that the fifth bin always held the highest number. This can be more easily observed in a graph of one cycle:
![alt text](/assets/lab2/660Hz_reading.png)
![alt text](/assets/lab2/excel_bins.png)
As we can see above, the fifth bin holds the highest value, and this pattern continued over multiple cycles. 
Looking at team Alpha’s calculation, we can determine how many frequencies are represented by one bin:  
ADC sampling frequency = 38kHz = 16MHz/ 32 prescalar / 13 clock cycles  
We know one cycle takes 256 samples, so by taking this sampling frequency divided by the number of samples, we can get the number of frequencies per bin:  
38kHz / 256 =148.4 Hz per bin  
Now we can add the bin values to the excel sheet to determine the range of the fifth bin:
![alt text](/assets/lab2/close_up.png)
Now we know that when we hook up our circuit, we can look at the fifth bin and see if it is above a certain threshold (i.e. is the maximum over the samples) to determine if we are indeed hearing a 660 Hz start sound.

This is what the FFT looks like for noise, for comparison:
![alt text](/assets/lab2/noise_fft.png)

The image below shows the code used to demonstrate that our robot could detect the audio signal and respond by lighting up an LED. A video of this response is shown in the video found at the bottom of this page.  
![alt text](/assets/lab2/mic_code.png)

*Circuit:* 

 ![alt text](/assets/lab2/signal_gen_mic.png)
 The oscilloscope view above depicts our function generator outputting a 660 Hz signal (CH2) and our amplifier amplifying the signal input (CH1). In this case the amplifier was simply hooked up to the function generator and not to the microphone. This allowed us to troubleshoot and proof each stage of the circuit independently. It also proved that our amplifier has a gain of 10, which we later modified to ~100 after signal testing with the microphone. 


![alt text](/assets/lab2/actual_mic_gain.png)
This figure depicts the entirety of the complete circuit. The microphone input (CH2) is hearing a tone generated at 660Hz. It then passes through a DC filter, and finally through an amplifier to generate the resulting signal (CH1). 


![alt text](/assets/lab2/mic_osc_fft.png)
Above is an oscilloscope capture for 660 Hz.

![alt text](/assets/lab2/noise_osc_fft.png)
Above is an oscilloscope capture for noise.

The figure below  illustrates our completed circuit. We have a DC blocking component between the microphone and the amplifier, and then a final resistor to Vout as its connected directly to an Arduino pin. The theoretical gain is 390k/3.3k which is approximately 118. This circuit was modified slightly from Team Alpha’s FA17 circuit.
![alt text](/assets/lab2/mic_circuit_diagram.png)


**Video**  
