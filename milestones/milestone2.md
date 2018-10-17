# Milestone 1


**Goal:**
The goal of this milestone was to enable our robot to follow walls and to use our hardware and software from lab 2 to make our robot stop when it detects other robots.

**Materials**
The materials used for this milestone are as follows:
* 

**Procedure:**  


*Part 1: Wall Following*  
We first added three distance sensors to the front, left, and right of our robot. Once they were attached, we printed the output of the sensors and determined that values above 200 indicated the presence of a wall. We hooked up 3 LEDs to demonstrate that the robot could detect walls on all three sides. This was implemented with the following code and is shown in the video below:  

![alt text](/assets/milestone2/LED_code.png)

We then modified our original line following code so that when the robot detected a junction, it paused to detect walls around it rather than automatically turning right. We designed the following state machine to allow the robot to decide which direction to turn based on the walls it detected:  

![alt text](/assets/milestone2/wall_detection_fsm.png)

In the above state machine, V stands for vertex, L stands for left distance sensor, M stands for middle distance sensor, and R stands for right distance sensor. If the robot detects a vertex while line following, V is set to true (T). If any sensor value is above our threshold of 170 (modified from the value of 200 above to more accurately account for maze distances), it is set to true, or else it is false (F). After turning in either direction, we hard code V to be set to false. For this milestone, we do not deal with the case of having all sensor values set to true. In this case, we would need to go backwards and then turn in the opposite direction of our most recent turn. We plan to implement this when we begin to store information about the maze in lab 3.  
  
This state machine is implemented with the following code:  

![alt text](/assets/milestone2/fsm_code.png)

This implementation worked relatively well. However, the robot would occasionally miss a turn or turn when it was not supposed to. We therefore decided to add an averaging function to our code for the wall detection sensors. When the robot reaches a junction, it averages the most current five sensor values for each sensor. The robot decides to turn based on the average value, rather than one measurement. The averaging code is found below:  

![alt text](/assets/milestone2/read_dist_sensors_code.png)
To avoid other robots, we used the IR sensor that we set up in lab 2. The difficulty came from combining the servos while simultaneously running an FFT for the IR sensor. To demonstrate that the robot can successfully avoid other robots, we showed that it can stop when it detects a robot nearby.  


*Part 2: Avoiding Other Robots*  
To avoid other robots, we used the IR sensor that we set up in lab 2. The difficulty came from combining the servos while simultaneously running an FFT for the IR sensor. To demonstrate that the robot can successfully avoid other robots, we showed that it can stop when it detects a robot nearby.  
In our loop function, we have the robot go straight and then call the detectIR() function which will return true if another robot is nearby. If it returns true, the robot will stop. Within the stop function, there is a 5 second delay after the servos are set to 90, so that the servo will stop for 5 seconds and then continue going straight. In lab 3, we will be able to add logic for what the robot should do if it detects a robot (delay until the robot passes, go anyway, etc.), but for now we just want to demonstrate that the robot can avoid another robot by stopping or doing some action. Below is the code for the loop:    

![alt text](/assets/milestone2/IR_loop_code.png)

Below is the code for the detectIR() function, and the explanation of the code will follow.
![alt text](/assets/milestone2/Idetect_IR_part1.png)
![alt text](/assets/milestone2/Idetect_IR_part2.png)

In Section 1 of the code, we save the values of the registers that the FFT modifies in temporary variables. In Section 3 we set the registers back to their temporary values before the FFT modified them. This is so that when we get back to our program loop, the servos are able to use the original values before we went through the FFT. Otherwise, the FFT works as normal. In section 2 we take an average over the FFTs, so really the 42nd bin for 6kHz is only read every 5 iterations through the program’s loop() function. We found that this works fast enough and is more accurate. When the counter has gotten to 5, we check the average for the 42nd bin to see if it is above our set threshold. If it is we return true, triggering the servos to stop for 5 seconds.  

We can also see that on line 98 the cli() function is commented out, which was not true for lab 2. This is because we do not want to disable interrupts for this function. When this was not commented out, the robot just turned in circles arbitrarily. By commenting this out along with saving the temporary register values, we were able to get the IR to work with the servos.

This is a picture of our robot after this lab. The FPGA and extra breadboard for a mux will be able to go on top.
![alt text](/assets/milestone2/robot_view1.png)
The IR hat is under the top blue piece.
![alt text](/assets/milestone2/robot_view2.png)

**Video of Figure Eight**  
<a href="https://www.youtube.com/watch?v=l3SK6mzpvlA
" target="_blank"><img src="http://img.youtube.com/vi/l3SK6mzpvlA/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>



