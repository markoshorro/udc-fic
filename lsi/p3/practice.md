Practice 3: Backups and File Systems (Debian)
=============================================

### The original practice
http://www.tic.udc.es/~nino/blog/lsi/prac-semi/lsi-practica3-rev-2013.pdf

# Concepts

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

Check that our machine uses ext3 and swap (used for pagging). As the comments say, we test blkid:

    root@debian:/home/lsi# blkid
    /dev/sda1: UUID="7c98b741-2b0c-4dda-aa98-2e826077d1de" TYPE="ext3" 
    /dev/sda5: UUID="30f4e6eb-5548-4c85-8f9f-4465ac92e16d" TYPE="swap" 

Efectively, our machine uses ext3 and swap. If we want to check out the partitions table:

    root@debian:/home/lsi# fdisk -l

    Disco /dev/sda: 8589 MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders
    Units = cilindros of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00010585
    
    Disposit. Inicio    Comienzo      Fin      Bloques  Id  Sistema
    /dev/sda1   *           1         996     7993344   83  Linux
    La partición 1 no termina en un límite de cilindro.
    /dev/sda2             996        1045      392193    5  Extendida
    /dev/sda5             996        1045      392192   82  Linux swap / Solaris
    
    Disco /dev/sdb: 268 MB, 268435456 bytes
    64 heads, 32 sectors/track, 256 cylinders
    Units = cilindros of 2048 * 512 = 1048576 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000
    
    El disco /dev/sdb no contiene una tabla de particiones válida
    
    Disco /dev/sdc: 268 MB, 268435456 bytes
    64 heads, 32 sectors/track, 256 cylinders
    Units = cilindros of 2048 * 512 = 1048576 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000
    
    El disco /dev/sdc no contiene una tabla de particiones válida

The difference between a logic and a primary partition, mainly, is the purpose. There are three possible partitions: primary, extended and logic. There only can be four primary or three and one extended on a device. These partitions are referenced on a table. Logic partitions are contained in the extended partition. These partitions are needed, e.g, if you want to have multiple file system types on the same devie, e.g. if you want to have multiple SO installed on the same device.
In our case, we have three virtual disk (we have a virtual machine): /dev/sda, /dev/sdb and /dev/sdc. These two last have no partitions.
We can check the disk usage with df, e.g:

    root@debian:/home/lsi# df
    File system       Blocks of 1K      Used    Dispon Use% Mounted on
    /dev/sda1              7867856   3589932   3878260  49% /
    tmpfs                   513452         0    513452   0% /lib/init/rw
    udev                    508600       132    508468   1% /dev
    tmpfs                   513452         0    513452   0% /dev/shm

What does Mounted on means? It means that the file system on the first column, is attached on that level of Unix hierarchy.
If we want to check all devices mounted on our machine:

    root@debian:/home/lsi# mount
    /dev/sda1 on / type ext3 (rw,errors=remount-ro)
    tmpfs on /lib/init/rw type tmpfs (rw,nosuid,mode=0755)
    proc on /proc type proc (rw,noexec,nosuid,nodev)
    sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
    udev on /dev type tmpfs (rw,mode=0755)
    tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
    devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=620)
    fusectl on /sys/fs/fuse/connections type fusectl (rw)
    binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,noexec,nosuid,nodev)

# The practice

We have answer the first question on the previous section, so we will continue with b).
As it says and as we saw before, we have two disks with no partitions. To create new partitions we can use the partition manager command fdisk.

    root@debian:/home/lsi# fdisk /dev/sdb
    El dispositivo no contiene una tabla de particiones DOS válida ni una etiqueta de disco Sun o SGI o OSF
    Building a new DOS disklabel with disk identifier 0x1d7a80ed.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.
    
    Atención: el indicador 0x0000 inválido de la tabla de particiones 4 se corregirá mediante w(rite)
    
    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

    Orden (m para obtener ayuda): 

So this is an interactive partition manager. Typing m:

    Orden (m para obtener ayuda): m
    Orden  Acción
       a   Conmuta el indicador de iniciable
       b   Modifica la etiqueta de disco bsd
       c   Conmuta el indicador de compatibilidad con DOS
       d   Suprime una partición
       l   Lista los tipos de particiones conocidos
       m   Imprime este menú
       n   Añade una nueva partición
       o   Crea una nueva tabla de particiones DOS vacía
       p   Imprime la tabla de particiones
       q   Sale sin guardar los cambios
       s   Crea una nueva etiqueta de disco Sun
       t   Cambia el identificador de sistema de una partición
       u   Cambia las unidades de visualización/entrada
       v   Verifica la tabla de particiones
       w   Escribe la tabla en el disco y sale
       x   Funciones adicionales (sólo para usuarios avanzados)

We are going to create a new partition table on that disk, as follows:

    root@debian:/home/lsi# fdisk /dev/sdb
    El dispositivo no contiene una tabla de particiones DOS válida ni una etiqueta de disco Sun o SGI o OSF
    Building a new DOS disklabel with disk identifier 0x4ba3174a.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.
    
    Atención: el indicador 0x0000 inválido de la tabla de particiones 4 se corregirá mediante w(rite)
    
    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
        switch off the mode (command 'c') and change display units to
        sectors (command 'u').
    
    Orden (m para obtener ayuda): n
    Acción de la orden
    e   Partición extendida
        p   Partición primaria (1-4)
    p
    Número de partición (1-4): 1
    Primer cilindro (1-256, valor predeterminado 1): 
    Se está utilizando el valor predeterminado 1
    Last cilindro, +cilindros or +size{K,M,G} (1-256, valor predeterminado 256): 
    Se está utilizando el valor predeterminado 256
    
    Orden (m para obtener ayuda): t
    Se ha seleccionado la partición 1
    Código hexadecimal (escriba L para ver los códigos): L
    
    ...
    1  FAT12           39  Plan 9          82  Linux swap / So c1  DRDOS/sec (FAT-
    2  XENIX root      3c  PartitionMagic  83  Linux           c4  DRDOS/sec (FAT-
    3  XENIX usr       40  Venix 80286     84  Unidad C: ocult c6  DRDOS/sec (FAT-
    ...
    
    Código hexadecimal (escriba L para ver los códigos): 83
    
    Orden (m para obtener ayuda): w
    ¡Se ha modificado la tabla de particiones!
    
    Llamando a ioctl() para volver a leer la tabla de particiones.
    Se están sincronizando los discos.

Once finished, we are able to make the partitions:

    root@debian:/home/lsi# mkfs -t ext4 /dev/sdc1
    mke2fs 1.41.12 (17-May-2010)
    Etiqueta del sistema de ficheros=
    Tipo de SO: Linux
    Tamaño del bloque=1024 (bitácora=0)
    Tamaño del fragmento=1024 (bitácora=0)
    Stride=0 blocks, Stripe width=0 blocks
    65536 nodos-i, 262128 bloques
    13106 bloques (5.00%) reservados para el superusuario
    Primer bloque de datos=1
    Número máximo de bloques del sistema de ficheros=67371008
    32 bloque de grupos
    8192 bloques por grupo, 8192 fragmentos por grupo
    2048 nodos-i por grupo
    Respaldo del superbloque guardado en los bloques: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185
    
    Escribiendo las tablas de nodos-i: hecho                           
    Creating journal (4096 blocks): hecho
    Escribiendo superbloques y la información contable del sistema de ficheros: hecho
    
    Este sistema de ficheros se revisará automáticamente cada 30 montajes o
    180 días, lo que suceda primero.  Utilice tune2fs -c o -i para cambiarlo.

To check that this partition exists:
    
    root@debian:/home/lsi# blkid 
    /dev/sda1: UUID="7c98b741-2b0c-4dda-aa98-2e826077d1de" TYPE="ext3" 
    /dev/sda5: UUID="30f4e6eb-5548-4c85-8f9f-4465ac92e16d" TYPE="swap" 
    /dev/sdb1: UUID="5290293d-3dd4-4551-a17f-f542dd2d439f" TYPE="ext4"

For /dev/sdc disk, the steps are the same. The final result should be something like:

    root@debian:/home/lsi# blkid 
    /dev/sda1: UUID="7c98b741-2b0c-4dda-aa98-2e826077d1de" TYPE="ext3" 
    /dev/sda5: UUID="30f4e6eb-5548-4c85-8f9f-4465ac92e16d" TYPE="swap" 
    /dev/sdb1: UUID="5290293d-3dd4-4551-a17f-f542dd2d439f" TYPE="ext4" 
    /dev/sdc1: UUID="9b355f01-c1d2-46a3-baaf-a39b0db662da" TYPE="ext4"

So now we can attach these partitions (c) paragraph) to, e.g., directories of our system.
We can complete this action creating directories in /mnt and then _mounting_ /dev/sdb1 and /dev/sdc1 partitions on them.
The secuence could be:

    # mkdir -p /mnt/testA /mnt/testB
    # mount /dev/sdb1 /mnt/testA
    # mount /dev/sdc1 /mnt/testB

Checking if these actions were successful:
    
    root@debian:/home/lsi# mount
    /dev/sda1 on / type ext3 (rw,errors=remount-ro)
    tmpfs on /lib/init/rw type tmpfs (rw,nosuid,mode=0755)
    proc on /proc type proc (rw,noexec,nosuid,nodev)
    sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
    udev on /dev type tmpfs (rw,mode=0755)
    tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
    devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=620)
    fusectl on /sys/fs/fuse/connections type fusectl (rw)
    binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,noexec,nosuid,nodev)
    /dev/sdb1 on /mnt/testA type ext4 (rw)
    /dev/sdc1 on /mnt/testB type ext4 (rw)

