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

Use [angristan's OpenVPN install script](https://github.com/angristan/openvpn-install).

### Port forwarding

1. Stop the iptables-openvpn service: `service iptables-openvpn stop`
2. Add to `/etc/iptables/add-openvpn-rules.sh`:
```
iptables -t nat -A PREROUTING -d PublicIP -p tcp --dport 51413 -j DNAT --to-dest 10.8.0.2:51413
iptables -t filter -A INPUT -p tcp -d 10.8.0.2 --dport 51413 -j ACCEPT
```
3. Add to `/etc/iptables/rm-openvpn-rules.sh`:
```
iptables -t nat -D PREROUTING -d PublicIP -p tcp --dport 51413 -j DNAT --to-dest 10.8.0.2:51413
iptables -t filter -D INPUT -p tcp -d 10.8.0.2 --dport 51413 -j ACCEPT
```
4. Start the iptables-openvpn service: `service iptables-openvpn start`

## Other configuration

Allow an executable to bind reserved ports without being launched as root:
```
apt-get install libcap2-bin
setcap cap_net_bind_service=+ep <executable>
```
