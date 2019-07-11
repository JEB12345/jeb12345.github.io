---
layout: post
title:  "Schematic and Layout For SUPERball v2"
date:   2019-07-01 09:22:22 -0600
tags: SBv2 Electronics
---
## Revision C - Teensy Mod
> You can find the git repo for SUPERball v2 [here][Electronics Github], which houses the schematics and board files for each version of our custom electronics board. Note that this page refers to the "Onion_Teensy_M0" branch. This may or may not be merged with master at some point in the future.

> The project was developed on [KiCAD] v6 as of the writing of this document.

This is the third revision of our electronics board for wireless communication and battery power distribution on SUPERball v2. The first and second boards were design steps which each had different issues that made them unusable on the full system. Below is a 3D rendering of the board generated in KiCAD.

![3D Board](/assets/img/main_3D_combined.png "Top/Bottom Rendering of SBv2 Electronics Board")

### Schematic Overview

Below is a simple (and crude) overview of the schematic connections for the electronics board. The main features are:
* Battery Monitoring using TI's [**bq76930**][bq76930] chip for power and individual cell monitoring of our 6S battery pack
* Step down power from pack voltage to 5V then from 5V to 3.3V using TI's [**lmzm23601**][lmzm23601] and [**lp3965**][lp3965], respectively.
* Main real time communication and processing using a [**Teensy LC**][teensyLC]
* Onion's [**Omega2p**][omega2p] as our WiFi to Ethernet bridge and ROS node
  - Serial to USB communication for debugging for the Omeag2p was based on Omega's [schematics][onion schematic] using Silicon Labs' [**CP2102N**][cp2102n] chip.
* Wireless safety switch utilizing a custom layout of Nordic's [**nRF24L01p**][nRF24] chip controlled by a dedicated [**ATSAMD21E**][atsamd21e] M0 micro controller.

![Crude Electrical Diagram](/assets/img/SBv2_Electronics_Diagram.png "Electrical Diagram")

PDFs of the actual KiCAD schematics can be found in the GitHub repo [here][PDFs].

### Layout overview

The layout of the board has some unique constraints based on the design of SUPERball v2's Omni-directional holder and location of the board. The main driving parameter of the Omni-directional holder design was to make a strong an lightweight component, electronic form factor, IC component placement, and mounting were then driven by these design choices. The main constraints driven are:
* The board must allow for access to the batter compartment, thus a center cutout on the board.
* All through-hole parts must be mounted on the top (SOFTBALL) side of the board or within the flaired cavities if they are mounted on the bottom, so that they do not interfere with the function of the Omni-directional routers.
* The SOFTBALL end effector limits the amount the board may extend beyond the Omni-directional holder to 100mm in diameter.
* At least a 3mm gap is needed between the electronic board's edge and the Omni-directional holder to allow for a 3D printed cover to seal the electronics from outdoor elements.

The design of the electronic board layout taking into account these contraints my be seen in the image below. The white outline is a 2D projection of the top of an Omni-directional holder. the yellow is the board outline.

![full layout](/assets/img/Layout_with_Omni_Outline.png "All layers displayed with Omni Holder Outline")

> Note that the board was designed as a 4 layer PCB board with the top and bottom layers as signal layers and the middle two layers as power planes. The first middle layer is positive power and the second is ground.

### Top of the Board and Top layer

The top of the electronics board houses the Omega2p, Teensy LC, nRF24L01P, 2.4 GHz antennas, step down converters for 5 and 3.3 volts, communication connector to the SOFTBALL sensor and String LEDs, as well as the buzzer.

Technically, the ethernet port is also mounted on this side. However, the port housing goes through a hole in the board and the connection is on the bottom side.

![Top Render with Text](/assets/img/Main_Board_Top_with_Text.png "3D render of electronic board top with Omega and Teensy")

Some ICs are located under the Omega and Teensy boards, which are shown in the image below.

![Top Render noOmegaTeensy with Text](/assets/img/Main_Board_Top_noOmegaTeensy_with_Text.png "3D render of electronic board top without the Omega and Teensy boards")

Below is a rendering of the actual PCB layout for the top layer with Silkscreen.

![Top Copper Layer](/assets/img/layout_top.png "PCB layout of Top Copper Layer with Top Silkscreen")

### Middle layers

The middle layers going towards the bottom layer from the top are a power plane and a ground plane, respectively, and are shown as such below.

![Power Plane](/assets/img/layout_power.png "PCB layout of Power Plane Copper Layer")

![Ground Plane](/assets/img/layout_ground.png "PCB layout of Ground Plane Copper Layer")

> Note the missing copper pours around the 2.4 GHz antennas at the end of the board. This is done to prevent noise and reflections from the pours from interfering with the wireless signals.

### Bottom of the Board and Bottom layer

The bottom of the electronics board houses the battery monitor circuit, battery power connector, Hebi power connectors, the entry for the Ethernet connector, sensor board connector, M0 microcontroller with SWD programming connector, as well as a backup 3.3 volt input connector.

![Bottom Render with Text](/assets/img/Main_Board_Bottom_with_Text.png "3D render of electronic board bottom")

The majority of these connectors are grouped to fit within the "flared" openings on the Omni-directional holder, which can be seen in the PCB copper layout render below.

![Bottom Copper Layer](/assets/img/layout_bottom.png "PCB layout of Bottom Copper Layer")

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
[PDFs]: https://github.com/JEB12345/superball_v2_electronics/tree/Onion_Teensy_M0/main_board/PDFs
