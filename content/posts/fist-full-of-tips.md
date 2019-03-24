---
title: "Fist Full of Tips"
date: 2016-11-30T11:45:00-00:00
draft: false
---

# Fist Full Of Tips 

> SUMMARY:  Today I will cover a few tips that I picked up recently from my new job and noodling around a bit.
>           We will re-visit ngrok, explore iTerm, use netcat (nc) and brush up against bundler.


It has been an exciting year for me, chuck full of turberlance and change.  But things have settled down and I find myself once again pledging to be more timely with my blog posts.  I was listening to a  podcast the other day and the guest was talking about how his goal for 2017 is to write a blog post every day.  That is a tad ambitious for me, but I will be increasing my writing in the coming year.  Ok, let's get to it!


While preparing for my Binary Ipsum talk which I presented at RubyJax this past October, I ran into the first tip for today.  As much as I have used bundler these past 3 or so years, I didn't know that one could use a neat command where bundler will create a basic Gemfile file for you:


```
bundle init

➜  ognt_tips ls -la
total 0
drwxr-xr-x   2 jfhogarty  staff    68 Nov 29 19:41 .
drwxr-xr-x  43 jfhogarty  staff  1462 Nov 29 19:41 ..
➜  ognt_tips bundle init
Writing new Gemfile to /Users/jfhogarty/Documents/RoR/LEARNING_NOTES/ognt_tips/Gemfile
➜  ognt_tips cat Gemfile
# frozen_string_literal: true
source "https://rubygems.org"

# gem "rails"
➜  ognt_tips
```


I can't remember how many times I manually created a Gemfile for my Ruby command line type scripts and applications.  This was a nice time saver!  If you are interested, the material for my talk for RubyJax can be found [here](https://github.com/hogihung/binary_ipsum).   See the Tutorial.md file.


In a previous post I talked about ngrok.  At the time I had only thought and used ngrok with some work I was doing between a Shopify application and a Rails API.  But here is another cool feature of ngrok - using it for ssh.  For example, say you are home and trying to pair with a co-worker who just happens to be chilling at the local coffee shop.  You can fire up ngrok and send the related info to your co-worker and they can ssh to your computer.  See the example below:


```
#On one computer, for example your laptop at your house:
ngrok tcp 22


ngrok by @inconshreveable                                                                                                 (Ctrl+C to quit)

Session Status                online
Account                       Your N. Here (Plan: Free)
Version                       2.1.18
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    tcp://0.tcp.ngrok.io:13641 -> localhost:22

Connections                   ttl     opn     rt1     rt5     p50     p90
                              2       0       0.00    0.00    8.47    16.86

# On another computer, have your co-work run:
ssh [a_username_here]@0.tcp.ngrok.io -p 13641
```


**Wicked cool!**  For more information on ngrok, checkout their website [here](https://ngrok.com/).


This next tip I learned during a trouble shooting session my first week at the new job.  We were trying to see if one of our hosts was able to communicate on a particular TCP port.  In this example, we were testing port 443.  In the past, one way I would trouble shoot required having the telnet server service loaded on the target.  Then I would run telnet ip_address desired_port.  But this was a pain.  Enter netcat (nc) to the rescue!  Check out the following example:


```
# On computer#1 you want to see if the port is accessible:
nc -l 443

The -l tells netcat to 'listen' and the 443 in this example is the port we are checking.  Your computer is now in listen mode and will wait for a request.

# On another computer, let's call it computer#2, that can communicate with over the network to computer#1, issue the following:
nc -z -w2 ip_address_here port_number_here

# Example from computer#2:
jfhogarty@kaylee:~$ nc -z -w2 -v 192.168.1.43 443
Connection to 192.168.11.43 443 port [tcp/https] succeeded!
```


I really wish I had known this many, many years ago!


Lastly I'd like to talk about some tips with iTerm.  If you are not a Mac user, or don't use iTerm, then you can move along.  Otherwise, sit back and enjoy the ride!  For a while I was using Tmux - partly because of the way I could split my terminal window into 3 or 4 panes.  I also used Tmux so that I could utilize the syncronize-panes feature.

Earlier this year I had started using iTerms' ability to split the terminal horizontally or vertically.  You can find these options under the Shell menu item.  I then used Keys options under Preferences to map Control+H to 'Previous Pane' and Control+l to 'Next Pane'.  This helped me utilize iTerm for many of the things I was doing with Tmux.  But one thing I was missing was the syncronizing of panes.  Alas! iTerm can do this as well.  You can find this little gem under Shell, Broadcast Input and then choose 'All panes in All Tabs' or 'All panes in Current Tab.'  AWESOME!  The option for 'All panes in All Tabs' I think trumps what Tmux can do.  You be the judge.  ;)

Well, that wraps up today's blog post.  I hope that you find value in at least one of these tips.  Do you have a tip that you'd like to share?  Let me know - @hogihung on Twitter or john_hogarty@ognt.io


HoGi...

*Learning something new every day*

