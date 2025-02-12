# 7.2 文件系统的简单操作



* **`df：列出文件系统的整体磁盘使用量`**
* **`du：查看文件系统的磁盘使用量（常用在查看目录所占磁盘空间）`**



## df

```powershell
[root@study ~]# df [-ahikHTm] [目录或文件名]
选项与参数：
-h：以人们较易阅读的 GBytes、Mbytes、KBytes 等格式自行显示
-i：不用磁盘容量，而以 inode 的数量来显示


将系统内所有的文件系全列出来
[root@study ~]# df
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/vda1       41151808 6154184  32884192  16% /
devtmpfs          890852       0    890852   0% /dev
tmpfs             900224       0    900224   0% /dev/shm
tmpfs             900224     520    899704   1% /run
tmpfs             900224       0    900224   0% /sys/fs/cgroup
overlay         41151808 6154184  32884192  16% /var/lib/docker/overlay2/55fc4a726cf7f75fc0610e24da76e3a9ee218d0ad444f0fb01142a3f4c181b50/merged
overlay         41151808 6154184  32884192  16% /var/lib/docker/overlay2/bd9f55d3f8f09efa33f4496bbd431a848b4a7487001496c5d566d7b2878d2f56/merged
overlay         41151808 6154184  32884192  16% /var/lib/docker/overlay2/5a2c60118db3e4db089421ffa9a9771c62ecc8fa99a358060b550255f9c2cfdd/merged
tmpfs             180048       0    180048   0% /run/user/0


将容量结果以易读的格式显示出来
[root@study ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G  5.9G   32G  16% /
devtmpfs        870M     0  870M   0% /dev
tmpfs           880M     0  880M   0% /dev/shm
tmpfs           880M  520K  879M   1% /run
tmpfs           880M     0  880M   0% /sys/fs/cgroup
overlay          40G  5.9G   32G  16% /var/lib/docker/overlay2/55fc4a726cf7f75fc0610e24da76e3a9ee218d0ad444f0fb01142a3f4c181b50/merged
overlay          40G  5.9G   32G  16% /var/lib/docker/overlay2/bd9f55d3f8f09efa33f4496bbd431a848b4a7487001496c5d566d7b2878d2f56/merged
overlay          40G  5.9G   32G  16% /var/lib/docker/overlay2/5a2c60118db3e4db089421ffa9a9771c62ecc8fa99a358060b550255f9c2cfdd/merged
tmpfs           176M     0  176M   0% /run/user/0

将系统内的所有特殊文件格式及名称都列出来
[root@study ~]# df -aT
Filesystem     Type       1K-blocks    Used Available Use% Mounted on
rootfs         -                  -       -         -    - /
sysfs          sysfs              0       0         0    - /sys
proc           proc               0       0         0    - /proc
devtmpfs       devtmpfs      890852       0    890852   0% /dev
securityfs     securityfs         0       0         0    - /sys/kernel/security
tmpfs          tmpfs         900224       0    900224   0% /dev/shm
devpts         devpts             0       0         0    - /dev/pts
tmpfs          tmpfs         900224     520    899704   1% /run
tmpfs          tmpfs         900224       0    900224   0% /sys/fs/cgroup
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/systemd
pstore         pstore             0       0         0    - /sys/fs/pstore
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/cpuset
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/cpu,cpuacct
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/hugetlb
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/pids
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/net_cls,net_prio
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/memory
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/freezer
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/perf_event
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/blkio
cgroup         cgroup             0       0         0    - /sys/fs/cgroup/devices
configfs       configfs           0       0         0    - /sys/kernel/config
/dev/vda1      ext4        41151808 6154228  32884148  16% /
systemd-1      autofs             0       0         0    - /proc/sys/fs/binfmt_misc
hugetlbfs      hugetlbfs          0       0         0    - /dev/hugepages
debugfs        debugfs            0       0         0    - /sys/kernel/debug
mqueue         mqueue             0       0         0    - /dev/mqueue
overlay        overlay     41151808 6154228  32884148  16% /var/lib/docker/overlay2/55fc4a726cf7f75fc0610e24da76e3a9ee218d0ad444f0fb01142a3f4c181b50/merged
overlay        overlay     41151808 6154228  32884148  16% /var/lib/docker/overlay2/bd9f55d3f8f09efa33f4496bbd431a848b4a7487001496c5d566d7b2878d2f56/merged
overlay        overlay     41151808 6154228  32884148  16% /var/lib/docker/overlay2/5a2c60118db3e4db089421ffa9a9771c62ecc8fa99a358060b550255f9c2cfdd/merged
tmpfs          tmpfs         180048       0    180048   0% /run/user/0


将 /etc 下面的可用的磁盘容量以易读的容量格式显示
[root@study ~]# df -h /etc
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G  5.9G   32G  16% /


将目前各个硬盘分区可用的 inode 数量列出
[root@study ~]# df -ih
Filesystem     Inodes IUsed IFree IUse% Mounted on
/dev/vda1        2.5M  181K  2.4M    8% /
devtmpfs         218K   332  218K    1% /dev
tmpfs            220K     2  220K    1% /dev/shm
tmpfs            220K   431  220K    1% /run
tmpfs            220K    16  220K    1% /sys/fs/cgroup
overlay          2.5M  181K  2.4M    8% /var/lib/docker/overlay2/55fc4a726cf7f75fc0610e24da76e3a9ee218d0ad444f0fb01142a3f4c181b50/merged
overlay          2.5M  181K  2.4M    8% /var/lib/docker/overlay2/bd9f55d3f8f09efa33f4496bbd431a848b4a7487001496c5d566d7b2878d2f56/merged
overlay          2.5M  181K  2.4M    8% /var/lib/docker/overlay2/5a2c60118db3e4db089421ffa9a9771c62ecc8fa99a358060b550255f9c2cfdd/merged
tmpfs            220K     1  220K    1% /run/user/0
```

先来说明一下范例一所输出的结果信息为：

* **`Filesystem：代表该文件系统是在哪个硬盘分区，所以列出设备名称`**
* **`1k-blocks：说明下面的数字单位是 1KB，可利用 -h 或 -m 来改变容量`** 
* **`Used：顾名思义，就是使用掉的磁盘空间`** 
* **`Available：也就是剩下的磁盘空间大小`** 
* **`Use%：就是磁盘的使用率`** 
* **`Mounted on：就是磁盘的挂载目录`** 



## du

```powershell
[root@study ~]# du [-ahskm] 文件或目录名称
选项与参数：
-s：仅列出总量，而不列出每个各别的目录占用容量
```











