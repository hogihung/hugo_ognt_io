+++
title = "Go Grapple a Cobra and Rest Easy"
date = 2019-03-23T19:31:19-04:00
draft = true
tags = ['golang']
categories = ['cli', 'cobra']
+++

# Creating a Go Based CLI App (Part One)

Welcome back!  Today I would like to start a multi-part blog posting on a new
project that I am working on.  If you read my first post, *'CIRCLE OF (BLOG) LIFE'*,
you may have seen me mention my first Golang project - [JBlastor](https://github.com/hogihung/jblastor).

While I do use JBlastor at my day job, I have learned a lot since it was created.
Probably the most significant new knowledge was the [Cobra](https://github.com/spf13/cobra)
framework/library.  Interestingly enough, Cobra is written by one of the contributors
to [Hugo](https://gohugo.io/).  JBlastor uses [Kingpin](https://github.com/alecthomas/kingpin)
which served me well at the time, but depending on your needs, you may find that
Cobra is a much better fit.

Today I will not go deep into the details of Cobra, and my new app, 'REST Easy', 
as I want to save the 'details' for future posts.  For those GitHub lurkers, don't
trouble yourself too much looking for a 'REST Easy' repo - it is currently private
as I don't want to expose my many, many mistakes, err, learning opportunities at
this early stage :) 

Currently my plan for 'REST Easy', and this blog, is to provide a learning opportunity
based on my experience of creating the CLI based app.  I would like to provide
the experience in both blog/written form and through video tutorials. So if you
like what you see/read with the upcoming posts, and are like me, a visual learner
and would like to see some video tutorials, please let me know.  Shoot me an
email at 'john_hogarty@ognt.io'

So HoGi, what is this 'REST Easy' app?  I'm glad you asked!  This new project will
server two purposes:

  1.  help me learn more about Golang
  2.  provide a useful and much needed tool for my day job

Ok, ok, so what is it?  Let me answer that by first *showing* you:

```
➜  rest_easy git:(master) ✗ ./rest_easy 
REST Easy is a command line utility, which takes a JSON formatted configuration
file and performs REST GET requests against the defined target endpoints. 

Using this app, with JSON formatted config file, one can run n number of GET requests to the
defined target endpoints and display the response to the terminal (default) and/or write the
responses to a file.

Usage:
  rest_easy [command]

Available Commands:
  adhoc       Use the 'adhoc' sub-command to run GET requests against single endpoint
  help        Help about any command
  list        Use the 'list' sub-command to list the defined endpoints to be tested
  test        Use the 'test' sub-command to run GET requests to defined endpoints

Flags:
  -h, --help         help for rest_easy
      --log string   log file (default is ./rest_easy.log)

Use "rest_easy [command] --help" for more information about a command.
➜  rest_easy git:(master) ✗ 
```

In a nutshell, REST Easy is a command-line (CLI) based application that one can 
use to help them execute 'Health Checks' against a single (AdHoc) or group (via
JSON formatted file) of REST based endpoints.

Future blog posts will illustrate demonstrate how the above was created as well
as cover the build out of each component of the application.  I will attempt to
take small bites in an effort to keep the postings of a reasonable lenght and
not be something one reads to fall asleep.  :)

Until the next posting, be well and keep learning!

HoGi...

*Learning something new every day*
