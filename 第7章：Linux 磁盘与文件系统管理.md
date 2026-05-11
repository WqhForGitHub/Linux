# 7.2 文件系统的简单操作

大概了解了文件系统后，再来我们要知道如何查询文件系统的总容量与每个目录所占用的容量。此外，前两章谈到的文件类型中尚未讲得很清楚的链接文件（link file）也会在这一小节当中介绍。

## 7.2.1 磁盘与目录的容量

现在我们知道磁盘的整体数据是在超级区块中，但是每个文件的容量则在 inode 当中加载。那在命令行模式下面该如何显示这几个数据？下面就让我们来谈一谈这两个命令：
- df：列出文件系统的整体磁盘使用量
- du：查看文件系统的磁盘使用量（常用在查看目录所占磁盘空间）

### ◆ df

```shell
[root@study ~]# df [-ahikHTm] [目录或文件名]
选项与参数：
-a：列出所有的文件系统，包括系统特有的 /proc 等文件系统
-k：以 KBytes 的容量显示各文件系统
-m：以 MBytes 的容量显示各文件系统
-h：以人们较易阅读的 GBytes、Mbytes、KBytes 等格式自行显示
-H：以 M=1000K 替换 M=1024K 的进位方式
-T：连同该硬盘分区的文件系统名称（例如 xfs）也列出
-i：不用磁盘容量，而以 inode 的数量来显示

范例一：将系统内所有的文件全列出来
[root@study ~]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
tmpfs             381328     1500    379828   1% /run                              
/dev/vda2       61806672 11089056  48080084  19% /                                 
tmpfs            1906636       24   1906612   1% /dev/shm                          
tmpfs               5120        0      5120   0% /run/lock                         
overlay         61806672 11089056  48080084  19% /var/lib/docker/rootfs/overlayfs/b4805e14720e1aa29c7277cca3ac3a60b15f884fec7188f0f4f827a58ff39ac0                                                                     
overlay         61806672 11089056  48080084  19% /var/lib/docker/rootfs/overlayfs/d33db9cbfa2ba2f40a18c45ac8731becfb1f1cbd4fe873e1cf1e5e8719a723a8                                                                     
overlay         61806672 11089056  48080084  19% /var/lib/docker/rootfs/overlayfs/95ebdada6069b8509d09b8791ec34b43893f16062af421ef3bb0a0603344bd3d                                                                     
overlay         61806672 11089056  48080084  19% /var/lib/docker/rootfs/overlayfs/36582e26bc2af73090e0cec4cb9a242c702a69b24c04c01088b5be68a22ea92d                                                                     
overlay         61806672 11089056  48080084  19% /var/lib/docker/rootfs/overlayfs/9fc867dd1c1e6118ee8d7835be5b75b8dab020d211efc3d9329ff0055df9fae1                                                                     
tmpfs             381324       12    381312   1% /run/user/1000
```

先来说明一下范例一所输出的结果信息为：

