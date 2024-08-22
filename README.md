# Library
Library of random tasks and that I've needed answers to.


## Encrypt USB drive

### Wipe the drive
If there is anything on the drive, it's probably worth wiping it.
If really worried about security, doing a full ATA secure erase is advised.
See this answer #[here](https://security.stackexchange.com/questions/158424/does-a-drive-need-to-be-wiped-before-encrypting-it-to-be-completely-safe)
to see about this topic, and research ATA from there.

Find the drive with
```
lsblk
```

Unmount the drive:
```
sudo umount /dev/sdXn
```
where X is the drive seen in `lsblk` and n is the number associated
with a mount point.

Wipe the drive with:
```
sudo wipefs -a /dev/sdX
```
where X is the drive seen in `lsblk`. The `-a` means to erase all
available signatures.


### Create partition table
The partition table should then be created before encrypting the drive.
Do so with either gdisk or fdisk:
```
gdisk /dev/sdX
```

Then use the commands `o` to create the GUID partition table (GPT), and
`w` to write it to the drive.


### Encrypt the drive
Encrypt the drive using LUKS.

```
sudo cryptsetup luksFormat /dev/sdX
```

you will be prompted to input a passphrase and say "YES".


### Open the encrypted drive
After encrypting the drive, to be able to add the
filesystem, the drive must be opened.

```
sudo cryptsetup luksOpen /dev/sdX encrypted_usb
```
This creates a mapped device under `/dev/mapper/encrypted_usb`.

### Create partitions on the device
Now that the device has been opened, we can create partitions
within the decrypted volume using fdisk.

```
sudo fdisk /dev/mapper/encrypted_usb
```

If you want to encrypt the whole disk, you can just select the defaults after
using `n` to create a new partition and `w` to write the partition table and exit.

After this, check to find whether a new partition has been added.

You can check if the partition has been added to the drive by opening fdisk
again and checking with the `p` command to view partitions.

After doing this, you can check whether another location hsa been added using either
`lsblk` or `ls /dev/mapper`.


### Create the filesystem on the device
Format with either ext4, ntfs, or any other filesystem.

To do this run:
```
sudo mkfs.ext4 /dev/mapper/encrypted_usb
```


### Mount the drive
Mount using
```
mount /dev/mapper/encrypted_usb /mnt/mountpoint
```
NOTE: make sure you create the mountpoint first with something like
```
sudo mkdir /mnt/mountpoint
```


### Potential permission issues
If you open your drive through the GUI, it might be that you cannot
access the files within the drive. This is likely because of permission
issues. For example, the directory might be owned by root. You can either change
the ownership or permissions.


### Unmount the drive
Unmount using:
```
sudo /mnt/mountpoint
```

### Close the encrypted drive
Close the unencrypted mapping with:
```
sudo cryptsetup luksClose encrypted_usb
```

## Installing Kali with encrypted persistence

TODO:
https://www.geeksforgeeks.org/creating-an-encrypted-persistent-live-kali-usb/
