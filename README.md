# dspW245

A Python3 library used to interface with the D-Link DSP-W245 and DSP-W115.

Please note that some errors might occur when interfacing with the W115 since 
it seems to return different messages than the W245. To get around this you could
remove lines 247 to 249 (including).

The library is somewhat lacking in terms of what it can do. It could be
expanded to support more operations. It could also be refactored to look
prettier. I might revisit this in the future, but for the time being it is
at least capable of turning each socket on/off.

Any help will gladly be accepted. Don't hesitate to open an issue or submitting
a pull request.

## Disclaimer

As long as the library is only used for turning the sockets on or off, the
library should be safe to use. Although it also supports other functions such
as changing the firmware of the device. These functions can brick your device,
use them at your own risk.

## Usage

The easiest way to use this library is to use the PIN code of the device as
authentication. This requires the device to not be paired with the mydlink app.
The easiest way to have the device connected to the correct network and not
remain paired with the app is to follow the steps below.

1. If needed. Remove the device from mydlink.
2. Start the setup process of pairing the device with mydlink.
3. Abort the setup process once the device is connected to the desired network.

The device can then be interfaced with using the code below.

```
#!python3
from dspW245 import SmartPlug

# The latter part is the PIN code.
sp = SmartPlug("192.168.0.20", "000000")

# Turn socket [1,2,3,4] on or off.
sp.set_socket(1, on=True)
sp.set_socket(4, on=False)

# Upgrades the firmare to the firmware found at the provided url.
sp.upgrade_fw("http://example.com/somefirmware")

# Used to avoid the connection from timing out.
sp.keep_alive()

sp.close()
```

If the device is paired with the mydlink app, you need to use the device token
instead of the PIN. This token can be a bit tricky to get but can be obtained
through the following steps.

1. Set the device to factory mode. This is done using the below steps. It's
possible that this step requires the device to be removed from mydlink.
    1. Reset the device into recovery mode by holding the reset button during
  boot. If done correctly, a telnet server should be running on the device.
    2. Connect to the WiFi of the device and open a terminal.
    3. Run `telnet 192.168.0.20` (default credentials are `admin:123456`).
    4. Run `nvram_set FactoryMode 1`.
    5. Run `reboot; exit;`
2. If needed. Setup the device with mydlink like normal again.
3. Run `telnet 192.168.0.20` when connected to the same network as the device.
4. Run `cat /mydlink/config/device.cfg`. Copy the value for `DeviceToken`. The config file may also be present at `/mnt/user`.

You can then control the device using the token instead.

```
sp = SmartPlug(IP, "00A00A000000-511ea125-250b-0dc1-40f0-c6570ebc51a2")
```

Note that for the W115 it would seem that the token will reset from time to time.

While the library has only been tested on the W245, it's possible that it will
still work with other similar models like the W115. If another model than the
W245 is used, the model can be specified as below:

```
sp = SmartPlug("192.168.0.20", "000000", model="W115")
```

If the library is compatible with other models, let me know and I'll add the
information here.

## Support

Works with the DSP-W245 on firmware versions:

* `3.0.0-b45`
* `3.3.0-b03`

Works with the DSP-W115 although this potentially requires the removal of lines 247 to 249 (included) (thanks Garfonso).

## Other

A node.js version by @Garfonso can be found be found here: 
https://github.com/Garfonso/dlinkWebSocketClient
