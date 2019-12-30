# kostal-berry

Our Kostal Piko 10.1 inverter is located in the basement, the inverter is not connected to the LAN. Let's hook up the inverter to a Raspberry Pi 3b+ which can easily be connected to our Wi-Fi.
Use a reverse proxy (eg. NGINX) to redirect specific requests coming from our local network to the Kostal Piko 10.1 inverter, to be able to connect with either WebUI or the query tool provided by Kostal.

## Prerequisites

### Hardware
- Wi-Fi available, SSID with PSK configured
- Kostal Piko inverter + username & password ( default: _pvserver_ / _pvwr_ )
- 1x network cable to hook up the Raspberry Pi 3b+ link-local to the Kostal Piko inverter
- Raspberry Pi 3b+
- 32GB miniSD card for running an OS on Raspberry Pi 3b+
- Windows PC for formatting & flashing the miniSD card, also using your favorite web browser to connect to the Kostal Piko WebUI

### Software
- Tool to format the miniSD card ( https://www.sdcard.org/downloads/formatter/ )
- Tool to flash the miniSD card, ( https://sourceforge.net/projects/win32diskimager/ )
- Tool to connect to OS on the Raspberry Pi 3b+ ( https://www.putty.org/ )

### OS
- Linux OS, i picked HypriotOS v1.11.4 ( https://blog.hypriot.com/downloads/ )
    - easy-to-use initial setup (eg. hostname, user, locale, timezone, packages, Wi-Fi)
    - comes with pre-installed Docker

## Step 1 - Setup the Raspberry Pi 3b+
- format the miniSD card ( see Software )
- download Hypriot OS, unzip the .zip file to .img file
- flash the miniSD card with the .img file ( see Software )
- activate Wi-Fi by removing the leading `# ` in the Wi-Fi section of the `user-data`file.( Caution: Keep the file YAML compliant.)
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
#   - 'ifup wlan0'
```
- make sure to set your Wi-Fi SSID in `ssid=""`
- make sure to set your Wi-Fi SSID PSK in `psk=""`
    - (optional) make additional changes __before the first run__ , editing the `user-data` file (eg. hostname, user, locale, timezone, packages)
- when done, insert the miniSD card into the Raspberry Pi 3b+ and power it up
- check for the Raspberry Pi 3b+ IP address. This can be done by checking your Wi-Fi DHCP server for new DHCP leases (example: `192.168.178.26`)
- connect to the Raspberry Pi 3b+ using a ssh client (see Software)
- login with `username` and `password` (default: _pirate_ / _hypriot_)

## Step 2 - Connect the Raspberry Pi 3b+ to your Kostal Piko inverter
- connect the Raspberry Pi 3b+ to the Kostal Piko inverter, using a network cable
- find out the IP address of the Kostal Piko inverter in the menu of the inverter (example: `169.254.109.206/16`)
- ping the IP address (example: `ping 169.254.109.206`). If you get an answer, you can reach the inverter and you're ready to go.

## Step 3 - Setup a reverse proxy as a OS native service
- Install NGINX
  - `sudo apt-get autoclean`
  - `sudo apt-get update`
  - `sudo apt-get install -y nginx`
- Remove default site in NGINX
  - `sudo unlink /etc/nginx/sites-enabled/default`
- Create a new config file in NGINX using this as a template
  - `cd /etc/nginx/sites-available/`
  - `sudo vi reverse-proxy`
```
  server {
    listen 8888;

    error_log   /var/log/nginx/error.log  warn;
    access_log  /var/log/nginx/access.log;

    location / {
        proxy_pass http://169.254.109.206:80/;
    }
}
```
- Edit the `proxy_pass http://` with the link-local IP of the inverter (see Step 2)
- Link the new config file in NGINX 
  - `sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/reverse-proxy`
- Test the new config file in NGINX
  - `sudo nginx -t`
- Restart NGINX with the new config file
  - `sudo systemctl restart nginx`

## Step 4 - Login via WebUI
- Connect to the WebUI of the Kostal Piko inverter using the correct URL
  - the Raspberry Pi 3b+ IP address (example: `192.168.178.26`)
  - the port configured in the `listen` directive of the reverse proxy (example: `8888`)
  - in this combined example: `http://192.168.178.26:8888/`
- Login using the correct credentials (see Hardware)

## (Optional) Step 3 - Setup a reverse proxy as a docker container
- instead of using the OS native nginx service described in Step 3, you can also setup nginx as a docker container
- https://github.com/gmzabos/kostal-berry/tree/master/docker-nginx
