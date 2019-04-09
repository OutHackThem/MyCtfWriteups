---
layout: post
title: Natas 27 Writeup
date: 2019-04-8
author: th3-3inst3in
categories: overthewire
tags: [web,overthewire]
---

### An interesting abuse case for a feature in mysql
> Natas 27 was an amazing challenge , I had alot of fun doing it and this writeup might contain something new and interesting for most of you.
>


Opening the challenge and clicking on View Sourcecode gives us the following


```php

<?

// morla / 10111
// database gets cleared every 5 min 


/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/


function checkCredentials($link,$usr,$pass){
 
    $user=mysql_real_escape_string($usr);
    $password=mysql_real_escape_string($pass);
    
    $query = "SELECT username from users where username='$user' and password='$password' ";
    $res = mysql_query($query, $link);
    if(mysql_num_rows($res) > 0){
        return True;
    }
    return False;
}


function validUser($link,$usr){
    
    $user=mysql_real_escape_string($usr);
    
    $query = "SELECT * from users where username='$user'";
    $res = mysql_query($query, $link);
    if($res) {
        if(mysql_num_rows($res) > 0) {
            return True;
        }
    }
    return False;
}


function dumpData($link,$usr){
    
    $user=mysql_real_escape_string($usr);
    
    $query = "SELECT * from users where username='$user'";
    $res = mysql_query($query, $link);
    if($res) {
        if(mysql_num_rows($res) > 0) {
            while ($row = mysql_fetch_assoc($res)) {
                // thanks to Gobo for reporting this bug!  
                //return print_r($row);
                return print_r($row,true);
            }
        }
    }
    return False;
}


function createUser($link, $usr, $pass){

    $user=mysql_real_escape_string($usr);
    $password=mysql_real_escape_string($pass);
    
    $query = "INSERT INTO users (username,password) values ('$user','$password')";
    $res = mysql_query($query, $link);
    if(mysql_affected_rows() > 0){
        return True;
    }
    return False;
}


if(array_key_exists("username", $_REQUEST) and array_key_exists("password", $_REQUEST)) {
    $link = mysql_connect('localhost', 'natas27', '<censored>');
    mysql_select_db('natas27', $link);
   

    if(validUser($link,$_REQUEST["username"])) {
        //user exists, check creds
        if(checkCredentials($link,$_REQUEST["username"],$_REQUEST["password"])){
            echo "Welcome " . htmlentities($_REQUEST["username"]) . "!<br>";
            echo "Here is your data:<br>";
            $data=dumpData($link,$_REQUEST["username"]);
            print htmlentities($data);
        }
        else{
            echo "Wrong password for user: " . htmlentities($_REQUEST["username"]) . "<br>";
        }        
    } 
    else {
        //user doesn't exist
        if(createUser($link,$_REQUEST["username"],$_REQUEST["password"])){ 
            echo "User " . htmlentities($_REQUEST["username"]) . " was created!";
        }
    }

    mysql_close($link);
} else {
?>
```


As you can see thoughout the code `mysql_real_escape_string()` is being used so no sql injection there

#### What does the program do ?
	
	The program asks us for a username & password checks if that user already exists , ifexists then it basically logs us in if not then creates the user.
There are 2 prominent functions in this program 
1. checkCredentials()
2. dumpData()

The first function validates both the username & the password. The second only validates the username verify it exists and prints out the data (evil grin).

### Here is something really interesting about MySQL.

I assume most of you have worked with mysql. Lets create a simple database called `testdb` in phpmyAdmin.


![create db](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas27/createuser.png)


Lets add a table named `users` & add a column in it called username of type _VARCHAR_ of length **10**.

![create table](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas27/createtable.png)

.

![create column](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas27/createcolumn.png)


Now lets add a username "abc" in it.

![entry1](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas27/add1.png)


Now lets select the newly created username.

Query:

* SELECT * FROM 'users' WHERE username='abc';
> abc

The result from the last query is `abc` . Now lets add another entry in the db `"abc          something"` , just to be clear thats abc followed by about 12 spaces and then the word something.


![entry2](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas27/add2.png)


Run the same query as before 

Query:

* SELECT * FROM 'users' WHERE username='abc';
> abc
> abc

And now the result contains 2 `abc.

If you look back we added 2 things in our database one a simple `abc` and another `abc          something` . The WHERE statement shouldn't have selected the second entry right ? , cause that string is copletely different from `abc`.