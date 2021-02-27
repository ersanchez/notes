# ZFS Notes

This version is aimed at FreeBSD and GNU/Linux. Future revisions will include MacOS.

This note page is intended as an informal stash of commands to help me when I need to quickly replicate an event.

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
* creating a zfs filesystem

### Creating a `zpool`

First off you need to determine which disks you will use and what type of redundancy you need.

Use Bidule0hm's [calculator][calc] to determine how the level of redundancy (mirror, raidz1, raidz2, raidz3) will impact your pool capacity. I intentionally left off a stripe of disks. In a striped set, the loss of one disk will cause the entire pool to fail - too risky in my book.

Determine the names of the disks in the system.

	$ sudo camcontrol devlist

This will print a listing of the hardware currently in use. Determine which disks will be used in the new zpool. Be sure to backup any data that used to live on the disk before creating the zpool to avoid data loss.

#### Create a Mirrored Pool

Note, by default the new pool will be mounted at the root of the filesystem. You can change this if you want - check out the [man][manzpoolF] page.

You can create a zpool out of two disks (disk1 is `ada0`, `disk2` is ada1)jthat will be mirrors (exactly the same):

	$ sudo zpool create newPoolName mirror ada0 ada1

This creates a new mirrored pool named "newPoolName" using disks `ada0` and `ada1`.

#### Create a RAIDZ Pool

RAIDZ pools are cool because they can withstand the loss of X number of disks without resulting in a failure of the pool:

* `raidz` (same as `raidz1`) can withstand the loss of one disk
* `raid2` can withstand the loss of two disks
* `raid3` can withstand the loss of three disks    

    $ sudo zpool create

	$ sudo zpool create newPoolName raidZtype ada0 ada1 ada2 ada3

### Create a ZFS Filesystem

In zfs filesystems are cheap - make tons of them. This makes it easier manipulate them later.

	$ sudo zfs create newPoolName/newFilesystemName

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
* zfs send and receive - for example, moving a zfs filesystem from one zpool to another zpool
* incremental zfs send and receive - for example backing up a zfs filesystem to a remote location or USB disk

### Restore From Snapshot

	# zfs rollback -r tank/home/ahrens@tuesday
	
-r option is needed to force the rollback if more recent snapshots exist

### ZFS Send and Receive

You can move a zfs filesystem from one zpool to another zpool (same or remote computer). Using zfs send and receive buys you error checking on the receiving end. This is more desireable than `dd` or `rsync`.

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

This section is for making the first move of a zfs dataset from an original zpool to a new zpool. Making incremental updates is included in the next section.

First determine the name of the most recent snapshot. This snapshot will include the **entire** contents of the zfs filesystem as it was on the date/time of the snapshot. The snapshot might be 100kB and the entire zfs filesystem may be 100 TB. Run `zfs list` to see the size of the zfs filesystem.

You can see all of the snapshots:

	$ zfs list -t snapshot 

Or you can see snapshots of a specific zfs filesystem:

	$ zfs list -t snapshot -r zpoolName/specificZfsFilesystem

If there are no shapshots of the zfs filesystem that you wish to move, create one:

	$ sudo zfs snapshot -r zpoolName/specificZfsFilesystem@TIMESTAMP
	
Use something descriptive for the TIMESTAMP such as the [ISO8601][timestamp] time/date representation.

In this scenario, I will send (copy) a zfs filesystem from the origin to a remote computer over `ssh`. The zfs filesystem to be sent (copied) is named `oringinalFS` living on the `oringinalPool`. It is being sent (copied) to `remotePool` on the remote computer.

Note: the zfs dataset that is being moved cannot already exist on the target zpool. If a snapshot of the zfs dataset already exists on the target zpool, you _really_ want to do an incremental update, so, skip to the next section.

	# zfs send originalPool/originalFS@TIMESTAMP | ssh user@remotehost zfs receive remotePool

[calc]: https://jsfiddle.net/Biduleohm/paq5u7z5/1/embedded/result/
[manzpoolF]: https://mdoc.su/f/zpool
[timestamp]: https://www.iso.org/iso-8601-date-and-time-format.html
[zfsmast]: https://www.tiltedwindmillpress.com/?product=fmaz
