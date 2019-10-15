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


## DD-WRT Router configuration

Under Services > Services Management > DHCP Server, add a static lease for the Raspberry Pi.

Under Setup > Basic Setup > Network Setup > Network Address Server Settings (DHCP), set the Raspberry Pi's address as Static DNS 1.

## Pi-hole configuration

Install Pi-hole:
```
curl -sSL https://install.pi-hole.net | bash
```

Set a new password
```
sudo pihole -a -p
```

Under Settings > Blocklists, remove all default blocklists and add StevenBlack's "Unified hosts + fakenews + gambling + porn" list:
```
https://raw.githubusercontent.com/StevenBlack/hosts/master/alternates/fakenews-gambling-porn/hosts
```

Under Settings > DNS > Upstream DNS Servers, configure 4 custom DNS from [OpenNIC](https://www.opennic.org/).
