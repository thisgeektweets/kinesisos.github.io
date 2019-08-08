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

> ##### Note: It is recommended to go through the k_os setup as the root user.

---

### Extracting

First off, create an ext4 partition on the disk you'll be using 
Kinesis OS on using your preferred tool such as [GParted](https://gparted.org/) 
or [Fdisk](https://wiki.archlinux.org/index.php/Fdisk). If you would like
to reuse an existing partition for k_os, the following commands can be issued
directly from a Unix/Linux terminal. Next, mount the partition you'll be using
at `/mnt/` or at any other mount point you like.

{% highlight bash %}
lsblk
mkfs.ext4 /dev/sdXn 
mount /dev/sdXn /mnt/
{% endhighlight %}

You can also make use of a swap partition if desired.

{% highlight bash %}
mkswap /dev/sdXn
swapon /dev/sdXn
{% endhighlight %}

Continuing with the k_os setup, download and extract the 
[source files for Kinesis OS](https://download1506.mediafire.com/fr228ct7tzyg/yhyhrli8vrirbmo/k_os-source-files.tar.xz) 
to the ext4 partition that is going to be used. Another way to do this could be by issuing the following commands. 

> ##### Replace any instance of `/mnt/` by your custom mount point if you are using one onwards.

{% highlight bash %}
cd /mnt/
wget https://download1506.mediafire.com/fr228ct7tzyg/yhyhrli8vrirbmo/k_os-source-files.tar.xz
tar -xvf k_os-source-files.tar.xz
rm k_os-source-files.tar.xz
{% endhighlight %}

For the next steps, it is recommended to install the 
[arch install scripts](https://www.archlinux.org/packages/extra/any/arch-install-scripts/)
package for your current platform although you can do everything manually. 

Finally, just create using the `fstab` file which will hold information about the
mount point of disk partition(s) that k_os will be using. If the
[arch install scripts](https://www.archlinux.org/packages/extra/any/arch-install-scripts/)
package has been installed, just run the following command.

{% highlight bash %}
genfstab -U /mnt/ >> /mnt/etc/fstab
{% endhighlight %}

Else, edit the `fstab` file at `/mnt/etc/fstab` using your favourite tool. For this example,
we will be assuming that our `/` partition (the ext4 one) is at `/dev/sda2` and our `swap`
partition, at `/dev/sda3`. **Replace these by your own actual partitions that you will be using.**

{% highlight bash %}
cat > /mnt/etc/fstab << "EOF"
# <device>        <dir>        <type>        <options>        <dump> <fsck>
/dev/sda2         /            ext4          defaults         0      1
/dev/sda3         none         swap          defaults         0      0
EOF
{% endhighlight %}

---

### Chroot-ing

In this part, you are going to change root to the newly created Kinesis OS `/` partition in order
to complete all the necessary initial configuration and preparation for the final system.

If the
[arch install scripts](https://www.archlinux.org/packages/extra/any/arch-install-scripts/)
package has been installed, just issue this one line command.

{% highlight bash %}
arch-chroot /mnt/
{% endhighlight %}

Else, execute the following commands if you are going with a more classical approach.

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
while updating `<Region>` and `<Country>` with your own values.

{% highlight bash %}
ln -sf /usr/share/zoneinfo/<Region>/<Country> /etc/localtime
hwclock --systohc
{% endhighlight %}

For using particular language(s) in your new operating system, lines corresponding to the
language(s) you need must be uncommented and generated. Additionally, the `/etc/locale.conf` 
file needs to be edited to correspond to the main language that you'll be using. By default,
Kinesis OS comes with the `en_US.UTF-8 UTF-8` locale.

{% highlight bash %}
nano /etc/locale.gen
locale.gen
nano /etc/locale.conf
{% endhighlight %}

If a custom hostname is desired for your machine, any instance of `'k_os'` needs to be replaced
by that hostname in the `/etc/hostname` and `/etc/hosts` files.

{% highlight bash %}
nano /etc/hostname
nano /etc/hosts
{% endhighlight %}

Finally, packages can be made sure to be up-to-date by running these commands. If you won't
be using a wireless internet connection, omit the first command.

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
package is going to be installed alongside, make sure that you have an active 
internet connection. Refer to the last part of the previous section if you will be
using a wireless internet connection.

{% highlight bash %}
k_addUser <UserName>
{% endhighlight %}

If you want WiFi support out of the box after installing Kinesis OS, run the following.

{% highlight bash %}
k_wifi
{% endhighlight %}

---

### Installing a/multiple Desktop Environment(s)

This section can be skipped if Kinesis OS will be used as a terminal-based operating system.

> If you will be installing multiple desktop environments, **issue the following command just
before the last desired desktop environment is installed.** Failure to do so might result
in conflicts between display managers being used by the different desktop environments installed.

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

A bootloader will allow us to boot in this Kinesis OS install.
[GRUB](https://en.wikipedia.org/wiki/GNU_GRUB) is the preffered and recommended
choice due to its many benefits. A command similar to the format given below
needs to be issued.

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

If the system is an UEFI one and the EFI partition (usually the smallest one) is at
`/dev/sda1`, the following command can be issued.

{% highlight bash %}
k_grub y /dev/sda 1
{% endhighlight %}

If your setup of Kinesis OS is over, you can leave the Chroot environment and boot to this
fresh new install of Kinesis OS!

{% highlight bash %}
exit
cd /
umount --recursive /mnt/
reboot
{% endhighlight %}
