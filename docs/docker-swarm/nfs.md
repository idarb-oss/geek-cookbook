---
title: NFS
summary: Shared storage with NFS
authors:
  - Idar Bergli
date: 2021-04-22
---

One way to have a shared storage between the `swarm nodes` is by using **NFS** lets configure this onto the system.

## Install NFS for client

Firstly we need to make sure we have the needed package for using _NFS_ by doing the following:

```sh
sudo apt-get insall nfs-common
```

## Create shared directory

So lets start by creating the area where we want to mount the _NFS_ folder.

```sh
sudo mkdir /data/nfs/volumes -p
```

Then lets mount our shared folder from the _NFS_ server:

```sh
sudo mount host_ip:/path/to/folder /data/nfs/volumes
```

These commands will mount the shares from the host computer onto the **client** machine. You can double-check that they mounted successfully in several ways. You can check this with a `mount` or `findmnt` command, but `df -h` provides a more readable output:

```sh
df -h

Filesystem                  Size  Used Avail Use% Mounted on
udev                        406M     0  406M   0% /dev
tmpfs                       91M  3.9M   87M   5% /run
/dev/mmcblk0p2              59G  3.4G   53G   6% /
tmpfs                       455M     0  455M   0% /dev/shm
tmpfs                       5.0M     0  5.0M   0% /run/lock
tmpfs                       455M     0  455M   0% /sys/fs/cgroup
/dev/loop1                  49M   49M     0 100% /snap/core18/2002
/dev/loop0                  49M   49M     0 100% /snap/core18/1949
/dev/loop2                  62M   62M     0 100% /snap/lxd/19040
/dev/loop3                  63M   63M     0 100% /snap/lxd/19648
/dev/loop4                  27M   27M     0 100% /snap/snapd/10709
/dev/loop5                  29M   29M     0 100% /snap/snapd/11584
/dev/mmcblk0p1              253M  119M  134M  47% /boot/firmware
tmpfs                       91M     0   91M   0% /run/user/1000
host_ip:/path/to/folder     11T  6.5T  4.3T  61% /data/nfs/volumes
```

## Mount the NFS directory as boot

We can mount the remote NFS shares automatically at boot by adding them to `/etc/fstab` file on the client.

At the bottom of the file, add a line for each of our shares. They will look like this:

```
host_ip:host_ip:/path/to/folder    /data/nfs/volumes   nfs auto,nofail,noatime,nolock,intr,tcp,actimeo=1800 0 0
```

The client will automatically mount the remote partitions at boot, although it may take a few moments to establish the connection and for the shares to be available.

## NFS from Docker compose

To use NFS as volumes add the following in the docker compose files:

```yaml 
version: "3"

volumes:
    data:
        driver: local
        driver_opts:
            type: nfs
            o: addr=<nfs-Host-domain-name>,rw,sync,nfsvers=4.1
            device: ":<path to directory in nfs server>"
```