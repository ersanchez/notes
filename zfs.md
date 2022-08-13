# ZFS Notes

This note page is intended as an informal stash of commands to help me when I need to quickly replicate an event.

These notes are intended for a homelab user not a datacenter administrator.

It is not intended to replace the `zfs` or `zpool` manual pages.

## Server Setup

### FreeBSD

FreeBSD comes with ZFS already installed.

### GNU/Linux

Ubuntu server 20.04 installation

	$ sudo apt install zfsutils-linux

## First Steps

This section details commands used for:

* creating a zpool
* creating a ZFS filesystem

### Creating a `zpool`

#### Redundancy

First off you need to determine which disks you will use and what type of redundancy you need.

Use Bidule0hm's [calculator][calc] to determine how the level of redundancy (mirror, raidz1, raidz2, raidz3) will impact your pool capacity. I intentionally left off a stripe of disks. In a striped set, the loss of one disk will cause the entire pool to fail - too risky in my book.

Determine the names of the disks in the system. 

* FreeBSD   

	`$ sudo camcontrol devlist`
	
* Linux   

	`$ sudo hdparm -I /dev/sdX`

This will print a listing of the hardware currently in use. Determine which disks will be used in the new zpool. Be sure to backup any data that used to live on the disk before creating the zpool to avoid data loss.

#### Sector Size

_Most_ hard drives that you can buy today use a 4k sector size. Keep in mind that sometimes modern SSDs report having a 512k sector size when they really have a 4k (or larger) sector size.

This is set using the `ashift` property. `ashift=12` is the setting to use for disks with a 4k sector size.

**Note**: unless you specify this, your zpool will default to a 512b sector size.

You can set the sector size property when you are running the `zpool create` command. Better yet, in FreeBSD, you can add a line to your `/etc/sysctl.conf` file to make sure that the default changes to a 4096k sector size (`ashift=12`) by running this command:

	$ sudo sysctl vfs.zfs.min_auto_ashift=12

#### SSD TRIM

TRIM lets the operating system instantly erase data on solid state drives (SSD).

Enable this for zpools that live on SSDs by adding the option `autotrim=on` during zpool creation.

#### Create a Mirrored Pool

Note, by default the new pool will be mounted at the root of the filesystem. You can change this if you want - check out the [man][manzpoolF] page.

You can create a zpool out of two disks (disk1 is `ada0`, `disk2` is ada1)jthat will be mirrors (exactly the same):

	$ sudo zpool create -o ashift=12 -o autortrim=on newPoolName mirror ada0 ada1

This creates a new mirrored pool named "newPoolName" using disks `ada0` and `ada1`.

#### Create a RAIDZ Pool

RAIDZ pools are cool because they can withstand the loss of X number of disks without resulting in a failure of the pool:

* `raidz` (same as `raidz1`) can withstand the loss of one disk
* `raidz2` can withstand the loss of two disks
* `raidz3` can withstand the loss of three disks    

    `$ sudo zpool create`

	`$ sudo zpool create -o ashift=12 -o autotrim=on newPoolName raidZtype ada0 ada1 ada2 ada3`
	
	where `raidZtype` is either `raidz`, `raidz2`, `raidz3`

### ZFS Filesystem

Before creating a ZFS filesystem there are a few things to consider.

#### Encryption

You *must* set this when creating your ZFS filesystem. You can't encrypt an existing ZFS filesystem.

If you want to encrypt your ZFS filesystem set the following options in your `zfs create` command:

* `encryption=[algorithm]` - `aes-256-gcm` is the current default
* `keylocation=[location]` - this can be the path to somewhere on disk or you can ask the system to `prompt` you for it
* `keyformat=[format]` - passphrase|hex|raw

The passphrase can be anywhere between 8 and 512 bytes long. Whereas both hex and raw must be exactly 32 bytes long.

You'll be prompted twice for the encryption passphrase.

One more note, any child ZFS directories will inherit the encryption of the parent ZFS filesystem.

**NOTE**: If you enable encryption, you will need to enter the key each time you startup the system. This may or may not be desirable depending on the demands on your system.

**NOTE 2**: Your encrypted ZFS filesystem will automagically be mounted and unlocked when you create it. After a reboot, you will need to do two things _in order_ before you can use your encrypted filesystems:

1. unlock it
2. mount it

	`$ sudo zfs load-key -r -a zpoolName/zfsDataset    `
	
	`$ sudo zfs mount -a` to mount _all_ zfs filesystems or `$ sudo zfs mount zpoolName/zfsDataset` to mount a specific dataset

#### Compression

At the time of this edit, lz4 appears to be the most effective compression algorithm for general use.

If you will have database storage on the ZFS filesystem, you may need to choose a different compression setting.

#### File Size

Use the default settings for typical office files (.docx, .pdf, etc.)

If your ZFS filesystem will store huge files (ex: Blu Ray, CD-ROM, etc.) consider enabling the large block feature. **NOTE**: the large_blocks feature must be enabled on the zpool.

