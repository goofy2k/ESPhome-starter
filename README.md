# ESPhome starter

## Contents

1. General
2. Basic example
3. TODO
4. References / Further reading

## 1. General

Repository for experimental ESPHome codes (yaml files).

ESPHome is a system for generating firmware for ESP32 / ESP8266 boards and uploading this wirelessly to WIFI capable boards. For more information on ESPHome, see reference 1. A nice introduction can also be found in ref. 3 .

Normally the firmware (a binary file) is obtained writing C++ code or C++ - like code (Arduino .ino)  and compiling this. ESPHome uses a yaml file containing a description of the firmware functionality. The system converts this into the C++ code and subsequently compiles it. The generated C++ code and the firmware file (.bin)  


## 2. Basic example
**Assumption:**  you have an ESPHome server up and running and have access to the dashboard. This README was started when I was using V1.18.0 . For subsequent dashboard versions the menu structure will evolve. I will not try to keep up with these changes here. For a beginning user it may be helpful to start with V1.18.0, get acquainted with the basics an then move to a more recent version.



I have a server running under Docker on my Synology NAS. I can access the ESPHome dashboard in my browser with http://NAS-IP:6052, which uses the default port number. The credentials for access to the dashboard can be found in the Docker setup. The docker container uses an external bound volume, which means that contents of the /config folder of the application is directly accessible for me on the storage volume of the NAS.

NOTE: the Docker container must have the network set to "host". If you use "bridge" there are connection problems with a dynamic IP address and with the "fallback hotspot" (see later).

