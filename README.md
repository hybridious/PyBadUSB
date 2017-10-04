# PyBadUSB
PyBadUSB was created to implement BadUSB **ONLY** on a Phison 2303 (2251-03) device using Python.
The project is currently designed to inject USB Rubber Ducky scripts to a Phison 2303 device.  The python module ```pybadusb``` was created to communicate with the specified USB device.

The project was inspired by [adamcaudill/Psychson](https://github.com/adamcaudill/Psychson) and [flowswitch/phison](https://bitbucket.org/flowswitch/phison).

## Requirements
* Python 2.7
* Windows or Linux

## Installation
Run the setup script:
```
python setup.py install
```

## Running
You can run the module as a script:
```
python -m pybadusb
```

Example using Windows:
```
python -m pybadusb --device H --firmware bin/fw.bin --burner bin/BN03V114M.BIN --payload rubberducky/inject.bin
```

Example using a Linux distro:
```
python -m pybadusb --device /dev/sg2 --firmware bin/fw.bin --burner bin/BN03V114M.BIN --payload rubberducky/inject.bin
```

## Example Code
You can also use the module within a python script.

Here's some example code to get you started:
```python
from pybadusb import badusb, phison

device_name = 'H'           # Windows
#device_name = '/dev/sg2'   # Linux

# Set firmware file names
payload  = 'rubberducky/inject.bin'
firmware = 'bin/fw.bin'
burner   = 'bin/BN03V114M.BIN'
embedded = 'hid.bin'

# Get device located at device_name
device = badusb.get_device(phison.Phison2303, device_name)

# Embed firmware
badusb.embed(payload, firmware, embedded)

# Flash new firmware using burner image
badusb.burn_firmware(device, burner, fwfile=firmware)

# Finished
device.close()
```
Another example can be found in [example.py](example.py)

## Firmware
The base firmware you can use is in [bin/fw.bin](bin/fw.bin).
You can compile your own [here](https://github.com/adamcaudill/Psychson/tree/master/firmware).

The burner image used is in [bin/BN03V114M.BIN](bin/BN03V114M.BIN).
Links for finding your own burner image:
* May find one on [usbdev](http://www.usbdev.ru/files/phison/)
* [More info](https://github.com/adamcaudill/Psychson/wiki/Obtaining-a-Burner-Image)

## Rubber Ducky
PyBadUSB is currently only designed to embed compiled USB Rubber Ducky scripts.  The test script used can be found in [rubberducky/keys.txt](rubberducky/keys.txt).

You may create your own according to the [Rubber Ducky format](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Duckyscript) or use one of [these](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads).

Use [DuckEncoder](https://code.google.com/p/ducky-decode/downloads/detail?name=DuckEncoder_2.6.3.zip&can=2&q=) to compile your script:
```
java -jar encoder.jar -i keys.txt -o inject.bin
```
The created binary can then be embedded into the base firmware.  See example code below.

## Phison Device
This project has only been developed to work with USB devices with the Phison 2303 chipset.

It doesn't seem to be documented which USB devices use the chipset, but a list of devices may be found on the [Psychson wiki](https://github.com/adamcaudill/Psychson/wiki/Known-Supported-Devices).

If you are looking for a device, it should be noted that the Phison 2303 chipset is only for USB3.0.
It should also be noted that this project will not work with USB2.0 or lower because they do not use SCSI commands.
More info on this can be found [here](http://en.wikipedia.org/wiki/USB_Attached_SCSI).

You can check a device's chipset using a one of these scripts:  [Windows](win_checkinfo.py) or [Linux](linux_checkinfo.py).

## Module
The Python module is split up into three files:
* [badusb](lib/pybadusb/badusb.py)
  - Used to embed firmware and burn firmware to a device.
* [phison](lib/pybadusb/phison.py)
  - Used to create SCSI commands for the device to get info, burn firmware, etc.
* [scsi](lib/scsi.cpp)
  - Used to send SCSI commands to the device
  - Written in C++ as a Python extension module
