# ZFS Notes

This version is aimed at FreeBSD. Future revisions will include commands used on GNU/Linux and MacOS.

This note page is intended as an informal stash of commands to help me when I need to quickly replicate an event.

It is not intended to replace the `zfs` or `zpool` manual pages.

## First Steps

This section details commands used for:

* creating a zpool
* creating a zfs filesystem

### Creating a `zpool`

First off you need to determine which disks you will use and what type of redundancy you need.

Use Bidule0hm's [calc][calculator] to determine how the level of redundancy (mirror, raidz1, raidz2, raidz3) will impact your pool capacity. I intentionally left off a stripe of disks. In a striped set, the loss of one disk will cause the entire pool to fail - too risky in my book.

Determine the names of the disks in the system.

	$ sudo camcontrol devlist

This will print a listing of the hardware currently in use. Determine which disks will be used in the new zpool. Be sure to backup any data that used to live on the disk before creating the zpool to avoid data loss.

#### Create a Mirrored Pool

Note, by default the new pool will be mounted at the root of the filesystem. You can change this if you want - check out the [manzpoolF][man] page.

You can create a zpool out of two disks (disk1 is `ada0`, `disk2` is ada1)jthat will be mirrors (exactly the same):

	$ sudo zpool create newPoolName mirror ada0 ada1

This creates a new mirrored pool named "newPoolName" using disks `ada0` and `ada1`.

#### Create a RAIDZ Pool

RAIDZ pools are cool because they can withstand the loss of X number of disks without resulting in a failure of the pool:

* `raidz` (same as `raidz1`) can withstand the loss of one disk
* `raid2` can withstand the loss of two disks
* `raid3` can withstand the loss of three disks

### Create a ZFS Filesystem

In zfs filesystems are cheap - make tons of them. This makes it easier manipulate them later.

	$ sudo zfs create newPoolName/newFilesystemName

## Routine Events

## Major Changes

This section details recipes to use for the following events:

* zfs send and receive - for example, moving a zfs filesystem from one zpool to another zpool
* incremental zfs send and receive - for example backing up a zfs filesystem to a remote location or USB disk

### ZFS Send and Receive

You can move a zfs filesystem from one zpool to another zpool (same or remote computer). Using zfs send and receive buys you error checking on the receiving end. This is more desireable than `dd` or `rsync`.

#### Move a ZFS Filesystem

First determine the name of the most recent snapshot. This snapshot will include the **entire** contents of the zfs filesystem as it was on the date/time of the snapshot. The snapshot might be 100kB and the entire zfs filesystem may be 100 TB. Run `zfs list` to see the size of the zfs filesystem.

You can see all of the snapshots:

	$ zfs list -t snapshot 

Or you can see snapshots of a specific zfs filesystem:

	$ zfs list -t snapshot -r zpoolName/specificZfsFilesystem

If there are no shapshots of the zfs filesystem that you wish to move, create one:

	$ sudo zfs snapshot -r zpoolName/specificZfsFilesystem@TIMESTAMP
	
Use something descriptive for the TIMESTAMP such as the [timestamp][ISO8601] time/date representation.



In this scenario, I will send (copy) a zfs filesystem from the origin to a remote computer over `ssh`. The zfs filesystem to be sent (copied) is named `oringinalFS` living on the `oringinalPool`. It is being sent (copied) to `remotePool` on the remote computer.

Prior to sending and receiving over ssh you have to consider which user (person or automated process) will be doing the send and receive and what permissions that user has on the origin computer and on the remote computer.

I searched all over the interwebs trying to figure this out before I remembered that I bought [zfsmast][FreeBSD Mastery: Advanced ZFS] by Allan Jude and Michael W. Lucas. Problem: solved. 

Jude & Lucas describe the process in Chapter 4. Here is the overview:

* create an unpriviliged user (in my case, _dataslinger_) with SSH keys on both the sending and receiving computers
* give the new user the rights necessary for doing the sending zfs datasets on the local computer
* give the new user the rights necessary for receiving data sets on the remote computer


	# zfs send originalPool/oringinalFS@TIMESTAMP | ssh user@remotehost zfs receive remotePool

[calc]: https://jsfiddle.net/Biduleohm/paq5u7z5/1/embedded/result/
[manzpoolF]: https://mdoc.su/f/zpool
[timestamp]: https://www.iso.org/iso-8601-date-and-time-format.html
[zfsmast]: https://www.tiltedwindmillpress.com/?product=fmaz
