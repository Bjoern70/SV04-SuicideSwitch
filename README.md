# SUICIDE SWITCH FOR SOVOL SV04 IDEX 3D-PRINTER

## Specification:

### Requirements:

 - Add funtionality to power off SV04 printer automatically after finishing print.
 - Safe electrical relization with minimum changes on existing high voltage connections.
 - Low power consumption of additional electronic components iot ensure motherboard / printer stability.
 - Maximum cost below 50 EUR / US-$


### Hardware approach:

 - Use solid state relais (SSR) to switch high voltage phase connection of power supply unit (PSU).
 - Avoid separate high voltage switching outside of SSR. Use low power push-button & separate 9V battery instead.
 - Build separate power-on panel to start printer. Use LEDs to visualize when SSR is powered by Battery and/or PSU.
 - Fix power-on panel to printer structure outside of printer cabinet.
 - Minimize number of & electronic components inside power-on panel.
 - Electronic components inside cabinet have to be fixed casing without impact on cooling & minimum impact on existing components.
 - Use 24V DC to power SSR when printer has started. Limit current to minimize power consumption & SSR input.
 - Use darlington transistor to minimize current consumption from J2 connector.
 
 
### Software approach:
 - Utilize spare J2 connector on v5.2.1 motherboard as suicide pin:
 - Add pin definition to motherboard "pins_CREALITY_V4.h" / "pins_CREALITY_V521.h" file: 
  ```//
  // SuicidePin
  //
  #define SUICIDE_PIN PE4 // Connector J2
  #ifndef SUICIDE_PIN_STATE
    	#define SUICIDE_PIN_STATE LOW
  #endif
  Reconfigure Marlin firmware "configuration.h" file iot use M81 g-code command (Power Off):
  // Custom Power Supply Control
  //
  #define HAS_SUICIDE_PIN
  //
  ```
- Add enable/disable auto power-off function to print options display screen.
- Add global bool variable to file "LCD_RTS.h" representing auto power-off enabled/disabled state. Add icons representing state to file "41_control.icl".
- Add AUTO_POWER_OFF_ICON_VP address definition to file "LCD_RTS.h". Add menu item to change state to adjustment phase in 32_main.icl.
- Add handling routine for state change to file "LCD_RTS.cpp" Add state check to suicide() function in MarlinCore implementation.
 
# Operating instructions:

## Power on printer:

- Switch on printer power switch => printer stays powered off. 

- Press push button on panel => yellow LED lights up. 

![IMG01.png](/Images/IMG01.png)

- Hold button until green LED lights up.

![IMG02.png](/Images/IMG02.png)

- Release button => printer stays powered on.

![IMG03.png](/Images/IMG03.png)

## Turn off printer: 

- Option A: Use power switch to turn off.

- Option B: Use power off function in settings menu.

![IMG04.png](/Images/IMG04.png)

- Option C: Add M81 gcode command to custom slicer end gcode. Printer will turn off when executing this instruction. 
   ```
   ; Example for prusa-slicer:
   ; nozzle cool-down & power off command
   M84 ; disable motors
   M104 S0 T0 ; turn off nozzle 0 temperature
   M104 S0 T1 ; turn off nozzle 1 temperature
   M140 S0 ; turn off bed
   M106 P0 ; fan max power iot prevent dropping of filament
   M109 S90 T0 ; wait for nozzle 0 cooldown
   M109 S90 T1 ; wait for nozzle 1 cooldown
   M81 ; suicide
   ```
# Build instructions:

## Important note: 	Changing your printer hardware might void your warranty. Follow this instructions on your own risk & liability.

