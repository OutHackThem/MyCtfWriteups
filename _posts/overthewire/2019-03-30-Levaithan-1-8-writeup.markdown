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

![lev1](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/lev1))