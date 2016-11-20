+++
date = "2013-05-17"
tags = ["docker","raspberrypi","linux","kernel","aufs","lxc","go","raspbian"]
categories = ["blog"]
topics = ["blog"]
slug = "running-docker-on-a-raspberrypi"
url = "blog/2013/05/running-docker-on-a-raspberrypi/"
title = "Getting Docker up and running on a RaspberryPi"
+++

This year I attended [PyCon US](https://us.pycon.org/2013/) and I was
lucky enough to get a FREE [RaspberryPi](http://www.raspberrypi.org). At
the same conference [dotCloud](http://www.dotcloud.com) (The company I
work for), was giving a lightning talk for a project that we have been
working on, called Docker. [Docker](http://www.docker.io) is a tool that
allows you to better manage your Linux Containers
([LXC](http://lxc.sourceforge.net)). Shortly after I got the
RaspberryPi, I started wondering if it would be possible to run Docker
on it.

![image](/rpi/RaspberryPi.jpg){.img-polaroid}

I did some digging and technically speaking the Pi should be able to run
Docker, it satisfies all of the requirements.

-   Linux
-   LXC
-   AUFS
-   Go

Getting it up and running isn't going to be easy. The Linux kernels that
come with the different Linux distros for the RaspberryPi, are kind of
old, and don't come with AUFS built in. The RaspberryPi also runs on an
ARM based chip, and it is only 32bit. Currently Docker only supports
64bit OS's. There are plans to add 32 bit support in the future, but it
isn't there yet.

Doing some research I was able to find blog posts on how to get LXC and
AUFS up and running on the RaspberryPi. Using those guides, I was able
to make some progress but I'm not all of the way there yet. I'm hoping
to describe my steps here so that others can see what I have done, and
if they want, help me get over the hump.

Goals
=====

My goals for the project is to:

-   provide a prebuilt image that people can download that has
    everything they needed in order to get started.
-   I also want to provide a prebuilt kernel, people can download and
    use without having to build their own.
-   Port Docker to 32bit so that it will run on RaspberryPi and provide
    a Debian package for easy install.

Instructions
============

Here are the steps that I used to make it so that my RaspberryPi could
run Docker. These are still a work in progress, so please let me know if
you have any issues, or you found a better way to do this.

Install Linux OS
----------------

1.  Download [Raspbian](http://www.raspberrypi.org/downloads) and [make
    an SD card](http://elinux.org/RPi_Easy_SD_Card_Setup) (I used the
    2013-02-09-wheezy-raspbian.zip image)
2.  Once you have the SD card, put it in the Pi and boot it up.

Update and Prepare Raspbian
---------------------------

1.  Switch to Root User on the Pi. These commands must be run as root.
    You can also use "su" or "sudo", what ever you prefer:

``` {.sourceCode .bash}
$ sudo su root
```

2.  Expand to fill SD card and reboot after entering:

``` {.sourceCode .bash}
$ raspi-config
```

3.  Update Raspbian

``` {.sourceCode .bash}
$ apt-get update

$ apt-get dist-upgrade
```

4.  Install git

``` {.sourceCode .bash}
$ sudo apt-get install git-core
```

5.  Update Firmware

The clone will take a while. You might consider cloning on a desktop
machine to save time. Just transfer the firmware/boot and modules/
directories from your desktop PC to the Pi after the checkout. Be aware
that checking out on some OS's that are case insensitive, may result in
some files being missing. If you see funky issues, this might be the
cause.

``` {.sourceCode .bash}
$ cd /opt

$ git clone git://github.com/raspberrypi/firmware.git

$ cd firmware/boot

$ cp * /boot

$ cd ../modules

$ cp -r * /lib/modules

$ reboot
```

6.  Increase the Swap File Size

I found that in order to check out the source on the Pi, you’ll need a
swap file with the 256MB Pi, otherwise it will run out of RAM during the
checkout (with fatal: index-pack failed).

``` {.sourceCode .bash}
# use your favorite editor here.
$ pico /etc/dphys-swapfile

# change to 500 (MB)

$ sudo dphys-swapfile setup

$ sudo reboot
```

7.  Prepare to Build Kernel

We are going to use the 3.6 kernel since it is the lastest stable one.
There is an effort to get [3.8
working](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=87&t=40664),
it isn't 100% there yet, for more info see.

The clone will take a while. Again, you may consider using a desktop PC.
Of course, if you do that, you’ll need to issue the “zcat” command from
your Pi and copy the resulting “.config” file to the “linux” directory
on your desktop PC.

``` {.sourceCode .bash}
$ cd /opt

$ mkdir raspberrypi

$ cd raspberrypi

$ git clone git://github.com/raspberrypi/linux.git

$ cd linux

$ zcat /proc/config.gz > .config
```

8.  Decrease the Swap Space File

``` {.sourceCode .bash}
$ pico /etc/dphys-swapfile

# change to 100 (MB)

$ sudo dphys-swapfile setup

$ sudo reboot
```

9.  Install Packages for Kernel Compilation

``` {.sourceCode .bash}
$ apt-get install ncurses-dev
```

10. Adding AUFS Patches

``` {.sourceCode .bash}
$ cd /opt/raspberrypi/linux

git clone git://aufs.git.sourceforge.net/gitroot/aufs/aufs3-standalone.git
cd aufs3-standalone
git checkout origin/aufs3.6
cp -rp *.patch ../
cp -rp fs ../
cp -rp Documentation/ ../
cp -rp include/ ../
cd ..

patch -p1 < aufs3-base.patch
patch -p1 < aufs3-proc_map.patch
patch -p1 < aufs3-standalone.patch
patch -p1 < aufs3-kbuild.patch
```

If you get this error

``` {.sourceCode .bash}
root@raspberrypi:/opt/raspberrypi/linux# patch -p1 < aufs3-kbuild.patch
patching file fs/Kconfig
patching file fs/Makefile
patching file include/linux/Kbuild
Hunk #1 FAILED at 66.
1 out of 1 hunk FAILED -- saving rejects to file include/linux/Kbuild.rej
```

Then you will need to manually update include/linux/Kbuild because the
patch failed.

First I reverted change on the file, and manually added. the line
(below) to line 66, below audit.h

``` {.sourceCode .bash}
header-y += aufs_type.h
```

here is my git diff:

``` {.sourceCode .bash}
diff --git a/include/linux/Kbuild b/include/linux/Kbuild
index fa21760..ee029e3 100644
--- a/include/linux/Kbuild
+++ b/include/linux/Kbuild
@@ -66,6 +66,7 @@ header-y += atmppp.h
 header-y += atmsap.h
 header-y += atmsvc.h
 header-y += audit.h
+header-y += aufs_type.h
 header-y += auto_fs.h
 header-y += auto_fs4.h
 header-y += auxvec.h
```

11. Configuring Kernel

You’ll now need to set some kernel options to support LXC, via the menu
config tool.

``` {.sourceCode .bash}
$ cd /opt/raspberrypi/linx

$ make menuconfig
```

You need to enable these options:

-   General -&gt; Control Group Support -&gt; Memory Resource Controller
    for Control Groups (*and its three child options*)

![image](/rpi/raspberrypi_kernel_config_1.png){.img-polaroid}

(this has high overhead;only enable if you really need it, or else
enable and remember to disable using the Kernel command line option
“cgroup\_disable=memory”) (image not shown)

-   General -&gt; Control Group Support -&gt; cpuset support
-   Device Drivers -&gt; Character Devices -&gt; Support multiple
    instances of devpts

![image](/rpi/raspberrypi_kernel_config_2.png){.img-polaroid}

-   Device Drivers -&gt; Network Device Support -&gt; Virtual ethernet
    pair device

![image](/rpi/raspberrypi_kernel_config_3.png){.img-polaroid}

-   File Systems --&gt; Miscellaneous filesystems -&gt;select "Aufs
    (Advanced multi layered unification filesystem) support (NEW)" (mine
    was the the very bottom)

![image](/rpi/docker_rasberrypi_aufs_kernel_config.png){.img-polaroid}

12. Build Kernel

This could take many hours if you compile on the Pi, there are ways to
[compile on another
machine](http://elinux.org/RPi_Kernel_Compilation#2._Cross_compiling_from_Linux)
and transfer the kernel to the Pi when completed. If you are in a hurry,
use this approach.

``` {.sourceCode .bash}
$ make

$ make modules_install

$ cd /opt/raspberrypi

$ git clone git://github.com/raspberrypi/tools.git

$ cd tools/mkimage

$ python ./imagetool-uncompressed.py /opt/raspberrypi/linux/arch/arm/boot/Image

$ cp /boot/kernel.img /boot/kernel-old.img

$ cp kernel.img /boot/

$ reboot
```

13. Download Latest LXC

The LXC tools provided with Raspbian are out-of-date, so let's update to
the latest version.

``` {.sourceCode .bash}
$ mkdir /opt/lxc

$ cd /opt/lxc

$ git clone https://github.com/lxc/lxc.git

$ apt-get install automake libcap-dev

$ cd lxc

$ ./autogen.sh && ./configure && make && make install
```

Testing the Install

Check LXC is happy with your kernel:

``` {.sourceCode .bash}
$ lxc-checkconfig
```

User namespace should be “missing” (it checks for a kernel option that
no longer exists) and Cgroup namespace should say “required”.

14. Installing Go

If you install Go using the Go package that is available (apt-get
install golang). You will get a floating point issue. For more
information about the floating point issues see this page.
<http://www.raspberrypi.org/phpBB3/viewtopic.php?p=129647>

To get it to work, we will compile Go from source. Feel free to change
the location on where we are installing it.

``` {.sourceCode .bash}
$ sudo apt-get install -y mercurial gcc libc6-dev

$ hg clone -u default https://code.google.com/p/go $HOME/go
warning: code.google.com certificate with fingerprint 9f:af:b9:ce:b5:10:97:c0:5d:16:90:11:63:78:fa:2f:37:f4:96:79 not verified (check hostfingerprints or web.cacerts config setting)
destination directory: go
requesting all changes
adding changesets
adding manifests
adding file changes
added 14430 changesets with 52478 changes to 7406 files (+5 heads)
updating to branch default
3520 files updated, 0 files merged, 0 files removed, 0 files unresolved

$ cd $HOME/go/src
$ ./all.bash

ALL TESTS PASSED

---
Installed Go for linux/arm in /home/dfc/go
Installed commands in /home/dfc/go/bin
```

If there was an error relating to out of memory, or you couldn’t
configure an appropriate swap device, you can skip the test suite by
executing

``` {.sourceCode .bash}
$ cd $HOME/go
$ ./make.bash
```

as an alternative to ./all.bash.

The go command needs to be added to your \$PATH, you should also edit
your profile script (.bashrc, etc) to include these changes.

``` {.sourceCode .bash}
$ export PATH=$PATH:$HOME/go/bin
$ go version
go version devel +30c566874b83 Wed May 08 16:06:25 2013 -0700 linux/arm
```

15. Installing Docker

``` {.sourceCode .bash}
$ apt-get -y install wget bsdtar curl git

export GOPATH=~/docker/go/
export PATH=$GOPATH/bin:$PATH

$ mkdir -p $GOPATH/src/github.com/dotcloud
$ cd $GOPATH/src/github.com/dotcloud
$ git clone git://github.com/dotcloud/docker.git  # or clone your own fork/branch
$ cd docker

$ go get -v github.com/dotcloud/docker/...
$ go install -v github.com/dotcloud/docker/...

$ docker version
$ docker -d
The docker runtime currently only supports amd64 (not arm). This will change in the future. Aborting.
```

Docker is installed but due to current limitations it won't run. It is a
start, we now have a development environment to start hacking on Docker
to get it to work with the RaspberryPi.

What's Next
===========

-   Now we have everything up and running, we need to change docker so
    that it will work on the ARM with only 32bit support.
-   I need to take my compiled kernel and make it downloadable to others
-   I need to make an SD card image of my setup for easy download, so
    that people can get started easier.

Want to Help?
=============

If you want to help me with this, please send me a message on twitter
[@KenCochrane](https://twitter.com/kencochrane) and also add your name
to this [Docker issue](https://github.com/dotcloud/docker/issues/636).

Resources:
==========

-   LXC:
    <http://raspberrypicloud.wordpress.com/2013/03/12/building-an-lxc-friendly-kernel-for-the-raspberry-pi/>
-   AUFS: <http://rpitc.blogspot.sg/p/kernel-rebuild.html>
-   Go: <http://dave.cheney.net/tag/go-golang-raspberrypi>
-   Docker:
    <http://docs.docker.io/en/latest/contributing/devenvironment.html>