We can observe the two last lines to realize that _mount_ was successful.
If we want to _deattach_ these partitions of our system, the actions are analogous:

    root@debian:/home/lsi# umount /dev/sdb1
    root@debian:/home/lsi# umount /dev/sdc1
    root@debian:/home/lsi# mount
    /dev/sda1 on / type ext3 (rw,errors=remount-ro)
    tmpfs on /lib/init/rw type tmpfs (rw,nosuid,mode=0755)
    proc on /proc type proc (rw,noexec,nosuid,nodev)
    sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
    udev on /dev type tmpfs (rw,mode=0755)
    tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
    devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=620)
    fusectl on /sys/fs/fuse/connections type fusectl (rw)
    binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,noexec,nosuid,nodev)

## Backups

dump is a tool for ext2/3/4 backup. It determinates what needs to be saved. If we remember the first configuration file /etc/fstab, we can see column <dump>. All rows have 0 this field, because dump is not installed by default.
This tool has a lot of options. In our case, we just want to make a simple backup, so for making an initial backup we can type:

    # dump -u0 -f <file> <file-to-back-up>

The option -u updates the file /var/lib/dumpdates after a successful dump. -<level#> option, sets the dump level: 0 tells dump to backup the entire FS; a level number above copies all files new or modified since the last dump of a lower level.
In our case, e.g, we could want to bakcup critical data in /dev/sdb1. Imagine we have something like this:

    root@debian:/home/lsi/dump-restore/critical_data# ls -al
    total 8
    drwxr-xr-x 2 root root 4096 dic 16 14:23 .
    drwxr-xr-x 3 root root 4096 dic 16 14:22 ..
    -rw-r--r-- 1 root root    0 dic 16 14:23 file1
    -rw-r--r-- 1 root root    0 dic 16 14:23 file2
    -rw-r--r-- 1 root root    0 dic 16 14:23 file3
    -rw-r--r-- 1 root root    0 dic 16 14:23 file40
    -rw-r--r-- 1 root root    0 dic 16 14:23 file41
    -rw-r--r-- 1 root root    0 dic 16 14:23 file42
    
We can execute:

    root@debian:/home/lsi/dump-restore# dump -0a -f /home/lsi/dump-restore/back.dump critical_data/
    DUMP: Date of this level 0 dump: Tue Dec 16 14:24:08 2014
    DUMP: Dumping /dev/sda1 (/ (dir home/lsi/dump-restore/critical_data)) to /home/lsi/dump-restore/back.dump
    DUMP: Label: none
    DUMP: Writing 10 Kilobyte records
    DUMP: mapping (Pass I) [regular files]
    DUMP: mapping (Pass II) [directories]
    DUMP: estimated 158 blocks.
    DUMP: Volume 1 started with block 1 at: Tue Dec 16 14:24:08 2014
    DUMP: dumping (Pass III) [directories]
    DUMP: dumping (Pass IV) [regular files]
    DUMP: Closing /home/lsi/dump-restore/back.dump
    DUMP: Volume 1 completed at: Tue Dec 16 14:24:08 2014
    DUMP: Volume 1 150 blocks (0.15MB)
    DUMP: 150 blocks (0.15MB) on 1 volume(s)
    DUMP: finished in less than a second
    DUMP: Date of this level 0 dump: Tue Dec 16 14:24:08 2014
    DUMP: Date this dump completed:  Tue Dec 16 14:24:08 2014
    DUMP: Average transfer rate: 0 kB/s
    DUMP: DUMP IS DONE

