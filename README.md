# livebackup
Simply create a bootable squashfs live systems from your running Linux

This is a bash script designed to create a bootable squashfs file from either an installed Linux system or the live system itself. It's an adaptation of the "Live Ubuntu Backup" script originally authored by billbear@gmail.com. I've simplified its code, concentrating solely on live system backups and ensuring it functions for more than just Ubuntu; it supports any Debian-based Linux distribution.

The squashfs backup file can be booted directly using GNU grub:

```
 menuentry "GNU/Linux in SQUASHFS IMAGE (Live CD mode, read only)" {
	set gfxpayload=keep
	insmod probe
	insmod squash4
	set file="/backup.squashfs"
	search --no-floppy -f --set=SFSROOT  $file
	probe -u --set=SFSUUID ${SFSROOT}
	loopback loop (${SFSROOT})/$file
	set root=${SFSROOT}
	linux (loop)/boot/vmlinuz root=UUID=${SFSUUID} squashfs=$file rw quiet splash locale=en_US.UTF-8 acpi_backlight=vendor
	initrd (loop)/boot/initrd.img
 }
```
Alternatively, you can manually restore the squashfs backup to a physical hard drive partition or a disk image file:

 MOUNT YOUR HD PARTITION OR DISK IMAGE FILE (eg. /VirtualBox/linux.vhd ) TO /mnt,
```
 cd /mnt
 sudo unsquashfs /backup.squashfs
 sudo mv squashfs-root/* ./
 sudo rm -rf squashfs-root
```
EDIT fstab, change the root file system type and root partition UUID:

```
 sudo vi ./etc/fstab
```
And then boot from it.
Here is an example of a grub.cfg configuration for booting from a VHD file:

```
 menuentry "GNU/Linux in VHD IMAGE (Hard disk mode, read and write)" {
	set gfxpayload=keep
	insmod probe
	set file="/VirtualBox/linux.vhd"
	search --no-floppy -f --set=SFSROOT  $file
	set root=${SFSROOT}
	probe -u --set=SFSUUID ${SFSROOT}
	loopback loop (${SFSROOT})/$file
	linux (loop,msdos1)/boot/vmlinuz root=UUID=${SFSUUID} kloop=$file kroot=/dev/mapper/loop0p1 rw quiet splash locale=en_US.UTF-8 acpi_backlight=vendor
	initrd (loop,msdos1)/boot/initrd.img
 }
```   
 
# Thanks to
* "Live Ubuntu Backup" script originaly written by billbear@gmail.com
* "kloop" scripts written by niumao
