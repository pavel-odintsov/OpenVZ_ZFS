Table with storage backends comparison

| Quality | OpenVZ simfs over ext4         | OpenVZ ploop       | LVM volumes with ext4         | ZFS |
| --------| -------------  |-------------| ------------| -------|
| Maturity| 10+ years      | Since 2012  | Since 1998  | Since 2014 (Linux), Since 2005 (Solaris)        |
| Speed   | Very fast      | Fast        | Fast        | Fast enough |
| I/O isolation | Very bad (shared filesystem journal) | Excellent   | Excellent   | Excellent |
| Wasted space amount      | Very small (ext4 block size) | Big enoungh (ploop uses 1MB block) | Small (ext4 block size) | Extremely small (veriable block size) | 
| Ability to overcommit disk space (provide more space for containers then abilible on server now) | Yes | Yes | No | Yes |
| Reliability | Very small, big amount of files produce ext4 corruption so often | Small, fsck, power loss and HW Raid without cache can kill whole data | Good enough (but LVM metadat can be corrupted completelt) | Excellent (no write hole, cheksumming and COW) | 
| Backup speed | Very slow (in case of big number of files ) | Very fast block level | Very fast | Very fast (with abilit to send via network) |
| Backup consistency | Inconsistent | Cosistent with snapshots | Cosistent with snapshots | Consistens with cheksummed snapshots |
| Snapshots | No | Yes | Yes | Yes |
| Deduplication | No | No | No | Yes |
| Compression | No | No | No | Yes |
| Effective cache | Normal, Linux page cache | Normal, Linux page cache | Normal, Linux page cache | Excellet, L2ARC cache |
| Live migration | Very slow and impossible sometimes | Very Fast | Not applicable | Very fast (theoretically) |
| Second level quotes in Linux (inside container) | Yes | Yes | Yes | No |
