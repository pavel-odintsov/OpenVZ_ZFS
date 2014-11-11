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

