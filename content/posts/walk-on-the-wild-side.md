---
title: "Walk on the Wild Side"
date: 2016-07-20T20:00:13-04:00
draft: false
---

# Walk On The Wild side 

> SUMMARY:  In this post I will cover how I solved a problem of creating multiple 
>           Virtual Machines, in an automated fashion, using virt-install.  Our 
>           Virtual Server host system is an Dell R610 (physical) Server 
>           running Ubuntu 14.04 64 bit and KVM.  I will discuss using a preseed
>           file, hosted on one of my webservers, along with two terminal 
>           sessions - one to ssh to the KVM host and one to use ssh as a tunnel.  
>           We will use the ssh tunnel connection so we can connect to target new 
>           VMs with a GUI using a VNC client.


Today I'd like divert from talking about Ruby and take a 'walk on on the wild side.'  Well, not really too wild - we will talk about using virsh-install and a preseed file to quickly build a based Ubuntu Virtual Machine.  But before we dive in I would first like to apologize for my absence here on the blog.  I've always had a goal of producing content at least once a month if not more.  Life got in the way - I started a new job the first week of May and then we found out the shop had to let us all go the first week of July.

Ok back to our walk.  When I was at AT&T we had been using VMWare for some time to create Virtual Machines (VMs).  We then switched to using KVM for all newly needed VMs.  We had a fairly manual process where we started part of the process on our KVM host and then used [Virt-Manager](https://virt-manager.org/) to step through the install process.  Once the base install finished, we would then log in using ssh and follow a documented, manual process.  Even back then I thought there must be a better way.  But I left AT&T to try out a career as a Ruby developer.

Fast forward to May 2016.  When I joined the new team they were switching over to using Vagrant for the developer environments.  To help with speeding up the test suite, there was a rack of multi-core servers.  I've always liked tinkering and building servers.  I had been using Virtual Box and Parallels for some time to spin up different Linux based VMs to help with my learning journey.  It was time to take things to the next level so I purchased a [refurbished](https://www.savemyserver.com/) Dell R610 24 core rack mount server with 24GB of RAM.  I added a pair of 250GB SSD drives and built myself a KVM server based on Ubuntu 14.04.

Initially I was spinning up new VMs using Vagrant on my home lab server.  But the more I worked with Vagrant and the KVM host, the more I realized I wanted to build VMs using the tools that come with KVM, e.g. virt-install.  My goal was to build a lab environment consisting of the following:


```
    (4) Test VMs for doing parallel testing of Rails applications
    (1) Staging Web Application Server (Nginx, Passenger, Rails)
    (1) Production Web Application Server (Nginx, Passenger, Rails)
    (1) Database Server to host Postgresql and MySQL databases
    (1) Gitlab Server
    (1) Chef Server
    (1) Chef Workstation
```


For this lab I really did not want to have to manually build these VMs.  My plan was to use virsh-install, with a Debian preseed file, to build the base machines.  Then I would build out the Chef Server and Workstation so that I could use Chef to build and manage all of the VMs.

So off to the Internet I went to research possible solutions.  With the newly gained knowledge, with a dash of previous experience, I came up with a basic formula:


```
-------------------------------------------------------------------------------
Example:  You would run this command on your KVM host
-------------------------------------------------------------------------------
virt-install \
--virt-type=kvm \
--name devbox \
--ram 2048 \
--vcpus=2 \
--hvm \
--network bridge:br0 \
--graphics vnc \
--disk path=/var/lib/libvirt/images/ubunt1404_devbox.img,size=15,bus=virtio \
--location http://archive.ubuntu.com/ubuntu/dists/trusty/main/installer-amd64/ \
--extra-args 'auto=true interface=eth0 hostname=devbox domain=localdomain url=http://ognt.io/ubuntu1404-64-preseed.cfg'

Replace [devbox] in the above example with the name you wish to use.

Feel free to change the amount of ram, cpus, (disk)size and possibly network.

Note: I am using bridged networking so all of my VMs will get a DHCP lease on
      my private network.

Note: If you change the size value, be sure to adjust the partitioning entries
      accordingly in the *-preseed.cfg file.
-------------------------------------------------------------------------------
```


You will notice the last argument to virsh-install, **--extra-args**, we specify a url for the preseed file.  You are welcome to use the one provided, but I encourage you to download the file, and adjust as needed.  Make sure you change the password for root after your VM has been created.  

Example preseed file:  **http://ognt.io/ubuntu1404-64-preseed.cfg**


```
d-i debian-installer/language string en
d-i debian-installer/country string IE
d-i debian-installer/locale string en_US.UTF-8

d-i console-setup/ask_detect boolean false
d-i keyboard-configuration/layoutcode string us

d-i mirror/country string manual
d-i mirror/http/hostname string archive.ubuntu.com
d-i mirror/http/directory string /ubuntu
d-i mirror/http/proxy string

d-i passwd/root-login boolean true
d-i passwd/make-user boolean false

# Root password, either in clear text
d-i passwd/root-password password changemeN0w
d-i passwd/root-password-again password changemeN0w
# or encrypted using an MD5 hash.
#d-i passwd/root-password-crypted password [MD5 hash]

d-i time/zone string America/New_York 
d-i clock-setup/utc boolean true

d-i partman-auto/method string regular

d-i partman-lvm/device_remove_lvm boolean true
d-i partman-md/device_remove_md boolean true
d-i partman-lvm/confirm boolean true

d-i partman-auto/expert_recipe string        \
  boot-root ::            \
    10240 10240 10240 ext4        \
      $primary{ } $bootable{ }    \
      method{ format } format{ }    \
      use_filesystem{ } filesystem{ ext4 }  \
      mountpoint{ / }        \
    .            \
    2048 2048 2048 linux-swap      \
      $primary{ }        \
      method{ swap } format{ }    \
    .            \

d-i partman-partitioning/confirm_write_new_label boolean true
d-i partman/choose_partition select finish
d-i partman/confirm boolean true
d-i partman/confirm_nooverwrite boolean true

d-i pkgsel/update-policy select none

tasksel tasksel/first multiselect Basic Ubuntu server, OpenSSH server

d-i grub-installer/only_debian boolean true
d-i grub-installer/with_other_os boolean true

d-i finish-install/reboot_in_progress note
```


You can learn more about using a preseed file and the different available options [here](https://wiki.debian.org/DebianInstaller/Preseed).


I used the above method to quickly spin up ten new Ubuntu 14.04 based VMs.  I am working on a script to automate this a little bit more, but that is for another day.  So you have built a new VM using virt-install and now you want to log in to it.  If you have been following along, you probably don't know what IP address to use for connecting via ssh.  

Luckily, we have a solution.  All you need is a VNC client and two terminal sessions.  Use one of your terminal sessions to log in to your KVM host.  Next we will see if our new VM is up using virsh:


```
jfhogarty@kaylee:~$ virsh list
 Id    Name                           State
----------------------------------------------------
 33    book                           running
 35    badger                         running

jfhogarty@kaylee:~$
```


If you do not see your VM running, run the command:


```
virsh start [vm_name_here]
```


You can re-run the virsh list command to confirm that your VM is up and running.  Next, we need to see which port VNC is listening on for our target VM.  Use the following command:


```
sudo netstat -nap | egrep '(kvm|qemu)'
jfhogarty@kaylee:~$ sudo netstat -nap | egrep '(kvm|qemu)'
[sudo] password for jfhogarty:
tcp        0      0 127.0.0.1:5900          0.0.0.0:*               LISTEN      28336/qemu-system-x
tcp        0      0 127.0.0.1:5901          0.0.0.0:*               LISTEN      29492/qemu-system-x
{--snip--}
```


If you have more than one VM running you should see several ports listed in the 5900 range.  Each port corresponds to a running VM as displayed when you ran virsh list.

For this example, we will assume that your target VM is on port 5900. Go to your other terminal window and create a ssh tunnel.


```
ssh user_name_here@host_name_or_ip_here -L 5900:localhost:5900
```


With the above ssh tunnel established, we should now be able to connect using our VNC client. Make sure that you do not close your terminal session with the ssh tunnel. OS X has a VNC client built in, 'Screen Sharing,' but I had issues getting it to work. I ended up purchasing a very good VNC client for OS X called [JollyFastVNC](https://www.jinx.de/JollysFastVNC.html).

Once you have successfully connected to your VM using the VNC client, run the following command to see what your ip address is:


```
#ifconfig
jfhogarty@book:~$ ifconfig
eth0      Link encap:Ethernet  HWaddr 52:54:00:cd:5c:af
          inet addr:192.168.1.66  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::5054:ff:fecd:5caf/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2039784 errors:0 dropped:24 overruns:0 frame:0
          TX packets:345925 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1649716601 (1.6 GB)  TX bytes:34485371 (34.4 MB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:54465755 errors:0 dropped:0 overruns:0 frame:0
          TX packets:54465755 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:5243043458 (5.2 GB)  TX bytes:5243043458 (5.2 GB)

jfhogarty@book:~$
```


In my example, I will be able to login to the new VM using:  ssh user_name_here@192.168.1.66.

If all went well, you have a newly created VM using virsh-install and are able to login both using VNC or via ssh.

I know this was a bit long today, but I wanted to cover a lot of material - especially due to my absence here at the blog.  Stay tuned for future postings where I will cover using Chef and other tools to assist with the automation of building out different VMs.


HoGi...

*Learning something new every day*

