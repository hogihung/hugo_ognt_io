---
title: "Time to Share Add Ngrok"
date: 2016-08-02T16:20:33-04:00
draft: false
---

# When It Is Time To Share, Add A Dash of Ngrok 

> SUMMARY:  Today I will cover a possible solution for sharing your Rails 
>           Application from your development environment to your client over 
>           the internet.  I will be using a tool called Ngrok which has clients 
>           for OS X, Linux and Windows. 

I've been helping out on a new web application which has an interesting twist.  Well, it is interesting to me as it is something new that I have not yet  had to deal with.  The application is a Rails App that is used within a Shopify application.  In order to connect my developer environment to the Shopify development application, the Shopify app needs to be able to communicate with my laptop.  My laptop does not have a public IP address - it is not directly connected.  In fact my laptop, and all of my home computers, are a couple layers deep behind my public facing interface and a firewall.

One possible solution would be for me to use a Vagrant based Virutal Machine and then use Vagrant Share.  I believe this would work, though I did not test it out.  But I wanted to follow the same workflow, as close as I could, to my peers on this project.  And they introduced me to Ngrok.  Ngrok is a free tool and very easy to install as well as simple to use.  Download and install the appropriate client for your operating system.  While you do not need to sign up at the Ngrok site, doing so will give you some additional options like:


```
    Password Protection
    TCP Tunnels
    Multiple Simultaneous Tunnels
```


With the ngrok client installed, how do I use this shiney new tool?  This is the workflow I use - I have iTerm running with four tabs.  My first tab is for running the rails server using 'rails server'.  The second tab I use for Vim.  My third tab is normally used for running tests - either manually or by using grunt.  Lastly, the fourth tab is where I would initiate ngrok.  Let's take a look at what happens when I launch ngrok:


```
➜  ~ ngrok http 3000
ngrok by @inconshreveable                                                                                                                                                    (Ctrl+C to quit)

Tunnel Status                 online
Version                       2.1.3
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://61a08467.ngrok.io -> localhost:3000
Forwarding                    https://61a08467.ngrok.io -> localhost:3000

Connections                   ttl     opn     rt1     rt5     p50     p90
                              478     0       0.00    0.00    0.02    0.15

HTTP Requests
-------------
```


**NOTE:**  That url is no longer in use by me, it was for illustrative purposes.

If you want to share you Rails app, make sure you leave ngrok up and running and then bring up your rails server.  You can then text/email your client the http address that is listed next to the row that says Forwarding.

Pretty simple right?  For my use with Shopify I had to take it a little further.  I copied the https version listed next to Forwarding and then had to make a few adjustments in the Shopify admin tool for the development version of the application I am working on.

Do you like this solution?  Do you have a better one?  Please let me know.  I'm always eager to learn new things.


HoGi...

*Learning something new every day*

