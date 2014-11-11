Create container:
```bash
export CTID=10001
vzctl create $CTID --ostemplate debian-7.0-x86_64-minimal --layout simfs --ipadd 5.45.112.45 --hostname zfs-tests.fastvps.ru --config vswap-2g --diskspace 10G
vzctl set $CTID --onboot yes --disabled no --quotaugidlimit 2048 --ram 4G --swap 2G --cpus 8 --ioprio 4 --cpuunits 2000 --cpulimit 800 --numproc 1024 --numiptent 256 --save
vzctl start $CTID
```

Create ZFS volume:
```bash
zpool create data mirror /dev/sda3 /dev/sdb3
```

Move container disk to ZFS:
```bash
vzctl stop 10001
zfs create data/10001
mv /vz/private/10001 /vz/private/101_old
```

Change container root folder:
```bash
vim /etc/vz/conf/10001.conf
VE_PRIVATE="/data/$VEID"
```

Disable OpenVZ quota completely:
```
vim /etc/vz/vz.conf
DISK_QUOTA=no
```
Download image:
```bash
vztmpl-dl debian-7.0-x86_64-minimal
tar -xf /vz/template/cache/debian-7.0-x86_64-minimal.tar.gz -C /data/10001
```

Create container template based on ZFS volume:
```bash
zfs create data/debian-7.0-x86_64-minimal
tar -xf /vz/template/cache/debian-7.0-x86_64-minimal.tar.gz -C /data/debian-7.0-x86_64-minimal
zfs set readonly=off data/debian-7.0-x86_64-minimal
```

Create snapshot:
```bash
zfs snapshot data/debian-7.0-x86_64-minimal@111114
```

Create container from template volume:
```
zfs send data/debian-7.0-x86_64-minimal@111114 |zfs recv data/10001
```

Set container quota on ZFS level:
```bash
zfs set quota=10G data/10001
```

Start container:
```bash
vzctl start 10001
```

If you do not disable OpenVZ quota you will got error:
```bash
vzctl restart 101
Restarting container
Starting container...
Initializing quota ...
vzquota : (error) Quota getstat syscall for id 101: Inappropriate ioctl for device
vzquota init failed [3]
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
