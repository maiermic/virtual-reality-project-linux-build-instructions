# Virtual Reality Project - Linux Build Instructions

I will explain how to

 - build Boost, OpenSG2, VRPN and inVRs in Linux
 - configure, build and run the initial student project (of lecture [Virtual Reality][vr-lecture] at Ludwig Maximilians University Munich).

[vr-lecture]: http://www.nm.ifi.lmu.de/teaching/Vorlesungen/2016ss/vr/


## Project

The initial `cmake` configuration of the student project only allows to develop in Windows and run the project in the cave at V2C/LRZ. We will modify the configuration files and install the required dependencies. Our goal is to be able to run it on our Linux system. I use Linux Mint 18, but you should be able to adjust the instructions to your distribution.


### Dependencies

You need to build the following dependencies:

 - Boost (version 5.13.0)
 - OpenSG2 (Alpha released on 2015-01-16)
 - VRPN (version 7.33)
 - inVRs (revision 2647)
