# kostal-berry
Accessing the Kostal Piko inverter via Raspberry Pi

## Prerequisites

### Hardware
- Wi-Fi available, SSID with PSK configured
- Kostal Piko 10.1 inverter, located in the basement, no cable connection to LAN possible
- 1x network cable to hook up the Raspberry Pi 3b+ link-local to the Kostal Piko 10.1 inverter
- Raspberry Pi 3b+
- 32GB miniSD card for running an OS on Raspberry Pi 3b+
- Windows PC for formatting & flashing the miniSD card

### Software
- Tool to format the miniSD card ( https://www.sdcard.org/downloads/formatter/ )
- Tool to flash the miniSD card, ( https://sourceforge.net/projects/win32diskimager/ )
- Tool to connect to OS on the Raspberry Pi 3b+ ( https://www.putty.org/ )

### OS
- Linux OS, i picked HypriotOS v1.11.4 ( https://blog.hypriot.com/downloads/ )
    - easy-to-use initial setup (includes: hostname, user, locale, timezone, packages, Wi-Fi)
    - comes with pre-installed Docker

## 1.) Setup the Raspberry Pi 3b+
- format the miniSD card ( see Software )
- download Hypriot OS, unzip the .zip file to .img file
- flash the miniSD card with the .img file ( see Software )
    - (optional) make initial setup changes __before the first run__ , editing the `user-data` file, which is now on the miniSD card

## 2.) Connect the Raspberry Pi 3b+ to your Kostal Piko inverter


