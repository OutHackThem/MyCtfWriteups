---
layout: post
title: Leviathan 1-8 Writeup
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

:-------------------------------------------------------------------------------------

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


:-------------------------------------------------------------------------------------
