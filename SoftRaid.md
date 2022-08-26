# Software RAID
## Part 0
All wanted partitions shall be "Linux RAID" type
## Commands
1. Create RAID
```
mdadm -v --create /dev/md/0 --auto=yes --level=$RAIDLEVEL --raid-devices=$PCOUNT $DEVICES
```
2. Autoconfig
```
mdadm --examine --scan >> /etc/mdadm/mdadm.conf
update-initramfs -u
reboot
```
