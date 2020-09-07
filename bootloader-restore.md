# Bootloader Restore
### Summary
I dual boot Windows 10 and Arch Linux.
These are basic instructions on how I fix my GRUB bootloader after Windows 10 or a bios update has wiped it out.

#### 1. If you don't have a bootable USB key with Arch Linux on it, go get one 
[Download Arch Linux](https://www.archlinux.org/download/)

Figure out which device is your USB drive  
```$ sudo fdisk -l```

Install dosfstools if you don't already have it  
```$ sudo pacman -S dosfstools```

Unmount the drive and format it.  *(Replace /dev/sdb with your device location)*
```
$ sudo umount /dev/sdb 
$ sudo mkfs.vfat /dev/sdb
```

Write the arch image to the USB drive  
``` $ sudo dd bs=4M if=./arch.iso of=/dev/sdb```

Flush any data buffered in memory out to the disk  
``` $ sync```

#### 2. Re-install and re-configure GRUB
Boot the computer into the USB key Arch Linux

Make note of the divices of your EFI partitions and linux partition  
```# fdisk -l```  

For the rest of the instructions I'm going to use `/dev/nvme0n1p2` as the Windows 10 EFI partition, `/dev/nvme0n1p6` as the GRUB partition, and `/dev/nvme0n1p8` as the Linux partition.

Mount the Linux partition and then chroot
```
# mount /dev/nvme0n1p8 /mnt
# arch-chroot /mnt
```

Install os-prober if you don't already have it  
```$ sudo pacman -S os-prober```

Mount the GRUB partition  
``` # mount /dev/nvme0n1p6 /boot```

Re-install grub  
``` $ grub-install --target=x86_64-efi --efi-directory=/boot/ --bootloader-id=GRUB```

Mount the Windows 10 EFI partition  
``` # mount /dev/nvme0n1p2 /mnt2```

Re-generate GRUB configuration file  
``` # grub-mkconfig -o /boot/grub/grub.cfg```

#### 3. Reboot and Win  
```
# exit
# reboot
```
