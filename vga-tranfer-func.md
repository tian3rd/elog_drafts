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

## Updates

### [30/06/2022] Results

1. Connect the three wires to the filters, and test the transfer functions.
   1. 0dB ![0dB no filters](https://cdn-std.droplr.net/files/acc_498334/oBFAf7)
   2. 21dB ![21dB no filters](https://cdn-std.droplr.net/files/acc_498334/AkfbDY)
2. Turn on the filters, and test the transfer functions.
   1. Gain is set to 0dB
      1. Turn on 1st filter ![0dB 1st filter](https://cdn-std.droplr.net/files/acc_498334/9WSwc9)
      2. Turn on 1st & 2nd filters ![0dB 1st & 2nd filters](https://cdn-std.droplr.net/files/acc_498334/cRJ9WK)
      3. Turn on all filters ![0dB all filters](https://cdn-std.droplr.net/files/acc_498334/JGR4oP)
   2. Gain is set to 21dB
      1. Turn on 1st filter ![21dB 1st filter](https://cdn-std.droplr.net/files/acc_498334/8fetsP)
      2. Turn on 1st & 2nd filters ![21dB 1st & 2nd filters](https://cdn-std.droplr.net/files/acc_498334/l2d0Zr)
3. Switch to the next channel to test the transfer functions. Since the `C5` part is disconnected, I didn't test the filters.
   1. Channel B 0dB ![0dB channel B](https://cdn-std.droplr.net/files/acc_498334/zNun1D)
   2. Channel B 12dB ![12dB channel B](https://cdn-std.droplr.net/files/acc_498334/5xghX8)

### [30/06/2022] Analysis

1. The rolloff is universal in each transfer function even when the gain and filter stages are bypassed.
2. When `C5` is shorted, the filters act like gains, and the schematics are quite similiar.
3. The differential receiver and differential driver can be the cause of the 10k rolloff. Need to find a way to test them with oscilloscope separately.
   1. To start with, maybe test the output from `TP1` and GND to see if the differential receiver is causing the rolloff. Note: make sure all gain and filter stages are bypassed.
   2. Similar for the differential driver, test the output from `TP7` and `TP9`.

### [01/07/2022] Results

1. Still channel B with 0dB, but the analog output is connected to test point 14 (`TP14`) and `TP9` (GND). This part is to test if there's a rolloff just from the differential receiver part of the circuit.

![0dB channel B tp14 tp9](https://cdn-std.droplr.net/files/acc_498334/3OFBoe)

We can see that the rolloff still exists and it is at around 10kHz.

### [01/07/2022] Analysis

![differential receiver shematic](https://cdn-std.droplr.net/files/acc_498334/dV2Vbu)

### [07/07/2022] Results of the Noises

Noise at test points `TP14` (after the differential receiver).

1. `SR785` spectrum analyzer is used to measure the noise, (since `AD2` has an internal noise floor that's higher than the measured components, so it's not used here).

![noise at tp14 measured by sr785](https://cdn-std.droplr.net/files/acc_498334/2wMql4)

Frequency range: 0 - 1kHz
Typical noise: 25 nVrms/sqrt(Hz), averaging around 30 nVrms/sqrt(Hz) in the range

### [07/07/2022] Analysis

It's consistent with the [datasheet of the op amp LT1125CS](https://www.analog.com/media/en/technical-documentation/data-sheets/11245ff.pdf) (2.7 nV/sqrt(Hz) typical) which is the main part of the differential receiver. Due to the interference of other components on the board, a magnitude higher is normal, and the value is acceptable for the our purpose in using VGA board.
