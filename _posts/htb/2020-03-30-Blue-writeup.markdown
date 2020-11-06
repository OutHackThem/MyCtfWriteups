---
layout: post
title: Blue
date: 2020-03-30
author: OutHackThem
comments: true
categories: htb
tags: [Windows,htb]
---

## Blue writeup

Easy Windows machine

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Blue/main.png)


Let's do a quick nmap all ports

```perl

Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-30 04:21 EDT
Nmap scan report for 10.10.10.40
Host is up (0.17s latency).

PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -14m46s, deviation: 34m34s, median: 5m11s
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2020-03-30T09:28:04+01:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-03-30T08:28:00
|_  start_date: 2020-03-30T08:19:18

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 75.01 seconds                              

```

<br>

### Enumeration

- Tried to enumerate using smbclient

```perl

root@kali:~/Desktop/htb# smbclient -L \\\\10.10.10.40\\
Enter WORKGROUP\root's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Share           Disk      
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.10.40 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
root@kali:~/Desktop/htb# 


---------
# Trying to identify the version 

msf5 auxiliary(scanner/smb/smb_version) > exploit

[+] 10.10.10.40:445       - Host is running Windows 7 Professional SP1 (build:7601) (name:HARIS-PC) (signatures:optional)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed


```

### Exploitation

Found vulnerable to eternalblue exploiting which dropped me in a root/ nt authority shell.



![root-msfconsole]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Blue/eternalblue.png)