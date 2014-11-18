Practice 3: File Systems (Debian, UNIX)
=======================================

Firstable, a FS is used to organize and store files in a device. There are several ways of doing this and, therefore, there are many types of filesystems, e.g. (Debian): ext2, ext3, ext4, jfs, xfs.
We can find information about our/s FS in our machine viewing some configuration files or typing some commands.
Focusing on our case, we are running a Debian 6 machine. We can check our static file system info on /etc/fstab

    root@debian:/home/lsi# cat /etc/fstab 
    # /etc/fstab: static file system information.
    #
    # Use 'blkid' to print the universally unique identifier for a
    # device; this may be used with UUID= as a more robust way to name devices
    # that works even if disks are added and removed. See fstab(5).
    #
    # <file system> <mount point>   <type>  <options>       <dump>  <pass>
    proc            /proc           proc    defaults        0       0
    # / was on /dev/sda1 during installation
    /dev/sda1	/               ext3    errors=remount-ro 0       1
    # swap was on /dev/sda5 during installation
    /dev/sda5	none            swap    sw              0       0
    /dev/scd0       /media/cdrom0   udf,iso9660 user,noauto     0       0
    /dev/fd0        /media/floppy0  auto    rw,user,noauto  0       0

Check that our machine uses ext3 and swap (used for pagging). 

