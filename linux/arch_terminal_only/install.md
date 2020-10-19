## *Arch Linux installation - legacy mode*

used:
1. https://wiki.archlinux.org/index.php/Installation_guide
2. https://averagelinuxuser.com/a-step-by-step-arch-linux-installation-guide/
3. https://gist.github.com/CodingCellist/05556e0cb6cde146fc3f70b578b73da3
4. https://www.linuxjournal.com/content/without-gui-how-live-entirely-terminal
5. https://joshtronic.com/2018/10/12/hibernate-laptop-on-lid-close-with-systemd-boot-on-arch-linux/

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

make one partition for the system, make it bootable swap partition is not need, we will create and setup swap file later

then format them

```shell
$ mkfs.ext4 /dev/sda1

$ mount /dev/sda1 /mnt
```

### *Install the system*

```shell
$ pacstrap -i /mnt base sudo vim mc w3m git python3 powertop jdk-openjdk zsh netctl dialog tmux htop dhcpcd physlock
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

### *Setup swap*

create and enable
```shell
$ fallocate -l 8G /swapfile
$ chmod 600 /swapfile
$ mkswap /swapfile
$ swapon /swapfile
$ echo '/swapfile none swap defaults 0 0' >> /etc/fstab
$ free -m # check
```
hibernation on disk
```shell
$ sudo filefrag -v /swapfile | sed -n '4p' | awk '{print $4+0}'
    548832 <-- Copy this number
$ sudo vim /etc/default/grub
    GRUB_CMDLINE_LINUX_DEFAULT="quiet resume=/dev/your_root_disk resume_offset=the_number_just_copied"
$ sudo grub-mkconfig -o /etc/grub/grub.cfg
$ sudo vim /etc/mkinitcpio.conf
    HOOKS="base udev resume autodetect modconf block filesystems keyboard fsck"
$ sudo mkinitcpio -p linux # or linux-lts if you're using the LTS kernel
$ sudo vim /etc/systemd/logind.conf
    HandleLidSwitch=hibernate  # if you want to hibernate when the laptop lid closes
$ sudo reboot
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

### *Update system*

```shell
$ pacman -Syu
```

### *Setup zsh*
add nord theme
```shell
```

### *Setup tmux*
https://unix.stackexchange.com/questions/43601/how-can-i-set-my-default-shell-to-start-up-tmux
https://www.nordtheme.com/docs/ports/tmux/installation

add nord theme and run on start
```shell

```

### *Setup vim*
add nord theme

### *Setup physlock*

### *Add audio* - check
```shell
pacman -S pulseaudio pulseaudio-alsa
```
