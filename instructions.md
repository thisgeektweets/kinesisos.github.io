---
layout: post
title: Instructions
subtitle: The best guide you'll find for setupping Kinesis OS. Literally.
permalink: /instructions/
---
### Prerequisites

1. A usable machine, preferably one using a Linux-based OS.
2. At least 4 GB of free storage to create an ext4 partition.
3. The [source files for Kinesis OS](https://download1506.mediafire.com/fr228ct7tzyg/yhyhrli8vrirbmo/k_os-source-files.tar.xz).
4. An Internet connection if additional files are going to be downloaded
   during the setup.

> ##### Note: It is recommended to go through the Kinesis OS setup as the root user.

---

### Extracting

First off, create an ext4 partition on the disk where Kinesis OS will be installed 
on using a tool such as [GParted](https://gparted.org/)  or [Fdisk](https://wiki.archlinux.org/index.php/Fdisk). 
If an existing partition will be used for Kinesis OS, the following commands can be issued directly from a 
Unix/Linux shell. Next, mount the partition to be used at `/mnt/` or at any other mount point desired.

{% highlight bash %}
lsblk
mkfs.ext4 /dev/sdXn 
mount /dev/sdXn /mnt/
{% endhighlight %}

A swap partition can also be made use of if desired.

{% highlight bash %}
mkswap /dev/sdXn
swapon /dev/sdXn
{% endhighlight %}

Going on with the Kinesis OS setup, download and extract the 
[source files for Kinesis OS](https://download1506.mediafire.com/fr228ct7tzyg/yhyhrli8vrirbmo/k_os-source-files.tar.xz) 
to the ext4 partition that is going to be used. Another way to do this could be by issuing the following commands. 

> ##### Replace any instance of `'/mnt/'` in the instructions by a custom mount point if one is being used onwards.

{% highlight bash %}
cd /mnt/
wget https://download1506.mediafire.com/fr228ct7tzyg/yhyhrli8vrirbmo/k_os-source-files.tar.xz
tar -xvf k_os-source-files.tar.xz
rm k_os-source-files.tar.xz
{% endhighlight %}

For the next steps, it is recommended to install the 
[arch install scripts](https://www.archlinux.org/packages/extra/any/arch-install-scripts/)
package for the current platform being used although everything can be done manually. 

Finally, just create the `fstab` file which will hold information about the
mount point of disk partition(s) that Kinesis OS will be using. If the
[arch install scripts](https://www.archlinux.org/packages/extra/any/arch-install-scripts/)
package has been installed, just run the following command.

{% highlight bash %}
genfstab -U /mnt/ >> /mnt/etc/fstab
{% endhighlight %}

Else, edit the `fstab` file at `/mnt/etc/fstab` manually. For this example,
it will be assumed that the `/` partition (the ext4 one) is at `/dev/sda2` and the `swap`
partition, at `/dev/sda3`. **Replace these by the actual partitions that will be used.**

{% highlight bash %}
cat > /mnt/etc/fstab << "EOF"
# <device>        <dir>        <type>        <options>        <dump> <fsck>
/dev/sda2         /            ext4          defaults         0      1
/dev/sda3         none         swap          defaults         0      0
EOF
{% endhighlight %}

---

### Chroot-ing

In this section, root will be changed to the newly created Kinesis OS `/` partition in order
to complete all the necessary initial configurations and preparations for the final system.

If the
[arch install scripts](https://www.archlinux.org/packages/extra/any/arch-install-scripts/)
package has been installed, just issue this one line command.

{% highlight bash %}
arch-chroot /mnt/
{% endhighlight %}

Else, execute the following commands if a more classical approach is being taken.

{% highlight bash %}
cd /mnt/
mount -t proc /proc proc/
mount --rbind /sys sys/
mount --rbind /dev dev/
mount --rbind /run run/
chroot /mnt/ /bin/bash
{% endhighlight %}

---

### Custom initial configurations (Optional)

To set a timezone and update the hardware clock of the new system, use the following commands
while updating `<Region>` and `<Country>` with specific values.

{% highlight bash %}
ln -sf /usr/share/zoneinfo/<Region>/<Country> /etc/localtime
hwclock --systohc
{% endhighlight %}

For using particular language(s) in the new Kinesis OS install, lines corresponding to the
language(s) needed must be uncommented and generated. Additionally, the `/etc/locale.conf` 
file needs to be edited to correspond to the main language that will be used. By default,
Kinesis OS comes with the `en_US.UTF-8 UTF-8` locale.

{% highlight bash %}
nano /etc/locale.gen
locale.gen
nano /etc/locale.conf
{% endhighlight %}

If a custom hostname is desired for the machine, any instance of `'k_os'` needs to be replaced
by that hostname in the `/etc/hostname` and `/etc/hosts` files.

{% highlight bash %}
nano /etc/hostname
nano /etc/hosts
{% endhighlight %}

Finally, packages can be made sure to be up-to-date by running these commands. If
a wireless internet connection won't be used, omit the first command.

{% highlight bash %}
wifi-menu
pacman -Syu
{% endhighlight %}

---

### Users and WiFi configuration (Optional)

The password of the root user can be changed using the following command.

{% highlight bash %}
passwd
{% endhighlight %}

Additionally, a new user can be created and a password can be set. As the ``sudo``
package is going to be installed alongside, make sure that an active internet connection
is available. Refer to the last part of the previous section if a wireless internet 
connection is going to be used.

{% highlight bash %}
k_addUser <UserName>
{% endhighlight %}

If WiFi support out of the box is desired after installing Kinesis OS, execute the following command.

{% highlight bash %}
k_wifi
{% endhighlight %}

---

### Installing a/multiple Desktop Environment(s)

This section can be skipped if Kinesis OS will be used as a terminal-based operating system.

> If multiple desktop environments will be installed, **issue the following command just
before the last desired desktop environment is installed.** Failure to do so might result
in conflicts between display managers used by the different desktop environments installed.

{% highlight bash %}
k_disable_dm
{% endhighlight %}

Issue the following command(s) **only** for the desktop environment(s) desired.

[KDE Plasma](https://kde.org/plasma-desktop):
{% highlight bash %}
k_install_plasma
{% endhighlight %}

[GNOME](https://www.gnome.org/):
{% highlight bash %}
k_install_gnome
{% endhighlight %}

[Cinnamon](https://en.wikipedia.org/wiki/Cinnamon_(Desktop_Environment)):
{% highlight bash %}
k_install_cinnamon
{% endhighlight %}

[XFCE](https://www.xfce.org/):
{% highlight bash %}
k_install_xfce
{% endhighlight %}

[MATE](https://mate-desktop.org/):
{% highlight bash %}
k_install_mate
{% endhighlight %}

[Budgie](https://en.wikipedia.org/wiki/Budgie_(desktop_environment)):
{% highlight bash %}
k_install_budgie
{% endhighlight %}

[Deepin](https://www.deepin.org/en/):
{% highlight bash %}
k_install_deepin
{% endhighlight %}

[LXDE](https://lxde.org/):
{% highlight bash %}
k_install_lxde
{% endhighlight %}

[LXQT](https://lxqt.org/):
{% highlight bash %}
k_install_lxqt
{% endhighlight %}

---

### Installing a bootloader (GRUB)

A bootloader will allow booting Kinesis OS. [GRUB](https://en.wikipedia.org/wiki/GNU_GRUB) 
is the preffered and recommended choice due to its many benefits. A command similar to 
the format given below needs to be issued.

{% highlight bash %}
k_grub [y/n] /dev/sdX [n]
{% endhighlight %}

The first parameter is whether (y) or not (n) the system is an UEFI one, and the
second and third parameters correspond to the disk (and partition number) where 
[GRUB](https://en.wikipedia.org/wiki/GNU_GRUB) should be installed. The third
parameter needs to be omitted in case the system is not an UEFI one.

If the system is not an UEFI one and the target disk is `/dev/sda`, the following command can be issued.

{% highlight bash %}
k_grub n /dev/sda
{% endhighlight %}

If the system is an UEFI one and the EFI partition (usually the smallest one) corresponds
to `/dev/sda1`, the following command can be issued.

{% highlight bash %}
k_grub y /dev/sda 1
{% endhighlight %}

If the setup of Kinesis OS is over,the Chroot environment can be left and this
fresh new install of Kinesis OS can be booted!

{% highlight bash %}
exit
cd /
umount --recursive /mnt/
reboot
{% endhighlight %}
