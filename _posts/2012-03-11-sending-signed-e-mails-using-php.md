---
layout: post
title: Sending signed e-mails using php
date: 2012-03-11 14:47
author: admin
comments: true
categories: [email, email signing, email verification, PHP, php email, php email sign, Programming, School, security, Sending signed emails using php, signed e-mails using php, Signed email, Tech, Webdevelopment]
---
Last term we had to build a "secure" website. This included using SSL/https and signed e-mails.
Since we were going to build a website and I'm an experienced PHP web developer we decided to go to work with PHP. This gave us a small problem, how do we sign e-mail messages using PHP. Using multiple how-to’s and tutorials I was able to figure it out. Helping others being able to find it quicker I’m writing yet another tutorial hoping to help people to get it working in an hour instead of a few days. In this tutorial I’m not going into details about creating certificates and keys to use, I’ll only talk about how to sign emails using PHP. I will include some quirks you could encounter when using Xampp or some other form of apache on windows.

<!--more-->

There are a few things important to know when signing emails and doing this on windows. Windows does not like relative paths, use absolute paths. The headers and the content of the email send should not be changed after signing. If you do the verification, the thing it’s all about, will fail. You should know a thing or two about PHP before using this tutorial, it’s not hard but if you don’t know PHP it is. Having said that lets get to work.

The first part is creating files to read from and write to, why need them will become clear later. In the folder you are working create two files, msg.txt and signed.txt and make sure they are writeable by PHP, in Windows create them as the same user running apache, on Linux chown them to apache or chmod them to 777. After that create a file called mail.php or something. This file will contain the class.

In the file you should create a class and a method, I have called them mail and sendSignedMail. The method has $toAdr, $subject, $message as parameters, these will be used later. First create a few variables in the method.
<ol>
	<li>$prfile, this will be the private key pointer. I have placed it in the same folder as the file so the path is $prfile = 'file://'.realpath(dirname(__FILE__)).'/key.pem';</li>
	<li>$pufile, this will be the public key pointer, $pufile =  <a href="file:///\\'.realpath(dirname(__FILE__)).'\cert.pem">file://'.realpath(dirname(__FILE__)).'/cert.pem</a>';</li>
	<li>$msg, this is a pointer to the previous create msg.txt, $msg = realpath(dirname(__FILE__))."/msg.txt";</li>
	<li>$sign, this is a pointer to sign.txt, $sign = realpath(dirname(__FILE__))."/signed.txt";</li>
</ol>
&nbsp;

Having those variables is just to prevent typing those paths all the time. Now let’s start with some code.
<pre lang="php">$fp = fopen($msg,'w');

fwrite($fp,'Content-Type: text/html;charset="us-ascii"Content-Transfer-Encoding: 7bit

'.$message);

fclose($fp);</pre>
Do not copy this code but type it since it won’t work because of whitespace problems.  First I open the msg.txt and create a filepointer. After that I write the message and a content header. This header is to tell mail clients how to read this message. After that I close the file again since it is not needed to keep open.
<pre lang="php">openssl_pkcs7_sign($msg, $sign,$pufile, array($prfile,'onzebank'),array("To"=&gt;$toAdr,'subject'=&gt;$subject));</pre>
Using openssl_pkcs7_sign will sign the message. What it does is take the content from the msg.txt file create a hash from it using the private key, the public key and also including the subject and the destination address.

Now you have a signed message, but it is on your server and not in the destination email box. Let’s make that last part work. I could open signed.txt get the content and mail that to the destination. That will get the message to the destination but not usable. Since the signature won’t be correct. To do it correctly we first have to get the content from signed.txt with:
<pre lang="php">$data = file_get_contents($sign);</pre>
Now we have all the data we now have to separate the message form the headers which are also in the data variable. This is also the part where a lot can go wrong because of white spacing.
<pre lang="php">$parts = explode(‘

’,$data);</pre>
This will explode the data on triple new line sequence, if you get verification errors this is the part to look for trouble.

Now we have the headers and the message, $parts[0] and $parts[1] respectively, now we can send the email.
<pre lang="php">Mail($toAdr,’’,$parts[1],$parts[0]);</pre>
I am not setting the subject using the mail function since that would mess up the headers and thus the signature. After this I clean up the files to prevent data to be stored or lost or messing around with the next mail.
<pre lang="php">$fp = fopen($msg,'w');
fwrite($fp,' ');
fclose($fp);
$fp = fopen($sign,'w');
fwrite($fp,'');|
fclose($fp);</pre>
Just to be nice I also added the file containing the class: <a href="http://www.jansman.eu/wp-content/uploads/2012/03/mail.php_.txt" target="_blank">mail.php</a>
