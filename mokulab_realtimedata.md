# Problems in getting real-time signals around 0V from Moku Lab API

## Script used to fetch the data from Moku Lab

```python
# this is a jupyter notebook code block with some plotting plugins
# copy to jupyter notebook, and open it in a browser to see the plots

import time
from datatime import datetime
import matplotlib.pyplot as plt
from pymoku import Moku
from pymoku.instruments import Oscilloscope

# plotting setup
%matplotlib notebook
plt.rcParams['animation.html'] = 'jshtml'

fig = plt.figure()
ax = fig.add_subplot(111)
plt.show()

# Connect to your Moku by its device name
# Alternatively, use Moku.get_by_serial('#####') or Moku('192.168.###.###')
m = Moku.get_by_serial('000818')
print(f'Moku found:\n\
        IP: {m.get_ip()}\n\
        Name: {m.get_name()}\n\
        Version: {m.get_version()}\n\
        Build: {m.get_firmware_build()}')

try:
    i = m.deploy_or_connect(Oscilloscope)

    # leave all the settings unchanged, only set the timebase for now
    fetching_freq = 10
    # i.set_timebase(0, 1.0 / freq / 10) to get the first 1/10 th of the data
    tb = 1.0 / fetching_freq / 10
    i.set_timebase(0, tb)

    x, y = [], []

    # counter to keep track of time elapsed
    counter = 0

    # record for e.g. 15s
    end_time = time.time() + 15

    while 1:
        start = time.time()
        elapsed = counter * 1.0 / freq
        data = i.get_realtime_data()
        print("Waveform id: {data.waveformid}, \
            timestamp: {datetime.fromtimestamp(time.time()).strftime('%Y-%m-%d %H:%M:%S')}\
            ")

        x_data = data.time
        y_data = data.ch1
        x_avg = elapsed + sum(x_data)/len(x_data)
        y_avg = sum(y_data)/len(y_data)
        counter += 1

        # add new point to the plot
        x.append(x_avg)
        y.append(y_avg)
        ax.plot(x, y, color='b')
        fig.canvas.draw()

        now = time.time()
        # stop after 15s
        if now > end_time:
            break
        # otherwise keep on waiting until 1.0/fetching_freq (0.1s or 10Hz) has passed
        while now - start < 1.0 / fetching_freq:
            now = time.time()

except Exception as e:
    print(e)

finally:
    m.close()
```

## Description of the problem

When using the [`get_realtime_data()`](https://pymoku.readthedocs.io/en/2.8.3/oscilloscope.html#pymoku.instruments.Oscilloscope.get_realtime_data), we got the same waveform if the voltage fell around 0V for around 0.5s. This is not normal because we are fetching the data at 10Hz, and the `get_realtime_data()` should return a new object with new waveform data at 10Hz by default. Even if we set it to be 20Hz or 27Hz (maximum), the waveform still got stuck and we kept getting the same old data from this function. This problem is confirmed by printing out the returned waveform id which should be unique if there's new data.

Visually, the sine wave will show a flat line around 0V.

![ndscope scrot 1](https://cdn-std.droplr.net/files/acc_498334/f9GmKV)

![sine wave flat line 1](https://cdn-std.droplr.net/files/acc_498334/KJ6O35)

Whether we use a sine wave or a ramp wave, whenever it hit 0, the flat line appeared.

![ramp wave flat line 1](https://cdn-std.droplr.net/files/acc_498334/2S6EaR)

## One way to get rid of the flat line

If we add an offset to the signal (keep this as the only difference), so that the signal don't across value 0, then the flat lines are gone. See the screenshots, notice the y-axis value:

![sine wave offset 1](https://cdn-std.droplr.net/files/acc_498334/eCC0zq)

![ramp wave offset 1](https://cdn-std.droplr.net/files/acc_498334/QT7v1R)

## Possible reason for this flat line

1. @YaZhang brought up the possbility of data conversion around 0.
2. I think maybe there's a downsampling issue or implementation in the API that's affecting the data when it's hitting 0, becasue the Moku app shows no such flat lines.
