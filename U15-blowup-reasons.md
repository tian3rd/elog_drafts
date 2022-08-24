# How `U15` broke?

## Schematic where it is used in VGA

![schematic](https://cdn-std.droplr.net/files/acc_498334/4hunKn)

When the incident happened, the VGA was connected with Arduino which input default high voltages (5V) to the four channels. In this case, `LE`, `i0` to `i6` were all set to be 5V.

When the board does not take any controls from outside (e.g. Arduino), if we give it an analog input, and we test the output, the VGA should do nothing and the output should be the same as the input. This is verified by the schematic designed above.

When we only connect the VGA without Arduino inputs, the `VCC` in R29 will put `LE` and `i0` to `i6` to 5V, and therefore, `D0` to `D6` are high and `R0` to `R6` and `R_LE` are low. Because `D0` to `D6` are high, the gains and filters are bypassed based on the schematic, so the input signal will not be affected and we can get the same output as the input.

The function of `U13` (`74ALS573`): The `U13` `Qx` can only be connected to `Dx` if the `LE` is high.

The funtion of `U15`, and `U14` (`DG412LE`): To switch `NOx` to `COMx` or leave it floating. In this way, we can read the signals back with an extra 5V sinking design.

## Possible reasons

## Ways to mitigate the risks

1. USB-A to USB-B connection between Raspberry Pi and Arduino Mega should be limited to data tranfer only.
   1. E.g., customize a connector, or just cut the wire and connect only the data lines.
   2. ![cut the wire](https://cdn-std.droplr.net/files/acc_498334/hotPDc).
   3. Test results: without the power lines, the data can still transfer between raspberry pi and Arduino Mega.
