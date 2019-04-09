---
layout: post
title: Leviathan 1-7 Writeup
date: 2019-03-30
author: th3-3inst3in
categories: overthewire
tags: [web,overthewire]
---


## About Leviathan

You do not need any programming knowledge or any sort of hacking knowledge. All you need to know is basic linux/unix CLI knowledge .
    
### Leviathan0

Login to the server with the following ssh creds

` Host: leviathan.labs.overthewire.org Port: 2223 `
* username: leviathan0
* password: leviathan0

You'll land in the home directory of user levathan0 quick _la -la_ you'll see a **.backup** folder cd in to it and you'll notice a file named _bookmarks.html_ cat that and grep for passowrd and you'll find the password for the next level.

![lev1](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/lev1.png))

* * *


### Leviathan1

Using the same host + port  and password gotten from the last level login as user leviathan1.

Do a quick _ls -la_ and you see there is a binary named `check`

* -r-sr-x---  1 leviathan2 leviathan1 7452 Oct 29 21:17 check

```bash
leviathan1@leviathan:~$ ./check
password: testpass
Wrong password, Good Bye ...

leviathan1@leviathan:~$ ltrace ./check
__libc_start_main(0x804853b, 1, 0xffffd794, 0x8048610 <unfinished ...>
printf("password: ")                                                         = 10
getchar(1, 0, 0x65766f6c, 0x646f6700password: wrongpass
)                                        = 119
getchar(1, 0, 0x65766f6c, 0x646f6700)                                        = 114
getchar(1, 0, 0x65766f6c, 0x646f6700)                                        = 111
strcmp("wro", "sex")                                                         = 1
puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
)                                         = 29
+++ exited (status 0) +++
leviathan1@leviathan:~$

```

So looks like our password is >**sex** 

#### About ltrace

`ltrace` s a debugging utility in Linux, used to display the calls a userspace application makes to shared libraries.

using the password we get a


```bash
leviathan1@leviathan:~$ ./check
password: sex
$ whoami
leviathan2

$ cat /etc/leviathan_pass/leviathan2
ougahZi8Ta

```


* * *


### Leviathan2

Using the same host + port  and password gotten from the last level login as user leviathan2.

```bash
leviathan2@leviathan:~$ ls
printfile
leviathan2@leviathan:~$ ./printfile
*** File Printer ***
Usage: ./printfile filename


leviathan2@leviathan:~$ ./printfile /etc/leviathan_pass/leviathan3
You cant have that file...

```

Seems like there we can't read the leviathan3 file lets try creating a symlink to some directory that I( the user ) owns

```bash
leviathan2@leviathan:~$ mkdir /tmp/mytempdir
leviathan2@leviathan:~$ cd /tmp/mytempdir
leviathan2@leviathan:/tmp/mytempdir$ ln -s /etc/leviathan_pass/leviathan3
leviathan2@leviathan:/tmp/mytempdir$ ls
leviathan3

leviathan2@leviathan:/tmp/mytempdir$ /home/leviathan2/printfile leviathan3
You cant have that file...

leviathan2@leviathan:/tmp/mytempdir$ ltrace /home/leviathan2/printfile leviathan3
__libc_start_main(0x804852b, 2, 0xffffd754, 0x8048610 <unfinished ...>
access("leviathan3", 4)                                                = -1
puts("You cant have that file..."You cant have that file...
)                                     = 27
+++ exited (status 1) +++
leviathan2@leviathan:/tmp/mytempdir$ echo "test" > file.txt
leviathan2@leviathan:/tmp/mytempdir$ ltrace /home/leviathan2/printfile file.txt
__libc_start_main(0x804852b, 2, 0xffffd754, 0x8048610 <unfinished ...>
access("file.txt", 4)                                                  = 0
snprintf("/bin/cat file.txt", 511, "/bin/cat %s", "file.txt")          = 17
geteuid()                                                              = 12002
geteuid()                                                              = 12002
setreuid(12002, 12002)                                                 = 0
system("/bin/cat file.txt"test
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                 = 0
+++ exited (status 0) +++

```

