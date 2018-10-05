---
title: ""
image: /assets/LeekColor.png
---

# Lab 2

**Goal:**  
The goal of this lab was to develop an audio circuit and an optical circuit to interface with our Arduino. The audio circuit allows our robot to detect a 660Hz starting signal and the optical circuit allows our robot to detect and avoid other robots emitting an IR frequency of 6kHz and ignore decoys.



**Materials:**
The main portion of this lab required the following materials for each subteam:

Acoustic Team:
* Arduino Uno
* Electret microphone
* 1 µF capacitor
* 300 Ω resistors
* 3 kΩ resistor
* LF353 Op Amp

IR Team:
* OP598 IR transistor (Phototransistor)
* 1 IR hat
* 1 IR decoy
* LF353 Op Amp

Both Teams used the following equipment:
* Oscilloscope
* Function Generator
* DC Voltage Supply
* Wires


**Acoustic Team:**  
Liam & Katarina

*Unit Testing:*  
Our first task was to test the arduino to make sure that the serial monitor’s output matches the frequency output from the signal generator. We took the sample code from the FFT library for the serial_adc and ran it with the signal generator outputting 660 Hz. At first, we were having a lot of issues because we could not see anything on the serial output. After a lot of issues with configuration and trying to change the sample code to use the analogread function instead, we discovered with the help of a TA that our version of the Arduino IDE was too new for the FFT library. We had to use an IDE version 1.8.5 or lower in order to run the code. Once we did this, we were able to correctly view the serial output.

*Finding Bins:*  
Once we got a correct output, we took data from multiple readings and put them into an Excel sheet then plotted the data in order to determine the different frequency bins. Because our signal generator produced a 660 Hz signal, we knew that the highest number in one cycle of our data would represent the bin that holds the frequency range that 660 Hz falls into.

We determined that the first two bins always had the highest numbers, and can discard this data as noise. Beyond this, for 660 Hz we found that the fifth bin always held the highest number. This can be more easily observed in a graph of one cycle:
<br>
<img src="/assets/lab2/660Hz_reading.png" alt="drawing" height="300px"/>
<br>
<img src="/assets/lab2/excel_bins.png" alt="drawing" height="400px"/>
<br>
As we can see above, the fifth bin holds the highest value, and this pattern continued over multiple cycles.
Looking at team Alpha’s calculation, we can determine how many frequencies are represented by one bin:  
ADC sampling frequency = 38kHz = 16MHz/ 32 prescalar / 13 clock cycles  
We know one cycle takes 256 samples, so by taking this sampling frequency divided by the number of samples, we can get the number of frequencies per bin:  
38kHz / 256 =148.4 Hz per bin  
Now we can add the bin values to the excel sheet to determine the range of the fifth bin:
<img src="/assets/lab2/close_up.png" alt="drawing" height="200px"/>
<br>
Now we know that when we hook up our circuit, we can look at the fifth bin and see if it is above a certain threshold (i.e. is the maximum over the samples) to determine if we are indeed hearing a 660 Hz start sound.

This is what the FFT looks like for noise, for comparison:
<br>
<img src="/assets/lab2/noise_fft.png" alt="drawing" height="300px"/>
<br>

The image below shows the code used to demonstrate that our robot could detect the audio signal and respond by lighting up an LED. A video of this response is shown in the video found at the bottom of this page.  
<img src="/assets/lab2/mic_code.png" alt="drawing" height="200px"/>
<br>

*Circuit:*

<img src="/assets/lab2/signal_gen_mic.png" alt="drawing" height="300px"/>
<br>

 The oscilloscope view above depicts our function generator outputting a 660 Hz signal (CH2) and our amplifier amplifying the signal input (CH1). In this case the amplifier was simply hooked up to the function generator and not to the microphone. This allowed us to troubleshoot and proof each stage of the circuit independently. It also proved that our amplifier has a gain of 10, which we later modified to ~100 after signal testing with the microphone.

 <img src="/assets/lab2/actual_mic_gain.png" alt="drawing" height="300px"/>
 <br>

This figure depicts the entirety of the complete circuit. The microphone input (CH2) is hearing a tone generated at 660Hz. It then passes through a DC filter, and finally through an amplifier to generate the resulting signal (CH1).

