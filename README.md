## Arduino Theremin using ToF Sensors  

This project operates as an instrument making use of distance sensors to control volume and pitch in real time. It also includes 10 second recording capabilities and 
playback functionality. Developed as part of a freshman engineering course (EPICS) at UTSA with a partner. I was responsible for coding and implementing real time audio and recording/playback while my teammate was in charge of the physical shell and master volume knob. 


 ## How it works  
Distance sensors measure hand position and record distance which is mapped to pitch and volume values. These values are processed in the Mozzi audio library in order to
achieve continuous audio during playback of recordings and dynamic volume. This system allows seamless changes to sound allowing actual music to be played on the 
device. Added functionality is a master volume to assist with volume control depending on surroundings,10 second long recordings for playback, and screen to allow for 
better use experience.


## Features
-Dynamic pitch and volume control using ToF sensors  
-10-Second recording and playback functionality  
-LCD display for user feedback  
-Physical control using tactile buttons and volume knob  


## Hardware
-Arduino Uno  
-VL530X ToF Sensor (2)   
-Single Cavity mini Speaker (2)  
-HiLetgo Amplifier Board (1)   
-I2C LCD Display (1)  
-Tactile buttons (2)  
-Breadboard (2)  
-Potentiometer (1)  


## Tools
-Arduino IDE  
-Mozzi Library (and all dependencies)  
-Vl53L0X Library  
-Wire library  
-Liquic Crystal library  

## Contributions  
-Implemented real-time audio using Mozzi  
-Integrated ToF sensors for volume and pitch control  
-Developed recording and playback capabilities  
-Mapped sensor input to audio output for wide range  
-Debugged timing and signal processing issues  
 

## Future Improvements  
-External storage to expand recording duration   
-Include more information on LCD display  
-Create cleaner pitch  


## Wire Diagram  

![Wiring Diagram](Wire%20Diagram.png)
