# Red Hat Linux 5.1 #

26-Jul-2017

## Required Downloads ##

Download the RHL 5.1 release from
[ftp://ftp.ibiblio.org/pub/historic-linux/distributions/redhat-5.1/i386/].
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

## Formatting The Installation Source Image ##

## Installing ##

## Configuration ##

## Resizing The Disk Image ##

## Running The Guest System ##
