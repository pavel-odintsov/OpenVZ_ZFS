## Seven problems of Ploop disk layout

This list will describe weak sides of OpenVZ ploop disk layout system. This issues collected in few years of active use of ploop and simfs on few hundreds production servers.

- Ploop is very fragile because index block (BAT) located in first few MB of file and haven't any backup in another place. If you lost this block you can't do anything with your data. For example, ext4 have multiple copies of superblock in different places of block device. Details: [ploop specification](https://openvz.org/Ploop/format).
- Ploop is very sensitive to fsck of underlying ext4 filesystem. Fsck can corrupt and broke ploop completely if it changed any related with ploop extent of ext4 underlying filesystem. 
- Ploop increase disk fragmentation because new blocks allocated in random places of underlying hard drive. There are no any ways to do defragmentation of disk (but it's possible, theoretically). Any linear read from ploop can produce random seek over hard drive.
- Ploop usage without battery backed hardware controller is a Russian Roulette. Any hard reboot of server can broke hundreds of ploop drives. Why it happens? I don't know and Parallels discribed it as side feature of writeback without BBU in Parallels #1925475.
- Ploop uses very big block size (1MB) and this can produce wasted space ("slick space") for small allocated blocks. You can't set smaller block with vzctl now. But you can do it with ploop tool. 
- Ploop does very bad things with O_DIRECT and fsync (they are completely disabled inside container). This can produce data loss for critical databases but fortunately it's possible to enable fsync over ploop. More details: [here](http://habrahabr.ru/company/parallels/blog/240197/)
- Troubles with vzctl compact which can't compact really huge not used disks (described in openvz mail list [here](https://lists.openvz.org/pipermail/users/2015-January/006026.html)).
