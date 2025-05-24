---
layout: post
title: proxmox - physical to virtual migration
---

{{ page.title }}
================

<p class="meta">May 24 2025</p>

this guide will describe how to migrate a physical machine to a proxmox virtual machine.

## create disk image of the physical machine

1. boot the machine using a linux live cd. I'm using Knoppix 7.2 because it works with the Dual Pentium III machine.

2. find the source disk

```shell
lsblk

# assuming the output as follows

# /dev/sda      <-- destination
# /dev/sdb      <-- source
```

3. mount destination disk (previously formatted as ext3 for knoppix compatibility)

```shell
mkdir -p /mnt/destination
mount -t ext3 /dev/sda1 /mnt/destination
```

4. create disk image

```shell
dd if=/dev/sdb of=/mnt/destination/server.img bs=8M conv=noerror,sync
```

## import image into virtual machine

1. create a new proxmox virtual machine without a disk. note the vmid, i'll use vmid 109 as an example.

2. on the proxmox instance, mount the destination disk.

3. convert disk image to qcow2

```shell
qemu-img convert -f raw -O qcow2 server.img server.qcow2
```

4. import disk into virtual machine

```shell
# vm-storage is an example, this would be the storage volume that you store proxmox vm disks.
qm disk import 109 server.qcow2 vm-storage
```
