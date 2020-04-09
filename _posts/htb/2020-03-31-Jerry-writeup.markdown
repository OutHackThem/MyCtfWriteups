---
layout: post
title: Jerry
date: 2020-03-31
author: th3-3inst3in
comments: true
categories: htb
tags: [Windows,htb]
---

## Legacy writeup

Easy linux machine

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Jerry/main.png)


Let's do a quick nmap all ports

```perl
Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-31 09:23 EDT
Nmap scan report for 10.10.10.95
Host is up (0.16s latency).

PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.78 seconds                               
```

<br>

### Enumeration

So tomcat is running on port 8080 I used the following default passwords and one of them worked, I'll leave that up to you.
https://github.com/netbiosX/Default-Credentials/blob/master/Apache-Tomcat-Default-Passwords.mdown  


![Tomcat Manager]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Jerry/tomcatManager.png)


### Exploitation

Used msf to upload shell to tomcat

![Shell Upload]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Jerry/shellUpload.png)


and I dropped directly in to a root  / system shell

```perl
meterpreter > shell
Process 3 created.
Channel 4 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\apache-tomcat-7.0.88>whoami
whoami
nt authority\system

```
That's all pretty simple