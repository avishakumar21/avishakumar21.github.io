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
![alt text](/assets/lab3/mux_blank.png)

We only have 6 analog pins, but we need to include 3 line sensors, 3 wall detection sensors, one IR sensor, and one microphone. We decided to move the wall detection sensors onto the mux because we only need to check for walls at a vertex. This means we can explicitly set the enable bit to low (it is active low, which caused us some confusion at first!) when the robot reaches a vertex. The robot can enable each channel and check for a wall, and then continue based on the logic from which walls are around it.  
![alt text](/assets/lab3/our_mux_circuit.png)
 

Below is the code for our updated readDistanceSensors() function. We have a case variable which is a 3-bit binary number. If the leftmost bit is set, then there is a wall to the left. If the middle bit is set, then there is a wall in the middle, and so on for the right wall. This is used in our loop so that we can determine where to turn based on which walls are around us. Originally we checked the threshhold in the loop() function, but by moving the threshhold checking to here and saving it in a global case variable, we are able to better detect the walls around us in a faster way.
  
![alt text](/assets/lab3/dist_sensor_code.png)

Before reading a channel’s output, we must choose the channel. We do this by setting the select bits. The select bits go to three digital pins on the arduino. Below is an example of a chooseChannel() function which sets the select bits to high or low depending on which sensor it should read. This function chooses channel one by setting S0 S1 S2 to 001.  
![alt text](/assets/lab3/chn_code.png)

Below is our code for the switch statement in the loop() which has the logic for which way to turn.  
![alt text](/assets/lab3/case_statement.png)

We then focused on making sure our robot could run two FFTs in order to both start at a 660Hz tone and detect other robots. To accomplish this, we added the audio FFT (from lab 2) the same way we added the IR FFT to our milestone 3 code. We made adjustments to our loop so that our robot would run the audion FFT until the start tone played, and then proceeded to run the previously written integrated program with line following, wall following, and IR detection. This code is shown below: 
![alt text](/assets/lab3/integrated_code.png)

This integration is shown in the video below.


**Radio Code:**
Liam

*Data Scheme*

In an effort to reduce the amount of bits needed to send over to the base station, we encoded the maze data in bytes through the use of bit masking. In total, we need to encode 9 bits of data per square. The first four bits encode the wall presence and locations (North, East, South, West). The next two encode the type of treasure present (No treasure, triangle, circle, square). The bit after that defaults to treasure red, and a value of 1 indicates blue treasure. The final bit in the first byte indicates another robot present. We use a full byte to indicate our robot’s coordinates ( four bits x, four bits y ) since we need to navigate a 9x9 maze. And finally, we have to use one more bit ( and consequently one more whole byte ) to represent a square we’ve navigated to so that way we know we’ve explored it if we return to it.

*Establishing Reliable Communication*

The Arduino’s send each other information through a process, similar to a TCP handshake. The first Arduino sends the information over to the second. The second Arduino receives it and sends the packet back, acting as both an acknowledgement as well as an information check. The first Arduino checks to make sure that the sent packet matches with the acknowledged packet. This allows for reliable communication between the devices. We also send packet identifiers through this process, allowing the packets to be sorted correctly.

*Parsing GUI Information*

We also do packet parsing to output the correct GUI information within the receive() function. For any given byte to be parsed, we run it through a helper function ( either firstByteRead() or coordinateRead() ) and then add it to our output string, which runs Serial.println to pass it on to the GUI.

Initially, we ran the GUI code locally to test that the GUI could receive and process data. However, we’d already established our radio code, so parsing the radio data to display was not much more difficult to establish.

*Implementation*

Our code for the radio is posted below. It is at the moment encased in one file, and roles can be changed between Arduinos. Upon integration, the program will be separated into base-station and robot specific functions, to optimize memory space.

