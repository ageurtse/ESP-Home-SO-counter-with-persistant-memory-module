
# ESP Home, SO 4 channel counter

I wrote this peace of software because i was in the need of a device which could count my pulses from a water meter and from my solar pannels. 
The default counter from within ESPHome does the thrick, one could also preserve te counted value in the memory off a ESP device. 
But on my electrical meter which counts 2000 pulses per kW it will wearout in a couple of days. 

So i wen't searching and found this beaty which doesn't coast that much at all. 
https://www.adafruit.com/product/1895

After finding the device i need a way to use it in home assistant, that was for me as a hobby programmer the hardes part, first search for a library which could do the trick. 
and second how to interface the library with ESPHome so i could use it. 

For the library a big thanks to @RobTillart for his great library https://github.com/RobTillaart/FRAM_I2C which i'm implementing in it. 

I'm still working on a test setup, next step is to develop a need 4 wat SO circuit and PCB so one could rebuild.



## Description about the FRAM module.

FRAM stands for Ferroelectric RAM - https://en.wikipedia.org/wiki/Ferroelectric_RAM

FRAM memory is much faster than EEPROM and almost as fast as (Arduino UNO) RAM. Another important feature FRAM has in common with EEPROM is that FRAM keeps its content after a reboot as it is non-volatile, even for years. That makes it ideal to store configuration or logging data in a project.
## Operational

There are 2 parts that one should keep in mind.

**First:**
One should make use off i2cfram.h this is the interface file between ESPHome and the library from https://github.com/RobTillaart/FRAM_I2C
in this file there is a setup part, where at boot the device is looking for a memory module and initializes it. 
further there are 2 function which could be called from within Lambda

float readfromfram(uint32_t memaddr), which reads from the given adres slot and returns a float value
void writetofram(uint32_t memaddr, float value), writes a float to the given memory slot
because of the use of floats in ESPhome, every memory slot contains 4 bytes. so if one give memaddr 10, it will write or read 4 bytes from addres 40


**Second:**
This is the yaml file.
For every counter i make use of a global variabel, which will be loaded from memory at boot time. 
It is also posible to (re)set te counters from Homeassistant. 

I disabled the timout so one could use the device without home assistant, i did a test run of 2 days without problems. 

There is the need for the custom component to initializes the memory module. 

From within the binary sensor, the global value is added with 1 at a time, on every count the new value is written to the memory module. 
