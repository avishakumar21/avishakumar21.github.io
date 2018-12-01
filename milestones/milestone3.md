# Milestone 3


**Goal:**
The goal of this milestone was to have the robot use a maze algorithm and go through mazes while also updating the GUI.



**GUI:**  
Our base station and transmission scheme for GUI updating remains largely unchanged from prior implementations. We still have a payload scheme set at a maximum of 8 bytes per transmission and our transmission rate at 2 Mbps. We maintain our robot’s orientation relative to north with a global orientation state variable (an enum containing four possible directions). We also maintain our robot’s current coordinates in a global byte, where the first four bits are the xVal and the last four bits are the yVal.    
We have two helper functions to help maintain the state of these variables. The orientation is updated whenever a turn function is called, and simply iterates through the enum itself. Coordinate tracking takes in the value of the orientation and then adds or subtracts from Y or X accordingly.    
These are then transmitted to the GUI base station, which then decodes the bit masks, and sets the appropriate text to be outputted accordingly. The base station code is relatively simple, as it’s simply a receive function (that acknowledges and verifies the data sent by the robot) paired with a decoding helper function (which simply shifts the bits as necessary).     

**Search Algorithm:** 
For our search algorithm, we wrote a modified version of Depth First Search (DFS). Below is a high level overview of the algorithm in partial pseudocode:  
![alt text](/assets/milestone3/highLevelCode.png)   
We have a stack that holds only one node at a time in our modified version, because we did not want to implement the entire traditional DFS algorithm with a neighbors list. Before the above while loop, the start node of the robot is pushed to the stack.   
In the while loop, the node on the stack is popped and this indicates that is the current node the robot is sitting on. The algorithm then checks the surrounding nodes to see if the robot can move to an unvisited node. We check to see if the nodes around it are unvisited by decoding the possible moves the robot could make. We use our 3 bit case variable to figure out which walls are surrounding the robot. The priority is to move straight, then left, then right, which changes depending on where the robot can move.   
We then decode the possible moves by calculating what the node would be if the robot moved forward, left, or right. We search for this possible node in the set of visited nodes (represented by an array in our actual code), and if it is not visited and there are no walls there then the robot will move there. If the robot could not move to an unvisited node, we check to see if it can move to a visited node. If the robot can move to a node in the priorities listed in the pseudo code, then the possible node is pushed to the stack and we have the robot actually move to that node.
We created a couple of helper functions for this integration. We created an in() function and an add() function so we could add things to our visited array and also check to see if a node was in the array to see if it was visited. We also added a decodePossibleSets() function which decodes what the possible nodes surrounding the robot are by using its current orientation.  

*Conclusion:*  
We are still trying to improve our algorithm, but overall were able to get it to explore a smaller scale maze in its entirety.


**Video of Milestone 3**  
<a href="https://www.youtube.com/watch?v=VNQdwcPG0ts
" target="_blank"><img src="http://img.youtube.com/vi/VNQdwcPG0ts/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>



