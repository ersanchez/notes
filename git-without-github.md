# Git Without Github

Github is fantastic! 

That said, there may be a time when you need to clone, push, and pull to a `git` repository that does not live on Github but rather on a remote computer not set up like Github. This note explains how to accomplish this.

I am capturing this information here because _almost_ every bit of documentation I could find online assumes that you are either using Github or Gitlab. There are not many guides that describe how to use `git` without also using Github or Gitlab.

## Requirements

Here are the things you will need:

* SSH access to a remote server - I will use "server" to describe this computer from now on
    * with `git` installed & configured with your user information
* access to a client computer - I am using a laptop, so I will call it "laptop" in this note
    * with `git` installed & configured with your user information

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
* I will use an imaginary user named Alice in the demonstration steps


## Detailed Tasks

Here are the tasks we will perform:

* initialize a repository
* clone the new repository into a bare repository that we can clone onto other computers

### Initializing a Repository

Let's say you have a directory (folder) filled with server configuration scripts that you want track with `git`. Your directory is named `server-scripts` and it lives in your home directory: `/home/alice/server-scripts`. 

We will start by initializing the `server-scripts` directory into a `git` repository. 

Initialize the directory by navigating into the `server-scripts` directory and then issuing the `git init` command

    $ cd /home/alice/server-scripts
    $ git init
    
You will get several lines of information about how to change the name later on - which you can ignore for now - and one final line that says:

`Initialized empty Git repository in /home/alice/server-scripts/.git/`

When you issued the `git init` command inside the `server-scripts` folder it added a hidden directory, `.git` that contains files and subdirectories that `git` will use to track any changes you make from now on.

### Task: Create a Bare Directory

Next we move up one level in the directory tree. 

   $ cd /home/alice

After that we will take the newly initialized repository and convert it into a bare repository named `script-repo.git`. The `.git` on the end of the file name helps people identify it as a `git` repo and not just a regular directory. 

**NOTE**: the directory and the repository _can_ have the same name, but guides that use the same names for two different objects suck because this can be confusing to people learning something for the first time (_like me_).

    $ git clone --bare server-scripts script-repo.git
    Cloning into bare repository 'script-repo.git`...
    done.
    
Ok, now we are ready to start cloning the bare repository named `script-repo.git`.

### Task: Clone Bare Repository From Server to Laptop

The act of cloning the repository from the server to the laptop will download the git repository and set the repository up to:

* pull changes from the server to the laptop
* push changes from the laptop to the server

This is possible because that `.git` directory in the repository will have the URL for the server.

Navigate to the location your laptop where you want the git repository to be located. Let's say that you want to put the cloned repository in a specific directory named `git-files` on your laptop:

   $ cd /home/alice/git-files/

Next, on your laptop you will issue the following command to clone the repository from the server to the laptop:

   $ git clone alice@example.com:/home/alice/script-repo.git
   Enter passphrase for key alice_id_ed25519 (_or something like that_):
   remote: Enumerting objects: done.
   remote: Counting objects: done.
   remote: Compressing objects: done.
   remote: Total 233, reused 0, pack-reused 0
   Receiving objects: 100% (233/233), done.


    
    
