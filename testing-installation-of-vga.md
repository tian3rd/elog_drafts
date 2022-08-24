# Testing and installation of VGA in chassis

## Current results

[updated on 27/07/2022] Gains in each of the four channels are performing as expected. (Filter channels are not used by not activating/clicking the buttons on MEDM screen).

## Soldering of D-sub connectors

1. The soldering in the current phase is only for testing and prototyping purpose.
2. Apart from the 37 d-sub connectors (readback channels) which need to extend to an external breadboard with resistors on it, other connections are better made with stranded wires for better bendability and flexibility.
3. The length of the wires should be adjusted based on the arrangement of the components in the chassis and the height of the unit.

## Wiring of each part

1. Currently the power and switch components are not installed and wired to the VGA board, so I still use the differential 15V power supply directly to the VGA board.
2. Also, for the powering of Arduino Mega, I use the ddr-15g-12 converter to convert 15V down to 12V to input into the Arduino Mega, in this way, without USB connection to raspberry pi, the Arduino Mega can still be powered.
3. USB connection between raspberry pi and Arduino Mega can be established by using the USB cable with the USB-A to USB-B connector anytime. The raspberry pi can also be replaced by any linux computer with USB port.

## Testing of MEDM for controlling gains of VGA

Analog discovery 2 is used as an alternative for MOKU to test the gains of each channel. Each of the four gain channels are tested from 0 to 45dB with the following setup:

Frequency: 5Hz and 10Hz
Amplitude: 10mV

## Next steps

1. Soldering stranded wires to D-Sub connectors.
2. Soldering the PCB board to convert 18V to 15V to power the VGA.
3. Arranging the components in the chassis.
4. Use LM7812 regulator to flywire to Arduino Mega to power it (15V -> 12V).
5. (Optional) Use LEDs to indicate if the VGA board is powered on or not, because the two green LEDs are not visible from the front of the chassis if we don't drill holes on the chassis.

## Pictures

[updated on 27/07/2022]

<img src="https://cdn-std.droplr.net/files/acc_498334/GWpyBm" alt="27072022Testing" width="600">
