---
title: "Pour Some Subdomains on Me"
date: 2016-07-20T19:59:59-04:00
draft: false
---

# Pour Some Subdomains On Me

> SUMMARY:  This post will cover one possible solution for working with subdomains 
>           in a rails development environment.  The solution will use lvh.me and
>           comes with an example Rails 5 application, in a very basic form, to 
>           assist with demonstrating why one would need to use this type of solution.

Recently I began working on a new Rails application.  The goal of this application is to be a SAAS *(Software As A Service)* solution.  This solution would be account based where an account will have an owner.  There will also be users, and those users can be associated with more than one account.  

For our solution, we want to host all of the different accounts on one server with the domain name and url of  *http://demoapp.dev*.  Each account would be a subdomain.  For example, say we have an account named **demon-strings**.  When our users would log in and access their content, they would navigate to:  *http://demon-strings.demoapp.dev*.

I have created a basic Rails 5 application that you can clone if you would like to get some hands on experience.  You can find the application [here](https://github.com/hogihung/demo-app).   Follow the steps provided in the **README.md** file to setup the demo application on your computer.

Here is a brief summary of those steps (please see **README.md** for full details):


```
cd ~/path/to/where/your/projects-go/
git clone git@github.com:hogihung/demo-app.git
cd demo-app
bundle install
rails db:setup
rails db:seed
```


Let's fire up the rails server the normal way using:  rails s

Next, visit our applications home page by pointing your browser to:  http://localhost:3000.  If all went well you should see the typical basic Bootstrap Jumbotron with a 'Create Account' button.  Feel free to create an account.  If you ran the rails db:seed command from above, two accounts were created for you:


```
Subdomain      User                   Password
-------------------------------------------------
root           sysadmin@demo-app.com  pw
demon-strings  sconner@skynet.com     pw
```


Next we will try to log in.  Let's try visitng http://localhost.:3000/users/sign_in.  Hmmm, we get an error - **"The page you were looking for doesn't exist."**

In order to support subdomains, I made an update to the **config/routes.rb** file:


```
class SubdomainPresent
  def self.matches?(request)
    request.subdomain.present? && !EXCLUDED_SUBDOMAINS.include?(request.subdomain)
  end
end

Rails.application.routes.draw do
  constraints(SubdomainPresent) do
    devise_for :users
  end

  root to: "welcome#index"
  resources :accounts

  get '*unmatched_route', to: 'application#raise_not_found'
end
```


So, how can we use a subdomain in our development environment?  If we try to use a subdomain with localhost *(http://demon-strings.localhost:3000/users/sign_in)* we will get an error.  Go ahead and try it, I'll wait.

In the past I have used Pow which you can learn more about [here](http://pow.cx/).  But I learned about a new method while at my last job and came across the topic again recently - lvh.me.  Let's try it out.  Visit the url of: http://lvh.me:3000.

Looks just like when we go to localhost:3000.   Now let's try using our subdomain with lvh and visit:  *http://demon-strings.lvh.me:3000* and *http://demon-strings.lvh.me:3000/users/sign_in.*  You should now be able to log in.  Pretty neat, eh?

The keen observer, or tinkerer will notice that at this point it doesn't matter what the subdomin is.  That topic will be saved for another day.


HoGi...

*Learning something new every day*

