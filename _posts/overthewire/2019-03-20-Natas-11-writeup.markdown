---
layout: post
title: Natas 11 writeup
date: 2019-03-20
author: th3-3inst3in
categories: overthewire
tags: [web,overthewire]
---

## Natas 11

Visit this [link](http://natas11.natas.labs.overthewire.org/) to go to level 11 for which you’ll need the password from the previous level.
You'll land here
![main](https://mbilalrizwan.github.io/MyCtfWriteups/assets/images/overthewire/natas11main.png)

Clicking the view Sourcecode link we see the following code

```php
<?

$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}

$data = loadData($defaultdata);

if(array_key_exists("bgcolor",$_REQUEST)) {
    if (preg_match('/^#(?:[a-f\d]{6})$/i', $_REQUEST['bgcolor'])) {
        $data['bgcolor'] = $_REQUEST['bgcolor'];
    }
}

saveData($data);



?>

<h1>natas11</h1>
<div id="content">
<body style="background: <?=$data['bgcolor']?>;">
Cookies are protected with XOR encryption<br/><br/>

<?
if($data["showpassword"] == "yes") {
    print "The password for natas12 is <censored><br>";
}

?>
```

###### Lets go over this code and see how we can get to our flag

-First we have a xor_encrypt() function which takes in a value and xors that with a secret key
-loadData() function checks if cookies exist , if yes then it base64 decodes it xor_encrypt()s it tries to load the json object in to an array
	-It also checks if certain certain conditions are met if yes then the value of the $mydata is updated
	-Then it returns the value of $mydata
-Main thing to note is if the value of $data[‘showpassword’] is yes then it displays the flag

#### What is our objective ?

We need to somehow modify the data within the cookies such that $data[‘showpassword’] == yes .
In order to modify the cookies we first need to somehow find the key which is used to xor_encrypt() the cookies

<dl>
<dt>We know that</dt>
<dd>data ^ key = xorOut</dd>
<dd>xorOut ^ data = key</dd>
</dl>

Thus taking the cookies base64 decoding it and running the xor_encrypt() function on it with the array as the key we can find the original key

![php code run to get key](https://i.imgur.com/KP5GZoN.png)

I took the quickest route and just copy pasted the xor_encrypt() function directly which gave me the output as shown below after trying the whole string as my key I realized that it wasn’t working , a few failed attempts and I realized the repeating value `“qw8J”` was my key

With our newly found key we can just modify our php script insert that as the key change the value of “showpassword” to yes and get our new cookie

![getting new cookies](https://i.imgur.com/1lCD9XI.png)

Add the outputed string as our cookie and vola !

![gotflag](https://i.imgur.com/6zeobp0.png)