The sym link didnt help so what I did was used ltrace to find out what is actully happening in the background
It turns out that `access()` function is actually used to check and verify the user id. and then 
`snprintf()` is used to create a string which is then passed to `system()` to be executed as a system command & we can also see that cat is being used to read the file.

Searching for vulnerabilites in `access()` function I stumbled upon [this](https://security.stackexchange.com/questions/42659/how-is-using-acces-opening-a-security-hole)

```bash
leviathan2@leviathan:/tmp/mytempdir$ ls
file  leviathan3
leviathan2@leviathan:/tmp/mytempdir$ touch "leviathan3 file"
leviathan2@leviathan:/tmp/mytempdir$ /home/leviathan2/printfile leviathan3\ file
Ahdiemoo1j
test
leviathan2@leviathan:/tmp/mytempdir$

```
Lets move on to the nex tone.


* * *


## Leviathan3

Using the same host + port  and password gotten from the last level login as user leviathan3.

```bash

leviathan3@leviathan:~$ ./level3
Enter the password>
bzzzzzzzzap. WRONG
leviathan3@leviathan:~$ ltrace ./level3
__libc_start_main(0x8048618, 1, 0xffffd794, 0x80486d0 <unfinished ...>
strcmp("h0no33", "kakaka")                       = -1
printf("Enter the password> ")                   = 20
fgets(Enter the password> test
"test\n", 256, 0xf7fc55a0)                 = 0xffffd5a0
strcmp("test\n", "snlprintf\n")                  = 1
puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
)                       = 19
+++ exited (status 0) +++
leviathan3@leviathan:~$ ./level3
Enter the password> snlprintf
[You've got shell]!
$ cat /etc/leviathan_pass/leviathan4
vuH0coox6m
$

```


## leviathan4

Using the same host + port  and password gotten from the last level login as user leviathan4.


```bash
leviathan4@leviathan:~$ ls -la
total 24
drwxr-xr-x  3 root root       4096 Oct 29 21:17 .
drwxr-xr-x 10 root root       4096 Oct 29 21:17 ..
-rw-r--r--  1 root root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root root       3526 May 15  2017 .bashrc
-rw-r--r--  1 root root        675 May 15  2017 .profile
dr-xr-x---  2 root leviathan4 4096 Oct 29 21:17 .trash
leviathan4@leviathan:~$ cd .trash/
leviathan4@leviathan:~/.trash$ ls
bin

leviathan4@leviathan:~/.trash$ ltrace ./bin
__libc_start_main(0x80484bb, 1, 0xffffd774, 0x80485b0 <unfinished ...>
fopen("/etc/leviathan_pass/leviathan5", "r")     = 0
+++ exited (status 255) +++

```

So looks like the binary returned is actully the key we just have to convert it in to  ascii I used the folloing [link](https://www.rapidtables.com/convert/number/binary-to-ascii.html)

![Binary to ascii converion image](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/lev4bin2ascii.png)


* * *

## leviathan5


```bash
leviathan5@leviathan:~$ ls
leviathan5
leviathan5@leviathan:~$ ./leviathan5
Cannot find /tmp/file.log
leviathan5@leviathan:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
leviathan5@leviathan:~$ ./leviathan5
UgaoFee4li
leviathan5@leviathan:~$

```

Binary file was reading content of /tmp/file.log I just created a soft link of the flag file and named it file.log Simple right ?!


* * * 

## leviathan6

Just have to bruteforce a 4 digit pin in this challenge.

```bash
leviathan6@leviathan:~$ ls
leviathan6
leviathan6@leviathan:~$ ./leviathan6
usage: ./leviathan6 <4 digit code>
leviathan6@leviathan:~$ for key in $(seq 0 10000); do ./leviathan6 $key; done

...
Wrong
Wrong
Wrong
Wrong
$ whoami
leviathan7
$ cat /etc/leviathan_pass/leviathan7
ahy7MaeBo9
$

```

* * *