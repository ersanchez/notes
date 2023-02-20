# Git Without Github

Github is fantastic! 

That said, there may be a time when you need to clone, push, and pull to a `git` repository that does not live on Github but instead it lives on a remote computer not set up like Github. This note explains how to accomplish this.

I am capturing this information here because _almost_ every bit of documentation I could find online assumes that you are either using Github or Gitlab. There are not many guides that clearly describe how to use `git` to track changes between two computers without also using Github or Gitlab.

## Requirements

Here are the things you will need:

* SSH access to a remote server - I will use "server" to describe this computer from now on
    * with `git` installed & configured with your user information
* access to a client computer - I am using a laptop, so I will call it "laptop" in this note
    * with `git` installed & configured with your user information
* experience using `git` basic commands like `git add`, `git commit`, etc.
    * this note is not intended to be a `git` tutorial

## Some Things to Know

* A directory (folder) is not a repository until you first initialize it with `git`
   * After you initialize the directory it becomes a repository
* You can't just start syncing a repository back-and-forth between your server and your laptop
   * You must first export that existing repository into what is called a _bare_ repository
   * You will clone this bare repository
* I will use an imaginary user named Alice in the demonstration steps

## Detailed Tasks

Here are the tasks we will perform:

* initialize a directory into a git repository
* clone the git repository on the server into a bare repository on the server
* clone the bare repository from the server to the laptop
* clone the bare repository from the server to the server (that is _not_ a typo)

### Initializing a Directory into a `git` Repository

Let's say you have a directory (folder) on your server filled with server configuration scripts that you want track with `git`. Your directory is named `server-scripts` and it lives in your home directory on the server: `/home/alice/server-scripts`. 

We will start by initializing the `server-scripts` directory into a `git` repository. 

On the server, initialize the directory into a `git` repository by first navigating into the `server-scripts` directory and then issuing the `git init` command

    $ cd /home/alice/server-scripts
    $ git init
    
    # You will get several lines of information about how to change the\
    name later on - which you can ignore for now - and one final line\
    that says:

    Initialized empty Git repository in /home/alice/server-scripts/.git/

When you issued the `git init` command inside the `server-scripts` folder it added a hidden directory, `.git` that contains files and subdirectories that `git` will use to track any changes you make from now on.

### Task: Clone the `git` Repository into a Bare Repository

On the server, next we move up one level in the directory tree. 

    $ cd /home/alice

After that we will take the newly initialized `git` repository `server-scripts` on the server and convert it into a bare repository named `script-bare-repo.git` on the server. The `.git` suffix on the end of the filename helps people recognize that it is a `git` repo and not just a regular directory. 

**NOTE**: the `git` repository and the new _bare_ `git` repository _can_ have the same name, but guides that use the same names for two different objects suck because this can be confusing to people learning something for the first time (_like me_).

    $ git clone --bare server-scripts script-bare-repo.git
    Cloning into bare repository 'script-bare-repo.git`...
    done.
    
Ok, now we are ready to start cloning the bare repository named `script-bare-repo.git`.

I have some very important points to make about this `script-bare-repo.git` later on in this note. Please read on.

### Task: Clone Bare Repository From Server to Laptop

The act of cloning the repository from the server to the laptop will download the git repository and set the repository up to:

* pull changes from the server to the laptop
* push changes from the laptop to the server

This is possible because that `.git` directory in the repository will have the URL for the server.

On your laptop, navigate to the location on your laptop where you want the git repository to be located. Let's say that you want to put the cloned repository in a specific directory named `git-tracked-files` on your laptop:

    $ cd /home/alice/git-tracked-files/

On your laptop issue the following command to clone the repository from the server to the laptop. Note: we will change the name of the resulting cloned repository one more time to help keep things straight.

    $ git clone alice@example.com:/home/alice/script-bare-repo.git server-scripts-laptop-copy
    Enter passphrase for key alice_id_ed25519: # (or something like that)
    remote: Enumerting objects: done.
    remote: Counting objects: done.
    remote: Compressing objects: done.
    remote: Total 233, reused 0, pack-reused 0
    Receiving objects: 100% (233/233), done.

Now you have the git repository cloned onto your laptop as `server-scripts-laptop-copy`. 

Notice that the newly-cloned directory does not have the `.git` suffix on the filename.

On your laptop, you can now make changes to the files in the `server-scripts-laptop-copy` repository. Then you can use the normal `git add`, `git commit`, `git push`, and `git pull` to synchronize changes between your laptop and your server.

**Important note**: In our example, we took a directory on the server `server-scripts`, we initialized it as a git repository, and we cloned the git repository into a bare repository `script-bare-repo.git`. When we cloned `script-bare-repo.git` from our server to our laptop, the resulting repository on the laptop was renamed `server-scripts-laptop-copy` (no `.git` suffix). Any changes made on the laptop's `server-scripts-laptop-copy` repository  that are pushed/pulled to the server will **_only_** affect the `script-bare-repo.git` bare repository on the server **NOT** the original `server-scripts` repository on the server.

This brings me to my final point: tracking changes on the server. 

Since we cloned `server-scripts` on the server to a bare repository `script-bare-repo.git` on the server, we no longer have a connection between `server-scripts` and `script-bare-repo.git` both on the server. While I am certain there is a way to connect the existing `server-scripts` repository on the server with the `script-bare-repo.git` bare repository also on the server, I have not yet been able to figure it out.

In the meantime, you can just clone the `script-bare-repo.git` on the server to a new repository the server named `server-scripts-on-server`. Then you can push/pull from `server-scripts-on-server` to `script-bare-repo.git` (_both on the server_) which can also then be pushed/pulled from `server-scripts-laptop-copy` on the laptop.
    
On the server:

    $ git clone script-bare-repo.git server-scripts-on-server
    
This will result in a new repository on your server named `server-scripts-on-server` (without .git suffix). You can push/pull any changes you make in the `server-scripts-on-server` repository on the server to the `script-bare-repo.git` repository on the server.

## Final Recap

We now have the following files and capabilities:

* server: `server-scripts` 
    * basis for `script-bare-repo.git` on the server
    * is no longer connected to `server-scripts` on the server
* server: `script-bare-repo.git`
    * can be pulled from _or_ pushed to _from_ `server-scripts-laptop-copy` the laptop
    * can be pulled from _or_ pushed to _from_ `server-scripts-on-server` the server
* server: `server-scripts-on-server` 
    * can push/pull to `script-bare-repo.git` on the server
* laptop: `server-scripts-laptop-copy`
    * clone of `script-bare-repo.git` on the server
    * can push to or pull from `script-bare-repo.git` on the server
