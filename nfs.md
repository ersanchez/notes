# NFS Notes

These steps have been tested on Ubuntu 22.04 server and desktop. This note is aimed at home users sharing files on a local LAN not exposed to the Internet.

You can use the network file system (NFS) to store files on one central server and access the files from one or more client computers like your desktop or laptop. 

Here is an overview of the steps you need to take to get things running:

1. Create a shared directory on your server
2. Install NFS on your server
3. Configure NFS on your server

## Create a Shared Directory on Server

If you don't already have a server directory that you want to share you can create one now. Here, I will make a directory called `nfs-shares`.

    $ sudo mkdir /nfs-shares
    
You can also create any subdirectories:

    $ sudo mkdir /nfs-shares/documents
    $ sudo mkdir /nfs-shares/music

Note: If you're using `zfs` you can set the `sharenfs` parameter on the server's zfs dataset you wish to share.

    $ sudo zfs set sharenfs=on poolName/datasetName
    
## Install NFS on Server

Use the built in `apt` package manager to install the NFS service.

    $ sudo apt install nfs-kernel-server
    
## Configure NFS on Server

NOTE: If you toggled the `sharenfs=on` for the dataset on the server you can skip this step.

Edit the `/etc/exports` file on the server to include the directories you want to share with your client computers.

Add a line for each directory in the following format:

