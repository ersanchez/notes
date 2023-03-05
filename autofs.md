# AutoFS

Use AutoFS to automatically mount/unmount NFS shares. See the note named `nfs` for background information.

AutoFS will mount any subdirectories listed in the `/etc/server-nfs-shares` (or whatever you have named your mountpoint) so you can delete any existing mountpoints that you have made (if any).

Here is an overview of the process:

* Unmount any shares
* Delete any mountpoints
* Install AutoFS
* Configure AutoFS

## Unmount Shares on Client

Before you delete any mountpoints, on your client computer you must first unmount the any mounted NFS shares.

    $ sudo unmount /mnt/server-nfs-shares/documents
    $ sudo unmount /mnt/server-nfs-shares/music

Now that you have unmounted the NFS shared directory, you can delete the mountpoint.

## Delete Mountpoints on Client

You can now delete the mountpoints on the client that AutoFS will be automatically mounting and unmounting.

**WARNING** running the `rm -r` command on a mounted NFS share will delete the contents (if you have read/write) access!

    $ cd /mnt/server-nfs-shares
    $ sudo rm -r documents
    $ sudo rm -r music
    
## Install AutoFS on Client

AutoFS is part of the Ubuntu package repository. Here's how you can install AutoFS on your client computer.

    $ sudo apt install autofs
    
You can verify that AutoFS is running on the client by typing this command:

    $ systemctl status autofs
    
## Configure AutoFS on Client

There are two files you will need to edit on your client computer to configure AutoFS:

* `/etc/auto.master` - this is the first file that AutoFS reads 
* `/etc/auto.nfs` 

Let's edit `/etc/auto.master` first. Add a line to the very end of the existing `auto.master` file that references your base mountpoint and the `auto.nfs` file.

    $ sudo vim /etc/auto.master
    
Navigate to the end of the file and add:

    /mnt/server-nfs-shares /etc/auto.nfs --ghost --timeout=60
    
* Substitute the path to your base mountpoint for `/mnt/server-nfs-shares`
* `/etc/auto.nfs` is just a pointer to this file which contains the info for the NFS shares that AutoFS will manage
* `--ghost` adds subdirectories
* `--timeout=60` unmounts any shares that you haven't accessed in the last XX seconds - 60 seconds in this case

Save and close the `/etc/auto.master` file.

Next, edit `/etc/auto.nfs` on your client computer:

    $ sudo vim /etc/auto.nfs
    
In this example I will use the two example NFS server shares from the note titled, `nfs.md`. Please refer to that separate note for background information.

Add the server's NFS share information to the `auto.nfs` file:

    documents -fstype=nfs4, rw 192.168.1.1:/nfs-shares/documents
    music     -fstype=nfs4, rw 192.168.1.1:/nfs-shares/music
    
* a `documents` subdirectory will be created in the client directory we specified when we edited `auto.master` above
    * in this case, `documents` will be created on our client computer at `/mnt/server-nfs-shares/documents`
* `-fstype=nfs4` this is the NFS protocol version number
* `rw` mount the NFS share as read/write - alternative is `ro` or read-only'
* `192.168.1.1` is the ip address of the NFS server - substitute your server's ip address here
* `/nfs-shares/documents` is the path to the NFS share on the server - AutoFS will mount this share at the 

Save and close the `/etc/nfs.md` file.