## List of parts (maybe some components seem over-powered, but I had them available):
- solid state relais type SSR DA-20 
- NPN darlington transistor BDX53C
- Mini push button 0.3A, normally open, 7.2mm (9/32") mounting hole
- LED green, 5mm, 20mA
- LED yellow, 5mm, 20mA
- resistor 10KΩ, 0.25W
- resistor 1KΩ, 0.25W
- resistor 470Ω, 0.25W
- 9V block battery
- 9V battery snap
- cable grommet, 6mm diameter
- 10-20x cable zip tie
- 2x forked cable lug, 1.5-2.5mm², hole diameter M5
- 2m wire yellow+brown+red
- heat shrink tube 4mm diameter
- JST XH 2.54 3-Pin female connector
- 2x socket / button head screw M4x20mm + hex nut
- flat / countersunk screw M3x8mm + washer + hex nut
- 4x socket cap head screw M4x20mm + M4 T-slot nut

## Optional parts to support inside cabinet installation:
- SV04_Side_Supports.stl derived from: https://www.thingiverse.com/thing:5674611
- 3x socket / button head screw M4x10mm + washer + M4 T-slot nut

## Circuit diagram:

![IMG05.png](/Images/IMG05.png)

## Build side panel:
- Print side panel file "SidePanel.stl".
- Minimum cable length is 60cm/24inch. 3 wires (red, brown, yellow) are needed. 
- Insert yellow LED next to push button opening. Insert green LED. Ensure that LED cathodes point to each other. Bend, cut & solder both LED cathodes with brown cable.
- Bend, cut & solder green LED anode with red cable. Use shrink tube to insulate.
![IMG06.png](/Images/IMG06.png)

- Insert & fasten mini push button. Bend, cut & solder yellow LED anode with mini push button contact. Attach other mini push button contact to positive/red battery snap wire & solder. 
- Connect negative/black battery snap wire to yellow cable & solder. Use shrink tube to insulate.
- Add cable tie for strain relief or just wrap cable around post as pictured. 
- Attach 9V battery to battery snap & insert into side panel. 
- Insert four M4x20mm screws & attach M4 T-slot nuts.

![IMG07.png](/Images/IMG07.png)

![IMG08.png](/Images/IMG08.png)

## Cabinet preparation:

### IMPORTANT SAFETY NOTE: Unplug power cord first.
- Mark SSR drill holes on top of the cabinet.

![IMG09.png](/Images/IMG09.png)

- Install optional side supports, lay down printer on its right side.

![IMG10.png](/Images/IMG10.png)

- Remove bottom plate screws.
- Cover inside of drilling area with a strip of duct tape iot catch all metal drilling chips & a piece of wooden board iot avoid damage to motherboard / ribbon cable in case of accidently drilling too deep. Carefully drill 6mm hole into left side of cabinet.

![IMG11.png](/Images/IMG11.png)

- Cover inside of drilling area with a strip of duct tape iot catch all metal drilling chips. Carefully drill two 4,5 mm SSR holes into top side of cabinet. Ensure that holes match SSR fasteners & that sufficent space is left to mount & connect the SSR.

![IMG12.png](/Images/IMG12.png)

- Insert cable grommet into 6mm hole. Mount side panel, pull side panel cable through cable grommet.

![IMG13.png](/Images/IMG13.png)

- Prepare mainboard JST connector:
- Minimum cable length is 40cm/16inch. Only 2 wires (PE4 + GND) are needed. 

![IMG14.png](/Images/IMG14.png)

##Build inside cabinet unit:
- This design does not use a circuit board. All electronic parts will be attached to the SSR/SSR cover. 
- Print SSR cover file "SSR_cover.stl".
- Insert M3x8mm screw into countersunk hole.

![IMG15.png](/Images/IMG15.png)

- Fix transistor (& optional cooling plate) to M3x8 screw using hex nut & washer.


- Put 40mm shrink tube on JST connector violet cable (PE4). Cut & solder 10KΩ resistor to transistor base (bottom pin). Cut & solder other resistor end to JST connector violet cable (PE4). Shrink tube to insulate transistor pin & resistor.
- Cut & solder 1KΩ resistor to transistor collector (middle pin). Cut & solder other 1KΩ resistor end to 470Ω resistor & 10cm red wire (will be attached to SSR terminal 4). Put 40mm shrink tube on yellow side panel cable. Cut & solder other 470Ω resistor end to yellow side panel cable. Shrink tube to insulate 470Ω resistor.
- Use shrink tube to insulate red SSR wire next to 1KΩ resistor. Fasten red SSR wire to SSR terminal 4 (-).
- Put 40mm shrink tube on JST connector black cable (GND). Cut & solder transistor base (top pin) to JST connector black cable (GND). Shrink tube to insulate transistor pin & cable.
- Fasten brown side panel cable wire to SSR terminal 3 (+).

![IMG16.png](/Images/IMG16.png)

- Place SSR & insert M4x20mm screws into cabinet holes & SSR mounts. Remove mounted cable ties if necessary. Attach cutting mark onto red power cable from power inlet. Ensure to have sufficent cable length to mount both power cable ends to SSR connectors 1 & 2.
- Cut red power cable at cutting mark, strip a bit of insulation & use crimping tool to attach fork cable lugs to power cable ends. Attach power cable from power inlet to SSR terminal 1. Attach power cable from power supply unit to SSR terminal 2.
- Ensure that all SSR connectors are fixed firmly. 
### Note: All pictured power cables were shielded with extra silicone tubes.

![IMG17.png](/Images/IMG17.png)

- Insert M4 nuts into SSR shield. Fasten M4x20mm screws through cabinet top side holes. Ensure that screws are tight.

![IMG18.png](/Images/IMG18.png)

- Strip 20mm of side panel red cable end. Form a loop with 5mm diameter & solder it. Remove power supply unit terminal cover. Attach loop to power supply unit "+V" terminal. Ensure that terminal screw is tight. Fix power supply unit terminal cover.

![IMG19.png](/Images/IMG19.png)

## Test installation:
- The power cord stays disconnected. Use multimeter to check resistance between power inlet & power supply unit "L" terminal. Resistance diplayed should be very high (between xxx MΩ and infinite). 
- Press & hold push button. Ensure yellow LED lights up. Ensure SSR LED lights up, too. Ensure resistance displayed is low (between 0 and xx Ω).
- Unscrew side panel.  Use multimeter to check resistance between 9 V battery negative-terminal and transistor housing. Resistance diplayed should be 1470 Ω +/- 10% resistor tolerance.
- Use multimeter to check resistance between JST connector PE4 pin and GND pin. Resistance diplayed should be higher than 20 KΩ.

## Final hardware installation:
- Plug JST connector into port J2 on motherboard

![IMG20.png](/Images/IMG20.png)

- Reinstall all removed cable ties. Firmly fix all resistors & SSR wires to SSR cover with cable ties. Fix all other addional wires with cable ties.
- Fix bottom plate by installing all 8 screws. Connect power cord.
- Power on printer as described in operating instructions above.

# Software installation:
- Use SDcard to install printer firmware that supports power-off functionality from  https://github.com/Bjoern70/SV04-IDEX-3D-Printer-Mainboard-Source-code/releases
- Turn printer off & remove display backcover. Use microSD card to install display software that supports power-off functionality from https://github.com/Bjoern70/SV04-Touchscreen/releases
- Install display backcover.
- Change custom end gcode settings in your slicer profiles iot to power-off printer after printing process by default.
### Enjoy your new printer features!

