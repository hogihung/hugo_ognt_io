---
title: "Own and Fix Mistakes"
date: 2016-07-24T19:59:27-04:00
draft: false
---

# Own And Fix Your Mistakes

> SUMMARY:  This post will cover one possible solution for fixing an issue where 
>           one has committed sensitive data to a remote repo.  I will be using a 
>           tool called BFG Repo Cleaner to assist me in solving this problem.

Recently I've been a bit down since I've had to look for a new career home.  I was driving home from a weekly meetup.  Call it coincidence, but while I was driving and listening to the radio, one of my favorite artists and songs was playing - "You're Only Human (Second Wind)" by **Billy Joel**.  There is a line in the lyrics that hit home with me, especially related to today's post:  **"You're only human, your'e allowed to make your share of mistakes."**

I was notified by a friendly email by github notifications, (thank you Marko!,) that I had a big no-no in one of my repos.  I had included a file in one of my repos that should not have been there.  A file that had semi-sensitive data in it.  Luckily, this info was just for development, but never the less, it shouldn't have been committed.

It is not good enough to just delete the files in your local repo and push the changes.  Remember, git tracks your changes.  Since I haven't perfected time travel yet, I needed to find another way.  Perhaps a way to re-write history.  I will review the steps I followed to remedy this issue.  You can learn more by checking out this [article](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository) on github and this [one](https://rtyley.github.io/bfg-repo-cleaner/) regarding bfg-repo-cleaner.


```
Visit url https://rtyley.github.io/bfg-repo-cleaner/ and download bfg (version 1.12.12 at the time of this posting.)
Take note of where the downloaded file is - for example ~/Downloads/bfg-1.12.12.jar

# Create a location for our recovery operation:
cd ~
mkdir CLEANUP
cd CLEANUP

# Clone the repo with the issue, be sure to use the --mirror flag:
git clone --mirror git@github.com:hogihung/your_repo_name_here.git

# Move the bfg file to our operating room:
mv ~/Downlaods/bfg-1.12.12.jar bfg.jar

# Operate (repair) our patient, err, repo:
java -jar bfg.jar --delete-files your_sensitive_data_file_here your_repo_name_here.git

# Close up the patient
cd your_repo_name_here.git
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```


Depending on your circumstance, you may need to diverege a little bit from my final clean up steps. In my case, I had a file that I want to keep in the root of my repo, but I do not want that file to included when I push to my remote (github.) So I temporarily moved the file out of the directory, updated my .gitignore file file, committed my changes and pushed to the remote.


```
# Assume you are still in the your_repo_name_here.git directory
mv your_sensitive_data_file_here ~/Desktop

vim .gitignore
# Add the following:
your_sensitive_data_file_here

Save and quit (:wq!)

# Add and commit your changes:
git add .
git commit -m 'Remove sensitive data from repo, ensure file will be ignored going forward.'

# Push our changes to the remote:
git push
```


After following the above steps, I visited my repo on github and looked through the history/commits to make sure that my sensitive data was indeed 'erased.'  I strongly encourage you to create a sample repo with some fake 'sensitive' data and push it up to GitHub.  Throw in a handful of commits just so you have some nice history.  Then re-enact surprise and horror that you commited sensitive information and practice the steps I outlined above.  You never know when you too may prove to be **human** and have to perform emergency surgeory.


HoGi...

*Learning something new every day*

