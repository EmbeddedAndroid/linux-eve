# linux-eve
Linux Support for the Eve Chromebook (Pixelbook i7)

## Linux Hardware Support (v4.14)
### Functional:
  * Webcam
  * USB
  * Keyboard Backlight
  * Intel WiFi and Bluetooth
  * PCIe NVME Hard Drive
  * Wacom Touch Screen
  * Touchpad (needs pressure tweaks for gestures, see below)
  * ACPI Buttons
  * Backlight control (needs some custom scripts, see below)
  * Audio (needs chromeos firmware, see below)
    * Microphone
    * Headphone Jack
    * External Speakers
### Needs Debugging
  * Audio Routing
    ```
    Kbl Audio Port: ASoC: no backend DAIs enabled for Kbl Audio Port
    ```

## Overview

This is a guide to setup a native Linux install on your Eve Chromebook. I have chosen Ubuntu 18.04 LTS (daily snapshot) + Mainline v4.14 kernel as my target.

### Setup

  * Enable Developer Mode
  * Install the latest SeaBIOS firmware (the stock firmware won't detect NVME hard drive)
    * https://mrchromebox.tech/#fwscript
    * Run the command in the terminal, and select the "Install/Update the RW_LEGACY firmware" option
    * The script will ask if you want to enable USB boot by default, select YES. (So you can install a distro from a USB stick)
  * Download Ubuntu 18.04 LTS, and create a bootable USB drive.
  * Attach USB drive through a TYPE-C USB adapter, and reboot.
  * At the OS verification prompt, issue a CTRL-L and ESC and select the USB drive as the boot option.
  * Install the OS to the hard drive, the touchpad pressure sensitivity defaults are horrid, and life might be easier for you to use a USB mouse.
  * After the install is done, reboot, and boot the machine via Legacy mode off the internal hard drive. 

### Tweaks
#### Kernel
  * Install the latest released Linux Kernel - http://kernel.ubuntu.com/~kernel-ppa/mainline/
#### Touchpad
  * Fix the touchpad pressure sensitivity 
    * As root create a file at /etc/udev/hwdb.d/99-touchpad-pressure.hwdb
    * My configuration is listed below, if you don't like it, knock yourself out - https://wayland.freedesktop.org/libinput/doc/latest/touchpad_pressure.html#touchpad_pressure_hwdb
    ```
    libinput:name:*ACPI0C50:00 18D1:5028 Touchpad:dmi:*svnGoogle:*pnEve*
    LIBINPUT_ATTR_PRESSURE_RANGE=15:35
    ```
    * Once you have added the configuration, run the follow command to update
    ```
    sudo udevadm hwdb --update
    ```
    * Reboot
#### Backlight
  *  Clone this repo, and run the scripts/backlight/setup-systemd.sh
    * This will install a simple service to enable and control the backlight
  * Reboot
  * Map the brightness
    * Setting -> Keyboard -> Custom
    * Increase brightness = /usr/bin/local/brightness --increase
    * Decrease brightness = /usr/bin/local/brightness --decrease
### Audio
  * Copy the dfw_sst.bin firmware provided in this repo to /lib/firmware
  * Add a blacklist entry in /etc/modprobe.d/blacklist.conf
    ```
    blacklist snd_hda_intel
    ```