- Filesystem：代表该文件系统是在哪个硬盘分区，所以列出设备名称
- 1k-blocks：说明下面的数字单位是 1KB，可利用 -h 或 -m 来改变容量
- Used：顾名思义，就是使用掉的磁盘空间
- Available：也就是剩下的磁盘空间大小
- Use%：就是磁盘的使用率，如果使用率高达 90% 以上，最好需要注意一下，免得容量不足造成系统问题，例如最容易被占满得 /var/spool/mail 这个保存邮件的目录
- Mounted on：就是磁盘的挂载目录。（挂载点）
```shell
范例二：将容量结果以易读的格式显示出来。
[root@study ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on                                   
tmpfs           373M  1.5M  371M   1% /run                                         
/dev/vda2        59G   11G   46G  19% /                                            
tmpfs           1.9G   24K  1.9G   1% /dev/shm                                     
tmpfs           5.0M     0  5.0M   0% /run/lock                                    
overlay          59G   11G   46G  19% /var/lib/docker/rootfs/overlayfs/b4805e14720e1aa29c7277cca3ac3a60b15f884fec7188f0f4f827a58ff39ac0                                                                     
overlay          59G   11G   46G  19% /var/lib/docker/rootfs/overlayfs/d33db9cbfa2ba2f40a18c45ac8731becfb1f1cbd4fe873e1cf1e5e8719a723a8                                                                     
overlay          59G   11G   46G  19% /var/lib/docker/rootfs/overlayfs/95ebdada6069b8509d09b8791ec34b43893f16062af421ef3bb0a0603344bd3d                                                                     
overlay          59G   11G   46G  19% /var/lib/docker/rootfs/overlayfs/36582e26bc2af73090e0cec4cb9a242c702a69b24c04c01088b5be68a22ea92d                                                                     
overlay          59G   11G   46G  19% /var/lib/docker/rootfs/overlayfs/9fc867dd1c1e6118ee8d7835be5b75b8dab020d211efc3d9329ff0055df9fae1                                                                     
tmpfs           373M   12K  373M   1% /run/user/1000
# 不同于范例一，这里会以 G/M 等容量格式显示出来，比较容易看。

范例三：将系统内的所有特殊文件格式及名称都列出来。
[root@study ~]# df -aT
Filesystem     Type        1K-blocks     Used Available Use% Mounted on            
sysfs          sysfs               0        0         0    - /sys                  
proc           proc                0        0         0    - /proc                 
udev           devtmpfs      1865724        0   1865724   0% /dev                  
devpts         devpts              0        0         0    - /dev/pts              
tmpfs          tmpfs          381328     1496    379832   1% /run                  
/dev/vda2      ext4         61806672 11091300  48077840  19% /                     
securityfs     securityfs          0        0         0    - /sys/kernel/security  
tmpfs          tmpfs         1906636       24   1906612   1% /dev/shm              
tmpfs          tmpfs            5120        0      5120   0% /run/lock             
cgroup2        cgroup2             0        0         0    - /sys/fs/cgroup        
pstore         pstore              0        0         0    - /sys/fs/pstore        
bpf            bpf                 0        0         0    - /sys/fs/bpf           
systemd-1      -                   -        -         -    - /proc/sys/fs/binfmt_misc                                                           
hugetlbfs      hugetlbfs           0        0         0    - /dev/hugepages        
mqueue         mqueue              0        0         0    - /dev/mqueue           
debugfs        debugfs             0        0         0    - /sys/kernel/debug     
tracefs        tracefs             0        0         0    - /sys/kernel/tracing   
fusectl        fusectl             0        0         0    - /sys/fs/fuse/connections                                                           
configfs       configfs            0        0         0    - /sys/kernel/config    
binfmt_misc    binfmt_misc         0        0         0    - /proc/sys/fs/binfmt_misc                                                           
tracefs        tracefs             0        0         0    - /sys/kernel/debug/tracing                                                          
overlay        overlay      61806672 11091300  48077840  19% /var/lib/docker/rootfs/overlayfs/b4805e14720e1aa29c7277cca3ac3a60b15f884fec7188f0f4f827a58ff39ac0                                                                     
overlay        overlay      61806672 11091300  48077840  19% /var/lib/docker/rootfs/overlayfs/d33db9cbfa2ba2f40a18c45ac8731becfb1f1cbd4fe873e1cf1e5e8719a723a8                                                                     
nsfs           nsfs                0        0         0    - /run/docker/netns/66b72d7f42f6                                                     
nsfs           nsfs                0        0         0    - /run/docker/netns/c4118a1fa484                                                     
overlay        overlay      61806672 11091300  48077840  19% /var/lib/docker/rootfs/overlayfs/95ebdada6069b8509d09b8791ec34b43893f16062af421ef3bb0a0603344bd3d                                                                     
nsfs           nsfs                0        0         0    - /run/docker/netns/4e0ab01eb0b8                                                     
overlay        overlay      61806672 11091300  48077840  19% /var/lib/docker/rootfs/overlayfs/36582e26bc2af73090e0cec4cb9a242c702a69b24c04c01088b5be68a22ea92d                                                                     
overlay        overlay      61806672 11091300  48077840  19% /var/lib/docker/rootfs/overlayfs/9fc867dd1c1e6118ee8d7835be5b75b8dab020d211efc3d9329ff0055df9fae1                                                                     
nsfs           nsfs                0        0         0    - /run/docker/netns/a2097883c260                                                     
nsfs           nsfs                0        0         0    - /run/docker/netns/0f613162c343                                                     
tmpfs          tmpfs          381324       12    381312   1% /run/user/1000
# 系统里面其实还有很多特殊的文件系统存在，那些比较特殊的文件系统几乎都是在内存当中，
# 例如 /proc 这个挂载点。因此，这些特殊的文件系统都不会占据磁盘空间。

范例四：将 /etc 下面的可用的磁盘容量以易读的容量格式显示。
[root@study ~]# df -h /etc
Filesystem      Size  Used Avail Use% Mounted on                                   
/dev/vda2        59G   11G   46G  19% /
# 这个范例比较有趣一点，在 df 后面加上目录或是文件时，
# df 会自动的分析该目录或文件所在的硬盘分区，并将该硬盘分区的容量显示出来，
# 所以，您就可以知道某个目录下面还有多少容量可以使用了。

范例五：将目前各个硬盘分区可用的 inode 数量列出。
[root@study ~]# df -ih
Filesystem     Inodes IUsed IFree IUse% Mounted on                                 
tmpfs            466K   860  465K    1% /run                                       
/dev/vda2        3.8M  179K  3.6M    5% /                                          
tmpfs            466K     7  466K    1% /dev/shm                                   
tmpfs            466K     3  466K    1% /run/lock                                  
overlay          3.8M  179K  3.6M    5% /var/lib/docker/rootfs/overlayfs/b4805e14720e1aa29c7277cca3ac3a60b15f884fec7188f0f4f827a58ff39ac0                                                                     
overlay          3.8M  179K  3.6M    5% /var/lib/docker/rootfs/overlayfs/d33db9cbfa2ba2f40a18c45ac8731becfb1f1cbd4fe873e1cf1e5e8719a723a8                                                                     
overlay          3.8M  179K  3.6M    5% /var/lib/docker/rootfs/overlayfs/95ebdada6069b8509d09b8791ec34b43893f16062af421ef3bb0a0603344bd3d                                                                     
overlay          3.8M  179K  3.6M    5% /var/lib/docker/rootfs/overlayfs/36582e26bc2af73090e0cec4cb9a242c702a69b24c04c01088b5be68a22ea92d                                                                     
overlay          3.8M  179K  3.6M    5% /var/lib/docker/rootfs/overlayfs/9fc867dd1c1e6118ee8d7835be5b75b8dab020d211efc3d9329ff0055df9fae1                                                                     
tmpfs             94K    32   94K    1% /run/user/1000
# 这个范例则主要列出可用的 inode 剩余量与总容量。分析一下与范例一的关系。
# 你可以清楚地发现到，通常 inode 的剩余数量都比区块还要多。
```

