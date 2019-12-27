LooperTRX
=========

This tool allows transferring recorded audio data from/to some China-made
looper pedals (see also: https://en.wikipedia.org/wiki/Looper_pedal) over USB.
Unfortunately they are officially only supported by a Windows tool.
To be able to use them with Linux, I analyzed and replicated their USB protocol
in this small script. Though I only tested it with Linux, it should work on all
platforms supported by pyusb (e.g. MacOSX, \*BSD).

I primarily wrote the tool to get support for my "Harley Benton Mini Looper",
but I found that several other brands use the same OEM product; even the
transfer software binaries are almost identical (with minor differences because
of varying company logos).

The compatible loopers that I found so far are:

 * Harley Benton Mini Looper
 * FAME Looper
 * Rowin LEF-332
 * Donner Looper
 * ammoon AP-09 nano looper

Note: The Harley Benton looper has the USB ID 0483:572a, so the software is
looking for these devices. Please let me know if other devices are using
different IDs.

Installation
------------

1. Download the contents of this repository.
2. Run the installation script:

```bash
python setup.py install
```

### macOS Notes ###

The script might not install all dependencies correctly on macOS. If that is the case, try installing them manually. This worked for me using macOS 10.15 Catalina.

Install or upgrade python3 via [homebrew](https://brew.sh):

```bash
brew install python # or
brew upgrade python
```

Install [pyusb](https://walac.github.io/pyusb/) via [pip](https://pip.pypa.io):

```bash
pip3 install pyusb
```

Install [libusb](https://libusb.info) via [homebrew](https://brew.sh):

```bash
brew install libusb
```

Usage
-----

Call it without any parameters to start the graphical interface (if tkinter is available).

For command line usage:

```bash
$ loopertrx.py rx audio.wav
Receiving..... Done.

$ loopertrx.py tx audio.wav
Transmitting..... Done.
```

Data format
-----------

The looper stores files as mono PCM WAV files with 24 bits per sample and a
sample rate of 48000 Hz.
To convert arbitrary audio files to this format, the swiss army knife of sound
processing (sox) can be used:

```bash
$ sox input.mp3 -c1 -r 48000 -b 24 -t wavpcm output.wav
```

External dependencies
---------------------

 * pyusb (https://walac.github.io/pyusb/)
   (On Debian systems: # apt install python3-usb)
 * tkinter [optional] (installed by default on most systems)
   (On Debian systems: # apt install python3-tk)


Linux notes
-----------

To allow unprivileged users access to the devices, a udev rule can be created.
Create the file /etc/udev/rules.d/99-looperpedal.rules with the following content:

```bash
 SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="572a", GROUP="plugdev", MODE="0660"
```

Users in the 'plugdev' group will then be allowed to send/receive data.

Current limitations
-------------------

The official Windows transfer tool does additional audio processing of the
received/sent data (what they store is not the same data that is transferred over USB).
The files are slightly larger in size, perhaps because of some speed adjustments for
local playback.
LooperTRX curently only stores/restores the raw data from the looper devices, without
any modifications. Because of this the audio sounds slightly different when played back
on a computer.
