---
title: "Build Rest Easy, A Command Line Utility Written In Go"
date: 2019-05-14T19:31:19-04:00
draft: false
---

# LESSON ZERO

Near the end of March, I published an article, "Go Grapple a Cobra and REST Easy"
over at [Medium](https://medium.com/@hogihung/go-grapple-a-cobra-and-rest-easy-6996d5a4cc1c)
as well as the new home of my blog at [OGNT](https://ognt-io-hugo.herokuapp.com/posts/go-grapple-a-cobra-and-rest-easy/).  

In that article I teased about future posts/lessons that I have planned to create.
These posts/lessons will take us through the steps of building out the 'rest_easy'
application.  As a refresher, from a 100 ft. view, Rest Easy is a command line
utility that will look like the following:

```
➜  rest_easy: ./rest_easy
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
    ➜  rest_easy:
```

The original purpose of the 'Rest Easy' application was to give me a much needed
command line tool for my day job, as a project for me to build out in a new
programming language [Golang](https://golang.org/)

My hope is to share some knowledge for other new gophers who need to see how to
build a command line utility using go.  Depending on how well these posts/lessons 
go, I would like to reproduce this project in other languages, e.g. Elixir and 
Ruby.

Let's review the proposed Lesson Outline and wrap up this posting!

## Lesson Outline

1.  Initial setup
  - Confirm go is installed
  - Add the [Cobra](https://github.com/spf13/cobra) library/framework
  - Configure your cobra environment (~/.cobra.yaml)
  - Demonstrate cobra tool with --help flag to see options
  - Create basic rest_easy application
  - Initialize git for our project

2.  Create Sub-Commands
  - Create a new branch for addition of sub-commands via cobra
  - Create sub-command list
  - Create sub-command test
  - Create sub-command adhoc

3.  Add logging using [Logrus](https://github.com/sirupsen/logrus) library
  - Create a new branch for implementing logging via Logrus
  - Add basic logging to standard out for each sub-command

4.  Discuss the targets.json file
  - Create a new branch for dealing targets.json file
  - Review the different settings we will support which include:
    - Auth => None, Basic, Token
    - User
    - Pass
    - Token
    - Target URL
    - Label
    - Group

5.  Add file reading/writing support
  - Create a new branch for addition of reading/writing functionality
  - Modify our logging via Logrus to write to a log file
  - Check for existence and be able to read our targets.json file

6.  Create data structure to handle data from targets.json
  - Create a new branch for working with new struct
  - Discuss type struct in go
  - Build a struct to handle data from targets.json file, which can support JSON format

7.  Begin build out of List sub-command
  - Create branch for build out of List sub-command
  - Enable List sub-command to process contents of data in targets.json file
  - Start with listing of 'all' entries of targets.json file  
  - Have List sub-command print the results

8.  Add Filtering to List sub-command
  - Create branch for the work involved with adding filtering for List sub-command
  - Update/Refactor our List code from previous lesson to allow for filtering on:
    - all
    - label (selection)
    - group
  - Enable List sub-command to print only filtered items to the screen

9.  Begin build out of Test sub-command
  - Create new branch for build out of Test sub-command
  - Test sub-command shares some behaviour of List command including:
    - Filtering on all, label (selection) or group
  - Initally we will print to screen

10. Continue work on Test sub-command
  - Create a new branch for additional work on Test sub-command
  - Remove printing of filtered data to screen
  - Build logic to perform REST call for each defined record in targets.json
  - Ensure our REST calls are only executed for the desired filtered items

11. Update initial Adhoc sub-command logic to perform REST call
  - Create a new branch for work on the Adhoc sub-command
  - Leverage what we learned, and code we wrote for the Test sub-command
  - Ensure we can successfully run an Adhoc command for testing a REST endpoint


I've tried to make the above Outline comprehensive and cover all the bits and
pieces we will need to assemble in order to build out the 'rest_easy' command
line utility.

As I move forward with creating and publishing each lesson, should I find that
the lesson is too long, I will break it down into multiple lessons.

You can find the Github repo for the code that we will build, as well as all of
the lesson plans, on my Github:  [rest_easy_go](https://github.com/hogihung/rest_easy_go).

Until the next posting, be well and keep learning!

HoGi...

*Learning something new every day*