由于 df 主要读取的数据几乎都是针对一整个文件系统，因此读取的范围主要是超级区块内的信息，所以这个命令显示结果的速度非常快。在显示的结果中你需要特别留意的是根目录（/）的剩余容量。因为我们所有的数据都是由根目录衍生出来的，因此当根目录的剩余容量剩下 0 时，你的 Linux 可能就问题很大了。
另外需要注意的是，如果使用 -a 这个参数时，系统出现 /proc 这个挂载点，但是里面的东西都是 0，不要紧张。/proc 的东西都是 Linux 系统所需要加载的系统数据，而且是挂载在内存当中，所以当然没有占任何的磁盘空间。
至于**那个 /dev/shm/ 目录，其实是利用内存虚拟出来的磁盘空间，通常是总物理内存的一半**。由于是通过内存模拟出来的磁盘，因此你在这个目录下面建立任何数据文件时，访问速度是非常快的。（在内存中工作。）不顾哦，也由于它是内存模拟出来的，因此这个文件系统的大小在每台主机上都不一样，而且建立的东西在下次启动时就会消失，因为是内存中嘛。

## ◆ du
```shell
[root@study ~]# du [-ahskm] 文件或目录名称
选项与参数：
-a：列出所有文件与目录容量，因为默认仅统计目录下面的文件量
-h：以人们较易读的容量格式（G/M）显示
-s：仅列出总量，而不列出每个各别的目录占用容量
-S：不包括子目录下的总计，与 -s 有点差别
-k：以 KBytes 列出容量显示
-m：以 MBytes 列出容量显示

范例一：列出目前目录下的所有文件容量。
[root@study ~]# du

范例二：同范例一，但是将文件的容量也列出来
[root@study]# du -a

范例三：检查根目录下面每个目录所占用的容量。
[root@study ~]# du -sm /*
```