#### Creating the ZFS Filesystem

Now that we have that out of the way, let's create the ZFS filesystem

In ZFS filesystems are cheap - make tons of them. This makes it easier manipulate them later.

	$ sudo zfs create newPoolName/newFilesystemName
	
Customized

	$ sudo zfs create -o compress=lz4\ 
	-o feature@large_blocks=enabled\ 
	-o encryption=aes-256-gcm\ 
	-o keylocation=prompt\
	-o keyformat=passphrase\
	newPoolName/newFilesystemName

## Routine Events

### Snapshots

#### Create a Snapshot

Creating a snapshot takes almost no time at all. In order to easily tell snapshots apart, consider using a standard timestamp like [ISO8601][timestamp].

Create a snapshot of a directory and all descendent file systems:

	$ sudo zfs snapshot -r zpoolName/datasetName@TIMESTAMP

Create a snapshot of a specific directory:

	$ sudo zfs snapshot zpoolName/datasetName@TIMESTAMP

#### Destroy a Snapshot

Consider first creating a snapshot as of NOW.

Delete a specific snapshot:

	$ sudo zfs destroy zpoolName/datasetName@TIMESTAMP

#### List Snapshots

See all snapshots:

	$ zfs list -t snapshot

See snapshots in a specific directory:

	$ zfs list -r -t snapshot zpoolName/datasetName

## Major Changes

This section details recipes to use for the following events:

* restoring from a specific snapshot
* ZFS send and receive - for example, moving a ZFS filesystem from one zpool to another zpool
* incremental ZFS send and receive - for example backing up a ZFS filesystem to a remote location or USB disk

### Restore From Snapshot

	# zfs rollback -r tank/home/ahrens@tuesday
	
-r option is needed to force the rollback if more recent snapshots exist

### ZFS Send and Receive

You can move a ZFS filesystem from one zpool to another zpool (same or remote computer). Using ZFS send and receive buys you error checking on the receiving end. This is more desireable than `dd` or `rsync`.

Prior to sending and receiving over ssh you have to consider which user (person or automated process) will be doing the send and receive and what permissions that user has on the origin computer and on the remote computer.

I searched all over the interwebs trying to figure this out before I remembered that I bought [FreeBSD Mastery: Advanced ZFS][zfsmast] by Allan Jude and Michael W. Lucas. Problem: solved. 

Jude & Lucas ([buy their book][zfsmast]) describe the process in Chapter 4. Here is the overview:

* create an unpriviliged user (in my case, _dataslinger_) with SSH keys on both the sending and receiving computers
* give the new user the rights necessary for doing the sending zfs datasets on the local computer
* give the new user the rights necessary for receiving data sets on the remote computer

Before you can do a send and receive, you must have both a user on the sending computer with privileges to send and a user on the receiving computer with privileges to receive.

On the sending computer:

	$ sudo zfs allow -u USERNAME send,snapshot zpoolName/datasetName

On the receiving computer:

	$ sudo zfs allow -u USERNAME create,mount,receive zpoolName/datasetName

#### Move a ZFS Filesystem

This section is for making the first move of a ZFS dataset from an original zpool to a new zpool. Making incremental updates is included in the next section.

First determine the name of the most recent snapshot. This snapshot will include the **entire** contents of the zfs filesystem as it was on the date/time of the snapshot. The snapshot might be 100kB and the entire zfs filesystem may be 100 TB. Run `zfs list` to see the size of the zfs filesystem.

You can see all of the snapshots:

	$ zfs list -t snapshot 

Or you can see snapshots of a specific zfs filesystem:

	$ zfs list -t snapshot -r zpoolName/specificZfsFilesystem

If there are no shapshots of the zfs filesystem that you wish to move, create one:

	$ sudo zfs snapshot -r zpoolName/specificZfsFilesystem@TIMESTAMP
	
Use something descriptive for the TIMESTAMP such as the [ISO8601][timestamp] time/date representation.

In this scenario, I will send (copy) a ZFS filesystem from the origin to a remote computer over `ssh`. The ZFS filesystem to be sent (copied) is named `oringinalFS` living on the `oringinalPool`. It is being sent (copied) to `remotePool` on the remote computer.

Note: the ZFS dataset that is being moved cannot already exist on the target zpool. If a snapshot of the ZFS dataset already exists on the target zpool, you _really_ want to do an incremental update, so, skip to the next section.

	# zfs send originalPool/originalFS@TIMESTAMP | ssh user@remotehost zfs receive remotePool

[calc]: https://jsfiddle.net/Biduleohm/paq5u7z5/1/embedded/result/
[manzpoolF]: https://mdoc.su/f/zpool
[timestamp]: https://www.iso.org/iso-8601-date-and-time-format.html
[zfsmast]: https://www.tiltedwindmillpress.com/?product=fmaz
