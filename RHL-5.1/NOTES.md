# Red Hat Linux 5.1 #

26-Jul-2017

## Required Downloads ##

Download the RHL 5.1 release from
ftp://ftp.ibiblio.org/pub/historic-linux/distributions/redhat-5.1/i386/.
Specific requirements:

| File or Directory | Description |
|:---|:---|
| images/boot.img | Installation boot floppy disk image. |
| images/supp.img | Supplemental floppy disk image. |
| images/rescue.img | Rescue mode supplemental floppy disk image. |
| RedHat/base/ | Files for the installation environment. |
| RedHat/RPMS/ | All available packages. |

This distribution boots from installation floppy disks, there's no way around
that.  The packages can be pulled from a number of locations, but it is easiest
to create a second hard disk image and install from that.  Download all of the
files and directories above.  Keep the RedHat/ files as their structure within
the RedHat/ subdirectory.

## Creating Hard Disk Images ##

Two hard disk images are needed for the installation.  One will be the main
virtual machine disk image, the other will contain our installation source
packages.

The first image will be the actual disk image.  1G is enough for this
distribution:

`qemu-img create hda.img 1G`

The second image will be the installation source.  We only need 400M:

`qemu-img create hdb.img 400M`

## Preparing The Installation Source Image ##

We need to copy the RedHat/ subdirectory on to the hdb.img disk, but in a
partition and on a compatible filesystem.  You cannot easily create a
compatible ext2 filesystem on a modern Linux system because incompatible
features have been added to ext2 in later kernels.  It's easiest to make the
filesystem under Red Hat Linux 5.1.  We can do this with the rescue media.

Boot qemu:

`qemu-system-i386 -m 64 -hda hdb.img -fda boot.img -boot a -monitor stdio`

The *-hda hdb.img* is not a typo.  We only need to work on the **hdb.img**
image file at this time, so we can just pass it as the first hard disk.

At the boot prompt, type `rescue` and press Enter.  The kernel will boot and
eventually you will see a message to remove the boot floppy and insert the
rescue floppy.  Go to the qemu monitor window and type:

`(qemu) **change floppy0 rescue.img**`

Then go back to the window with the message and press Enter.  This will load
the rescue system and dump you at a root shell.

From the root shell, launch fdisk and create 1 partition consuming the entire
disk and then format it as ext2 (commands you type are bold):

```
bash# **fdisk /dev/hda**

Command (m for help): **n**
Command action
   e   extended
   p   primary partition (1-4)
**p**
Partition number (1-4): **1**
First cylinder (1-812): **1**
Last cylinder or +size or +sizeM or +sizeK ([1]-812): **812**

Command (m for help): **w**
The partition table has been altered!

Calling ioctl() to re-read partition table.
 hda: hda1
 hda: hda1
Syncing disks.

WARNING: If you have created or modified any DOS 6x
partitions, please see the fdisk manual page for additional
information.
bash# **mke2fs /dev/hda1**
...[lots of stuff]...
Writing superblocks and filesystem accounting information: done
bash#
```

Now go back over to the qemu monitor and quit:

`(qemu) **quit**`

From the host system, we can mount this new ext2 partition and copy in the
packages:

```
# **losetup /dev/loop0 hdb.img**
# **kpartx -a /dev/loop0**
# **mount -t ext2 /dev/mapper/loop0p1 /mnt**
# **cp -pr RedHat/ /mnt**
# **umount /mnt**
# **kpartx -d /dev/loop0**
# **losetup -d /dev/loop0**
```

The first partition on hdb.img now contains the RedHat/ directory which
should contain at least the *base* and *RPMS* subdirectories containing
the actual installation files.

## Installing ##

At this point, installation is pretty easy.  Boot qemu install media with
both hard disks connected, partition hda.img, set mount points, select
packages, install, complete configuration information.

`qemu-system-i386 -m 64 -hda hda.img -hdb hdb.img -fda boot.img -boot a -monitor stdio`

At the boot menu, just press Enter to begin a normal installation.  Select
appropriate answers to the questions.  For me, I selected English and the
us keyboard layout.

For disk partitioning, I went with fdisk.  **NOTE: Only partition /dev/hda
because /dev/hdb is our installation source.**

During partitioning of /dev/hda, create three partitions:

| Partition | Size | Mount Point |
|:---|---|:---|
| /dev/hda1 | 20M | /boot |
| /dev/hda2 | 128M | swap |
| /dev/hda3 | remainder | / |

During partitioning, be sure to set the type code of the second partition to
82, which is Linux swap.  The installer expects this.

Set the mount points for the hda partitions.  The installer will ask if you
want to use hda2 as swap space, say yes.  It will ask if you want to format
the swap space.  Say yes, but uncheck the option to check for bad blocks.
Format the other mount points too, but also disable the check for bad blocks.

For package selection, select Everything or check every single box.

Proceed with the installation.  At the end it will begin to ask configuration
questions.  Skip as many as you can, like network setup and X configuration
because those can be taken care of later.

Install the boot loader to the MBR of /dev/hda.

The system will reboot the newly installed system and you should be good to
go.

## Configuration ##

## Resizing The Disk Image ##

## Running The Guest System ##
