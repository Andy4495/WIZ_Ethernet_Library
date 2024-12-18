# WIZ Ethernet Library

## Special Note

This is a fork of the [WIZnet Ethernet library][Wiznet]. The code has been changed from the upstream library as listed below. Also, the directory structure has been changed in order to conform to the [Arduino Library Specification][Libspec].

This README.md file has been changed to add this *Special Note* section and to resolve issues flagged by *[markdownlint][Lint]*.

### Code Changes

* w5100.h  
  Change the the following lines from:

  ```cpp
  //#define W5200_ETHERNET_SHIELD 
  #define W5500_ETHERNET_SHIELD
  ```

  To:

  ```cpp
  #define W5200_ETHERNET_SHIELD 
  //#define W5500_ETHERNET_SHIELD
  ```

  That is, uncomment the W5200 definition, and comment out W5500

* w5200.h

  In lines 315-339, delete or comment out all the #ifdef structures and leave only the following as active code:  

  ```cpp
  inline static void initSS()    { pinMode(SS, OUTPUT); \
                                   digitalWrite(SS, HIGH); };
  inline static void setSS()     { digitalWrite(SS, LOW); };
  inline static void resetSS()   { digitalWrite(SS, HIGH); };
  ```

* w5200.cpp
  
  1. Change the  line:  

      ```cpp
      #define SPI_CS 10
      ```

      To:  

      ```cpp
      #define SPI_CS 8
      #define ARDUINO_ARCH_AVR 
      ```

  2. After the following lines:  

      ```cpp
      #if defined(ARDUINO_ARCH_AVR)
      SPI.begin();
      initSS();
      ```

      Add this line  

      ```cpp
      SPI.setClockDivider(SPI_CLOCK_DIV8);
      ```

  3. In the second-to-last line of the file (right before the final #endif), add the following  

      ```cpp
      #undef ARDUINO_ARCH_AVR
      ```

* Ethernet.h

  Add a constructor prototype in the public section:  

  ```cpp
  EthernetClass();
  ```

* Ethernet.cpp

  Define the constructor, which initializes the `_dhcp` pointer to zero:

  ```cpp
  EthernetClass::EthernetClass() {
      _dhcp = 0;
  }
  ```

  In the two places where a new `DhcpClass` object is created, replace this:

  ```cpp
  _dhcp = new DhcpClass();
  ```

  with this (to check if an object was already created):

  ```cpp
  if (_dhcp == 0) {
      _dhcp = new DhcpClass();
  }
  ```

* Added file `Udp.h` from the standard Arduino Ethernet library.
* Filename changes to avoid ambiguity with built-in Ethernet library:
  * `Ethernet.cpp` -> `WIZ-Ethernet.cpp`
  * `Ethernet.h` -> `WIZ-Ethernet.h`
  * `EthernetClient.cpp` -> `WIZ-EthernetClient.cpp`
  * `EthernetClient.h` -> `WIZ-EthernetClient.h`
* Updated `#include` directives to use new file names:
  * `EthernetServer.cpp`
  * `EthernetUdp.cpp`
  * `WIZ-Ethernet.cpp`
  * `WIZ-Ethernet.h`
  * `WIZ-EthernetClient.cpp`
* Deleted files `Twitter.h` and `Twitter.cpp`, since there were not used for my application and had an imcompatiblity with the GCC9.6 compiler due to `println()` method

[Wiznet]: https://github.com/Wiznet/WIZ_Ethernet_Library
[Libspec]: https://arduino.github.io/arduino-cli/0.19/library-specification/
[Lint]: https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint

The original README begins after the horizontal rule below.

---

WIZ Ethernet library is made for various Open Source Hardware Platform and support WIZnet's W5100, W5200 and W5500 chip. The Ethernet library lets you connect to the Internet or a local network

## Supported devices

* ioShield, WIZ550io (using W5500)
* W5200 Ethernet Shield, WIZ820io (using W5200)
* Arduino Ethernet Shield (using W5100)

## Hardware

* [ioShield](http://wizwiki.net/wiki/doku.php?id=ioshield "ioShield")
* [W5200 Ethernet Shield](https://github.com/Wiznet/W5200-Ethernet-Shield "W5200 Ethernet Shield")  
* [Ethernet Shield](http://arduino.cc/en/Main/ArduinoEthernetShield "Ethernet Shield")  

## Software

### 1. Install WIZ Ethernet library  

#### Arduino IDE 1.0.x

Download all files and overwrite onto the "\libraries\Ethernet" folder in your project in sketch.

##### Arduino IDE 1.5.x

Download all files and replace the "\libraries\Ethernet\src" folder in your Arduino IDE. This will update the "utility" folder also under "\libraries\Ethernet\src".

#### 2. Select device: W5100, W5200 or W5500  

In the W5100.h file(\libraries\Ethernet\utility\w5100.h), uncomment the device(shield) you want to use.  

```cpp
#ifndef W5100_H_INCLUDED
#define W5100_H_INCLUDED

#include <avr/pgmspace.h>
#include <SPI.h>

typedef uint8_t SOCKET;
//#define W5100_ETHERNET_SHIELD
//#define W5200_ETHERNET_SHIELD
#define W5500_ETHERNET_SHIELD
```

By default, "WIZ550io_WITH_MACADDRESS" is commented and if you uncomment it, you can use the MAC address stored in the WIZ550io.

```cpp
#if defined(W5500_ETHERNET_SHIELD)
//#define WIZ550io_WITH_MACADDRESS // Use assigned MAC address of WIZ550io
#include "w5500.h"
#endif
```

## How to use the WIZ Ethernet library and evaluate existing Ethernet example

All other steps are the same as the steps from the Arduino Ethernet Shield. You can find examples in the Arduino IDE, go to Files->Examples->Ethernet, open any example, then copy it to your sketch file (gr_sketch.cpp) and change configuration values properly.
After that, you can check if it is work well. For example, if you choose 'WebServer', you should change IP Address first and compile and download it. Then you can access web server page through your web browser of your PC or something.

## Revision History

* Initial Release : 14 August 2013
* Adding function to read / write W5500 PHY configuration register : 4 December 2013
* Support the Arduino Due (Arduino IDE 1.5.x). Now it support 42Mhz SPI clock ! (by Jinbuhm Kim): 28 Feb. 2014
* Separate the folder for Arduino IDE 1.0.x & Arduino IDE 1.5.x