In this example I will create the firmware for a very basic application on a SB-NodeMCU-ESP32 board from Joy-It (https://joy-it.net/en/products/SBC-NodeMCU-ESP32).     
Documentation for this board can be found here: https://joy-it.net/files/files/Produkte/SBC-NodeMCU-ESP32/SBC-NodeMCU-ESP32-Manual-20200320.pdf 

![alt text](https://github.com/goofy2k/ESPhome_codes/blob/main/media/SBC-NodeMCU-ESP32-02_cropped.png "SBC-NodeMCU-ESP32 (Joy-it)")

This is a very affordable ESP32 board. It has one huge disadvantage if you want to use it for experimenting with connection of other hardware to it's pins:  the board does NOT fit on normal breadboards. The distance between the two pin rows does not fit with the breadboard spacing. There are solutions available for this on the web, but it takes some cutting...
         
![alt text](https://github.com/goofy2k/ESPhome_codes/blob/main/media/separated_breadboard_cropped.jpg "separated breadboard")

ESPHome is capable of doing over-the-air (OTA) firmware updates. It should be evident that before it is possible to do OTA updates, the firmware must be capable to receive those. This requires a WIFI connection and a routine to receive updates over WIFI. The very first firmware with this minimum capabilities must be loaded to the board over a wired (USB) connection. All subsequent updates can be done over-the-air. Unless something is wrong with the latest firmware uploaded to the board or something is wrong with the WIFI connection.

If the root cause of failure is in the firmware, a repaired firmware version must be uploaded via a USB connection. For cases where the previously used WIFI network is not available a fallback solution can be built into the firmware. After a predetermined number of connection attempts, the board can go into access point (AP) mode and serve a webpage where the user can set the credentials for an available wireless network. The board can then reboot and start connecting to the set network. Note, that after a subsequent firmware update the edited WIFI settings are lost and the user must set them again (see TODO 2).  

After completion of the basic firmware example, the board will be able to:
         
- connect to an available WIFI network
- receive OTA firmware updates
- go to AP mode and accept edited WIFI creedentials in case connection fails
         
**Next steps:**     

Note: as of ESPHome version 1.19.0 the dashboard offers a button "Install".  After pressing this, the user has several options. One of those combines steps 2 and 3 in the next list. The menu options "Compile"  (step2) and Download binary (step3) don't exist anymore. Also the use of an external flasher app (step4) may be replaced by one of the options, but this has not yet been tested by me. 

1. create yaml file with basic functionality         
2. compile / create firmware.bin
3. download firmware.bin to PC
4. upload firmware via USB     
5. make some firmware changes in yaml file
6. compile and upload (OTA)
         
### 1. create yaml file with basic functionality
         
Open ESPHome dashboard in browser
Create new node (+ button) 
Follow the steps in the wizzard       
Enter node name: joyit_nodemcu_esp32_ex
Select device type: Generic ESP32 (WROVER Module)
WiFi & Updates: WiFi credentials, OTA password
Submit
         

After this a file **joyit_nodemcu_esp32_ex.yaml** has been created in the /config folder. You can view it's contents [here](https://github.com/goofy2k/ESPhome_codes/blob/main/media/joyit_nodemcu_esp32_ex.yaml).
         
The file can be viewed and edited under the Edit button. The credentials for the fallback hotspot (AP) have been automatically generated. You may have to note those in case you have to connect to the hotspot.           
With **"Validate"** the contents of the yaml file can be validated. As this yaml file is auto-generated by the wizzard, the result is obviously positive. Once you start adding features by hand this feature is very useful as the yaml format is very sensitive to details :-)
It makes no sense to use the Upload as the board does not yet have WiFi connection and OTA capabilities. The Upload button actually first creates the binary by compilation and uploads it to the board in one go. You can try Logs and see from the messages that the board is not yet connected to the ESPHome API server.

### 2. compile / create firmware.bin
         
The firmware must be created without trying to upload it: choose **"Compile"** from the three dot menu of the board's box in the dashboard. In this action the C++ code is composed and saved in the /config [folder](https://github.com/goofy2k/ESPhome_codes/blob/main/media/esphome_files.jpg). The compiled firmware is stored somewhat [deeper](https://github.com/goofy2k/ESPhome_codes/blob/main/media/firmware_location.jpg) in this folder.

**/config folder**   
![alt text](https://github.com/goofy2k/ESPhome_codes/blob/main/media/esphome_files.jpg "/config folder")


**location of firmware within config folder**         
![alt text](https://github.com/goofy2k/ESPhome_codes/blob/main/media/firmware_location.jpg "location of firmware")




### 3. download firmware.bin to PC
         
With **"DOWNLOAD BINARY"** in the window that popped up for compilation, the firmware can downloaded to your computer.  So you don't have to dig into the /config folder to get the file. A file **joyit_nodemcu_esp32_ex.bin** is now in your downloads folder. Close the window (**"CLOSE"**).
         
### 4. upload / flash firmware via USB
Flashing of the file to the board over USB can be done with the ESPHome-flasher, see: 
https://esphome.io/guides/faq.html#i-can-t-get-flashing-over-usb-to-work
and get the program over here:         
https://github.com/esphome/esphome-flasher/releases .

The steps are:   
- connect the device to a USB port of your computer
- start ESPHome-Flasher-1.3.0-Windows-x64.exe 
- choose the COM port belonging to the USB port
- select the downloaded firmware file
- press Flash and see messages appear in the log screen

The log screen contains diagnostic information (such as device type, presence of a calibrated ADC etc. ) that can be especially helpful when something is wrong. Unfortunately the info scrolls over the screen rapidly and I did not yet discover a way to capture this output for better inspection (flasher_output.log ? ).

Be aware that it can take some time for the device to connect to the network and, if applicable, a Home Assistant server. 

More, but other, info can be obtained from the Logs window in the Esphome dashboard. If you want to inspect the order of events from the start, you may want to press the reboot button on the device.

When the device connected to your WiFi network .... and stays connected you can disconnect from USB (see Note 1 !). If everything works fine, you can still see information coming into the Logs screen(board_logs.log), now over the WiFi connection.
         

### 5. make some firmware changes in yaml file

Note: before you compile and upload, you can Validate the code. This will detect any errors in the yaml file. One other nice feature is that the output of this action shows the values of all parameters of the component, including those that have not been used in the yaml file and have got a default value. This will give you an overview of the possible settings.

### 6. compile and upload (OTA)
inspect logs
  

### Remarks:         

The source codes (in this case yaml files!) are saved in the /config folder and have the device/application name (appname.yaml). The output (C++ code, compiled firmware(?)) are stored in a separate folder per app:  /config/appname.  Other resources must be stored in .....          
         

## 3. TODO

1. DONE Put secrets (credentials, host IP's etc) in a separate file and do not publish those. Test Secrets Editor. See [ref 3.](https://blog.christophersmart.com/2020/03/31/defining-home-automation-devices-in-yaml-with-esphome-and-home-assistant-no-programming-required/ ) . In the documentation add a dummy_secrets.yaml to show the structure of the secrets.yaml file without showing the secrets.
2. Describe in the docs how to use secrets.yaml .
3. Find a way to synchronize the contents of your /config folder on the NAS with the Github repository
4. Find a solution for keeping WIFI credentials after a firmware update. Prevent that the end-user is forced to change a yaml file. He must be able to keep on going after a repair via the fallback hotspot. Is it possible to extend the web page of the fallback hotspot with an option for choosing static vs dynamic IP?
5. Go into using the Esphome component [Improv](https://www.improv-wifi.com/) to provision the WiFi connection of a device via bluetooth (BLE). The device contains a BLE server, which operates headless and accepts WiFi credentials sent by a BLE client. The BLE client is on a BLE capable device. The UI on that device may either run in Javascript on a web browser or in a dedicated app (code (Kotlin) for an Android app available). The easiest way for now seems to be via the web browser. The other route involves creation of an Android app. Note: the website must be either accessible via https OR run on localhost. Is the latter a reachable solution????
6. Investigate "Update All"
7. Investigate "Clean MQTT" ,  remove MQTT retained messages from the broker
8. Investigate "Clean Build"
9. Discuss (high level) integration in Home Assistant         
         

## 4. References / Further reading

1. https://esphome.io/
2. https://www.home-assistant.io/
3. https://blog.christophersmart.com/2020/03/31/defining-home-automation-devices-in-yaml-with-esphome-and-home-assistant-no-programming-required/

**Note 1:** The USB connector on some boards is fragile. Especially when Murphy passes by frequently, it is better to disconnect by removing the USB connector from your computer and leave the cable on your device until the debugging phase is over. Also, if your device does not have a separate battery connector, you have to power it up over the USB connector anyway.

## Scratch area  
Implement...  >> new yaml
compile
download binary
flash (over usb)
         
ESP_36BF6D ????
         
         
test fallback hotspot:
flash with wrong WIFI credentials
         
static IP adress
https://esphome.io/components/wifi.html#manual-ips
         
OK
Now upload (OTA) firmware with wrong wifi credentials...
         
AP appears: ESP_36BF6D  but with a different name than the configured one.  After some time (minutes) the configured one appears. Connection to it is not possibel
         
captive portal
https://esphome.io/components/captive_portal.html
http://192.168.4.1/
