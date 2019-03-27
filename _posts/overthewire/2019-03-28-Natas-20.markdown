---
layout: post
title: Natas 20 writeup
date: 2019-03-28
author: th3-3inst3in
categories: overthewire
tags: [web,overthewire]
---

## Natas 20

Starting the Challenge off  Where you have to enter a name.
Upon viewing the code it is apparently clear that we have to login as the admin.This can be understood by viewing the _print_credentials()_ function which has a condition of `$_SESSION["admin"] == 1`

Since the code for this challenge is a bit long I  won't be including all of it just little snippets

```php

function print_credentials() { /* {{{ */
    if($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
    print "You are an admin. The credentials for the next level are:<br>";
    print "<pre>Username: natas21\n";
    print "Password: <censored></pre>";
    } else {
    print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.";
    }
}


function myread($sid) { 
    debug("MYREAD $sid"); 
    if(strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
    debug("Invalid SID"); 
        return "";
    }
    $filename = session_save_path() . "/" . "mysess_" . $sid;
    if(!file_exists($filename)) {
        debug("Session file doesn't exist");
        return "";
    }
    debug("Reading from ". $filename);
    $data = file_get_contents($filename);
    $_SESSION = array();
    foreach(explode("\n", $data) as $line) {
        debug("Read [$line]");
    $parts = explode(" ", $line, 2);
    if($parts[0] != "") $_SESSION[$parts[0]] = $parts[1];
    }
    return session_encode();
}

function mywrite($sid, $data) { 
    // $data contains the serialized version of $_SESSION
    // but our encoding is better
    debug("MYWRITE $sid $data"); 
    // make sure the sid is alnum only!!
    if(strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
    debug("Invalid SID"); 
        return;
    }
    $filename = session_save_path() . "/" . "mysess_" . $sid;
    $data = "";
    debug("Saving in ". $filename);
    ksort($_SESSION);
    foreach($_SESSION as $key => $value) {
        debug("$key => $value");
        $data .= "$key $value\n";
    }
    file_put_contents($filename, $data);
    chmod($filename, 0600);
}


session_set_save_handler(
    "myopen", 
    "myclose", 
    "myread", 
    "mywrite", 
    "mydestroy", 
    "mygarbage");
session_start();

if(array_key_exists("name", $_REQUEST)) {
    $_SESSION["name"] = $_REQUEST["name"];
    debug("Name set to " . $_REQUEST["name"]);
}

print_credentials();

$name = "";
if(array_key_exists("name", $_SESSION)) {
    $name = $_SESSION["name"];
}

```

Viewing the code we should be focused on _session_set_save_handler()_ which bascially allows us to define our own storage and retrieval methods for our SESSION. Read more [here](https://www.php.net/manual/en/function.session-set-save-handler.php).

Further investigating _myread()_ and _mywrite()_ functions.

* _myread()_ and _mywrite()_ are actually inverses of each other i.e one does opposite of the other
* The $data is split in to $parts using the _explode()_ function in **foreach(explode(“\n”, $data) as $line)**
* The first part of the splited $line is taken as a **$key** and the second as a **$value**

#### What do we understand so far ?
	1.Variables are seperated using new line & the Key value pars are seperated using a space.
	2.To show the key/flag we have to set admin session == 1.

Since the name parameter is set to the Session we can simply just inject a new line in the name parameter setting admin == 1 and hense getting the  flag.

Final payload 

`http://natas20.natas.labs.overthewire.org/index.php?name=test%0D%0Aadmin%201%0D%0A`

![flag](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas20flag.png)