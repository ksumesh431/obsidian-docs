

# To create swap from root partition

[Use swap file to allocate memory as swap space in Amazon EC2 instance | AWS re:Post (repost.aws)](https://repost.aws/knowledge-center/ec2-memory-swap-file)


<br/>
<br/>
<br/>
<br/>
<br/>


# To create swap using external storage

- Attach ebs volume to instance
<br/>
- get the path of the ebs volume using: `lsblk --path`
<br/>
- start fdisk:
  `fdisk path_of_ebs_volume`
<br/>
- in fdisk, create a new partition using: n
  type t to change partition type
  type 82 to select NetBSD which is needed for swap partition
<br/>
- get the path of the ebs PARTITION using: `lsblk --path`
<br/>
- initialize it as a swap partition. it will format it as swap partition:
  `mkswap path_of_partition`
<br/>
- To start using the swap partition, you need to enable it using the swapon command:
  `swapon path_of_partition`
<br/>
- Add this line to /etc/fstab so it it mounts automatically on reboot
` path_of_partition       swap       swap       defaults       0 0`
<br/>
- use `swapon -s` to check if swap created or not