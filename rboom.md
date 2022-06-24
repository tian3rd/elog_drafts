# [RBOOM] [Raspberry Boom (RBoom) Infrasound Monitor](https://manual.raspberryshake.org/boom.html) Deployed and Mounted

> The static ip address is `192.168.1.82` (`rshakepi`). You can `ssh` into it by `ssh controls@192.168.1.82` with any of the lab machines. There's also a default web portal which can be accessed by typing `rs.local` in the browser.

[RBOOM (product)](https://shop.raspberryshake.org/product/turnkey-iot-atmospheric-infrasound-monitor-rboom/) was designed to record the imperceptible “noises” in the form of infrasonic signals that bounce around our atmosphere. A 20 seconds filter (yellow band) is installed instead of the default 1s filte (red band).

## EPICS channels

Check the updated in the file `/opt/rtcds/anu/n1/chans/daq/N1FE1_EDC.ini`.

[N1:PEM-LAB_INFRASOUND_COUNT]
[N1:PEM-LAB_INFRASOUND_PRESSURE]
units=Pa
[N1:PEM-LAB_INFRASOUND_FREQ]
units=Hz

### Restart Data Concentrator

Follow the following steps after modifing the `.ini` file:

```bash
# login to n1fe1:
ssh n1fe1
# to restart the data concentrator do:
sudo systemctl restart rts-edc
# check it it is happy:
sudo systemctl status rts-edc
# login to n1fb10: $
ssh n1fb10
# to restart the daqd do:
sudo systemctl restart rts-daqd
# check it it is happy:
sudo systemctl status rts-daqd
```

### Souce code of services and scripts

Refer to [GitHub Repo](https://github.com/OZGrav/rshake_ctrl) for more info.

### Access the `medm` screen

The `medm` file is located at `/opt/rtcds/anu/n1/softioc/rshake_ctrl/medm/RSHAKE_OVERVIEW.adl`.

```bash
# check if the local service in rshakepi (192.168.1.82) is running properly:
systemctl status rshake_ctrl_service.service
# open medm screen
medm -x /opt/rtcds/anu/n1/softioc/rshake_ctrl/medm/RSHAKE_OVERVIEW.adl
```

## Mounting

The RBoom has been mounted to the new ISI rack thanks to Sheon.

![Mounted Rboom on ISI rack](https://cdn-std.droplr.net/files/acc_498334/9yX5CT)
(Credit to Sheon)

- If it's mounted too close to the wall, will it affect the readings?

## To-do

- [ ] Restarting the data concentrator (whenever there's modification of `EPICS` channel) when there's no one recording important data in the lab. (Send slack messages in #torpedo channel to confirm)

## Trouble-shooting

1. If the CDS file system is not mounted correctly, the probable reason is that the RBoom is not connected to the CDS network. Check the connection mannually.

## Reference

1. [RBoom specifications PDF](https://manual.raspberryshake.org/_downloads/SpecificationsforBoom_SnB.pdf)
2. [RBoom Manual](https://manual.raspberryshake.org/boom.html)
