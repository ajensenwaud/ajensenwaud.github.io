---
layout: post
title:  "Simple incremental backup to external drive using ZFS"
date:   2017-01-15 09:04:21 +1100
categories: freebsd zfs
---
Being a ZFS fan / geek, I have multiple methods to backup my home storage. One of them is monthly backup to an external drive that I keep in a safe at work for which I only have the keys. The last Friday every month I bring the disk home, run the backup over the weekend, and return it to the safe on the Monday.

Instead of manually running the backup scripts every time I bring it home, I have a simple shell script that automates the task. All I have to do is to plug in the drive, ssh to the server, and run the shell script below. You might find it handy.

Note: In the below, *zdata* is my primary data pool (three WD Reds running in zraid1), *zssd* is my root file system on my SSD, and *zback* is the external drive.

The first time you run the script it will take some time because it will have to copy the entire data set(s) across. After that it will run incrementally (and therefore much faster next time).

I explicitly call each individual data set I wish to back up below; I could easily have used recursive zfs snapshots / send / receive to make it shorter, but there are data sets I do not wish to copy across. Therefore the script seems a bit repetitive. Feel free to amend it to suit your own needs

{% highlight shell %}
#!/bin/sh
SNAPSHOT="snap-`date -j +"%Y%m%d%H%M%S"`"
echo Named new ZFS snapshot: $SNAPSHOT
#zfs snapshot -r zdata@$SNAPSHOT

/root/zfs-backup.sh zssd/usr/home zback
/root/zfs-backup.sh zdata/media zback
/root/zfs-backup.sh zdata/backup zback
/root/zfs-backup.sh zdata/photos zback
/root/zfs-backup.sh zdata/btsync/archive zback/btsync
/root/zfs-backup.sh zdata/btsync/personal zback/btsync
/root/zfs-backup.sh zdata/btsync/books zback/btsync

#zfs destroy -r zdata@$SNAPSHOT
{% endhighlight %}

Happy ZFS hacking!