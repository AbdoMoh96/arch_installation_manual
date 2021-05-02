# Installation guide
This document is a guide for installing Arch Linux using the live system booted from an installation medium made from an official installation image.

<br/>
<br/>

# bre-install

## check boot mode (UEFI) >> must see some directories.


```
ls /sys/firmware/efi/efivars
```
OR

```
for UEFI check run :
 ls /sys/firmware/efi
```

<br/>
<br/>

## check internet connection 
```
ping www.google.com
```

<br/>
<br/>

## Update the system clock 
```
 timedatectl set-ntp true
```

<br/>
<br/>

# setting up the disks

## list all disks

```
fdisk -l
```

<br/>
<br/>

## run cfdisk to partition your disk

```
cfdisk /dev/<disk name>
```

<br />
<br />

## main system partitions
<br />

| Type | Size | Format |
| ----- |  ----- | ----- |
| EFI | 550M | fat32 |
| linux swap | size of device ram | none |
| linux file system (root)| rest of disk size or (custom)| ext4 |

<br />
<br />

## Formating partitions with the right file systems

<br />

### EFI partition format 
```
mkfs.fat -F32 /dev/<disk_name><partition_number>
example :: mkfs.fat -F32 /dev/sda1
```
<br />

### Swap partition format
```
mkswap /dev/<disk_name><partition_number>
```
<br />

### Linux File System (root) format
```
mkfs.ext4 /dev/<disk_name><partition_number>
```

<br />
<br />

# Mount partition and enableing Swap

<br />

## Enable Swap
```
swapon /dev/<disk_name><partition_number>
```

<br />

## Mount Linux File system partition 
```
mount /dev/<disk_name><partition_number> /mnt
```

<br />
<br />

# Installing Reflector and setting up mirrors

<br />

## Update System
```
pacman -Sy
```

<br />

## Install Reflector 
```
pacman -S reflector
```

<br />

## generte a mirror list using (reflector)
```
reflector --verbose --sort rate -l 20 --save /etc/pacman.d/mirrorlist
```

<br />
<br />

# System Install , Generate Fstap (Arch partition map)

<br />

## Install Base System
```
pacstrap /mnt base linux linux-firmware base-devel nano
```

<br />

## Generate fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```

<br />

# Update mirror list

<br />

## Remove default mirror list
```
rm /mnt/etc/pacman.d/mirrorlist
```

<br />

## Add reflector's generated mirror list
```
cp /etc/pacman.d/mirrorlist /mnt/etc/pacman.d/mirrorlist
```

<br />
<br />

# Setting Up hostname , Locale , Time Zone

<br />

## Chroot to new Install
```
arch-chroot /mnt
```

<br />

## Set HostName
```
echo "<your-custom-host-name>" > /etc/hostname
example :: echo "archuser" > /etc/hostname
```

<br />

## Setting the hosts file
RUN
```
nano /etc/hosts
```
THEN add and save
```
127.0.0.1	localhost
::1		    localhost
127.0.1.1	<your-hostname>.localdomain	<your-hostname>
```

## Set Locale 
RUN
```
nano /etc/locale.gen
```
THEN uncomment this line (save & exit)
```
en_US.UTF-8
```

<br />

## generating locale config
```
locale-gen
```

<br />

## adding en_us to generated config
```
echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8
```


<br />

## Setting The Time Zone
```
rm /etc/localtime
```
```
ln -sf /usr/share/zoneinfo/<continent>/<state> /etc/localtime
example :: ln -sf /usr/share/zoneinfo/Africa/Cairo /etc/localtime
```
THEN RUN
```
hwclock --systohc --utc
```

<br />
<br />

# Set Root password , Add user with superuser privileges

<br />

## Update the system (just in case)
```
pacman -Syu
```

<br />

## Set Root password
```
passwd
```

<br />

## Create New User and add user to the Wheel Group
```
useradd -mg users -G wheel,storage,power -s /bin/bash <user-name>
```
THEN set password for new user
```
passwd <user-name>
```

## Installing Sudo
```
pacman -S sudo
```

<br />

## Activating Sudo command for all users in the wheel Group
RUN
```
EDITOR=nano visudo
```
THEN uncomment this line :
```
%wheel All=(All) All
```
THEN save and exit.


<br />
<br />

# Installing essential packages

## installing network-manager
```
pacman -S networkmanager
```


<br />
<br />

# Installing and Configuring the bootloader

<br />

## Installing the bootloader
```
pacman -S grub efibootmgr dosfstools mtools os-prober
```

<br />

## Configuring the EFI partition
create EFI directory in boot
```
mkdir /boot/EFI
```
THEN mount EFI partition to it
```
mount /dev/<driv-name><EFI-partition-number> /boot/EFI
example :: mount /dev/sda1 /boot/EFI
```

<br />

## Configuring the bootloader (grub)
```
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
```
THEN generate the grub configuration file RUN ::
```
grub-mkconfig -o /boot/grub/grub.cfg
```
THEN RUN ::
```
exit
```

<br />
<br />

# Post Bootloader Install

<br />

```
umount -R /mnt
```
THEN
```
reboot
```
For VM RUN ::

```
shutdown now
```
then remove the arch iso

<br />
<br />

**Thats it, Hell Yeah!**