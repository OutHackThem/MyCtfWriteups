---
layout: post
title: AI Web 2
date: 2019-09-15
author: OutHackThem
comments: true
categories: vulnhub
tags: [ctf,vulnhub]
---

## AI Web 2 Vulnhub

Getting the IP of the AIweb2 machine I ran a directory search + a nmap scan after doing so many ctfs this should be like apart of your intuition

This is the first page I got running on port 80

![main page]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/index.png)

I clicked on the signup button created a user and logged in.


![logged in]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/login.png)


after that I went to google and searched for XuezhuLi FileSharing exploit and luckly me I found a path traversal and local file disclosure exploit


![local file disclosure exploit]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/lfd.png)

using the exploit I was able to read local files

![etc file]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/etcfile.png)

Though I did try for sql injection in the inital login and signup but no seed 



Well I was a bit stuck from here then after looking around in the dirsearcher results found that there is a http auth o  `/webadmin` 

now the thing is basicAuth passwords are always kept in a file that means I just have to find the location of the file that has the password
first guess was to look inside the public_html apache directory but nothing there then next guess was to check the /etc/apache2 directory 


![htpasswd]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/htpasswd.png)


confused on how I found the .htpasswd thing ? go here https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-14-04


I used john to crack the .htpasswd file 

Username: aiweb2admin

Password: c.ronaldo


ran dirsearch after I logged in and found the following 

![htpasswd]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/dirsearch.png)

I opened the first directory and happen to find a command injection 


![htpasswd]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/cmdinj.png)


got a reverse shell from there


```bash
www-data@aiweb2host:/var/www/html$ ls -lRah
ls -lRah
.:
.......

./webadmin/H05Tpin9555:
total 128K
drwxr-xr-x 2 www-data www-data 4.0K Sep 13 05:27 .
drwxr-xr-x 4 www-data www-data 4.0K Aug 28 12:19 ..
-rwxr-xr-x 1 www-data www-data  45K Sep 13 05:21 LinEnum.sh
-rw-r--r-- 1 www-data www-data  45K Sep 13 05:27 LinEnum.sh.1
-rw-r--r-- 1 www-data www-data 1.9K Aug 28 10:34 index.php
-rw-r--r-- 1 www-data www-data   27 Sep 13 05:05 sh.php
-rw-r--r-- 1 www-data www-data   29 Sep 13 05:07 shell.php
-rw-r--r-- 1 www-data www-data  12K Aug 18 09:08 style-main.css

./webadmin/S0mextras:
total 16K
drwxr-xr-x 2 www-data www-data 4.0K Aug 29 12:22 .
drwxr-xr-x 4 www-data www-data 4.0K Aug 28 12:19 ..
-rw-r--r-- 1 www-data www-data   49 Aug 29 12:11 .sshUserCred55512.txt
-rw-r--r-- 1 www-data www-data  135 Aug 29 12:22 index.html

```

in the sshUserCred55512.txt 

I found this

User: n0nr00tuser
Cred: zxowieoi4sdsadpEClDws1sf  

then sshed in 


I found out that the use is apart of the lxd group now if you do a bit of searching on that you'll find that there is an exploit to escape out of the lxd group 

n0nr00tuser@aiweb2host:~$ id
uid=1001(n0nr00tuser) gid=1001(n0nr00tuser) groups=1001(n0nr00tuser),108(lxd)


used this  exploit
https://www.exploit-db.com/exploits/46978

and simple as 1 2 3 , got the shell 


![htpasswd]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb2/root.png)
