# Wireless Serial Gateway using ESP-01 and Arduino IDE
A device that connects a 3.3V UART interface to a web browser UI with the help of a ESP8266 chip (e.g. ESP-01) connected through its WiFi link. 

Based on previous work by [MrDIY](https://mrdiy.ca) and others. Prior source code found at : 	https://gitlab.com/MrDIYca/wireless-serial-gateway

### Mode of operation 
The ESP device is programmed to connect to your WiFi network in client (station) mode to  put up a web interface on a browser similar to a terminal emulator. The user can then send, receive and change the baud rate on UART0 on the ESP8266  through the WiFi connection. 

 The Webpage that displays the terminal emulator is entirely generated by code stored on the ESP8266 and no other tools on a PC or tablet other than a browser are required. The data transfer to and  from the browser is accomplished through a web-socket interface on port 8000.

### Initial start-up and configuration
I order to first connect a freshly programmed device to your WiFi network it needs to be configured with your WiFi credentials , ssid and password. To facilitate this the device  initially starts up in Access Point (AP) mode with a default ssid of **WiFi_Serial** and password of **password**. Connect to this WiFi signal and navigate to the IP address **192.168.4.1**. A configuration page  for ssid, password and baud rate will be presented. Enter the info about your WiFi network, hit save and the reboot the device. If all goes well the device is now connecting to your WiFi networks and  after finding it's IP address on your router or from the startup diagnostics on the ESP UART, the terminal emulator page can now be seen in the browser.

Should the ssid or pwd have been mistype or changed since last configuration the device is automatically defaulting back to AP mode after 10 second of unsuccessful connection attempts   

### Start-up and diagnotics output from ESP on UART0
The UART on the ESP8266 displays some boot-loader information which can not be suppressed in code. In order to provide some start-up information to the user such as status of wifi connection and the obtained IP address, diagnostic output has been added to be sent the UART during the start-up phase. With the Arduino IDE's Serial monitor set to the unusual baud rate of *74880*, this info can be seen on the monitor an used to connect to the device with via a browser. 

### Hardware modification for "clean" UART0-TX 
In order to suppress the potentially unwanted serial data stream on the TX line to reach the attached device  an external gating circuit needs to be used to lock out the start-up messages from reaching the target device. An OR gate in conjunction with a GPIO from the ESP8266 the UART TX line going  to the target device can be gated off until the the start-up phase is complete. GPIO4 is chosen for the gating signal because the two other GPIO signals present on the ESP-01 module also have boot-load functions attached to them and can't be used until the boot process is complete.  GPIO4 is not brought out to a pin on the ESP-01 module but can be found on pin 16 of the chip and attached with fine solder skills. Other modules such as ESP-12 have GPIO4 readily available.

###Files:


- wifi-serial.ino -- The arduino Source code running on the ESP, see comments in the file as to defaults and require libraries to compile and link.


- includes.cpp 	-- Code,CSS and html data that eventually gets loaded and executed in the browse. Heavily obfuscated and compressed, stuffed into a c-string so that the ESP doesn't need a file system to store it.


- configure.png -- screen shot of the Arduino IDE "board configuration page"

###Browser side debugging 
Note that the files (css,html,js) loaded by the browser from the device are marked with a 7 day cache life (TTL). This requires to either turn of caching for the site in the browser or clearing the cache each time a change is made in the browser side code stored on the ESP. 

  
	