+++
date = "2017-06-17T08:11:04+05:30"
description = ""
tags = ["Github", "bash", "Ubuntu", "Mint"]
title = "Create Github repository from command line using shell script"

+++

## Welcome to my second post.

In this post, we will see how to create a new [Github](https://Github.com/) repository from command line/terminal using shell script (bash to be precisely) in Linux.

My OS is [Ubuntu 16.04 LTS](https://www.ubuntu.com/download/desktop) and I've tested the script on [Linux Mint](https://www.linuxmint.com/) as well (Linux Mint is created on top of Ubuntu).

Since last night, I'm struggling hard to automate build and deployment of my [Hugo](https://gohugo.io/overview/introduction/) website in a very simple fashion using bash script. Even though I've reached half-way through but I've no plans of implementing it in the near future. Breaking little things in Hugo with my bare hands seems a logical choice right now, which is why I'm avoiding automation at all costs. But once satisfaction will reach a certain point, I'll certainly implement full automation (to the extent it can happen).

Thinking about full automation, the idea of having entire things in shell scripts crossed my mind. So, I started with the very basic one - 

*Create a new Github repository from command line/terminal using [bash shell script](https://en.wikibooks.org/wiki/Bash_Shell_Scripting).*

After googling few times, I came to realize that such scripts are available but they are not optimized according to my needs. Hence, I created my own bash script which you can see below -

### Assumptions -
1. A verified Github account
2. Github username (not emailID) and password
3. [Git](https://en.wikipedia.org/wiki/Git) must be installed on local system, if not [install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) first
4. Doesn't matter if git global config settings are setup or not

### Limitations -
1. Tested only on personal Github user account
2. Will not work if 2FA(factor authentication) is activated in Github account

> NOTE: will modify script in future to allow for 2FA activated Github accounts

### Execution -
1. Copy below script in an empty file and save it as 'anyname'.sh (mine is `repo.sh`)
2. Create a new folder/directory (mine is `momos`) which will be local repository
3. Copy `repo.sh` script inside `momos`
4. Open a terminal, cd to the folder `momos`, and execute the script by typing `bash repo.sh`
5. Enter username (not emailID) & password of Github account as and when asked by terminal

### Advantages -
1. Option to create a new Github repo name different from the folder/directory name inside which `repo.sh` is placed

### Script -

```
#!/bin/bash
# 
# repo.sh
# Create a new repository in github

# clear the screen
clear

# get github username
ssh -T git@github.com >& data.txt
username=$(grep -Po 'Hi \K[^!]+' data.txt)
echo $username
rm data.txt

# get reponame
dir_name=`basename $(pwd)`
read -p "Do you want to use '$dir_name' as a repo name?(y/n)" answer_dirname
case $answer_dirname in
  y)
    # use currently dirname as a reponame
    reponame=$dir_name
    ;;
  n)
  	# create custom directory and use it as a reponame
  	# if blank then current directory will be used as a reponame
    read -p "Enter your new repository name: " reponame
    if [ "$reponame" = "" ]; then
        reponame=$dir_name
    fi
    ;;
  *)
    ;;
esac


# create repo
echo "Creating github repository '$reponame' ..."
curl -u "$username" https://api.github.com/user/repos -d '{"name":"'$reponame'"}'
echo " done."

# create README.md having text inside
echo "Creating README ..."
touch README.md
echo "Creating a new github repository from terminal using script" >> README.md
echo " done."

# push to remote github repo
echo "Pushing to remote ..."
git init
git add -A
git commit -m "first commit"
git remote add origin https://github.com/"$username"/$reponame.git
git push -u origin master
echo " Congrats!!"
echo " Your new github repository $reponame has been created ."

```


