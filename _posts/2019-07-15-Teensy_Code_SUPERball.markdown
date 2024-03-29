---
layout: post
title:  "Teensy Code Overview"
date:   2019-09-24 00:00:22 -0600
tags: SBv2 Electronics Code
---

> Our source code my be found on GitHub, [here][TeensyCode].

### Teensy 4.0

The Teensy 4.0 is an extremely fast and powerful version of the Teensy line of microcontrollers from PJRC. It utilizes a 600Mhz ARM Cortex-M7 with multiple pin outs for hardware peripherals in a relatively small form factor. More information on the Teensy 4.0 can be found on PJRC's website [here][teensy40].

> Refer to the [Schematic and Layout page][Schematic and Layout] for how the Teensy 4.0 chip is electrically integrated into our electronics board.

### Features

The Teensy 4.0 is our main real time microcontroller for the main board on SUPERball v2. It's main processes are:
* The companion processor to the battery monitor circuit
* Reading data from our sensor board located lower on SUPERball v2's rods
* Reading data from our SOFTBALL pressure sensor
* Communicating data to the Omega2p through a standard UART connection

The code's main loop runs every 1ms (1kHz) with plenty of extra process cycles to perform faster processing if needed outside of the main loop.

> Currently, not all features have been implemented in code. I will try to update the page as more features are implemented.

### Code Environment

The code has been developed using the Teensyduino library (Arduino for Teensy) under the PlatoformIO development environment. This uses the C++ style of the Arduino language and is a bit different in compilation than a standard INO style code.

Information about PlatoformIO can be found [here][platformIO], and the Teensy configuration can be found [here][platformIO Teensy].

> Note: that I used the [Visual Studio Code][VSCode] variation of PlatformIO. However, any variation of the IDE may be used.

Our source code my be found on GitHub, [here][TeensyCode].

Using Arduino and Teensyduino allows for a multitude of open source libraries to be used directly or modified and has sped up development. The libraries used are listed below:
* [Arduino][arduino]/[Teensyduino][teensyduino], this provides basic microcontroller functions to all Arduino compatible chips
* [IntervalTimer][intervaltimer], interrupt based library to call a function at some microsecond based interval
* [PacketSerial][packetserial], packet-based serial communication using the [COBS][cobs] encoding format
* [Adafruit NeoPixel][neopixel], A library for various single wire chain-able RGB LEDs (WS2812, WS2811 and SK6812)
* [bq769x0 Arduino Library (modified)][bq769x0 Library], This is the original code, however we use a heavily modified variant to support the Teensy's faster I2C library and CRC checking. Our version is [packaged with the Project's source code][bq769x0CRC]

### Timing loop

 As stated earlier, most functions will run at a 1ms (1kHz) timing loop. A simple custom library called [**timer**][timer] initializes an **IntervalTimer** instance and calls a function to increase a counter every 1ms. The main loop of our program then checks to see if the previously saved count is not equal to the current count (set by our interrupt timing function). If valid, then the main loop will run. Functions that should run slower than 1ms (at 1ms intervals) should use a conditional modulo statement on the current 1ms count.

 Below is a sample of our main loop blinking an LED every 500ms:

 ```rust
 void loop() {
   /*
   * Everythin in this if statement will run at 1ms
   * Make sure all tasks take less than 1ms
   * If you want to run something slower than 1ms,
   * create an if statement and modulo systime with
   * the multiple of 1ms delay you want the task to run
   * e.g. if(timer_state.systime % 500 == 0) for a 500ms loop
   */
  if(timer_state.systime != timer_state.prev_systime) {
    noInterrupts();
    timer_state.prev_systime = timer_state.systime;
    interrupts();

    if(timer_state.systime % 500 == 0) {
      // Status LED
      ledState = !ledState;
      digitalWrite(ledPin, ledState);
    }
  }
  /*
   * Put tasks that should run as fast as possible here
   * Limit this to only a few tasks if possible
   */
  else {
  }
}
 ```

### Serial communication

Serial communication, specifically two wire [UART][uart], was chosen as the method to relay data to our Omega2p board since both SPI and I2C had various application specific complications rendering them not as useful as Serial.

On top of the Serial communication, a [COBS][cobs] encoding protocol library (PacketSerial) is used to allow for arbitrary length data packets across the asynchronous communication lines. Please refer to the arduino library's [GitHub page][packetserial] for a good write up on how and why this is used.

In order to get data from the Teensy, the Omega2p board will send a simple 1 Byte command corresponding to a predetermined data type (using a COBS compatible protocol, e.g. pySerial + cobs). The Teensy will then reply with current data for the commanded data type. This is very similar to how most IC sensor chips relay data to a host microcontroller.

Below are some data commands:

Omega2p Command (1 byte) | Data Requested | Bytes Sent from Teensy
:---: | :---: | :---:
`0x01` | Battery Voltage & Current | 4<br/>(2-voltage + 2-current)
`0x02`  | Color Sensor Data from External Board  | 8
`0x0D`  | Remote Kill Switch Enabled  | 0<br/>(No data sent back)
`0x00`  | Remote Kill Switch Disabled  | 0<br/>(No data sent back)
`0x14`  | Dummy Command | 2
`0xFF`  | Battery Management Shutdown | 0<br/>(No data sent back)

> As of writing this document, frame work for commands has been setup (the actual byte commands are subject to change). I will update the page as new commands are set.


[teensy40]: https://www.pjrc.com/store/teensy40.html
[Schematic and Layout]: {% post_url 2019-06-14-Schematic_and_Layout_SUPERball %}
[platformIO]: https://docs.platformio.org/en/latest/ide/pioide.html
[platformIO Teensy]: https://docs.platformio.org/en/latest/platforms/teensy.html
[VSCode]: https://docs.platformio.org/en/latest/ide/vscode.html#ide-vscode
[TeensyCode]: https://github.com/JEB12345/Teensy40_SBv2
[arduino]: https://www.arduino.cc/reference/en/
[teensyduino]: https://www.pjrc.com/teensy/teensyduino.html
[packetserial]: https://github.com/bakercp/PacketSerial
[cobs]: https://en.wikipedia.org/wiki/Consistent_Overhead_Byte_Stuffing
[intervaltimer]: https://www.pjrc.com/teensy/td_timing_IntervalTimer.html
[neopixel]: https://learn.adafruit.com/adafruit-neopixel-uberguide/
[bq769x0 Library]: https://github.com/LibreSolar/bq769x0_ArduinoLibrary
[timer]: https://github.com/JEB12345/Teensy40_SBv2/blob/master/src/timer.cpp
[bq769x0CRC]: https://github.com/JEB12345/Teensy40_SBv2/blob/master/src/bq769x0CRC.cpp
[uart]: https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter
