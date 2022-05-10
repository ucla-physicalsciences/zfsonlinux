# zfsonlinux
Documentation &amp; scripts to deploy &amp; maintain ZFS on linux


Max:

ZFS caching is non-trivial business and very much depends on how your storage is used.  The old adage about storage - pick any two from 'performance, capacity, price' - still applies.  At the low end of ZFS systems with sufficient RAM and spinning disks do the job well with async writes.  At the high end you now have all NVMe systems with sync writes and no need for caching.  In between, you have to juggle with sync writes (for safety) and with ZIL SLOG on separate NVMe flash (for performance). 

ashift=12 (has to do with disk sector size, but there all kinds of magic on modern disks/controllers and setting of 12 or 13 is best)

checksum=sha256 (checksum algorithm, others available applicability depends on deduped dataset etc)

compression=lz4 (compression is not expensive on ZFS, other options including 'no' available)

xattr=sa (affects how extended attributes are handled, in the past we had some strange issues around SAMBA and SELinux without this setting, not sure how useful in the newer ZoL envs)

nbmand=on (locking, on systems where we do both NFS and CIFS)

atime=off (disabling access time improves performance, especially with many small files)

Rod:

ZFS on Ubuntu seems to default to setting ashift=0. I suspect this is ok for 99% of use cases. There are a few articles about gotchas with Ashift, but they mostly relate to consumer SSD’s that have weird sector sizes, or that lie to ZFS about their sector size (saying 512b vs 4k for example). I have it set to 13 on a pool of 12x raidz2 10tb Hitachi drives, on a LSI HBA(cant remember which one). This is running the zpool for my LXD instance, and its never given me performance  issues. The main takeaway with this setting is not to set it too small, or there is a nasty write amplification penalty. Zero seems like a very sane default.

 
