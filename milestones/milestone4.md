# Milestone 4


**Goal:**
The goal of this milestone was to implement shape detection for our robot.

**Procedure:**  

*Shape Detection* 

We began with the image processing code from lab 4, which could successfully differentiate red, blue, and no color. To modify this code to also detect shapes, we first had to consider which pixels on the screen would contain the shape. We decided to divide the screen into 9 sections, and to assume that the shape would be approximately contained in the middle section. We then sampled 5 lines from this approximate middle section, and saved the number of red/blue pixels in each line as separate variables. This line-specific color sampling occurs whenever VSYNC is low, indicating that the camera has not yet reached the end of a frame, and it remains separate from the lab 4 code that counts the overall number of red and blue pixels in the frame. The color sampling code for the middle of lines 50, 60, and 72 are shown below, and the code repeats for lines 80 and 90.

![alt text](/assets/milestone4/lineSamplingCode.png)

When VSYNC is high, indicating the end of a frame, we determine the color and shape of the image. Generally, if the color is determined to be red using the code from the previous lab, and if the lines sampled have a progressively increasing number of red pixels, the shape is determined to be a red triangle. If the color is red and the lines have an increasing number of red pixels before the middle line and a decreasing number after the middle line, the shape is determined to be a red diamond. If the color is red, but the shape is not a triangle or diamond, the shape is determined to be a red square. The shape detection code shown below is only for red, but the procedure is identical for blue. As in the lab 4 implementation, if the color is neither red nor blue, we say there is no treasure.

![alt text](/assets/milestone4/redShapeDetection.png)

*Robot Integration* 

In the above code, we save the shape and color information to the output variable RESULT, and map its bits to GPIO pins. These GPIO pins are wired to our arduino, which decodes the shape and color information and saves the result. Because the camera sometimes produces a noisy result, we make sure our output stays consistent for 10 loop cycles before saving the shape and color.

![alt text](/assets/milestone4/blueArduinoCode.png)

The above image shows the Arduino averaging code for blue shape detection; the code is identical for red and written directly before the blue code. In this code, we tally our result. When we pass a threshold of ten consistent shape detection outputs in a row, we both print this result and save it as a variable.

A video of our working shape detection is shown below.
 

**Video of Milestone 4**  
<a href="https://www.youtube.com/watch?v=jXZi5JDcKL0
" target="_blank"><img src="http://img.youtube.com/vi/jXZi5JDcKL0/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>



