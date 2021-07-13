# lvm-autosnap #

## Introduction ##

**lvm-autosnap** takes and maintains a periodic series of *lvm* snapshots for an arbitrary logical volume ##

## About ##

Yet another BASH script to take *lvm* snapshots?
Yes, indeed, but it's written to try to overcome the fragility of a script that parses
command line output then breaks in weird and wonderful ways.
So, instead of messing around with parsing dates and trying to do maths in BASH,
we simply ask *lvm* to do it for us.
Shocking eh?

The other big difference is that although the snapshots are dated using an ISO-86-1 format,
it's the metadata creation date that is used for processing, not the filename.
The advantage here is that one can rename the snapshots if desired, yet they will still process as normal.

A further difference is that **lvm-autosnap** snapshots are identified by a metadata tag,
causing **lvm-autosnap** to be immune from filename changes.
Thus, one can simply remove the tag `lvchange --deltag autosnap <vg>/<snapshot>` to allow
the snapshot to remain, if needed, for posterity.
Likewise, a tag can be added to a snapshot `lvchange --addtag autosnap <vg>/<snapshot>`
to add it to the pool of snapshots considered by **lvm-autosnap**.

## Usage ##

```sh
lvm-autosnap --vg=<vg> --lv=<lv> [optional parameters]
```

### Required parameters ###

+ -g | --vg
  
  The logical volume group

+ -l | --lv
  
  The logical volume name

### Optional parameters ###

+ -h | --help

  Hi there!

+ -d | --dry-run
  
  List commands, do not commit changes  

+ -k | --keep-time
  
  Retention period in "keep-units". Default=days

+ -m | --max-daily
  
  Maximum number of snaphots allowed per day, Default=5

+ -s | --size
  
  Snapshot size. default=2G

+ -t | --tag
  
  Tag to be applied to snapshots. Default=autosnap

+ -u | --keep-units
  
  Retention period (see keep-units). Default=8

### Examples ###

+ lvm-autosnap --vg=mv_vg --lv=my_lv
  
  take an autosnap of mv_vg/my_lv, prune using the default retention period of 8 days

+ lvm-autosnap --vg=mv_vg --lv=my_lv --size=4G

  take a 4G autosnap of mv_vg/my_lv, prune using the default retention period of 8 days

+ lvm-autosnap --dry-run --vg=mv_vg --lv=my_lv --keep-time=1 --keep_units=hour
  
  SIMULATE an autosnap of mv_vg/my_lv, with pruning of other autosnaps older than one hour

## Installation ##

+ Copy the *lvm-autosnap* executable to the directory of your choice.
  + `cp lvm-autosnap /usr/local/sbin/.`
+ Make it executable
  + `chmod 755 /usr/loca/sbin/lvm-autosnap`
+ Copy and amend the setting for logrotate and cron
  + `cp etc/logrotate.d/lvm-autosnap /etc/logrotate.d/.`
  + `cp etc/cron.daily/lvm-autosnap /etc/cron.daily/.`
+ Additionally, pay attention to the follow settings in
*/etc/lvm/lvm.conf*
  + snapshot_autoextend_threshold
  + snapshot_autoextend_percent
  + thin_pool_autoextend_threshold
  + thin_pool_autoextend_percent

---
Have fun -- aj
