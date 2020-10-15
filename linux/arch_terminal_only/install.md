## *Arch Linux installation - legacy mode*

used:
1. https://wiki.archlinux.org/index.php/Installation_guide
2. https://averagelinuxuser.com/a-step-by-step-arch-linux-installation-guide/
3. https://gist.github.com/CodingCellist/05556e0cb6cde146fc3f70b578b73da3
4. https://www.linuxjournal.com/content/without-gui-how-live-entirely-terminal

### *Setup wifi*

use iwd to access to wifi with WEP2 encription
https://wiki.archlinux.org/index.php/Iwd

```shell
$ ip link
$ iwctl
$ [iwd]# station <device> connect <SSID>
```

### *Partition*

```shell
$ fdisk -l #see all partition, then
$ fdisk /dev/sda #create a dos partition table and write it

$ cfdisk /dev/sda
```

make one partition for the system, make it bootable and then RAM*2 for swap (only suspend without hibernation)

then format them

```shell
$ mkfs.ext4 /dev/sda1
$ mkswap /dev/sda2

$ mount /dev/sda1 /mnt
$ swapon /dev/sda2
```

### *Install the system*

```shell
$ pacstrap -i /mnt base sudo vim mc w3m git python3 powertop java zsh netctl dialog tmux htop dhcpcd physlock
```

### *Generate fstab file*

```shell
$ genfstab -U /mnt >> /mnt/etc/fstab
```

### *Chroot to the installed system*

```shell
$ arch-chroot /mnt
```

### *Set locale*

```shell
$ vim /etc/locale.gen #You should jump to the line `#en_US.UTF-8 UTF-8`. Uncomment it by removing the # sign and save
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
`127.0.1.1 x61s.localdomain x61s`

### *Set root password*

```shell
$ passwd
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

### *Add user*

```shell
$ useradd -m -g users -G wheel -s /usr/bin/zsh <username>
$ passwd <username>
$ EDITOR=vim visudo
```
and uncomment `# %wheel ALL=(ALL) ALL`

```shell
$ exit
```

### *Enable network*

```shell
$ systemctl start dhcpcd.service
$ systemctl enable dhcpcd.service
$ wifi-menu
$ netctl start <saved network profile from wifi-menu>
```

### Update system

```shell
$ pacman -Syu
```

### Setup zsh

```shell
```

### Setup tmux

https://unix.stackexchange.com/questions/43601/how-can-i-set-my-default-shell-to-start-up-tmux
https://www.nordtheme.com/docs/ports/tmux/installation
```shell

```

### Setup vim

### Setup physlock

### *Add audio* - check
```shell
pacman -S pulseaudio pulseaudio-alsa
```
