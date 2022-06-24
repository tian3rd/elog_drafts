# VGA board Testing

## Testing Equipment

1. Power supply.
   1. Model: [E3620A Dual Output DC Power Supply, 0 - 25V, 0 - 1A](https://user.engineering.uiowa.edu/~expeng/equipment/HP_E3620A_manual.pdf)
   2. Purpose: power up VGA board with +15, GND, -15V
2. Arduino Mega.
   1. Model: [Arduino Mega 2560 Rev3 (Mega)](https://docs.arduino.cc/hardware/mega-2560)
   2. Purpose: output analog voltages (high: 5V, low: 0) to VGA board to control the gain and fileter stages
   3. Script:
3. Raspberry Pi.
   1. Model: [Raspberry Pi 4 Model B (RPI)](https://static.raspberrypi.org/files/product-briefs/Raspberry-Pi-4-Product-Brief.pdf)
   2. Purpose: run service scripts, create EPICS database, update EPICS channels, and display MEDM control screen.
   3. Script:
4. Oscilliscope.
   1. Model: [Analog Discovery 2 USB Oscilliscope (AD2)](https://digilent.com/reference/test-and-measurement/analog-discovery-2/start)
   2. Purpose: Generate wave forms (e.g., 20mV sine wave with 5Hz) as analog input for VGA board, and also test the analog output from the board with oscilliscope
   3. Optional: can also use [Moku](https://www.liquidinstruments.com/products/hardware-platforms/mokulab/), beware of the voltage limit
   4. Software: [Waveforms](https://digilent.com/reference/software/waveforms/waveforms-3/start) to observe and save the data
5. VGA board.
   1. Model: [ISC Whitening VGA Filter v7](https://anu365.sharepoint.com/sites/gw-torpedo/Shared%20Documents/Forms/AllItems.aspx?id=%2Fsites%2Fgw%2Dtorpedo%2FShared%20Documents%2FElectronics%20%2D%20Deployed%2FISC%20Whitening%20VGA%20Filter%20%2D%20ANUv1%2DLIGO%2DD1001530%2Fversion7&viewid=02868917%2D1285%2D4456%2Db0b8%2D9db2c542d8f6)
   2. Purpose: "It has 4 channels of which the gain can be adjusted in steps of 3dB from 0 to +44dB, as well it has the option to implement 3 pole-zero stages which can switched in and out independently. We use it now to condition (e.g. amplify) the cavity error signals prior they go into the CDS. They need 32 DI to control the gain and filter levels, per 4 channels, and 32 DO to monitor the state." (from [readme file](https://anu365.sharepoint.com/sites/gw-torpedo/Shared%20Documents/Forms/AllItems.aspx?id=%2Fsites%2Fgw%2Dtorpedo%2FShared%20Documents%2FElectronics%20%2D%20Deployed%2FISC%20Whitening%20VGA%20Filter%20%2D%20ANUv1%2DLIGO%2DD1001530%2FReadme%2Emd&parent=%2Fsites%2Fgw%2Dtorpedo%2FShared%20Documents%2FElectronics%20%2D%20Deployed%2FISC%20Whitening%20VGA%20Filter%20%2D%20ANUv1%2DLIGO%2DD1001530))
6. Other parts.
   1. Breadout boards:
      1. 37 pin male breakout board (x2)
      2. 15 pin male breakout board
      3. 9 pin male breakout board
   2. Jumper wires: multiple
   3. Resistors: multiple

## Preparation

1. Screw part U7 (part # LM7805C) to the board to connect the middle pin to ground. Make sure to check the voltages between test points. [e-log 18](https://cga-elog.anu.edu.au/Torpedo/18)
   1. Test the middle pin to GND pins on the board to see if it's connected as a quick test.
2. Solder filter channels with wires, and this step is just for testing the gains because there's no need to solder all the capacitors onto the board yet.

## Sanity Checks

1. Check the test points on the board.
   1. All the grounds should be connected (including the ground pin of components according to the schematic)
   2. TP5 - TP12 should give +5V
   3. TP3 - GND should give +15V
   4. TP6 - GND should give -15V
2. Based on the schematics, test relevant voltages.
3. When VGA is powered up

   1. According to _Switch Logic Circuit_, `LE` pin is 0V, and `U13` (part # 74ALS573) pin `Q1` to `Q6` would output high (about 3.7V).
   2. According to the datasheet of `U14`/`U15` (part # DG412LE (vishay)):

      | `IN` | `NO`     |
      | ---- | -------- |
      | 1    | `COM`    |
      | 0    | floating |

      Because `COMx` (pin 2, 15, 10, 7) are connected to GND, so when there's a high input, the the output `NOx` would be grounded. When inputs are low, then output would be floating.
      Therefore, we use sourcing (as opposed to sinking) from the Arduino board as a 5V input voltage source:
      ![exp1-vga-readback-sanity-check](https://cdn-std.droplr.net/files/acc_498334/TLCi1C)

4. When VGA is powered up, and we enable the `LE` and give an input of high or low from binary output module (Arduino Mega).
   1. According to the datasheet of `U13` (part # 74ALS573), when `LE` is high, `Qx` would correspond with `Dx` pins.
   2. We give `i0 (BI0)` a LOW signal, and test the output voltage in the sourcing design.
   3. We give `i0 (BI0)` a HIGH signal, and test the output voltage in the sourcing design.
      - ![exp2&3-readback-sanity-check](https://cdn-std.droplr.net/files/acc_498334/fXMJJv)

## Caveats

1. Max current allowed for Arduino pins
2. Are digital pins enough to read low/high voltages? (low is around 0.4V, and high would be 5V)
   1. Yes based on the documentation [here](https://www.arduino.cc/reference/en/language/variables/constants/constants/). The current for this voltage readout pin would be 0.74 mA.
   2. Use a larger resistor (10k ohm for example) to keep valtage below 0.25V

## Next Steps

1. Connect all 8 input pins, and all 8 output pins with proper resistors.
2. Use AD2 to test one channel of gains (screenshots)
3. Connect all 32 inputs and 32 outputs to test with gains in all four channels.
4. When capacitors are available to solder, also test the filters.
5. Try to design a PCB board to replace the resistors and wires on the breadboard.
