# Drewlee Openwrt

This repo record some openwrt compile experience.

[reference_link](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem)

## Build step by step

### Step 1

```sh
# Download and update the sources
git clone https://git.openwrt.org/openwrt/openwrt.git
cd openwrt
git pull
 
# Select a specific code revision
git branch -a
git tag
git checkout v21.02.3
 
# Update the feeds
./scripts/feeds update -a
./scripts/feeds install -a
```

### Step 2

```sh
wget [this_repo/.config] -O .config
```

### Step 3

```sh
make menuconfig
```

Load config file

```sh
make defconfig

# Need science network maybe
make download
```

### Step 4

First time recommend:

```sh
make -j1 -V=sc
```

Second time and for on:

```sh
make -j(nproc) -V=s
```

#### Trouble check

When some package compile error,
then rebuilding single package:

```sh
make package/utils/jsonpath/{clean,compile} V=s
```

## Install openwrt firmware

### Step 1

Use this package 👇

`openwrt-21.02.3-x86-64-generic-ext4-combined.img.gz`

Wirte *.img.gz file to usb storage device

### Step 2

Start usb storage device system, set side router.

```sh
# set static ip
uci set network.lan.ipaddr="192.168.2.1"
uci commit network
/etc/init.d/network restart
```

[ref1](https://pfschina.org/wp/?p=8031)
[ref2](https://sspai.com/post/68511)

### Step 3

Test network is ok
`ping baidu.com`

### Step 4 (Optional)

Resizing partitions

[ref](https://openwrt.org/docs/guide-user/installation/openwrt_x86#resizing_partitions)

1. Install fdisk.
1. Use fdisk to show the partitions.
1. Write down the starting sector address of /dev/sda2 (which is the root partition).
1. Use fdisk to delete the partition 2 (which is sda2), don't write the changes to disk yet.
1. Use fdisk to create a new partition 2, choose/type the starting sector address you wrote down earlier (as by default it will try to place it somewhere else), and leave the default end sector address (this will mean the partition will now use all available space).
1. Write the partition table changes to disk. It may complain about partition signatures already present, write n to NOT remove the partition signature to proceed.

ex:

```
Welcome to fdisk (util-linux 2.32).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): p
Disk /dev/sda: 7.2 GiB, 7751073792 bytes, 15138816 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xcbad8a62

Device     Boot Start    End Sectors  Size Id Type
/dev/sda1  *      512  33279   32768   16M 83 Linux
/dev/sda2       33792 246783  212992  104M 83 Linux

Command (m for help): d
Partition number (1,2, default 2): 

Partition 2 has been deleted.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): 
Partition number (2-4, default 2): 
First sector (33280-15138815, default 34816): 33792
Last sector, +sectors or +size{K,M,G,T,P} (33792-15138815, default 15138815): 

Created a new partition 2 of type 'Linux' and of size 7.2 GiB.
Partition #2 contains a ext4 signature.

Do you want to remove the signature? [Y]es/[N]o: n

Command (m for help): w

The partition table has been altered.
Syncing disks.
```

### Step 5 (Optional)

Resizing filesystem

[ref](https://openwrt.org/docs/guide-user/installation/openwrt_x86#resizing_ext4_rootfs)

Resize Ext4 rootfs for ext4-combined.img.gz:

```
opkg update
opkg install losetup resize2fs
BOOT="$(sed -n -e "/\s\/boot\s.*$/{s///p;q}" /etc/mtab)"
DISK="${BOOT%%[0-9]*}"
PART="$((${BOOT##*[^0-9]}+1))"
ROOT="${DISK}${PART}"
LOOP="$(losetup -f)"
losetup ${LOOP} ${ROOT}
fsck.ext4 -y ${LOOP}
resize2fs ${LOOP}
reboot
```








