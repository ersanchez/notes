# Git Notes

This file contains my notes on `git`.

## Add Your User Information

Before you make any git commits, you need to enter your user information into git. At a minimum, you will need to enter your:

* name
* email address
* preferred text editor
    * if you are not sure, you can leave this editor blank - git will choose your system's default editor

You can enter your information into git in two ways:

1. type a command in the command line
2. edit a configuration file

Before we do that, though, you need to know that there are actually three locations where your git user information can exist. This is because you might want to use different git information for your user depending ont the project you are working on. 

If your user information exists in two or more levels listed here, the higher level information will be used in place of information existing lower in the list. Local information overrides global information which overrides system-wide information. Here are those three user information locations:

* `.git` folder within the repository you are working in
    * this is the 'local' git configuration file
* `/home/user/.gitconfig` your .gitconfig file in your home directory
    * this one is referred to as the 'global' git configuration file
* `/etc/gitconfig` a system-wide or 'system' configuration file
    * depending on your setup this may or may _not_ exist
    * you can create it if you need it (and if you have admin rights)

As mentioned above there are two ways to enter user information. Let's start at looking at the command line. You can enter your information using this git command: `git config --level user.attribute "Name"` where --level will be either `--local` or `--global`. Here's an example of how to enter user information on the command line:

1. Add to the **local** configuration file from the command line

    $ git config --local user.name "Alice Brooks"
    $ git config --local user.email "aliceb@example.com"
    
 This will enter the user information into the `.git/config` direcotry in your working repository (the directory you are working in right now).
 
2. Add to the **global** configuration file from the command line

    $ git config --global user.name "Ms. Alice B. Brooks"
    $ git config --global user.email "abrooks4@example.com"

3. Add to the **system** configuration file from the command line

    $ git config --system user.name "Alice B. Brooks, CPA"
    $ git config --system user.email "ab4@office23.example.com"
       

## Start a New Project

There are two main ways to start a new project:

1. Initialize an existing (or newly-made) directory
2. Clone an existing directory (local or remote)

#### Initialize an Existing Directory

Navigate into the directory you would like git to start tracking.

	$ git init 

#### Clone an Existing Directory

Navigate to the directory in which you wish to clone the remote repository.

Get the link to the repository you want to make a local clone of. 

	$ git clone https://linktoremoterepo.com/repositoryname.git

This will clone everything from the remote repository in the current directory.
