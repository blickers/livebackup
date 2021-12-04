# livebackup
Simply create a bootable squashfs live systems from your running Linux

This is a bash script aiming to create a bootable squashfs file from a installed Linux system
or the live system it self. It's modified from the "Live Ubuntu Backup" script originaly
written by billbear@gmail.com. I simplified the code, focusing on only live system backup, and
make it working for not only Ubuntu, but also for any other Linux distribution.

The squashfs backup file can boot directly using GNU grub:

 menuentry "GNU/Linux in SQUASHFS IMAGE (Live CD mode, read only)" {
		set gfxpayload=keep
		insmod probe
		insmod squash4
		set file="/backup.squashfs"
		search --no-floppy -f --set=SFSROOT  $file
		probe -u --set=SFSUUID ${SFSROOT}
		loopback loop (${SFSROOT})/$file
		set root=${SFSROOT}
		linux (loop)/boot/vmlinuz root=UUID=${SFSUUID} squashfs=$file rw apparmor=0 quiet splash locale=en_US.UTF-8 acpi_backlight=vendor
		initrd (loop)/boot/initrd.img
 }

Or you can manually restore it to a physical HD partion or a disk image file:

 MOUNT YOUR HD PARTITION OR DISK IMAGE FILE (eg. /VirtualBox/linux.vhd ) TO /mnt,
 cd /mnt
 sudo unsquashfs /backup.squashfs
 sudo mv squashfs-root/* ./
 sudo rm -rf squashfs-root

EDIT fstab, change the root file system type and root partition UUID:

 sudo vi ./etc/fstab

And boot from it. Here is an GNU grub example of booting from a VHD file:

 menuentry "GNU/Linux in VHD IMAGE (Hard disk mode, read and write)" {
		set gfxpayload=keep
		insmod probe
		set file="/VirtualBox/linux.vhd"
		search --no-floppy -f --set=SFSROOT  $file
		set root=${SFSROOT}
		probe -u --set=SFSUUID ${SFSROOT}
		loopback loop (${SFSROOT})/$file
		linux (loop,msdos1)/boot/vmlinuz root=UUID=${SFSUUID} kloop=$file kroot=/dev/mapper/loop0p1 rw apparmor=0 quiet splash locale=en_US.UTF-8 acpi_backlight=vendor
		initrd (loop,msdos1)/boot/initrd.img
    
 
