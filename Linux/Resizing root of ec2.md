
[Resizing the Root Disk on a Running EBS Boot EC2 Instance](https://alestic.com/2010/02/ec2-resize-running-ebs-root/)

By **Eric Hammond** Feb 10, 2010[EC2](https://alestic.com/categories/ec2)[Ubuntu](https://alestic.com/categories/ubuntu)

In a previous article I described how to [run an EBS boot AMI with a larger root disk size than the default](https://alestic.com/2009/12/ec2-ebs-boot-resize). That’s fine if you know the size you want before running the instance, but what if you have an EC2 instance already running and you need to increase the size of its root disk without running a different instance?

As long as you are ok with a little down time on the EC2 instance (few minutes), it is possible to change out the root EBS volume with a larger copy, without needing to start a new instance.

Let’s walk through the steps on a sample Ubuntu 16.04 LTS Xenial HVM instance. I tested this with `ami-40d28157` but check for the latest AMI ids.

On the instance we check the initial size of the root file system (8 GB):

```
$ df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      7.8G  913M  6.5G  13% /
```

The following commands are all run on a system _other_ than the one we are resizing. Pick a new size (in GB) that is larger than the current size:

```
instance_id=<YOURINSTANCEID>
size=20
region=us-east-1
```

Get the root EBS volume id and availability zone for this instance:

```
root_device=$(aws ec2 describe-instances \
  --region "$region" \
  --instance-ids $instance_id \
  --output text \
  --query 'Reservations[*].Instances[*].RootDeviceName')
old_volume_id=$(aws ec2 describe-instances \
  --region "$region" \
  --instance-ids $instance_id \
  --output text \
  --query 'Reservations[*].Instances[*].BlockDeviceMappings[?DeviceName==`'$root_device'`].[Ebs.VolumeId]')
zone=$(aws ec2 describe-instances \
  --region "$region" \
  --instance-ids $instance_id \
  --output text \
  --query 'Reservations[*].Instances[*].Placement.AvailabilityZone')
echo "instance $instance_id in $zone with original volume $old_volume_id"
```

Stop (not terminate!) the instance:

```
aws ec2 stop-instances \
  --region "$region" \
  --instance-ids $instance_id
aws ec2 wait instance-stopped \
  --region "$region" \
  --instance-ids $instance_id
```

Detach the original volume from the instance:

```
aws ec2 detach-volume \
  --region "$region" \
  --volume-id "$old_volume_id"
```

Create a snapshot of the original volume:

```
snapshot_id=$(aws ec2 create-snapshot \
  --region "$region" \
  --volume-id "$old_volume_id" \
  --output text \
  --query 'SnapshotId')
aws ec2 wait snapshot-completed \
  --region "$region" \
  --snapshot-ids "$snapshot_id"
echo "snapshot: $snapshot_id"
```

Create a new volume from the snapshot, specifying a larger size:

```
new_volume_id=$(aws ec2 create-volume \
  --region "$region" \
  --availability-zone "$zone" \
  --size "$size" \
  --snapshot "$snapshot_id" \
  --output text \
  --query 'VolumeId')
echo "new volume: $new_volume_id"
```

Attach the new volume to the instance:

```
aws ec2 attach-volume \
  --region "$region" \
  --instance "$instance_id" \
  --device "$root_device" \
  --volume-id "$new_volume_id"
aws ec2 wait volume-in-use \
  --region "$region" \
  --volume-ids "$new_volume_id"
```

Start the instance and find its new public IP address/hostname. (If you were using an elastic IP address, re-assign it to the instance.)

```
aws ec2 start-instances \
  --region "$region" \
  --instance-ids "$instance_id"
aws ec2 wait instance-running \
  --region "$region" \
  --instance-ids "$instance_id"
aws ec2 describe-instances \
  --region "$region" \
  --instance-ids "$instance_id"
```

Connect to the instance with ssh (not shown) and resize the root file system to fill the new EBS volume. This step is done automatically at boot time on modern Ubuntu AMIs:

```
# ext3 root file system (most common)
sudo resize2fs /dev/xvda1

# XFS root file system (less common):
sudo apt-get update && sudo apt-get install -y xfsprogs
sudo xfs_growfs /
```

Show that the root file system is the new, larger size (20 GB):

```
$ df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G  960M   18G   6% /
```

Delete the old EBS volume and snapshot if you no longer need them, though I recommend you at least keep the snapshot for a while just in case:

```
aws ec2 delete-volume \
  --region "$region" \
  --volume-id "$old_volume_id"
aws ec2 delete-snapshot \
  --region "$region" \
  --snapshot-id "$snapshot_id"
```

Note: Since you manually created the new volume and attached it to the instance yourself, it will not be deleted automatically when the instance is terminated. You can modify the instance attributes to change the `delete-on-termination` flag for the volume if you wish.

_[Update 2011-11-21: Note that the resize2fs step is done automatically on modern Ubuntu AMIs.]_

_[Update 2011-11-21: Modern Ubuntu AMIs now call it /dev/xvda1 instead of /dev/sda1]_

_[Update 2016-10-28: Updated to latest aws-cli command syntax, and tested with recent Ubuntu AMI]_

[Show Comments](https://alestic.com/2010/02/ec2-resize-running-ebs-root/#)