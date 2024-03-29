# AutoFS

Use `autofs` to automatically mount/unmount NFS shares. See the note in my notes repository named `nfs` for background information.

Here is an overview of the setup process:

* Unmount any shares 
* Delete any mountpoints **READ THE WARNING BELOW**
* Install AutoFS
* Configure AutoFS
* Restart AutoFS

## Unmount Shares on Client

Before you delete any mountpoints, on your client computer you must first unmount the any mounted NFS shares.

    $ sudo unmount /mnt/server-nfs-shares/documents
    $ sudo unmount /mnt/server-nfs-shares/music

Now that you have unmounted the NFS shared directory, you can delete the mountpoint.

## Delete Mountpoints on Client

**WARNING running the `rm -r` command on a mounted NFS share will delete the contents (if you have read/write) access!**

You can now delete the mountpoints on the client that AutoFS will be automatically mounting and unmounting.

    $ cd /mnt/server-nfs-shares
    $ sudo rm -r documents
    $ sudo rm -r music
    
## Install `autofs` on Client

AutoFS is part of the Ubuntu package repository. Here's how you can install AutoFS on your client computer.

    $ sudo apt install autofs
    
You can verify that AutoFS is running on the client by typing this command:

    $ systemctl status autofs
    
## Configure `autofs` on Client

There are two files you will need to edit on your client computer to configure `autofs`:

* `/etc/auto.master` - this is the configuration file for `autofs`
* `/etc/auto.nfs` - this map file will contain our list of (Note: you can name this whatever you want)
    * mount points on our client
    * server NFS shares that we want autofs to mount on our client

### Edit `/etc/auto.master`

Let's edit `/etc/auto.master` first. 

Add a line to the very end of the existing `auto.master` file that references your base mountpoint and the `auto.nfs` file.

    $ sudo vim /etc/auto.master
    
Navigate to the end of the `auto.master` file and add:

    /mnt/server-nfs-shares /etc/auto.nfs --ghost --timeout=60
    
* Substitute the path to your base mountpoint for `/mnt/server-nfs-shares`
* `/etc/auto.nfs` is just a pointer to this file which contains the info for the NFS shares that AutoFS will manage
* `--ghost` adds subdirectories
* `--timeout=60` unmounts any shares that you haven't accessed in the last XX seconds - 60 seconds in this case

Save and close the `/etc/auto.master` file.

### Create & Edit `auto.nfs`

Next, create and edit the map file `/etc/auto.nfs` on your client computer:

    $ sudo vim /etc/auto.nfs
    
In this example I will use the two example NFS server shares from the note in this repository titled, `nfs.md`. Please refer to that separate note for background information.

Add the server's NFS share information to the `auto.nfs` file:

**WARNING: Do _NOT_ insert any tabs or extra spacing to make this look pretty**

    documents -fstype=nfs4,rw 192.168.1.1:/nfs-shares/documents
    music -fstype=nfs4,rw 192.168.1.1:/nfs-shares/music
    
This follows the `mount-point options location` format where:
    
* a `documents` is the mount point that will be created in the client directory we specified when we edited `auto.master` above
    * in this case, a ghost directory `documents` will be created on our client computer at `/mnt/server-nfs-shares/documents`
    * note: this `documents` does not need to be the same as the server's `documents`, you can name it anything
* `-fstype=nfs4` is the mount options - this is the NFS protocol version number
* `rw` mount the NFS share as read/write - alternative is `ro` or read-only'
* `192.168.1.1:/nfs-shares/documents` is the source ip address of the NFS server & path that will be mounted on the client
    * substitute your server's ip address here
    * don't forget the `:` in between the NFS server's ip address and the path to the target mount

Save and close the `/etc/nfs.md` file.

## Restart `autofs`

    $ sudo systemctl restart autofs
    
Navigate to your newly-mounted shares to verify they are there.

    $ cd /mnt/server-nfs-shares/documents
    $ ls
   
Verify that your server's NFS shares are listed.
