# [VGA] Board part U15 burnt and follow-up - Updating

## Background

First test of the [VGA](https://anu365.sharepoint.com/sites/gw-torpedo/_layouts/15/download.aspx?UniqueId=14504586%2Dfdff%2D4d6a%2Da107%2Db6268fb0d1e6) board channel A gain stages (0 - 45dB)

### Test 1 Setup

1. Connect VGA board to power supply (+15V GND -15V)
2. Use [Raspberry Pi (RPI)](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) as host to control the [Arduino Mega (Mega)](https://store-usa.arduino.cc/products/arduino-mega-2560-rev3?selectedStore=us) so Mega can send high/low analog voltages (5V: high, 0V: low) to switch the gain stages on or off. The Python script and Arduino setup code are available on [GitHub](https://github.com/OZGrav/lsc_vga_arduino_ctrl).
3. Use waveform generator and oscilliscope functions of [Ananlog Discovery 2 (AD2)](https://digilent.com/shop/analog-discovery-2-100ms-s-usb-oscilloscope-logic-analyzer-and-variable-power-supply/) to send a 5Hz, 20mV sine wave signal for the first test.

### Test 1 Results

1. Amplified amplitude of the sine wave is observed when gain stages vary from 3dB to 45dB. (Screenshot will be added later)
2. However, the readback signals from _Output To Binary Input Module_ are not correct (ideally, if we switch on a gain stage, the corresponding readback signal should be switched from default "0" to "1" accordingly).
   1. With a multimeter, the actual readback voltage is around 0.5V, which is intepreted as "0" by the Arduino.

## Description

Use the multimeter to test the voltage of LE (Latch Enable) pin. The multimeter is set to the DC voltage, and I used the red probe to touch the pin in the breakout board which connected to the LE pin of channel A on the board, and the black probe was not used. As soon as I touched the pin, `U15` (part no. DG412LEDY) was burnt.

By close inspection, pin 4 (V-) is burnt black

### More test with burnt part on VGA board

When the board is only connected to the external power supply (+15V GND -15V) alone, it triggers the overload protection of the power supply. The positive power of 15V is forced to decrease to around 5V, and the current is limited to 1.0A.

It's possibly due to the short circuit inside the `U15`(DG412LEDY).

## Next Steps

1. Order `U15` (part no. DG412LEDY) from mouser.com and fix the VGA board.
2. Look into the problem of wrong readback voltages by understanding the schematics of the board.
3. Next, use the remaining VGA board to further test the board (with Bram's help and supervising)
