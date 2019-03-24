---
title: "Net Cat Part Deux"
date: 2017-01-12T10:30:14-00:00
draft: true
---

# NetCat Part Deux - The Writing on the Wall

> SUMMARY: Use netcat to help troubleshoot a TCP Port issue on a Linux VM (Guest)
> and our Laptop (OS X) which is a host running VirtualBox.

My node (laptop) on the network cannot talk to a new application running on a linux server.  Ever found yourself in this scenario?  To add a layer of complexity, that application running on Linux server is a VirtualBox VM (virtual machine.)  The VM was created with the network settings set to 'Nat'.  What does that mean?  It means that the VM can use the host computers network connection to get on the network, and out to the Internet - assuming your laptop/desktop has a working Internet connection.  

But what if I want to connect to that VM (guest) using ssh from my laptop (host)?  We can do this with VirtualBox, but you need to update your Network settings in VirtualBox and enable port forwarding.  For example, you may add a port forwarding entry as follows:

 Use netcat to help troubleshoot a TCP Port issue on a Linux VM (Guest)
> and our Laptop (OS X) which is a host running VirtualBox.

My node (laptop) on the network cannot talk to a new application running on a linux server.  Ever found yourself in this scenario?  To add a layer of complexity, that application running on Linux server is a VirtualBox VM (virtual machine.)  The VM was created with the network settings set to 'Nat'.  What does that mean?  It means that the VM can use the host computers network connection to get on the network, and out to the Internet - assuming your laptop/desktop has a working Internet connection.  

But what if I want to connect to that VM (guest) using ssh from my laptop (host)?  We can do this with VirtualBox, but you need to update your Network settings in VirtualBox and enable port forwarding.  For example, you may add a port forwarding entry as follows:


```
Name   Protocol  Host IP     Host Port   Guest IP   Guest Port
ssh    TCP       127.0.0.1   2222                   22
```


Once the above is setup and you start your VM, from your laptop (host) you should be able to connect using:  ssh username@127.0.0.1 -p 2222

VirtualBox will forward your ssh connection from the laptop (host) on port 2222 on to the Linux server (guest) on port 22.  Normally, this setup works and you shouldn't encounter any issues.  However, if say you just installed a shiney new app on the Linux server (guest) that should run on ports 7001 and 7003, but you find that you have issues connecting from your laptop (host) then it would be time to roll up your sleeves and trouble shoot.

This is where NectCat (nc) can be helpful.  Remember, in this scenario we have our laptop (host) using VirtualBox to handle port forwarding to your Linux server (guest.)  If for example you try the following from your laptop, you may get a false-positive response:


```
jhogarty: ~ $ nc -v -z localhost 9001
found 0 associations
found 1 connections:
    1:    flags=82
   outif lo0
   src 127.0.0.1 port 58563
   dst 127.0.0.1 port 9001
   rank info not available
   TCP aux info available

Connection to localhost port 9001 [tcp/etlservicemgr] succeeded!
jhogarty: ~ $
```


In the above example I am using port 9001, an unprotected port, on my laptop (host) to try and connect to the Linux server (guest) on port 7001.  From the output in the above example, using netcat, all looks well.  But it isn't.  We need to modify the use of our tools to do a better test given our current scenario.

On the Linux server (guest) we will use NetCat with the following arguments:


```
nc -l 7001
# Example:
jhogarty: ~ $ ssh oracle@127.0.0.1 -p 2223
oracle@127.0.0.1's password:
Last login: Fri Jan 13 09:38:13 2017 from 10.0.2.2
[oracle@localhost ~]$ nc -l 7001
{cursor_here}
```


To help with our testing, you should have two terminal sessions up - one for your Linux server and one for your laptop.  We set up the Linux server in one terminal with the example above and now we need to use NetCat in a different terminal session on your laptop.  Use the following:


```
nc 127.0.0.1 9001
# Example
jhogarty: ~ $ nc 127.0.0.1 9001
{cursor_here}
```


Hmm, don't see much going yet.  But here comes the cool part, assuming that we have good network connectivity between the server and the laptop.  In the terminal window for your laptop, start typing something - 'Hey, is this working?'   Press enter.  Take a look in the other terminal window, the one connected to your Linux server.  Do you see the text that you typed?  If so, you have a clear networking path between the two computers (nodes.)  Congratulations!

```
# Example
LAPTOP
jhogarty: ~ $ nc 127.0.0.1 9001
Hey, is this working?   <- you just typed this!

And on the Linux Server you should see:

[oracle@localhost ~]$ nc -l 7001
Hey, is this working?     <- you should see this appear after typing on LAPTOP and pressing Enter
```


If you do not see the text on your Linux server, check to make sure you typed the commands correctly based on the examples given above.  If you are working in a similar scenario, using VirtualBox, make sure you have setup your port forwarding properly.  For example, for the Host Port setting, do not use any protected ports.  Another thing you can check, if using VirtualBox, are you using NAT?

Should you be troubleshooting a similar problem, but without something like VirtualBox, but instead your laptop/desktop/server-a is on a network that can normally talk to the target server, for example ssh works fine, then you may have a firewall or port blocking issue.  And that would be a topic for another day!


HoGi...

*Learning something new every day*
