Download a youtube video/playlist/channel and convert it to a low quality mp3:
```
youtube-dl --extract-audio --audio-format mp3 --audio-quality 9 --ignore-errors --download-archive downloaded.txt
```

Generate the linux hash of a password using sha512
```
python -c 'import crypt; print crypt.crypt("password", "$6$random_salt")'
```
# Network

| 192.168.1.1 | DD-WRT Router (DIR-615 H1) |
| 192.168.1.2 | Raspberry Pi               |


## Raspberry Pi configuration

Download the [Raspbian Buster Lite](https://www.raspberrypi.org/downloads/raspbian/) image, and write it to the an card:
```
unzip -p 2019-09-26-raspbian-buster-lite.zip | sudo dd of=/dev/sdb bs=4M conv=fsync
```

Create a file named `ssh` at the root of the Raspberry Pi boot partition
```
sudo mount /dev/sdb1 some_folder
sudo touch some_folder/ssh
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
