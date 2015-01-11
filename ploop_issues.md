This list will describe weak sides of OpenVZ ploop disk layout system. It builded in few years of active using ploop and simfs on few hundreds of production servers.

- Ploop is very fragile because index block (BAT) located in first few MB of data and haven't any backup on another place. If you lost this block you can't do anything with your data. For example, ext4 have multiple copies of superblock in different places of block device. Ploop (format)[https://openvz.org/Ploop/format].
- Ploop is very sensitive to fsck of underlying ext4 filesystem. Fsck can corrupt and broke ploop completely if it changed any related with ploop extent of ext4 underlying filesystem. 
- Ploop increase disk fragmentation because new blocks allocated in random places of underlying hard drive. There are no any ways to do defragmentation of disk (but it's possible, theoretically). Any linear read from ploop can produce random seek over hard drive.
- Ploop usage without battery backed hardware controller is a Russian Rullet. Any hard reboot of server can broke hundreds of ploop drives.
- Ploop uses very big block size (1MB) and this can produce wasted space ("slick") for small allocated blocks. 
- Ploop do very bad things with O_DIRECT and fsync (they are completely disabled). This can produce data loss for critical databased but fortunately it's possible to enable fsync over ploop. More details: (here)[http://habrahabr.ru/company/parallels/blog/240197/]
- Troubles with vzctl compact which can't compact really huge not used disks.
