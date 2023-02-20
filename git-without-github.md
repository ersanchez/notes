# Git Without Github

Github is fantastic! 

That said, there may be a time when you need to clone, push, and pull to a `git` repository that does not live on Github but rather on a remote computer not set up like Github. This note explains how to accomplish this.

I am capturing this information here because _almost_ every bit of documentation I could find online assumes that you are either using Github or Gitlab. There are not many guides that describe how to use `git` without also using Github or Gitlab.

## Requirements

Here are the things you will need:

* SSH access to a remote server - I will use "server" to describe this computer from now on
    * with `git` installed
* access to a client computer - I am using a laptop, so I will call it "laptop" in this note
    * with `git` installed

## Initial Conditions

Here are some tasks that I will describe:

1. Create a `git` repository
1. Clone a repository from your server to your laptop
1. Clone a repository from your laptop to your server

## Some Things to Know

* A directory (folder) is not a repository until you initialize it with `git`
   * After you initialize the directory it becomes a repository
* You can't just start syncing a repository back-and-forth between your server and your laptop
   * You must first export that existing repository into what is called a _bare_ repository
   * You will clone this bare repository


## Detailed Tasks

Here are the tasks we will perform:

* initialize a repository
* clone the new repository into a bare repository that we can clone onto other computers

### Initializing a Repository

Let's say you have a directory (folder) filled with server configuration scripts that you want track with `git`. Your directory is named `server-scripts` and it lives in your home directory: `/home/alice/server-scripts`. 

We will start by initializing the `server-scripts` directory into a `git` repository. 

Initialize the directory by navigating into the directory and then issuing the `git init` command

    $ cd /home/alice/server-scripts
    $ git init
    
You will get several lines of information about how to change the name later on - which you can ignore for now - and one final line that says:

`Initialized empty Git repository in /home/alice/server-scripts/.git/`

When you issued the `git init` command inside the `server-scripts` folder it added a hidden directory, `.git` that contains files and subdirectories that `git` will use to track any changes you make from now on.

### Task: Create a Bare Directory

Next we will take the newly initialized repository and convert it into a bare repository named `script-repo.git`. The `.git` on the end of the file name helps people identify it as a `git` repo and not just a regular directory. 

**NOTE**: the directory and the repository can have the same name, but guides that use the same names suck because they are very confusing to newbies (_like me_).

    $ git clone --bare server-scripts script-repo.git
    Cloning into bare repositoyr 'script-repo.git`...
    done.
    
    
    
