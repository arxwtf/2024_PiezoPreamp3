# Piezo Preamp with Baxandall Tone Control and Battery Indicator

A simple preamp to be used for e.g. guitar piezo (contact) microphones. 

Created in KiCAD v8 and LTSpice.

[User manual here.](https://arx.wtf/piezo-preamp-3/Arx_PiezoPreamp_v3.1-User_Manual.pdf)

[Detailed information / blog here.](https://arx.wtf/blog/3-piezo-preamp)

![Rendered PCB](render.png?raw=true "Rendered PCB")


## Main circuit blocks

### Charge amplifier for piezo 

Basically an integrator circuit. Provides low-impedance input. The gain is adjustable and depends on the input piezo capacitance. In my measurements, piezo discs have following capacitances:

- small disc: ~10nF
- medium disc: ~20nF
- large disc: ~40nF

But I've also seen mentions of piezo discs with <10nF capacitance. Connecting multiple piezo discs in parallel increases the capacitance, so the maximum piezo capacitance can be in order of ~100nF or so.

The gain of the charge amp is then set by the ration of piezo capacitance to charge amp feedback capacitance. The **gain slide switch** is used to select from these feedback capacitances:

- 1nF
- 5.7nF
- 10.4nF
- 23nF

Input is protected by ESD bidirectional diode.

OPA145 is the opamp of choice because of these characteristics:
- JFET input stage, which means low input bias and offset current - critical for a charge amplifier
- low quiescent current (0.7mA max) for battery operation
- acceptable input voltage noise density (7nV/rtHz)
- good current noise density (0.8fA/rtHz)
- sufficient slew rate for audio (20V/us)
- sufficient GBW (5.5MHz)
- single supply voltage range 4.5V - 36V
- output voltage swing (V- +0.3, V+ -0.3)

Opamp also unfortunately seems to have bias current cancellation, which might introduce more current noise - this has to be determined by measurement. The same opamp is also used for Baxandall tone control section. Two single OPA145 components are used instead of dual OPA2145 package because it's easier to route, minimizes crosstalk and improves power dissipation (less heat). This increases the price, of course.

### High-pass filter with HPF switch

The charge amp is followed by a simple first-order high-pass filter with switchable cutoff frequency:
- 0Hz 
- 50Hz
- 77Hz
- 113Hz

The cutoff frequency is selectable by **HPF slide switch**.

### Baxandall tone control with Center switch

Bass and treble can be adjusted independently by **Bass pot** and **Treble pot** in the ranges of +-10dB. The center frequency is switchable by **Center slide switch**:

- 350Hz
- 600Hz
- 1kHz

Output audio is routed through **Volume pot** and protected by ESD bidirectional diode.

### Power-on LED and battery indicator
The power-on LED can work in two modes, switchable by LED mode switch:
- always on (switch left)
- "battery indication mode" (switch right)

**Battery indication mode (BI)** causes the LED to flash briefly when the circuit is switched on (by inserting mono plug to output jack + moving the power-on switch to the right). The longer the flash, the lower the battery voltage (remaining capacity). Under certain threshold, the LED will stay on permanentnly, signalling the need for replacement. This **BI threshold** is adjustable by on-board trimmer pot and needs to be determined by voltmeter measurement.

### Power supply
Designed for 9V batteries or DC pedal power supplies, but should theoretically be able to work properly with up to 18V DC.

Power enters the PCB by means of standardi 5.5/2.1mm DC IN connector with switch to disconnect internal 9V battery.

Contains ESD protection (bidirectional diode), MOSFET reverse polarity protection (with Zener gate protection and CLR) and input pi filter.

### OFF/ON switch with true bypass
The power-on switch works as a true bypass switch in its left position. The device is switched on by moving the switch to the right and insertin a **mono** male audio plug into the output jack.


## Simulation: /ltspice folder
LTSpice simulations (transient, AC, noise, stability) and some graphs are available in `ltspice/` folder.

## /library folder
Folder containing all 3rd symbols, footprints and 3D models used in the project. Other than that, only standard KiCAD symbols are used (opamps, ).

## /production folder
Contains gerbers, position files, BOM file, screenshot of JLC settings etc.

## /resources folder
Contains some additional resources, inspiration and schematics used during development.

## /schematics folder
Contains schematic sheets plotted from KiCAD, individually as .png and in one file as .pdf.

## Known problems
- when the circuit is turned ON or OFF (by the power-on switch and inserting of cable into AUDIO OUT jack), it might produce a strong voltage transient (a loud "pop"). Be careful when you're using it with sensitive equipment downstream.

- battery spring (used only for grounding) is too close to the PCB edge. This creates unnecessary force between the PCB and the enclosure wall. The spring can be shortened, or not used at all.
