# ESPhome_codes

## Contents

1. General
2. Basic example
3. TODO
4. References / Further reading

## 1. General

Repository for experimental ESPHome codes (yaml files).

ESPHome is a system for generating firmware for ESP32 / ESP8266 boards and uploading this wirelessly to WIFI capable boards. For more information on ESPHome, see reference 1.

Normally the firmware (a binary file) is obtained writing C++ code or C++ - like code (Arduino .ino)  and compiling this. ESPHome uses a yaml file containing a description of the firmware functionality. The system converts this into the C++ code and subsequently compiles it. The generated C++ code and the firmware file (.bin)  


## 2. Basic example
Assumptions:  you have an ESPHome server up and running.


I have a server running under Docker on my Synology NAS. I can access the ESPHome dashboard in my browser with http://<NAS-IP>:6052, which uses the default port number. The docker container uses an external bound volume, which means that contents of the /config folder of the application is directly accessible for me on the storage volume of the NAS.

In this example I will create the firmware for a very basic application on a SB-NodeMCU-ESP32 board from Joy-It (https://joy-it.net/en/products/SBC-NodeMCU-ESP32).     
Documentation for this board can be found here: https://joy-it.net/files/files/Produkte/SBC-NodeMCU-ESP32/SBC-NodeMCU-ESP32-Manual-20200320.pdf 
![alt text](https://github.com/goofy2k/ESPhome_codes/blob/main/media/SBC-NodeMCU-ESP32-02.png "SBC-NodeMCU-ESP32 (Joy-it)")
This is a very affordable ESP32 board. It has one huge disadvantage if you want to use it for experimenting with connection of other hardware to it's pins:  the board does NOT fit on normal breadboards. The distance between the two pin rows does not fit with the breadboard spacing. There are solutions available for this on the web, but it takes some cutting...
![alt text](https://github.com/goofy2k/ESPhome_codes/blob/main/media/separated_breadboard.jpg "separated breadboard")
ESPHome is capable of doing over-the-air (OTA) firmware updates. It should be evident that before it is possible to do OTA updates, the firmware must be capable to receive those. This requires a WIFI connection and a routine to receive updates over WIFI. The very first firmware with this minimum capabilities must be loaded to the board over a wired (USB) connection. All subsequent updates can be done over-the-air. Unless....

         - something is wrong with the latest firmware uploaded to the board         .... test before uploading, if still fails: upload via USB  
         - something is wrong with the WIFI connection                               .... back up WIFI access point to set credentials via a http connection and web page
  




## 3. TODO

Put secrets (credentials, host IP's etc) in a separate file and do not publish those 

## 4. References / Further reading

1. https://esphome.io/
2. https://www.home-assistant.io/
