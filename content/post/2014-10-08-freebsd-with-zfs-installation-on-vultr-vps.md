---
title: "FreeBSD with ZFS installation on Vultr VPS"
date: 2014-10-08T11:55:59+08:00
draft: false
weight: 20
---

\(This article is part of the Chinese version [在Vultr的VPS上安装支持ZFS的FreeBSD及SMTP问题的解决](http://blog.csdn.net/raptor/article/details/39854397)\)

[Vultr](http://www.vultr.com/?ref=6811567) VPS is good cause thier VPS can be installed FreeBSD. The better thing is it can be installed OS from custom ISO.

I had tried the official FreeBSD 10/x64 image, but there is not ZFS. It had been fail when I tried to resize partition to create a ZPOOL. Fortunately,however, we can also install a FreeBSD from custom ISO.

### Installation

At first, Let's login the Vultr customer's panel. At the bottom of tab page _My Servers_, you will find the _My ISOs_ link, click into the page _Upload Iso_, fill in the _Remote URL_ box with the ISO file URL, eg. _FreeBSD-10.0-RELEASE-amdx64-bootonly_ is [ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/amd64/ISO-IMAGES/10.0/FreeBSD-10.0-RELEASE-amd64-bootonly.iso](ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/amd64/ISO-IMAGES/10.0/FreeBSD-10.0-RELEASE-amd64-bootonly.iso), and press the _Upload_ button. The ISO file will be transfered from _FreeBSD.org_.

Transfer file between servers is fast. When it was done, you can switch to the page _Deploy_ to create a new VPS. Select the appropriate _Server Type_, _Location_, Server Size_ according to your needs. Large memory is required for ZFS, 4G is recommended, bigger is better.

Select _Custom_ in _Operating System_ option and choose _Virtual ISO_ is just uploaded ISO file _ISO_ _FreeBSD-10.0-RELEASE-amd64-bootonly.iso_ .

Then press _Place Order_ button. You will get a new VPS and it will boot from this ISO now. Switch to the page _My Servers_ and press the VPS's _Manage_ link. In the page _Server Information_ you can press the _View Console_ button to open the console window.

Now, you can install the FreeBSD in the console. There are some key steps:

* make disk partition manually. eg. /boot=64K, /=4G, zfs=14G, swap=2G .
* don't set mount point for zfs and give it a label, eg. tank.
* minimal installation, docs/sources/ports/etc. are excluded.
* use DHCP to config the networking

After done, shutdown this VPS. Open the _Custom ISO_ tab in the page _Server Information_ , detach the ISO file and restart this VPS.

Now, you can log into the system via console or SSH and use it.

### ZFS

You must create a zpool in zfs partition at first:

```
zpool create ztank /dev/gpt/tank
```

_ztank_ is the zpool name, _tank_ is the partition label name.

and create zfs like this:

```
zfs create -o mountpoint=/home/myusername/myhome ztank/myhome
```

or create a zfs for ports:

```
mv /usr/ports /usr/ports_old
zfs create -o mountpoint=/usr/ports -o compression=on ztank/ports
mv /usr/ports_old/* /usr/ports
rmdir /usr/ports_old
```

Enjoy it.
