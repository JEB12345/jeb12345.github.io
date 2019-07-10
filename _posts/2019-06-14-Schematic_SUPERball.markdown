---
layout: post
title:  "Schematic For SUPERball v2"
date:   2019-06-14 09:22:22 -0600
tags: SBv2 Electronics
---
## Revision C - Teensy Mod
> You can find the git repo for SUPERball v2 [here][Electronics Github], which houses the schematics and board files for each version of our custom electronics board. Note that this page refers to the "Onion_Teensy_M0" branch. This may or may not be merged with master at some point in the future.

> The project was developed on [KiCAD] v6 as of the writing of this document.

This is the third revision of our electronics board for wireless communication and battery power distribution on SUPERball v2. The first and second boards were design steps which each had different issues that made them unusable on the full system. Below is a 3D rendering of the board generated in KiCAD.

![3D Board](/assets/img/main_3D_combined.png "Top/Bottom Rendering of SBv2 Electronics Board")

### Overview

Below is a simple (and crude) overview of the schematic connections for the electronics board. The main features are:
* Battery Monitoring using TI's [**bq76930**][bq76930] chip for power and individual cell monitoring of our 6S battery pack
* Step down power from pack voltage to 5V then from 5V to 3.3V using TI's [**lmzm23601**][lmzm23601] and [**lp3965**][lp3965], respectively.
* Main real time communication and processing using a [**Teensy LC**][teensyLC]
* Onion's [**Omega2p**][omega2p] as our WiFi to Ethernet bridge and ROS node
  - Serial to USB communication for debugging for the Omeag2p was based on Omega's [schematics][onion schematic] using Silicon Labs' [**CP2102N**][cp2102n] chip.
* Wireless safety switch utilizing a custom layout of Nordic's [**nRF24L01p**][nRF24] chip controlled by a dedicated [**ATSAMD21E**][atsamd21e] M0 micro controller.

![Crude Electrical Diagram](/assets/img/SBv2_Electronics_Diagram.png "Electrical Diagram")

[Electronics Github]: https://github.com/JEB12345/superball_v2_electronics/tree/Onion_Teensy_M0
[KiCAD]: http://www.kicad-pcb.org/
[bq76930]: https://www.ti.com/lit/ds/symlink/bq76930.pdf
[lmzm23601]: http://www.ti.com/lit/ds/symlink/lmzm23601.pdf
[lp3965]: https://www.ti.com/lit/ds/symlink/lp3965.pdf
[teensyLC]: https://www.pjrc.com/teensy/teensyLC.html
[omega2p]: https://docs.onion.io/omega2-docs/omega2p.html
[onion schematic]: https://github.com/OnionIoT/Onion-Hardware/blob/master/Schematics/Omega-Expansion-Dock.pdf
[cp2102n]: http://www.silabs.com/support%20documents/technicaldocs/cp2102n-datasheet.pdf
[nRF24]: https://www.sparkfun.com/datasheets/Components/nRF24L01_prelim_prod_spec_1_2.pdf
[atsamd21e]: http://ww1.microchip.com/downloads/en/DeviceDoc/SAMD21-Family-DataSheet-DS40001882D.pdf
