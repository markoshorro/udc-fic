Practice 3: Backups and File Systems (Debian, UNIX)
===================================================

## Concepts

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

The difference between a logic and a primary partition, mainly is the purpose. There are three possible partitions: primary, extended and logic. There only can be four primary or three and one extended on a device. These partitions are referenced on a table. Logic partitions are contained in the extended partition. These partitions are needed, e.g, if you want to have multiple file system types on the same devie, e.g. if you want to have multiple SO installed on the same device.
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

## The practice

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

We are goint to create a new primary partition on that disk, as follows:

mkfs

du

df


