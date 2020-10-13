## *Arch Linux installation - legacy mode*
used https://averagelinuxuser.com/a-step-by-step-arch-linux-installation-guide/

### *Setup wifi*

use iwd to access to wifi with WEP2 encription
https://wiki.archlinux.org/index.php/Iwd

```shell
$ iwctl
$ [iwd]# station device connect SSID`
```

### *Partition*

```shell
$ fdisk -l #see all partition, then
$ fdisk /dev/sda #create a dos partition table and write it

$ cfdisk /dev/sda
```

make one partition for the system, make it bootable and then RAM/2 for swap (only suspend without hibernation)

then format them

```shell
$ mkfs.ext4 /dev/sda1
$ mkswap /dev/sda2

$ mount /dev/sda1 /mnt
```

### *Install the system*

```shell
$ pacstrap -i /mnt base linux linux-firmware sudo vim mc w3m byobu netctl git python3 powertop zsh
```

### *Generate fstab file*

```shell
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```

### *Chroot to the installed system*

```shell
$ arch-chroot /mnt /bin/bash
```

### *Set locale*

```shell
$ vim /etc/locale.gen #You should jump to the line #en_US.UTF-8 UTF-8. Uncomment it by removing the # sign and save
$ locale-gen
```

### *Set the time zone*

```shell
$ ln -sf /usr/share/zoneinfo/Europe/Minsk /etc/localtime
```

### *Set local time*

```shell
$ hwclock --systohc --utc
$ date
```

### *Set hostname*

```shell
$ echo x61s > /etc/hostname

$ vim /etc/hosts
```
then add
127.0.1.1 localhost.localdomain x61s

### *Enable network*

```shell
$ pacman -S networkmanager
$ systemctl enable NetworkManager
```

### *Set root password*

```shell
$ passwd
```

### *Set SWAP*

```shell
$ swapon /dev/sda2
```

To enable this swap partition on boot, add an entry to /etc/fstab:
UUID=device_UUID none swap defaults 0 0

to find UUID run
```shell
$ blkid | grep UUID=
```

### *Install GRUB*

```shell
$ pacman -S grub
$ grub-install /dev/sda
$ grub-mkconfig -o /boot/grub/grub.cfg
```

### *Reboot*

```shell
$ exit
$ umount -R /mnt
$ reboot
```
