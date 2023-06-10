


# Working with storage drives 


## Partitioning using fdisk

- Create ebs volume. Set the device name as `/dev/xvdf` to avoid confusion. Select same AZ as instance and attach to it.
-  For having 5 partitions of 4 gb each, we need 21 GB instead of 20 as the partition table and file system struicture takes extra space.
- After attaching, ssh to the instance. Use `lsblk`  
	![](Images/Pasted%20image%2020230411192038.png)


- use fdisk comamnd to enter fdisk partitioning `fdisk /dev/xvdf`
- select `g` for selecting GPT partitioning table
- select `n` for creating a new partition, select the partition number, first sector and last sector. Can specify the size in s, m, or g for sectors, megabytes or gigabytes. 
- make multiple partitions using `n`.  use  `w`  to write the changes and exit. 
- After creating these partitions, format the partitions using a filesystem like ext4
	mkfs.ext4 /dev/xvdf1
    mkfs.ext4 /dev/xvdf2
    mkfs.ext4 /dev/xvdf3
    mkfs.ext4 /dev/xvdf4
    mkfs.ext4 /dev/xvdf5
<br/>
- Make directories for mounting and mount them in respective directores
	mkdir /mount1
    mkdir /mount2
    mkdir /mount3
    mkdir /mount4
    mkdir /mount5
    mount /dev/xvdf1 /mount1
    mount /dev/xvdf2 /mount2
    mount /dev/xvdf3 /mount3
    mount /dev/xvdf4 /mount4
    mount /dev/xvdf5 /mount5

- run `df -h`  to check if mounted.

<br/>
<br/>

---


<br/>
<br/>

---


<br/>
<br/>


## Partitioning using LVM

![](Images/Pasted%20image%2020230411195602.png)


- Install lvm if not present
`yum install lvm2` 

- Use fdisk to partition the disk if needed. (good practive to create a partition)
- create physical volume using `pvcreate /dev/xvdf1` . If partition is formatted as another file system, then will have to wipe.
- use vgcreate to create volume group ` vgcreate myvg /dev/sdf1 /dev/sdf2 /dev/sdf3 /dev/sdf4 /dev/sdf5`
- Use lvcreate to create logical volume `lvcreate -L 4G -n myvg-lv1 myvg` 
- Format the logical volumes using a file system like ext4 ` mkfs.ext4 /dev/myvg/myvg-lv1`
- Mount them on the respective folders `mount /dev/myvg/myvg-lv1 /mount1`
- To resize using `lvextend` or `lvreduce`  (Must do e2fsck -f /dev/myvg/lv1 before resizing file system to check file system for any errors)
>   - For lvreduce, unmount, check for filesystem error, use resize2fs and set the new reduced size , THEN ONLY USE lvreduce, mount again
  ![](Images/Pasted%20image%2020230411203501.png)
