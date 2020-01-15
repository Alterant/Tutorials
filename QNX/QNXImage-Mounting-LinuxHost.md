# QNX Image (IFS and SysFS) Mounting on Linux Host

This document provides the information on mounting the QNX IFS and Root file system images on Linux Host. Purpose of mounting the QNX tagert images on Linux host could be prodcution image verification or secure configuration analysis.Currently there are two approch to acheive this feature:

1. QNX6 filesystem driver for LinuxHost
2. Dumpifs utility for LinuxHost

## QNX6 filesystem driver

Blackberry provides LinuxHost drivers for supporting the QNX filesystem on Linux machine. The QNX filesystem drivers (QNX6 and QNX4) are by default exist on linux kernel as a module. We have to load drivers at runtime and utilize driver's functionaliity using mount command. Here is a step by step guide to do the same.

1. Check weather QNX filesystem modules are loadable or not (By default they are loadable)

   ```console
   yocto@ubuntu:~$ sudo grep QNX /boot/config-$(uname -r)
    [sudo] password for yocto:
    CONFIG_QNX4FS_FS=m
    CONFIG_QNX6FS_FS=m
    # CONFIG_QNX6FS_DEBUG is not set
    yocto@ubuntu:~$
    ```  

2. Check the path of loadable modules in kernel

   ```console
   yocto@ubuntu:~$ modinfo qnx6
    filename:       /lib/modules/4.15.0-74-generic/kernel/fs/qnx6/qnx6.ko
    license:        GPL
    alias:          fs-qnx6
    srcversion:     C9E5CC55C4C74A4063CF3FD
    depends:
    retpoline:      Y
    intree:         Y
    name:           qnx6
    vermagic:       4.15.0-74-generic SMP mod_unload 
    signat:         PKCS#7
    signer:
    sig_key:
    sig_hashalgo:   md4
    yocto@ubuntu:~$
   ```

3. Check weather QNX filesystem modules are already loaded

   ```console
   yocto@ubuntu:~$ cat /proc/filesystems
    nodev   sysfs
    nodev   rootfs
    nodev   ramfs
    nodev   bdev
    nodev   proc
    nodev   cpuset
    nodev   cgroup
    nodev   cgroup2
    nodev   tmpfs
    nodev   devtmpfs
    nodev   configfs
    nodev   debugfs
    nodev   tracefs
    nodev   securityfs
    nodev   sockfs
    nodev   dax
    nodev   bpf
    nodev   pipefs
    nodev   hugetlbfs
    nodev   devpts
            ext3
            ext2
            ext4
            squashfs
            vfat
    nodev   ecryptfs
            fuseblk
    nodev   fuse
    nodev   fusectl
    nodev   pstore
    nodev   mqueue
    nodev   autofs

   ```

4. If not loaded, Please load it with the command

```console
    yocto@ubuntu:~$ sudo modprobe fs_qnx6
    [sudo] password for yocto:
    yocto@ubuntu:~$
```

5. Check module successfully loaded or not

```console
    yocto@ubuntu:~$ cat /proc/filesystems | grep qnx
    qnx6
    qnx4
    yocto@ubuntu:~$

```

6. Now we can use the mount command to extract the QNX image on LinuxHost

```console
   yocto@ubuntu:~$ sudo mount -t qnx6 -o loop $image_path $mount_path
```

Image will be mounted on $mount_path with RO (read only) permissions.

7. Now we can apply verification or testing process on mounted filesystem.

## Dumifs Utility

Blackberry provides an utility to dump the information from IFS image using `dumifs` utiltiy. The `dumpifs` utiltiy is available for both QNX and Linux machines.
This document focuses on how to utilize `dumpifs` on LinuxHost.

The `dumpifs` utility can be get from QNX Software centre for both QNX and Linux target.
