# Configure `TORPEDO_ENV_CTRL` service

## PurpleAir (mac: EC-FA-BC-59-D7-EB) sensor ip address set up

1. PurpleAir is connected to the GW-LAB wifi (`192.168.30.1`) (Refer to the official set-up [guide](https://community.purpleair.com/t/sensor-start-up-guide/182?utm_source=pocket_mylist))
2. The IP address of PurpleAir is set to be `192.168.30.191` (permanent assigned now)

## Torpedo env script updates to the new static ip address

1. One line of change from previous `192.168.30.231` to `192.168.30.191` in function `get_purpleair_data(url)`.
   1. One potential issue can rise from the way `get_purpleair_data` function using `RepeatedTimer` class because it creates a different thread every time the function is called in every `interval` time. Now the `interval` is set to be 10s.
2. If we are in the same network GW-LAB, we can go to `http:192.168.30.191/json` in browser to check the real time data from the PurpleAir sensor.
3. Or we can login to `pemepicspi`, and in the terminal use `curl http:192.168.30.191/json` to make the http get request to show the data.

## Torpedo env script update 3 channels and update their units in .ini file

1. [PEM-LAB_TORPEDO_ENV_CTRL_VERSION], now updated from `1.2c` to `1.2d` (need to sync with GitHub repo [here](https://github.com/OZGrav/torpedo_env_ctrl/blob/main/python/torpedo_env_ctrl_ss.py))
2. [PEM-LAB_TORPEDO_ENV_CTRL_HOST], `pemepicspi`
3. [PEM-LAB_TORPEDO_ENV_CTRL_SERVICE], `torpedo_env_ctrl_service`
4. The newly generated channels in the ini file should be copied to `/opt/rtcds/anu/n1/chans/daq/N1FE1_EDC.ini`
5. E.g., adding `units=celcius` below the channel name [N1:PEM-LAB_TEMP_C] to configure the units for the channel (so we can see the correct y-axis unit in `ndscope`)

## MEDM screen update

1. Now the new screen can be accessed from the `N1SITEMAP.adl` (use `n1sitemap.sh`, or `medm /opt/rtcds/userapps/release/cds/n1/medm/1SITEMAP.adl` to edit & execute)
2. The medm file `TORPEDO_ENV.adl` (`/opt/rtcds/anu/n1/softioc/torpedo_env_ctrl/medm/TORPEDO_ENV.adl`) is copied to `/opt/rtcds/userapps/release/pem/common/medm/PEM_CUST_LAB_ENV.adl`, and synced locally on `pemepicspi`.

## DAQ restart

1. Manually copy the updated channel names and settings to the file `/opt/rtcds/anu/n1/chans/daq/N1FE1_EDC.ini`
2. Restart:

   ```bash
   ssh n1fb10
   sudo systemctl restart rts-daq
   ```

3. Restart:

   ```bash
   ssh n1fe1
   sudo systemctl restart rts-edc
   ```

   Note that the file `/etc/advligorts/edc.ini` (n1fe1) is pointing to `/opt/rtcds/anu/n1/chans/daq/N1FE1.ini`
