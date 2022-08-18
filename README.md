# Drewlee Openwrt

This repo record some openwrt compile experience.

[reference_link](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem)

## Step by step

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



