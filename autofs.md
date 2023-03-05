# AutoFS

Use AutoFS to automatically mount/unmount NFS shares. See the note named `nfs` for background information.

AutoFS will mount any subdirectories listed in the `/etc/server-nfs-shares` (or whatever you have named your mountpoint) so you can delete any existing mountpoints that you have made (if any).

Here is an overview of the process:

* Unmount any shares
* Delete any mountpoints
* Install AutoFS
* Configure AutoFS

## Unmount Shares

Before you delete any mountpoints, you must first unmount the any mounted NFS shares.

    $ sudo unmount /mnt/server-nfs-shares/documents
    $ sudo unmount /mnt/server-nfs-shares/music

Now that you have unmounted the NFS shared directory, you can delete the mountpoint.

## Delete Mountpoints

You can now delete the mountpoints that AutoFS will be automatically mounting and unmounting.

**WARNING** running this command on a mounted NFS share will delete the contents (if you have read/write) access.

    $ cd /mnt/server-nfs-shares
    $ sudo rm -r documents
    $ sudo rm -r music
    
## Install AutoFS

AutoFS is part of the Ubuntu package repository. Here's how you can install it.

    $ sudo apt install autofs
    
You can verify that it is running by typing this command:

    $ systemctl status autofs
    
## Configure AutoFS

There are two files you will need to edit to configure AutoFS:

* `/etc/auto.master` - this is the first file that AutoFS reads 
* `/etc/auto.nfs` 

Let's edit `/etc/auto.master` first. Add a line to the very end of the existing `auto.master` file that references your base mountpoint and the `auto.nfs` file.

    $ sudo vim /etc/auto.master
    
Navigate to the end of the file and add:

    /mnt/server-nfs-shares /etc/auto.nfs --ghost --timeout=60
    
* Substitute the path to your base mountpoint for `/mnt/server-nfs-shares`
* `/etc/auto.nfs` is just a pointer to this file which contains the info for the NFS shares that AutoFS will manage
* --ghost
* --timeout=60

Save and close the `/etc/auto.master` file.

Next, edit `/etc/auto.nfs`

