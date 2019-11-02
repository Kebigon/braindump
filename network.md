# Network

| IP Address  | What's there               |
| ----------- | -------------------------- |
| 192.168.1.1 | DD-WRT Router (DIR-615 H1) |
| 192.168.1.2 | Raspberry Pi               |


## Raspberry Pi configuration

Download the [DietPi](https://dietpi.com) image, and write it to the an SD card:
```
sudo dd if=DietPi_v6.25_RPi-ARMv6-Buster.img of=/dev/sdb bs=4M conv=fsync
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

## DD-WRT Router configuration: Pi-hole's static lease

Under Services > Services Management > DHCP Server, add a static lease for the Raspberry Pi.

## Pi-hole configuration

Install Pi-hole:
```
curl -sSL https://install.pi-hole.net | bash
```

Set a new password
```
sudo pihole -a -p
```

Under Settings > Blocklists, update the configuration to have the below lists:
```
https://raw.githubusercontent.com/StevenBlack/hosts/master/alternates/fakenews-gambling-porn/hosts
https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_malware.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_tracking.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt
https://mirror1.malwaredomains.com/files/justdomains
https://hosts-file.net/ad_servers.txt
http://sysctl.org/cameleon/hosts
```

Under Settings > DNS > Upstream DNS Servers, configure set one of the [OpenNIC](https://www.opennic.org/) DNS server as primary, and the router (192.168.1.1) as the secondary.

## DD-WRT Router configuration: Pi-hole as default DNS Server

Under Services > Services Management > Dnsmasq, add the below under "Additional Dnsmasq Options":
```
dhcp-option=6,192.168.1.2
```
