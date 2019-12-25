# kostal-berry
Accessing a Kostal Piko inverter via Raspberry Pi

## Prerequisites

### Hardware
- Wi-Fi available, SSID with PSK configured
- Kostal Piko inverter, located in the basement, no cable connection to LAN possible
- 1x network cable to hook up the Raspberry Pi 3b+ link-local to the Kostal Piko inverter
- Raspberry Pi 3b+
- 32GB miniSD card for running an OS on Raspberry Pi 3b+
- Windows PC for formatting & flashing the miniSD card

### Software
- Tool to format the miniSD card ( https://www.sdcard.org/downloads/formatter/ )
- Tool to flash the miniSD card, ( https://sourceforge.net/projects/win32diskimager/ )
- Tool to connect to OS on the Raspberry Pi 3b+ ( https://www.putty.org/ )

### OS
- Linux OS, i picked HypriotOS v1.11.4 ( https://blog.hypriot.com/downloads/ )
    - easy-to-use initial setup (eg. hostname, user, locale, timezone, packages, Wi-Fi)
    - comes with pre-installed Docker

## 1.) Setup the Raspberry Pi 3b+
- format the miniSD card ( see Software )
- download Hypriot OS, unzip the .zip file to .img file
- flash the miniSD card with the .img file ( see Software )
- activate Wi-Fi by removing the leading `# ` in the Wi-Fi section of the `user-data`file. (Caution: the file needs to be YAML compliant)
```
# # WiFi connect to HotSpot
# # - use `wpa_passphrase SSID PASSWORD` to encrypt the psk
# write_files:
#   - content: |
#       allow-hotplug wlan0
#       iface wlan0 inet dhcp
#       wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
#       iface default inet dhcp
#     path: /etc/network/interfaces.d/wlan0
#   - content: |
#       country=de
#       ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
#       update_config=1
#       network={
#       ssid="YOUR_WIFI_SSID"
#       psk="YOUR_WIFI_PASSWORD"
#       proto=RSN
#       key_mgmt=WPA-PSK
#       pairwise=CCMP
#       auth_alg=OPEN
#       }
#     path: /etc/wpa_supplicant/wpa_supplicant.conf

# These commands will be ran once on first boot only
runcmd:
  # Pickup the hostname changes
  - 'systemctl restart avahi-daemon'

#  # Activate WiFi interface
#  - 'ifup wlan0'
```
- make sure to set your Wi-Fi SSID in `ssid=""`
- make sure to set your Wi-Fi SSID PSK in `psk=""`
    - (optional) make additional changes __before the first run__ , editing the `user-data` file (eg. hostname, user, locale, timezone, packages)
- when done, insert the miniSD card into the Raspberry Pi 3b+ and power it up
- after a few minutes check for the Raspberry Pi 3b+ IP address. This can be done by checking your Wi-Fi DHCP server for new DHCP leases.

## 2.) Connect the Raspberry Pi 3b+ to your Kostal Piko inverter


