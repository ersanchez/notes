# NFS Notes

This note shows you how to set up the network file system (NFS) to store files on one central server and access the files from one or more client computers like your desktop or laptop. 

These steps have been tested on Ubuntu 22.04 server and desktop. This note is aimed at home users sharing files on a local LAN not exposed to the Internet.

Here is an overview of the steps you need to take to get things running:

1. Create a shared directory on your server
2. Install NFS on your server
3. Configure NFS on your server
4. Install NFS on the client
5. Create a mount point on the client
6. Configure NFS on the client

After you have a handle on this process, see the note named `autofs` to see how you can automount NFS shares. This will make your life much easier.

## Create a Shared Directory on Server

If you don't already have a server directory that you want to share you can create one now. Here, I will make a directory called `nfs-shares`.

    $ sudo mkdir /nfs-shares
    
You can also create any subdirectories:

    $ sudo mkdir /nfs-shares/documents
    $ sudo mkdir /nfs-shares/music
    
Populate these directories with a test file or two if they are empty. This will help you when you test the NFS functionality on the client computer later on in this note.

Note: If you're using `zfs` you can set the `sharenfs` parameter on the server's zfs dataset you wish to share.

    $ sudo zfs set sharenfs=on poolName/datasetName
    
## Install NFS on Server

Use the built in `apt` package manager to install the NFS service.

    $ sudo apt install nfs-kernel-server
    
## Configure NFS on Server

NOTE: If you toggled the `sharenfs=on` on your zfs dataset on the server you can skip this step.

Edit the `/etc/exports` file on the server to include the directories you want to share with your client computers.

    $ sudo vim /etc/exports

Add a line in `/etc/exports` on your server for each shared directory in the following format:

    /nfs-shares/documents   192.168.1.0/255.255.255.0(rw,no_subtree_check)
    /nfs-shares/music       192.168.1.0/255.255.255.0(rw,no_subtree_check)
    
Where:

* `192.168.1.0/255.255.255.0` is the network and subnet mask for your home network
* `rw` means that you will export this directory as read/write
    * you can choose `ro` for "read only" if you do not want anyone accidentally deleting your files
* `no_subtree_check` helps out security on your network

Save the `/etc/exports` file.

Next either restart the NFS service on the server.

    $ sudo systemctl restart nfs-kernel-server

## Install NFS on Client

On your client computers (desktop, laptop, etc.) install `nfs`:

    $ sudo apt install nfs-common
    
Repeat this step on any of your client computers on which you want to access the server's shares.
    
## Create Mount Point on Client

On your client make a directory upon which you will mount (attach) the server's shared directories. The name does not matter. In this case I'm giving it the name, `server-nfs-shares`, to make what I'm doing clear.

    $ sudo mkdir /mnt/server-nfs-shares
    
## Mount Server NFS Shares on Client

From the client computer you can list the server's shared directories. Insert your server's ip address in place of the `192.168.1.1` ip address:

    $ showmount --exports 192.168.1.1

When you type the command listed above on your client computer you'll get output that looks like this:

    Export list for 192.168.1.1:
    /nfs-shares/documents   192.168.1.0/255.255.255.0
    /nfs-shares/music       192.168.1.0/255.255.255.0
    
Now you can `mount` the server's shares onto your client's file system. Let's start by mounting the server's `/nfs-shares/documents` directory onto the client's `/mnt/server-nfs-shares` mountpoint.

    $ sudo mount 192.168.1.1:/nfs-shares/documents /mnt/server-nfs-shares
    
If you successfully mounted the server's shared directory onto your client's mountpoint, you should now see files in your client's /mnt/server-nfs-shares directory
