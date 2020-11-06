---
layout: post
title: Nibble
date: 2020-03-31
author: OutHackThem
comments: true
categories: htb
tags: [Linux,htb]
---

## Nibble writeup

Easy linux machine

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Nibble/main.png)


Let's do a quick nmap all ports

```perl
Nmap scan report for 10.10.10.75
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.55 seconds
                              
```

<br>

### Enumeration

After looking at the web app running it had to be vulnerable I tried bruteforcing the login and after alot of bruteforcing and exhausting my brain I took a hint :( from the htb forms I just couldn't figure it out the login details of the web app running yet it was so so simple and I face palmed so hard after finding out. 
User: `admin`
pass: `nibbles`  ( yeah I know bad habbit of making complex shit out of everything)

	

### Exploitation

Used msf to upload shell to tomcat

![Shell Upload]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Nibble/msfshell.png)

Post exploitation was not hard, just a simple `sudo -l` and found out that we can run the monitor.sh script as root echoed `bash -i` in the script and ran it to get root shell. 

![Root]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Nibble/Root.png)

That's all pretty simple