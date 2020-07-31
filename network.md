# Network

| IP Address  | What's there               | Usage
| ----------- | -------------------------- | ------------------- |
| 192.168.x.1 | DD-WRT Router (DIR-615 H1) | Wifi                |
| 192.168.x.2 | Raspberry Pi               | DNS and DHCP server |


## DD-WRT Router configuration: basic settings

Under Setup > Basic Setup > Network Setup > Router IP, change the Local IP Address.Under Setup > Basic Setup > Network Setup, change the Local IP Address

Under Setup > Basic Setup > Network Setup > Time Settings, change the Time Zone

Under Wireless > Wireless Security, set:
* Security Mode: WPA2-PSK
* WPA Algorithms: CCMP-128 (AES)
* WPA Shared Key: 12 chars with at least 72 bits of entropy
```
dd if=/dev/urandom count=1 2>/dev/null | base64 | head -1 | cut -c4-15
```

Generate a QR code for easy wifi setup:
```
qrencode -o wifi.png "WIFI:S:<yournetworkssid>;T:WPA2;P:<wpa2passphrase>;;"
```

## Raspberry Pi configuration

Download the [DietPi](https://dietpi.com) image, and write it to the an SD card:
```
sudo dd if=DietPi_RPi-ARMv6-Buster.img of=/dev/sdb bs=4M conv=fsync; sync
```

Mount the boot partition:
```
sudo mount /dev/sdb1 some_folder
```

In dietpi.txt, update the configuration as below
* AUTO_SETUP_HEADLESS=1
* CONFIG_CPU_GOVERNOR=powersave

Unmout the boot partition:
```
sudo umount some_folder
```

Connect to the raspberry pi as root via ssh, default password is `dietpi`, DietPi installer will run on first logon.

Run `dietpi-config` tool, and modify the ethernet configuration:
* Set the static IP to: 192.168.x.2
* Set the gateway to: 192.168.x.1
* Purge the wifi packages

## Pi-hole configuration

Install Pi-hole:
```
curl -sSL https://install.pi-hole.net | bash
```


Set a new password
```
sudo pihole -a -p
```

Under Group management > Adlists, update the configuration to have the below lists:
```
https://mirror1.malwaredomains.com/files/justdomains
https://s3.amazonaws.com/lists.disconnect.me/simple_tracking.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt
https://raw.githubusercontent.com/StevenBlack/hosts/master/alternates/fakenews-gambling-porn/hosts
https://block.energized.pro/unified/formats/hosts
https://block.energized.pro/extensions/xtreme/formats/hosts
https://block.energized.pro/extensions/porn-lite/formats/hosts
https://block.energized.pro/extensions/regional/formats/hosts
```

## DD-WRT Router configuration: disable DHCP server

Under Setup > Basic setup > Network Setup > Network Address Server Settings (DHCP):
* Set DHCP Server to Disable

## Raspberry Pi configuration: DHCP server

Under Settings > DHCP:
* Check DHCP server enabled

## Unbound configuration

Follow this tutorial: [Pi-hole as All-Around DNS Solution](https://docs.pi-hole.net/guides/unbound/)

Use the below file as root.hints (provided by package `dns-root-data`)
```
/usr/share/dns/root.hints
```

## DD-WRT Router configuration: Pi-hole as default DNS Server

Under Services > Services Management > Dnsmasq, add the below under "Additional Dnsmasq Options":
```
dhcp-option=6,192.168.1.2
```

## DD-WRT Route configuragion: block P2P

Under Access Restrictions > WAN Access > Add a new policy with the below (leave everything else as default)

| Parameter               | Value                       |
| ----------------------- | --------------------------- |
| Status                  | Enable                      |
| Policy Name             | P2P                         |
| PCs                     | 192.168.1.0 ~ 192.168.1.254 |
| Catch all P2P Protocols | Checked                     |
