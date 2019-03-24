---
title: "Importance of Naming Things"
date: 2016-08-04T16:15:23-04:00
draft: false
---

# Importance of Naming Things

> SUMMARY:  A quick look at the importance of using the correct file name format 
>           when creating Rspec tests.  In this example I had some issues with 
>           the creation of new feature tests and why they wouldn't run when 
>           using rspec spec/features/ 

Recently I have been working on improving my knowledge of testing with MiniTest.  I had an opportunity to do some freelance work and was working on a project that uses RSpec.  This particular project used model and controller tests.  I am more a subscriber of model and feature tests.  For some bugs that I needed to work on there were already some model tests in place.  So I worked with those files, fixed the bugs and moved on to a new feature request.  

Like I mentioned earlier, I prefer Feature tests over Controller tests.  So I set out to work in my comfort zone and created a features directory under the existing spec directory.  I then create two feature files in spec/features.  Let's call them visitor_feature.rb and admin_feature.rb.  I wrote out my tests in each file and then went to run the tests using:


```
rspec spec/features/
```


Hmmm, whey did I get 0 tests ran and 0 failures?  Oh well, let me move on and run my tests this way:


```
rspec spec/features/visitor_feature.rb 
rspec spec/features/admin_feature.rb
```


Ok, those ran and I see the expected failures.  I continued on to wrap up the tests I needed and then the code to make all those tests go green.  When I was done, I ran the entire test suite using rspec spec.  I noticed that my two new feature related tests were not running.

The keen observer may have already seen what my problem was.  I had not used the preferred format of naming my two new feature files.  I should have named them as visitor_spec.rb and admin_spec.rb.  Once I made the file name change, my tests ran as expected when I used either rspec spec/ or rspec spec/features

Ok, before you beat me up, my file names above are just for illustrative purposes.  I normally name my files in a more descriptive way.


HoGi...

*Learning something new every day*