<img src="/assets/lab2/mic_osc_fft.png" alt="drawing" height="300px"/>
<br>

Above is an oscilloscope capture for 660 Hz.

<img src="/assets/lab2/noise_osc_fft.png" alt="drawing" height="300px"/>
<br>
Above is an oscilloscope capture for noise.

The figure below  illustrates our completed circuit. We have a DC blocking component between the microphone and the amplifier, and then a final resistor to Vout as its connected directly to an Arduino pin. The theoretical gain is 390k/3.3k which is approximately 118. This circuit was modified slightly from Team Alpha’s FA17 circuit.
<br>
<img src="/assets/lab2/mic_circuit_diagram.png" alt="drawing" height="400px"/>
<br>

**Optical Team**  
Avisha & Emma

*Procedure:*
We began testing the serial monitor output using the FFT library. We followed the same procedure as outlined by the acoustic group, changing the baud rate to obtain numbers for our output. We then set up the following simple circuit with our phototransistor. A phototransistor allows more current to pass as it receives more light. Below is an image of a circuit using a phototransistor, which we implemented:

<img src="/assets/lab2/phototransistor.png" alt="drawing" height="200px"/>
<br>


By directly hooking up an oscilloscope, we observed that, when held up to our IR hat, our circuit could detect a frequency of 6kHz. We removed the oscilloscope probe and attached the output of our circuit to the Arduino through pin A0. When we ran the serial_adc script and output the data to Excel to be graphed, we could not see any peaks in the frequency data. Because our bins were large enough to capture 6kHz, we concluded that we needed an amplifier to increase the strength of our signal. We constructed the same circuit used by the acoustic team above, replacing the 390kΩ resistor with a 39MΩ resistor to create a gain of 10,000. Since the circuit has such high gain, we are able to effectively capture the IR hat frequency of 6kHz even at a range of ~1ft. However, through empirical testing, we found that the IR hat has a limited field of view in terms of emitting IR light. For this reason, we found that our IR detection is most accurate and receives the largest signal when directly head on with an IR LED. We anticipate using some sort of detection filter and/or adding a second IR receiver to enable high accuracy even when not seen head on, as the competition environment would necessitate this.

The modified IR amplifier is shown below:

<img src="/assets/lab2/ir_circuit_diagram.png" alt="drawing" height="400px"/>
<br>

A plot of a 6kHz signal in the frequency domain as output from our circuit is shown below:

<img src="/assets/lab2/3400Lab2IRGraph.png" alt="drawing" height="300px"/>
<br>


The 6kHz signal produces a spike in bin 42, as calculated using the values determined by the acoustic group. We could also detect the 18kHz decoy signal, which produces a spike in bin 120, as shown in the oscilloscope reading and the plot below.

<img src="/assets/lab2/Screen Shot 2018-10-05 at 11.30.27 AM.png" alt="drawing" height="300px"/>
<br>

 We decided to demonstrate that our robot could recognize a 6kHz signal and ignore the 18kHz decoy signal by lighting up LEDs for each case. To accomplish this, we implemented the following code ran after our baseline FFT code:

 <img src="/assets/lab2/3400Lab2DecoyGraph.png" alt="drawing" height="300px"/>
 <br>


 Below is a video of our robot detecting the 6kHz and 18kHz frequencies.


*Explanation of FFT:*
A Fast Fourier Transform converts a time domain signal into a frequency domain signal. Every signal in the time domain can be constructed from sine and cosine waves at different frequencies with different amplitudes and phases. Plotting a Fourier Transform in the frequency domain produces a graphs with peaks at the signal’s component frequencies. This is useful in that frequency domain graphs allow us to pinpoint signals from specific sources (i.e. an IR hat at 6kHz).


**Video**
This is a video that demonstrates the use of the microphone and the IR transistor.
<a href="https://www.youtube.com/watch?v=tcCEQtSohfE&feature=youtu.be
" target="_blank"><img src="http://img.youtube.com/vi/tcCEQtSohfE/0.jpg"
alt="IMAGE ALT TEXT HERE" width="480" height="320" border="10" /></a>
