# USB drive
- I prefer 3.5 inch "NAS rated" hard drives, in a powered enclosure.
- Things like "WD elements" drives will work, but not as reliable in my experience
- I prefer to use LABEL rather than UUID to mount usb drives.

- go to the gui "disks" utility
- format the drive with "gpt partition table" and an "ext4 filesystem"
- label the **filesystem** (not the partition). For example: mediasrv1
- can also use `sudo e2label /dev/whatever mediasrv1` ("whatever" will most likely be sda1 or sdb1)
- the partition you create is owned by root with 755 permissions, see below

NOTE:
In this guide the usb drive LABEL is **mediasrv1** and the mount point is **/mnt/usb1**
If you use different values, **Substitute YOUR values** in the following commands.
**Do NOT blindly copy/paste**

```
# Create a mount point.
sudo mkdir /mnt/usb1

# Edit /etc/fstab as root, and add at the end:
LABEL=mediasrv1  /mnt/usb1  auto  defaults,noatime,nofail,x-systemd.device-timeout=1ms 0 2

# Mount the usb drive
sudo mount -a

# Make sure it's mounted
mountpoint /mnt/usb1

# If it's mounted, chown and chmod the partition so non-root users can write to it
sudo chown 1000:1000 /mnt/usb1
chmod 777 /mnt/usb1
```

NOTE:
- Do NOT chown or chmod /mnt/usb1 if the drive is not mounted.
- This won't do anything useful.
- The mount point's properties disappear when something is mounted to it, and is replaced with the
properties of the mounted partition.

## Additional drives
- same procedure as above
- partition will be something like /dev/sdb1 or /dev/sdc1 not sda1
- run `blkid` to figure out what the device names are
- use different LABEL and different mount point for each drive
