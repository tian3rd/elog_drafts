# [VGA] Transfer Function of the VGA Gains

> Refer to [elog-37](https://cga-elog.anu.edu.au/Torpedo/37) for `MEDM` screen for controlling the gains;
> Refer to [elog-29](https://cga-elog.anu.edu.au/Torpedo/29) for the detailed test setup

### Tranfer Function

```bash
#Digilent WaveForms Network Analyzer - Bode
#Device Name: Discovery2
#Serial Number: SN:210321AA2251
#Date: 2022-06-29
#Start: 1 Hz
#Stop: 100000 Hz (100 kHz)
#Steps: 151/101/51
#Wavegen: Wavegen C1
#Amplification: 1 X
#Settle: 10 ms
#MinPeriods: 16
#Channel: Channel 1
#Range: 5.50025 V
#Offset: -0.000154649 V
#Relative: no
#Channel: Channel 2
#Range: 5.52608 V
#Offset: -0.000328523 V
#Relative: yes
```

1. 0dB

<img src="https://cdn-std.droplr.net/files/acc_498334/uVoyaw" alt="0dB transfer function" width="550">

2. 6dB

<img src="https://cdn-std.droplr.net/files/acc_498334/MGtl3Y" alt="6dB transfer function" width="550">

3. 15dB

<img src="https://cdn-std.droplr.net/files/acc_498334/qcFrwU" alt="15dB transfer function" width="550">

4. 45dB

<img src="https://cdn-std.droplr.net/files/acc_498334/VzdYQS" alt="45dB transfer function" width="550">

### Temperatures

Also tested the temperatures of the following components with an [infrared thermometer](https://www.amazon.com.au/gp/product/B07LBNWQXV/):

`U13`: 48°C
`U14`: 53°C
`U15`: 53°C
`R31`: 45°C

### Note

All of the transfer functions have a rolloff at around 10kHz.

Possible reasons for the rolloff:

I forgot to connect the three wires to the filters in the channel. According to the schematic of the [zero-pole stage](https://cdn-std.droplr.net/files/acc_498334/DikTXb), `MAX4659EUA+` has an input of low (0), so the filter is activated (instead of being bypassed).

### Next Steps

1. Connect the three wires to provide a high voltage for the filter stages, so that they are bypassed, and the expected outcome would not show any rolloff at higher frequency.
2. Test the other channels and provide the high voltages for the filter stages to bypass the filters. And if the bypassing is successful, we don't need to solder the `C5` part with the wire based on the schematic.

### Updates

1. [30/06/2022] Connect the three wires to the filters, and test the
