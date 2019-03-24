---
title: "Omg Net Cat Can Do That"
date: 2016-12-14T11:36:31-04:00
draft: false
---

# OMG Netcat Can Do That!!??

> SUMMARY:  Today we will see yet another cool thing netcat can do - file transfer.

I could be King if I surrounded myself by fools.  But I'd rather immerse myself in a group of people smarter than me so I can learn and grow.  I believe that I am pretty comfortable and good at the command line - far from a guru, but I've been known to impress.  Today however, I saw something my co-worker, Glenn Struzinski, did that just blew my mind.  

In my previous post one of the tips I covered included using netcat (nc).  When I saw that tip I thought I had learned something really cool about Netcat.  But what I will share with you today I think is a mind-bender of a tip - using netcat to transfer a file from Host-A to Host-B.  These hosts need to be able to communicate over the network.  My example will have Host-A as my server hosted at Digital Ocean, and Host-B is my laptop.  Currently my laptop is connected to the internet at a Starbucks cafe.

For illustrative purposes, let's say Host-A has an ip address of 104.237.47.10  (I am pulling this address out of the air, please use your own real address.)  While logged in to Host-A I enter the following:


```
➜  ~ cat BinaryIpsum.zip | nc -l -p 1234
```


Now you may have noticed that in the above terminal, the server is waiting for a connection to occur.  Let's review the command.  We have a file on our server called BinaryIpsum.zip.  Replace that with your desired file name.  Now we use cat and our file name and then we pipe that output to netcat (nc) and tell netcat to "listen" using the -l flag.  Lastly, we tell netcat to use port 1234 with the -p 1234.

So now we need to open up a terminal session on our laptop.  Navigate to a directory where you want to place the file and execute the following:


```
➜  NETCAT_TEST pwd
/Users/jfhogarty/Documents/NETCAT_TEST
➜  NETCAT_TEST ls -la
total 0
drwxr-xr-x    2 jfhogarty  staff    68 Dec 13 19:49 .
drwx------+ 176 jfhogarty  staff  5984 Dec 13 19:49 ..
➜  NETCAT_TEST

➜  NETCAT_TEST nc 104.237.47.10 1234 > NewFile.zip
➜  NETCAT_TEST

➜  NETCAT_TEST ls -la
total 10632
drwxr-xr-x    3 jfhogarty  staff      102 Dec 13 19:49 .
drwx------+ 176 jfhogarty  staff     5984 Dec 13 19:49 ..
-rw-r--r--    1 jfhogarty  staff  5440660 Dec 13 19:49 NewFile.zip
➜  NETCAT_TEST
```


**Is that FREAKING AWESOME?**


HoGi...

*Learning something new every day*

