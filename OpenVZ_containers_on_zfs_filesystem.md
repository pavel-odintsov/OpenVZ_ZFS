Install ZFS On Linux:
```bash
yum localinstall --nogpgcheck https://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum localinstall --nogpgcheck http://archive.zfsonlinux.org/epel/zfs-release.el6.noarch.rpm
yum install vzkernel-devel zfs
```

Create ZFS mirror pool (you can use raidz, raidz2, raidz3 instead mirror):
```bash
zpool create data mirror /dev/sda3 /dev/sdb3
```

Disable OpenVZ quota completely:
```
vim /etc/vz/vz.conf
DISK_QUOTA=no
```

Create container:
```bash
export CTID=10001
# Create ZFS volume
zfs create data/$CTID
# Set quota for ZFS volume
zfs set quota=10G data/$CTID
vzctl create $CTID --ostemplate debian-7.0-x86_64-minimal --layout simfs --ipadd 5.45.112.45 --hostname zfs-tests.fastvps.ru --config vswap-2g --diskspace 10G --private '/data/$VEID/disk'
vzctl set $CTID --onboot yes --disabled no --quotaugidlimit 2048 --ram 4G --swap 2G --cpus 8 --ioprio 4 --cpuunits 2000 --cpulimit 800 --numproc 1024 --numiptent 256 --save
vzctl start $CTID
```

We use --private '/data/$VEID/disk' instead /data/$VEID due to vzctl [bug](https://github.com/pavel-odintsov/OpenVZ_ZFS/blob/master/OpenVZ_containers_on_zfs_filesystem.md):
```bash
vzctl create $CTID --ostemplate debian-7.0-x86_64-minimal --layout simfs --ipadd 5.45.112.45 --hostname zfs-tests.fastvps.ru --config vswap-2g --diskspace 10G --private '/data/$VEID'
Creating container private area (debian-7.0-x86_64-minimal)
Can't rename /data/10001.tmp to /data/10001: Device or resource busy
Destroying container private area: /data/10001
Warning: directory /data/10001 is not on the same filesystem as /data/10001/vztmp - doing slow/sync removal
/bin/rm: cannot remove `/data/10001': Device or resource busy
Creation of container private area failed
```

Container destroy:
```
export CTID=10001
vzctl stop $CTID
zfs destroy data/$CTID
vzctl destroy $CTID
```
Manage disk space limit for container:
```bash
zfs set quota=20G data/$CTID
``` 

Check perfectly working container:
```bash
vzctl enter 101
entered into CT 101
root@evo1000:/# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/simfs       10G  279M  9.8G   3% /
none            128M  4.0K  128M   1% /dev
none             26M   36K   26M   1% /run
none            5.0M     0  5.0M   0% /run/lock
none            103M     0  103M   0% /run/shm
none            100M     0  100M   0% /run/user
```

Congratulations :)
