---
title: "lab 4"
image: /assets/LeekColor.png
---

# Lab 4

**Goal:**  



**FPGA Group:**  
Emma & Avisha

*Implementing a Multiplexer:*  

 




**Arduino Group:**
Katarina & Liam

For the Arduino, it took a long time to figure out which registers to set to enable the camera to properly function. Below is a screenshot of the registers we ended up setting. 
![alt text](/assets/lab4/write_regs.png)  
First we set a bit in COM 7 (address 0x12) to reset all of the other registers, and then we delay to make sure that this function can be carried out. Then we set more bits in COM 7 to enable the color bar, choose QCIF, and output in RGB form. Setting this to 0x0C will disable the color bar for the color test but set the other correct bits. Then we set a bit in CLKRC to use the external clock. Then we set a bit in COM 3 (address 0x0C) to enable scaling. Then we set bits in COM 15 (address 0x40) to choose RGB 565 format. Then we set a bit in COM 17 (address 0x42) to enable the color bar test. Explicitly setting this to 0x00 will turn off the color bar test. Finally, we set bits in COM 9 (address 0x14).

After this, we hooked up the Arduino to a circuit that connects the SCL and SDA lines to the camera. We also set up the camera and plugged it into the FPGA. We wrote a small program which can read two digital inputs as high or low. These inputs come from the FPGA, which writes two GPIO pins to be high or low depending on which color it has detected.
![alt text](/assets/lab4/color_receiver.png)  
A 00 indicates neither blue nor red has been detected, a 01 indicates red has been detected, and 10 indicates blue has been detected. Currently we have enough digital pins in order for this to work, but when we also need to detect shapes we will need 3 bits to encode with. For this, we will use the 8:1 mux we already have on board, to conserve pins. 



**Video**  
Note about video: The "camera detecting blue" clip is edited to slow down the printout of the serial monitor because when we took the video we accidentally taped this too quickly. The camera can detect blue though! 
<a href="https://www.youtube.com/watch?v=-ELxgAqNMMA
" target="_blank"><img src="http://img.youtube.com/vi/-ELxgAqNMMA/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