Now the backup is done, the analogous action is restore it. We are going to remove some files of the folder:

    root@debian:/home/lsi/dump-restore# rm -R critical_data/file4*
    root@debian:/home/lsi/dump-restore# ls -al critical_data/
    total 8
    drwxr-xr-x 2 root root 4096 dic 16 14:26 .
    drwxr-xr-x 3 root root 4096 dic 16 14:24 ..
    -rw-r--r-- 1 root root    0 dic 16 14:23 file1
    -rw-r--r-- 1 root root    0 dic 16 14:23 file2
    -rw-r--r-- 1 root root    0 dic 16 14:23 file3

To restore them:

    root@debian:/home/lsi/dump-restore# restore -i -f /home/lsi/dump-restore/back.dump
    restore >

Pressing ? for help we get:

    restore > ?
    Available commands are:
	    ls [arg] - list directory
	    cd arg - change directory
	    pwd - print current directory
	    add [arg] - add `arg' to list of files to be extracted
	    delete [arg] - delete `arg' from list of files to be extracted
	    extract - extract requested files
	    setmodes - set modes of requested directories
	    quit - immediately exit program
	    what - list dump header information
	    verbose - toggle verbose flag (useful with ``ls'')
	    prompt - toggle the prompt display
	    help or `?' - print this list
    If no `arg' is supplied, the current directory is used

So, we can select the files to extract from the backup:

    restore > cd home/lsi/dump-restore/critical_data
    restore > ls
    ./home/lsi/dump-restore/critical_data:
    file1  file2  file3  file40 file41 file42
    
    restore > add file4*
    restore > ls
    ./home/lsi/dump-restore/critical_data:
    file1   file2   file3  *file40 *file41 *file42
    restore > quit

Now we have the restored files:

    root@debian:/home/lsi/dump-restore# ls home/lsi/dump-restore/critical_data/file4*
    home/lsi/dump-restore/critical_data/file40  home/lsi/dump-restore/critical_data/file41	home/lsi/dump-restore/critical_data/file42

## Crontab and backup

The crontab configuration file, can be found at _/etc/crontab_. The content of it should be something like:

    # /etc/crontab: system-wide crontab
    # Unlike any other crontab you don't have to run the `crontab'
    # command to install the new version when you edit this file
    # and files in /etc/cron.d. These files also have username fields,
    # that none of the other crontabs do.
    
    SHELL=/bin/sh
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
    
    # m h dom mon dow user  command
    17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
    25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
    47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
    52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
    #

So, to make a new rule, we can add a line like this:

    * * * * *       root    dump -0a -f /home/lsi/dump-restore/back.dump critical_data/ # every minute

This rule would make a dump every minute, so, to make it more realistic we are going to make this command be executed once a week:

    0 0 * * 0       root    dump -0a -f /home/lsi/dump-restore/back.dump critical_data/ # every Sunday at 00:00h

## rsync and ssh tunneling

rsync can be used to backup files to/from remote hosts or to/from local host, e.g., to sincronize two directories between two machines on a net using rsync:

    rsync -av --delete -e ssh [user@]host:SRC... [DEST]

Or

    rsync -av --delete -e ssh SRC... [user@]host:DEST

_-a_ option, basically, conserves copied files as originals, as archives; _-e_ option specify the remote shell to use; _SRC_ and _DST_ are paths to directories/files.

## Mount remote Linux directory via ssh tunneling

SSHFS is a tool capable of operating on files on a remote computer using just a secure shell login on the remote computer (literally from man page). It is quite simple to use:

    $ sshfs username@server:/remotefolder /remote

So, _/remotefolder_ is mounted at local directory _~/remote_. We can also unmount:

    $ fusermount -u /remote

