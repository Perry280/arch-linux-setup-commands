# Optimal Logical Sector Size
Check sector size
```
lsblk -td
```
### HDD
Check supported sector sizes
```
hdparm -I /dev/sda | grep 'Sector size:'
```
Change sector size (4096)
```
hdparm --set-sector-size 4096 --please-destroy-my-drive /dev/sda
```

### NVMe SSD
Check supported sector sizes
```
nvme id-ns -H /dev/nvme0n1 | grep "Relative Performance"
```
Change sector size (use format number from previous command for `--lbaf`)
```
nvme format --lbaf=1 /dev/nvme0n1
```

### [Sector size and dm-crypt](https://wiki.archlinux.org/title/Advanced_Format#dm-crypt)

# Disk encryption

### Wipe HDD
Create encrypted container
```
cryptsetup open --type plain --key-file /dev/urandom --sector-size 4096 /dev/sda to_be_wiped
```
Wipe the container
```
dd if=/dev/zero of=/dev/mapper/to_be_wiped status=progress bs=1M
```
Close the container
```
cryptsetup close to_be_wiped
```
Without `cryptsetup`:
```
dd if=/dev/zero of=/dev/sdX bs=4096 status=progress
```

### Wipe NVMe SSD
Which commands are supported
```
nvme id-ctrl /dev/nvme0 -H | grep -E 'Format |Crypto Erase|Sanitize'
```
Format:
```
nvme format /dev/nvme0 -s 1 -n 0xffffffff
```
Or sanitize:
```
nvme sanitize /dev/nvme0 -a start-block-erase
```

