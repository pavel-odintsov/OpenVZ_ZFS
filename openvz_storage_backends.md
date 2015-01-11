Table with storage backends comparison

| Quality | OpenVZ simfs over ext4         | OpenVZ ploop       | LVM volumes with ext4         | ZFS |
| --------| -------------  |-------------| ------------| -------|
| Maturity| 10+ years      | Since 2012  | Since 1998  | Since 2014 (Linux), Since 2005 (Solaris)        |
| Wasted space due to architecture | No | Yes, up to 20% for allocated ext4 metadata | N/A | Zero |
| Speed   | Very fast with small amount of containers per node      | Fast in any case        | Fast in any case       | Fast enough in any case|
| I/O isolation | Very bad (shared filesystem journal) | Excellent   | Excellent   | Excellent |
| Wasted space amount      | Very small (ext4 block size) | Big enough (ploop uses 1MB block) | Small (ext4 block size) | Extremely small (variable block size) | 
| Ability to overcommit disk space (provide more space for containers then available on server now) | Yes | Yes | No | Yes |
| Reliability | Very small, big amount of files produce ext4 corruption so often | Small, fsck, power loss and HW Raid without cache can kill whole data | Good enough (but LVM metadata can be corrupted completely) | Excellent (no write hole, checksumming and COW) | 
| Backup speed | Very slow (in case of big number of files ) | Very fast block level | Very fast | Very fast (with ability to send via network) |
| Backup consistency | Inconsistent | Consistent with snapshots | Consistent with snapshots | Consistent with checksummed snapshots |
| Snapshots | No | Yes | Yes | Yes |
| Deduplication | No | No | No | Yes |
| Compression | No | No | No | Yes |
| Effective cache | Normal, Linux page cache | Normal, Linux page cache | Normal, Linux page cache | Excellent, L2ARC cache |
| Live migration | Very slow and impossible sometimes (inode change can broke some apps) | Very Fast | Not applicable | Very fast (theoretically) |
| Second level quotes in Linux (inside container) | Yes | Yes | Yes | No |
| vzctl integration | Complete | Complete | No integration, big amount of manual operations | No integration, small amount of manual operations |
