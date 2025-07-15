# FILE SYSTEM MANAGEMENT
- `ext2` <br>
Old file system, no journaling capabilities, less suited for modern systems but still useful in certain low-overhead scenarios (like USB drives)
- `ext3` and `ext4` <br>
More advanced with journaling (which helps in recovering from crashes). Default choice for most modern Linux systems, offers a balance of performance, reliability, and large file support
- `Btrfs` <br>
Known for advanced features like snapshotting, built in data integrity checks, ideal for complex storage setups
- `XFS` <br>
Excels at handling large files and has high performance, best suited for environments with high I/O demands
- `NTFS` <br>
Originally developed for Windows, useful for compatibility when dealing with dual-boot systems or external drives that need to work on both Linux and Windows.

## Inodes
`Inodes` are data structures that store metadata about each file and directory, including permissions, ownership, size, and timestamps. Inodes do not store the file's actual data or name, but they contain pointers to the blocks where the file's data is stored on the disk.

`inode` table is a collection of `inodes`, acting as a database that the kernel uses to track every file and directory on the system. This strucutre allows the OS to efficiently accesss and manage files. 

## Symbolic Links
Symbolic links (`symlinks`) act as shortcuts or references to other files or directories. Symbolic links allow quick access to files located in different parts of the file system without duplicating the file itself. 

## Disks & drives
`fdisk` allows users to create, delete, and manage partitions on a drive. Displays information about the partition table, including the size and type of each partition. Partitioning a drive on Linux involves dividing the physical storage space into separate, logical sections. Each partition can then be formatted with a file system. 

```
farelmusyaffa@htb[/htb]$ sudo fdisk -l

Disk /dev/vda: 160 GiB, 171798691840 bytes, 335544320 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x5223435f

Device     Boot     Start       End   Sectors  Size Id Type
/dev/vda1  *         2048 158974027 158971980 75.8G 83 Linux
/dev/vda2       158974028 167766794   8792767  4.2G 82 Linux swap / Solaris

Disk /dev/vdb: 452 KiB, 462848 bytes, 904 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```
## Mounting
Each logical partition must be assigned a specific directory in the file system. This process is known as mounting. The `mount` cmomand is commonly used to manually mount file systems on Linux. Mounting can also be done by defining mount points at system boot in `/etc/fstab`.
```

farelmusyaffa@htb[/htb]$ cat /etc/fstab

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a device; this may
# be used with UUID= as a more robust way to name devices that works even if
# disks are added and removed. See fstab(5).
#
# <file system>                      <mount point>  <type>  <options>  <dump>  <pass>
UUID=3d6a020d-...SNIP...-9e085e9c927a /              btrfs   subvol=@,defaults,noatime,nodiratime,nodatacow,space_cache,autodefrag 0 1
UUID=3d6a020d-...SNIP...-9e085e9c927a /home          btrfs   subvol=@home,defaults,noatime,nodiratime,nodatacow,space_cache,autodefrag 0 2
UUID=21f7eb94-...SNIP...-d4f58f94e141 swap           swap    defaults,noatime 0 0
```
To view currently mounted file systems, `mount` without any arguments can be used. 
```
farelmusyaffa@htb[/htb]$ mount

sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=4035812k,nr_inodes=1008953,mode=755,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,noexec,relatime,size=814580k,mode=755,inode64)
/dev/vda1 on / type btrfs (rw,noatime,nodiratime,nodatasum,nodatacow,space_cache,autodefrag,subvolid=257,subvol=/@)
```
## Mounting a USB drive
```
farelmusyaffa@htb[/htb]$ sudo mount /dev/sdb1 /mnt/usb
farelmusyaffa@htb[/htb]$ cd /mnt/usb && ls -l

total 32
drwxr-xr-x 1 root root   18 Oct 14  2021 'Account Takeover'
drwxr-xr-x 1 root root   18 Oct 14  2021 'API Key Leaks'
drwxr-xr-x 1 root root   18 Oct 14  2021 'AWS Amazon Bucket S3'
drwxr-xr-x 1 root root   34 Oct 14  2021 'Command Injection'
drwxr-xr-x 1 root root   18 Oct 14  2021 'CORS Misconfiguration'
drwxr-xr-x 1 root root   52 Oct 14  2021 'CRLF Injection'
drwxr-xr-x 1 root root   30 Oct 14  2021 'CSRF Injection'
drwxr-xr-x 1 root root   18 Oct 14  2021 'CSV Injection'
drwxr-xr-x 1 root root 1166 Oct 14  2021 'CVE Exploits'
...SNIP...
```
## Unmounting
```
farelmusyaffa@htb[/htb]$ sudo umount /mnt/usb
```
To automatically unmount a files system at shutdown, add `noauto` option to an entry in `/etc/fstab`.
```
/etc/fstab

/dev/sda1 / ext4 defaults 0 0
/dev/sda2 /home ext4 defaults 0 0
/dev/sdb1 /mnt/usb ext4 rw,noauto,user 0 0
192.168.1.100:/nfs /mnt/nfs nfs defaults 0 0
```
## Swap
When the system runs out of physical memory, the kernel moves inactive pages of memory to the swap space.

`mkswap` creates a swap space <br>
`swapon` actives the swap space