```cpp
/*
  Copyright (C) 2011 J. Coliz <maniacbug@ymail.com>

  This program is free software; you can redistribute it and/or
  modify it under the terms of the GNU General Public License
  version 2 as published by the Free Software Foundation.
*/

/**
  Example for Getting Started with nRF24L01+ radios.

  This is an example of how to use the RF24 class.  Write this sketch to two
  different nodes.  Put one of the nodes into 'transmit' mode by connecting
  with the serial monitor and sending a 'T'.  The ping node sends the current
  time to the pong node, which responds by sending the value back.  The ping
  node can then see how long the whole cycle took.
*/

#include <SPI.h>
#include "nRF24L01.h"
#include "RF24.h"
#include "printf.h"

//
// Hardware configuration
//

/***
   DEFINING BIT MASKS TO USE
   4 bits - WALLS ( N, E, S, W )
   2 bits - TREASURE SHAPE ( NONE, TRIANGLE, CIRCLE, SQUARE )
   1 bits - TREASURE COLOR ( RED, BLUE )
   1 bits - ROBOT IS HERE ( YES, NO, FROM IR )
   1 bits - EXPLORED? ( YES, NO )
***/

#define WALL_NORTH_PRESENT B10000000;
#define WALL_EAST_PRESENT  B01000000;
#define WALL_SOUTH_PRESENT B00100000;
#define WALL_WEST_PRESENT  B00010000;

#define TREASURE_NONE      B00000000;
#define TREASURE_TRIANGLE  B00000100;
#define TREASURE_CIRCLE    B00001000;
#define TREASURE_SQUARE    B00001100;

#define TREASURE_RED       B00000000;
#define TREASURE_BLUE      B00000010;

#define ROBOT_NOT_PRESENT  B11111110;
#define ROBOT_PRESENT      B00000001;

#define EXPLORED           B10000000;

/*** FINISHED DEFINING BIT MASKS **/
// Set up nRF24L01 radio on SPI bus plus pins 9 & 10

RF24 radio(9, 10);

//
// Topology
//

// Radio pipe addresses for the 2 nodes to communicate.
const uint64_t pipes[2] = { 0x000000000ALL, 0x000000000BLL };

//
// Role management
//
// Set up role.  This sketch uses the same software for all the nodes
// in this system.  Doing so greatly simplifies testing.
//

// The various roles supported by this sketch
typedef enum { role_ping_out = 1, role_pong_back } role_e;

// The debug-friendly names of those roles
const char* role_friendly_name[] = { "invalid", "Ping out", "Pong back"};

// The role of the current running sketch
role_e role = role_pong_back;


bool northWall;
bool southWall;
bool eastWall;
bool westWall;
bool treasureCircle;
bool treasureTriangle;
bool treasureSquare;
bool treasureRed;
bool treasureBlue;
bool robotPresent;


String parsedCoordinates;
String parsedFirstByte;
String parsedSecondByte;

int printFlag;

String serialOutput;
String coordinateString;
String firstString;
String secondString;

//CREATE MAZE
/**

      __0____1____2__
   0 | tTR       tSB |
     |    ___________|
   1 |       R       |
     |___________    |
   2 |      tCR      |
     |_______________|


*/

typedef struct {
  bool northWall;
  bool southWall;
  bool eastWall;
  bool westWall;
  bool treasureCircle;
  bool treasureTriangle;
  bool treasureSquare;
  bool treasureRed;
  bool treasureBlue;
  bool robotPresent;
} mazeSquare;

mazeSquare maze[3][3] {
  //{ { true, false, false, true, false, true, false, true }, { true, true, false, false }, { true, true, true, false, false, false, true, false, true } },
  { { true, false, false, true }, { true, true, false, false }, { true, true, true, false } },
  //{ { false, true, false, true }, { true, true, false, false, false, false, false, false, false, true }, { true, false, true, false } },
  { { false, true, false, true }, { true, true, false, false }, { true, false, true, false } },
  //{ { true, true, false, true }, { true, true, false, false, true, false, false, true }, {false, true, true, false} }  
  { { true, true, false, true }, { true, true, false, false }, {false, true, true, false} }  
};


void setup(void)
{
  //
  // Print preamble
  //

  Serial.begin(9600);
  printf_begin();
  //printf("\n\rRF24/examples/GettingStarted/\n\r");
  //printf("ROLE: %s\n\r", role_friendly_name[role]);
  //printf("*** PRESS 'T' to begin transmitting to the other node\n\r");

  //
  // Setup and configure rf radio
  //

  radio.begin();

  // optionally, increase the delay between retries & # of retries
  radio.setRetries(15, 15);
  radio.setAutoAck(true);
  // set the channel
  radio.setChannel(0x50);
  // set the power
  // RF24_PA_MIN=-18dBm, RF24_PA_LOW=-12dBm, RF24_PA_HIGH=-6dBM, and RF24_PA_MAX=0dBm.
  radio.setPALevel(RF24_PA_MAX);
  //RF24_250KBPS for 250kbs, RF24_1MBPS for 1Mbps, or RF24_2MBPS for 2Mbps
  radio.setDataRate(RF24_2MBPS);

  // optionally, reduce the payload size.  seems to
  // improve reliability
  radio.setPayloadSize(8);

  //
  // Open pipes to other nodes for communication
  //

  // This simple sketch opens two pipes for these two nodes to communicate
  // back and forth.
  // Open 'our' pipe for writing
  // Open the 'other' pipe for reading, in position #1 (we can have up to 5 pipes open for reading)

  if ( role == role_ping_out )
  {
    radio.openWritingPipe(pipes[0]);
    radio.openReadingPipe(1, pipes[1]);
  }
  else
  {
    radio.openWritingPipe(pipes[1]);
    radio.openReadingPipe(1, pipes[0]);
  }

  //
  // Start listening
  //

  radio.startListening();

  //
  // Dump the configuration of the rf unit for debugging
  //

  //radio.printDetails();
  Serial.println( "reset" );
}

void loop(void)
{
  while ( role == role_pong_back ) {
    receive();
    roleChange();
  }
  for ( int yVal = 0; yVal < 3; yVal++ ) {
    for ( int xVal = 0; xVal < 3; xVal++ ) {

      assignGlobalMaze( xVal, yVal );
      byte coordinates = xVal << 4 | yVal ;
      byte first = 0x1b;
      byte second = 0x2b;
      byte coords = 0xcc;
      byte firstByte = 0;
      firstByte = checkWalls( firstByte );
      firstByte = checkTreasure( firstByte );
      firstByte = checkIr( firstByte );
      byte secondByte = EXPLORED;

      transmit( first );
      delay( 250 );
      transmit( firstByte );
      delay( 250 );
      transmit( second );
      delay( 250 );
      transmit( secondByte);
      delay( 250 );
      transmit( coords );
      delay( 250 );
      transmit( coordinates );
      delay( 2000 );
    }
  }
  byte ended = 0xFF;
  transmit( ended );
  delay( 250 );
}


void assignGlobalMaze ( int xVal, int yVal ) {
  northWall = maze[ yVal ][ xVal ].northWall;
  southWall = maze[ yVal ][ xVal ].southWall;
  eastWall = maze[ yVal ][ xVal ].eastWall;
  westWall = maze[ yVal ][ xVal ].westWall;
  treasureCircle  = maze[ yVal ][ xVal ].treasureCircle;
  treasureTriangle = maze[ yVal ][ xVal ].treasureTriangle;
  treasureSquare = maze[ yVal ][ xVal ].treasureSquare;
  treasureRed = maze[ yVal ][ xVal ].treasureRed;
  treasureBlue = maze[ yVal ][ xVal ].treasureBlue;
  robotPresent = maze[ yVal ][ xVal ].robotPresent;
}


// vim:cin:ai:sts=2 sw=2 ft=cpp


void roleChange( void ) {
  //
  // Change roles
  //

  if ( Serial.available() )
  {
    char c = toupper(Serial.read());
    if ( c == 'T' && role == role_pong_back )
    {
      printf("*** CHANGING TO TRANSMIT ROLE -- PRESS 'R' TO SWITCH BACK\n\r");

      // Become the primary transmitter (ping out)
      role = role_ping_out;
      radio.openWritingPipe(pipes[0]);
      radio.openReadingPipe(1, pipes[1]);
    }
    else if ( c == 'R' && role == role_ping_out )
    {
      printf("*** CHANGING TO RECEIVE ROLE -- PRESS 'T' TO SWITCH BACK\n\r");

      // Become the primary receiver (pong back)
      role = role_pong_back;
      radio.openWritingPipe(pipes[1]);
      radio.openReadingPipe(1, pipes[0]);
    }
  }
}


byte checkWalls( byte firstByte ) {
  if ( northWall ) firstByte |= WALL_NORTH_PRESENT;
  if ( southWall ) firstByte |= WALL_SOUTH_PRESENT;
  if ( eastWall ) firstByte |= WALL_EAST_PRESENT;
  if ( westWall ) firstByte |= WALL_WEST_PRESENT;
  return firstByte;
}


byte checkTreasure( byte firstByte ) {
  if ( treasureTriangle ) firstByte |= TREASURE_TRIANGLE;
  if ( treasureCircle ) firstByte |= TREASURE_CIRCLE;
  if ( treasureSquare ) firstByte |= TREASURE_SQUARE;
  if ( treasureRed ) firstByte |= TREASURE_RED;
  if ( treasureBlue ) firstByte |= TREASURE_BLUE;
  return firstByte;
}


byte checkIr( byte firstByte ) {
  if ( robotPresent ) {
    firstByte |= ROBOT_PRESENT;
    }
  else if ( !robotPresent ) firstByte &= ROBOT_NOT_PRESENT;
  return firstByte;
}


void transmit( byte payload ) {
  // Ping out role.  Repeatedly send the current time
  //

  if ( role == role_ping_out )
  {
    // First, stop listening so we can talk.
    radio.stopListening();

    printf( "Now sending %x...", payload );
    bool ok = radio.write( &payload, sizeof(unsigned char) );
    if ( ok )
      printf( "ok..." );
    else
      printf( "failed.\n\r" );

    // Now, continue listening
    radio.startListening();

    // Wait here until we get a response, or timeout (250ms)
    unsigned long started_waiting_at = millis();
    bool timeout = false;
    while ( ! radio.available() && ! timeout )
      if (millis() - started_waiting_at > 1000 )
        timeout = true;

    // Describe the results
    if ( timeout )
    {
      printf("Failed, response timed out.\n\r");
    }
    else
    {
      // Grab the response, compare, and send to debugging spew
      unsigned char rxPayload;
      radio.read( &rxPayload, sizeof(unsigned char) );
      if ( rxPayload == payload ) {
        // Spew it
        printf("Received correct data %x, RT delay: %lu\n\r", rxPayload, millis() - started_waiting_at);
      }
      else {
        printf("That's not what I sent, received %x, RT delay: %lu\n\r", rxPayload, millis() - started_waiting_at);
      }
    }

    // Try again 1s later
    delay(1000);
  }
}

void receive( void ) {
  //
  // Pong back role.  Receive each packet, dump it out, and send it back
  //
  if ( role == role_pong_back )
  {
    // if there is data ready
    if ( radio.available() )
    {
      // Dump the payloads until we've gotten everything
      unsigned char rxPayload;
      bool done = false;
      while (!done)
      {
        // Fetch the payload, and see if this was the last one.
        done = radio.read( &rxPayload, sizeof(unsigned char) );

        // Spew it
        //printf("Got payload %x...", rxPayload);
        if ( rxPayload == 0xFF ) {
          printf( "reset \r\n" );
        }
        else if ( rxPayload == 0x1b ) {
          //printf( "\r\nThis is the first byte " );
          printFlag = 1;
        }
        else if ( rxPayload == 0x2b ) {
          //printf( "This is the second byte " );
          printFlag = 2;
        }
        else if ( rxPayload == 0xcc ) {
          //printf( "These are the coordinates " );
          printFlag = 3;
        }
        else {
          //printf("%x \n\r", rxPayload);
          switch ( printFlag ) {
            case 3:
              coordinateString = readCoordinates( rxPayload );
              Serial.println( coordinateString + firstString );
              break;
            case 2:
              //Serial.println( "2" );
              break;
            case 1:
              firstString = readFirstByte( rxPayload );
              break;
            default:
              break;


          }
        }
        // Delay just a little bit to let the other unit
        // make the transition to receiver
        delay(20);

      }

      // First, stop listening so we can talk
      radio.stopListening();

      // Send the final one back.
      radio.write( &rxPayload, sizeof(unsigned char) );


      // Now, resume listening so we catch the next packets.
      radio.startListening();
    }
  }
}

String readCoordinates( byte coordinates ) {
  String output = "";
  String xVal = String(coordinates >> 4);
  String yVal = String(coordinates & 0x0F);

  output = yVal + "," + xVal;
  return output;
}


String readFirstByte( byte firstByte ) {
  String output = "";
  if ( firstByte >> 7 ) output += ",north=true";
  if ( ( firstByte & B01000000 ) >> 6 ) output += ",east=true";
  if ( ( firstByte & B00100000 ) >> 5 ) output += ",south=true";
  if ( ( firstByte & B00010000 ) >> 4 ) output += ",west=true";
  switch ( ( firstByte & B00001100 ) >> 2 ) {
    case 1:
      //triangle
      output += ",tshape=triangle,tcolor=red";
      break;
    case 2:
      //circle
      output += ",tshape=circle,tcolor=red";
      break;
    case 3:
      //square
      output += ",tshape=square,tcolor=red";
      break;
    default:
      break;
  }
  if ( ( firstByte & B00000010 ) >> 1 ) output += ",tcolor=blue";
  if ( ( firstByte & B00000001 ) ) output += ",robot=true";
  return output;
}

```


**Video**   
<a href="https://www.youtube.com/watch?v=5FdXc0a85oY
" target="_blank"><img src="http://img.youtube.com/vi/5FdXc0a85oY/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

