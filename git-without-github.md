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
* Existing repositories on your laptop that synchronize with an online service like Github or Gitlab won't be affected
    * New connections between your server and laptop will function independently of the existing connections that you already have

## Detailed Tasks

Here is a list of all the tasks we will perform. The tasks are explained with examples below this list.

* initialize a directory into a git repository
* clone the git-initialized repository on the server into a bare repository on the server
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

### Task: Clone the git-initialized Repository into a Bare Repository

Now, we need to create a bare repository. A bare repository is a storage location _to which_ and _from which_ remote users can synchronize. The bare repository is considered the 'central storage location'.

On the **server**, next we move up one level in the directory tree. 

    $ pwd
    /home/alice/server-scripts
    
    $ cd ..   # move up one relative level in the file tree
    
    $ pwd
    /home/alice

After that we will take the newly initialized `git` repository `server-scripts` on the server and _copy_ it into a **bare** repository named `new-bare-repo-on-server.git` on the server. The `.git` suffix on the end of the filename helps people recognize that it is a `git` repo and not just a regular directory. 

**NOTE**: the `git` repository and the new _bare_ `git` repository _can_ have the same name, but guides that use the same names for two different objects suck because this can be confusing to people learning something for the first time (_like me_).

In this example we use these file names:

* `server-scripts`
* `new-bare-repo-on-server.git`

      $ git clone\
      --bare\                      # tells git the type of repo to clone into
      server-scripts\              # directory that we initialized above
      new-bare-repo-on-server.git  # new bare repository

      Cloning into bare repository 'new-bare-repo-on-server.git`...
      done.
    
Ok, now we are ready to start cloning the bare repository named `new-bare-repo-on-server.git` on our laptop computer.

I have some very important points to make about this `new-bare-repo-on-server.git` later on in this note. Please read on.

### Task: Clone Bare Repository From Server to Laptop

The act of cloning the bare repository from the server to the laptop will download the git bare repository to the laptop and set the repository up to:

* `pull changes from the server   to the laptop`
* `push changes to   the server from the laptop`

This is possible because that `.git` directory in the repository will have the URL for the server.

On your **laptop**, navigate to the location on your laptop where you want the git repository to be located. Let's say that you want to put the cloned repository in a specific directory named `git-tracked-files` on your laptop:

    $ cd /home/alice/git-tracked-files/

On your laptop issue the following command to clone the repository from the server to the laptop. Note: we will change the name of the resulting cloned repository one more time to help keep things straight.

    $ git clone alice@example.com:/home/alice/new-bare-repo-on-server.git server-scripts-laptop-copy
    Enter passphrase for key alice_id_ed25519: # Enter your SSH key passphrase if it has one
    remote: Enumerting objects: done.
    remote: Counting objects: done.
    remote: Compressing objects: done.
    remote: Total 233, reused 0, pack-reused 0
    Receiving objects: 100% (233/233), done.

Now you have the git repository cloned onto your laptop as `server-scripts-laptop-copy`. 

Notice that the newly-cloned directory does not have the `.git` suffix on the filename.

On your laptop, you can now make changes to the files in the `server-scripts-laptop-copy` repository. Then you can use the `git add`, `git commit`, `git push`, and `git pull` commands to synchronize changes between your laptop and your server.

**Important note**: In our example, we took a directory on the server `server-scripts`, we initialized it as a git repository, and we cloned the git repository into a bare repository `new-bare-repo-on-server.git`. When we cloned `new-bare-repo-on-server.git` from our server to our laptop, the we renamed the resulting repository on the laptop to `server-scripts-laptop-copy` (no `.git` suffix). Any changes made on the laptop's `server-scripts-laptop-copy` repository that are pushed/pulled to the server will **_only_** affect the `new-bare-repo-on-server.git` bare repository on the server **NOT** the original `server-scripts` repository on the server.

This brings me to my final point: tracking changes on the server. 

Since we cloned `server-scripts` on the server to a bare repository `new-bare-repo-on-server.git` on the server, we no longer have a connection between `server-scripts` and `new-bare-repo-on-server.git` both on the server. While I am certain there _is_ a way to connect the existing `server-scripts` repository on the server with the `new-bare-repo-on-server.git` bare repository also on the server, I have not yet been able to figure it out.

In the meantime, you can just clone the `new-bare-repo-on-server.git` on the server to a new repository the server named `server-scripts-on-server`. Then you can push/pull from `server-scripts-on-server` to `new-bare-repo-on-server.git` (_both on the server_) which can also then be pushed/pulled from `server-scripts-laptop-copy` on the laptop.
    
On the server:

    $ git clone script-bare-repo.git server-scripts-on-server
    
This will result in a new repository on your server named `server-scripts-on-server` (without .git suffix). You can push/pull any changes you make in the `server-scripts-on-server` repository on the server to the `new-bare-repo-on-server.git` repository on the server.

## Final Recap

We now have the following files and capabilities:

* server: `server-scripts` 
    * basis for `new-bare-repo-on-server.git` on the server
    * is no longer connected to `server-scripts` on the server
* server: `new-bare-repo-on-server.git`
    * which can be pulled from _or_ pushed to _from_ `server-scripts-laptop-copy` the laptop
    * which can be pulled from _or_ pushed to _from_ `server-scripts-on-server` the server
    * which can be pulled from _or_ pushed to _from_ any other computer that clones this bare repository
* server: `server-scripts-on-server` 
    * can push/pull to `new-bare-repo-on-server.git` on the server
* laptop: `server-scripts-laptop-copy`
    * clone of `new-bare-repo-on-server.git` on the server
    * can push to or pull from `script-bare-repo.git` on the server
