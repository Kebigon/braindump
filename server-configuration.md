# Server configuration

## System configuration

Connect as root and change the root password:
```
passwd
```

Create normal users
```
adduser user1
adduser user2
```

## SSH configuration

Edit the SSH service configuration
```
nano /etc/ssh/sshd_config
```

Change the port that the SSH service runs on
```
Port 1234
```

Disable root login
```
PermitRootLogin no
```

Limit the users who can connect through SSH
```
AllowUsers user1 user2
```

Check configuration
```
egrep "Port|PermitRootLogin|AllowUsers" /etc/ssh/sshd_config
```

Restart the servive
```
service ssh restart
```

## Firewall

Install UFW:
```
apt-get install ufw
```

Block everything by default:
```
ufw default deny incoming
ufw default deny outgoing
```

Allow SSH:
```
ufw allow 1234/tcp
```

Allow HTTP/HTTPS:
```
ufw allow 80/tcp
ufw allow 443/tcp
ufw allow out 80/tcp
ufw allow out 443/tcp
```

Allow DNS:
```
ufw allow out 53/udp
```

Allow SMTP:
```
ufw allow out 465/udp
```

Enable the firewall/check the status:
```
ufw enable
ufw status
```

## OpenVPN

Use [Nyr's OpenVPN install script][https://github.com/Nyr/openvpn-install].

## Other configuration

Allow an executable to bind reserved ports without being launched as root:
```
apt-get install libcap2-bin
setcap cap_net_bind_service=+ep <executable>
```
