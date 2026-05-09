# 5.2 Linux 文件权限概念
## 5.2.1 Linux 文件属性

嗯，既然要让你了解 Linux 的文件属性，那么有个重要的也是常用的命令就必须要先跟你说，哪一个呢？就是【ls】这一个查看文件的命令。在你以 dmtsai 登录系统，然后使用 su - 切换身份成为 root 后，执行【ls -al】看看，会看到下面的几个东西：
```shell
[root@study ~]# ls -al
total 72                                                                           
drwxrwxrwx 10 ubuntu ubuntu 4096 May  8 12:24 .                                    
drwxrwxrwx  4 root   root   4096 May  5 11:14 ..                                   
drwxrwxrwx  6 root   root   4096 May  6 11:52 app                                  
drwxrwxrwx  6 root   root   4096 May  7 08:00 backups                              
-rw-r--r--  1 ubuntu ubuntu  100 May  8 12:22 .bash_history                        
-rw-r--r--  1 ubuntu ubuntu  220 Mar 31  2024 .bash_logout                         
-rw-r--r--  1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc                              
drwx------  2 ubuntu ubuntu 4096 May  5 11:22 .cache                               
-rw-r--r--  1 root   root   1784 May  6 14:06 docker-compose.yml                   
-rw-r--r--  1 ubuntu ubuntu   44 May  5 11:14 .npmrc                               
drwxr-xr-x  2 ubuntu ubuntu 4096 May  5 11:14 .pip                                
drwxrwxrwx  4 root   root   4096 May  6 12:51 postgresql                           
-rw-r--r--  1 ubuntu ubuntu  807 Mar 31  2024 .profile                             
-rw-r--r--  1 ubuntu ubuntu   73 May  5 11:14 .pydistutils.cfg                     
drwxrwxrwx  4 root   root   4096 May  5 16:35 redis                                
drwx------  2 ubuntu ubuntu 4096 May  5 11:14 .ssh                                 
drwxrwxrwx  6 root   root   4096 May  6 10:52 ssl                                  
-rw-r--r--  1 ubuntu ubuntu    0 May  5 11:23 .sudo_as_admin_successful            
-rw-------  1 ubuntu ubuntu   60 May  8 12:24 .Xauthority
[    1   ] [ 2 ][ 3 ]  [ 4 ]  [ 5 ][    6    ][    7    ]
[   权限   ] [链接][拥有者]  [用户组]  [文件容量][修改日期][文件名]
```
>由于本章后续的 chgrp、chown 等命令可能都需要使用 root 的身份才能够处理，所以这里建议您以 root 的身份来学习。要注意的是，我们还是不建议你直接使用 root 登录系统，建议使用 su - 这个命令来切换身份，离开 su - 则使用 exit 回到 dmtsai 的身份即可。

ls 是 list 的意思，重点在显示文件的文件名与相关属性，而选项【-al】则表示列出所有的文件详细的权限与属性（包含隐藏文件，就是文件名第一个字符为【.】的文件）。如上所示，在你第一次以 root 身份登录 Linux 时，如果你输入上述命令后，应该有上列的几个东西，先解释一下上面七个字段每个的意思。
![文件属性的示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC5%E7%AB%A0%EF%BC%9ALinux%20%E7%9A%84%E6%96%87%E4%BB%B6%E6%9D%83%E9%99%90%E4%B8%8E%E7%9B%AE%E5%BD%95%E9%85%8D%E7%BD%AE/%E6%96%87%E4%BB%B6%E5%B1%9E%E6%80%A7%E7%9A%84%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

### ◆ 第一栏代表这个文件的类型与权限（permission）

这个地方最需要注意了，仔细看的话，你应该可以发现这一栏其实共有十个字符：
- 第一个字符代表这个文件是目录、文件或链接文件等：
- 当为 [ d ]则是目录，例如上表文件名为【.config】的那一行
- 当为 [ - ] 则是文件，例如上表文件名为【initial-setup-ks.cfg】那一行
- 若是 [ l ] 则表示为链接文件（link file）
- 若是 [ b ]则表示为设备文件里面的可供存储的周边设备（可按块随机读写的设备）
- 若是 [ c ]则表示为设备文件里面的串行端口设备，例如键盘、鼠标（一次性读取设备）
- 接下来的字符中，以三个为一组，且均为【 rwx 】的三个参数的组合。其中，[ r ]代表可读（read）、[ w ]代表可写（write）、[ x ]代表可执行（execute）。要注意的是这三个权限的位置不会改变，如果没有权限，就会出现减号[ - ]而已。
- 第一组为**文件拥有者可具备的权限**，以【initial-setup-ks.cfg】这个文件为例，该文件的拥有者可以读写，但不可执行
- 第二组为**加入此用户组之账号的权限**
- 第三组为**非本人且没有加入本用户组的其他账号的权限**
>请你特别注意，不论是那一组权限，基本上，都是针对某些账号来设计的权限。以用户组来说，它规范的是加入这个用户组的账号具有什么样的权限之意，以学校社团为例，假设学校有个电脑社的社团办公室，加入电脑社的同学就可以进出社办，主角是学生（账号）而不是电脑社本身，这样可以理解吗？

![文件的类型与权限之内容](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC5%E7%AB%A0%EF%BC%9ALinux%20%E7%9A%84%E6%96%87%E4%BB%B6%E6%9D%83%E9%99%90%E4%B8%8E%E7%9B%AE%E5%BD%95%E9%85%8D%E7%BD%AE/%E6%96%87%E4%BB%B6%E7%9A%84%E7%B1%BB%E5%9E%8B%E4%B8%8E%E6%9D%83%E9%99%90%E4%B9%8B%E5%86%85%E5%AE%B9.png)
### ◆ 第二栏表示有多少文件名链接到此节点（inode）

每个文件都会将它的权限与属性记录到文件系统的 inode 中，不过，我们使用的目录树却是使用文件名来记录，因此每个文件名就会链接到一个 inode，这个属性记录的就是有多少不同的文件名链接到相同的一个 inode 号码。关于 inode 的相关数据我们会在第 7 章谈到文件系统时再加强介绍。

### ◆ 第三栏表示这个文件（或目录）的拥有者账号

### ◆ 第四栏表示这个文件的所属用户组

在 Linux 系统下，你的账号会加入一个或多个的用户组中。举刚刚我们提到的例子，class1、class2、class3 均属于 projecta 这个用户组，假设某个文件所属的用户组为 projecta，且该文件的权限上图所示（-rwxrwx---），则 class1、class2、class3 三人对于该文件都具有可读、可写、可执行的权限（看用户组权限）。但如果是不属于 projecta 的其他账号，对于此文件就不具有任何权限。
### ◆ 第五栏为这个文件的容量大小，默认单位为 Bytes
### ◆ 第六栏为这个文件的创建日期或是最近的修改日期

这一栏的内容分别为日期（月/日）及时间，如果这个文件被修改的时间距离现在太久，那么时间部分会仅显示年份而已，如下所示：
```shell
[root@study ~]# ll /etc/services
-rw-r--r-- 1 root root 12813 Mar 28  2021 /etc/services
```
### ◆ 第七栏为这个文件名
这个字段就是文件名，比较特殊的是：**如果文件名之前多一个【.】，则代表这个文件为隐藏文件**，例如上表中的【.config】那一行，该文件就是隐藏文件，你可以使用【ls】及【ls -a】这两个命令去感受一下什么是隐藏文件。
>对于更详细的 ls 用法，还记得怎么查询吗？对，使用 ls --help 或 man ls 或 info ls 去看看它的基础用法。自我学习是很重要的，因为师傅带进门，修行看个人，自古只有天才学生，没有明星老师，加油吧。

这七个字段的意义是很重要的，务必清楚地知道各个字段代表的意义，尤其是第一个字段的九个权限，那是整个 Linux 文件权限的重点之一。
### ◆ Linux 文件权限的重要性

## 5.2.2 如何修改文件属性与权限

我们现在知道文件权限对于一个系统的安全重要性了，也知道文件的权限对于用户与用户组的相关性，那么如何修改一个文件的属性与权限？有多少文件的权限我们可以修改？其实一个文件的属性与权限有很多。我们先介绍几个常用于用户组、拥有者、各种身份的权限之修改的命令，如下所示：
- chgrp：**修改文件所属用户组**
* chown：**修改文件拥有者**
* chmod：**修改文件的权限，SUID、SGID、SBIT 等的特性**
### ◆ 修改所属用户组，chgrp

```shell
[root@study ~]# chgrp [-R] dirname/filename ...
选项与参数：
-R：进行递归（recursive）修改，亦即连同子目录下的所有文件、目录都更新成为这个用户组之意，常常用在修改某一目录内所有的文件之情况。

范例：
[root@study ~]# chgrp users initial-setup-ks.cfg
```
### ◆ 修改文件拥有者，chown

```shell
[root@study ~]# chown [-R] 账号名称 文件或目录
[root@study ~]# chown [-R] 账号名称:用户组名称 文件或目录
选项与参数：
-R：进行递归（recursive）修改，亦即连同子目录下的所有文件都修改

范例：将 initial-setup-ks.cfg 的拥有者改为 bin 这个账号
[root@study ~]# chown bin initial-setup-ks.cfg

范例：将 initial-setup-ks.cfg 的拥有者与用户组改回为 root
[root@study ~]# chown root:root initial-setup-ks.cfg
```
>事实上，chown 也可以使用【chown user.group file】，亦即在拥有者与用户组间加上小数点【.】也行。不过很多朋友设置账号时，喜欢在账号当中加入小数点（例如 vbird.tsai 这样的账号格式），这就会造成系统的误判，所以我们比较建议使用冒号【:】来隔开拥有者与用户组。此外，chown 也能单纯的修改所属用户组。例如【chown .sshd initial-setup-ks.cfg】就是修改用户组，看到了吗？就是那个小数点的用途。

知道如何修改文件的用户组与拥有者了，那么什么时候要使用 chown 或 chgrp？或许你会觉得奇怪吧？是的，确实有时候需要修改文件的拥有者，最常见的例子就是在复制文件给你之外的其他人时，我们使用最简单的 cp 命令来说明：
```shell
[root@study ~]# cp 源文件 目标文件
```
假设你今天要将 .bashrc 这个文件复制成为 .bashrc_test 文件名，且是要给 bin 这个人，你可以这样做：
```shell
[root@study ~]# cp .bashrc .bashrc_test
[root@study ~]# ls -al .bashrc*
```
由于复制操作（cp）会复制执行者的属性与权限，所以，怎么办？.bashrc_test 还是属于 root 所拥有，如此一来，即使你将文件拿给 bin 这个用户，那它仍然无法修改（看属性/权限就知道了吧），所以你就必须要将这个文件的拥有者与用户组修改一下，知道如何修改了吧？
### ◆ 修改权限，chmod

文件权限的修改使用的是 chmod 这个命令，但是，权限的设置方法有两种，分别可以使用数字或是符号来进行权限的修改，我们就来谈一谈。
#### 数字类型修改文件权限

Linux 文件的基本权限就有 9 个，分别是拥有者（owner）、所属群组（group）、其他人（others）三种身份各有自己的读（read）、写（write）、执行（execute）权限，先复习一下刚刚上面提到的数据：文件的权限字符为：【-rwxrwxrwx】，这九个权限是三个三个一组。其中，我们可以使用数字来代表各个权限，各权限的数字对照表如下：
```shell
r: 4
w: 2
x: 1
```
每种身份（owner、group、others）各自的三个权限（r、w、x）数字是需要累加的，例如当权限为：[-rwxrwx---] 数字则是：
```shell
owner = rwx = 4 + 2 + 1 = 7
group = rwx = 4 + 2 + 1 = 7
others = --- = 0 + 0 + 0 = 0
```
所以等一下我们设置权限时，该文件的权限数字就是 770，修改权限的命令 chmod 的语法是：
```shell
[root@study ~]# chmod [-R] xyz 文件或目录
选项与参数：
xyz：就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加。
-R：进行递归（recursive）修改，亦即连同子目录下的所有文件都会修改。
```
举例来说，如果要将 .bashrc 这个文件所有的权限都设置启用，那么就执行：
```shell
[root@study ~]# ls -al .bashrc
-rw-r--r-- 1 root root 3106 Apr 22  2024 .bashrc

[root@study ~]# chmod 777 .bashrc
[root@study ~]# ls -al .bashrc
-rwxrwxrwx 1 root root 3106 Apr 22  2024 .bashrc
```
那如果要将权限变成【-rwxr-xr--】？那么权限的数字就成为 `[4+2+1][4+0+1][4+0+0]`=754，所以你需要执行【chmod 754 filename】。另外，在实际的系统运行中最常发生的一个问题就是，常常我们以 vim 编辑一个 shell 的脚本文件后，它的权限通常是 -rw-rw-r--，也就是 664，如果要将该文件变成可执行文件，并且不要让其他人修改此一文件的话，那么就需要 -rwxr-xr-x 这样的权限，此时就得要执行：【chmod 755 test.sh】的命令。
另外，如果有些文件你不希望被其他人看到，那么应该将文件的权限设置为：【-rwxr-----】，那就执行【chmod 740 filename】。
#### 符号类型修改文件权限

还有一个修改权限的方法。从之前的介绍中我们可以发现，基本上就九个权限分别是（1）user（2）group（3）others 三种身份，那么我们就可以借由 u、g、o 来代表三种身份的权限。此外，a 则代表 all 亦即全部的身份。那么读写的权限就可以写成 r、w、x，也就是可以使用下面的方式来看：
chmod，u、g、o、a，+（加入）-（移除）=（设置），rwx，文件或目录
来实践一下吧，假如我们要设置一个文件的权限成为【-rwxr-xr-x】时，基本上就是：
- user（u）：具有可读、可写、可执行的权限
- group 与 others（g/o）：具有可读与执行的权限
所以就是：
```shell
[root@study ~]# chmod u=rwx,go=rx .bashrc
#注意，那个 u=rwx,go=rx 是连在一起的，中间并没有任何空格。

[root@study ~]# ls -al .bashrc
-rwxr-xr-x  1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc
```
那么假如是【-rwxr-xr--】这样的权限？可以使用【chmod u=rwx,g=rx,o=r filename】来设置。此外，如果我不知道原先的文件属性，而我只想要增加 .bashrc 这个文件的每个人均可写入的权限，那么我就可以使用：
```shell
[root@study ~]# ls -al .bashrc
-rwxr-xr-x  1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc

[root@study ~]# chmod a+w .bashrc
[root@study ~]# ls -al .bashrc
-rwxrwxrwx  1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc
```
而如果要将权限去掉而不修改其他已存在的权限？例如要拿掉全部人的可执行权限，则：
```shell
[root@study ~]# chmod a-x .bashrc
[root@study ~]# ls -al .bashrc
-rw-rw-rw-  1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc

[root@study ~]# chmod 644 .bashrc
```
知道 +、-、= 的不同点了吗？对，+ 与 - 的状态下，只要是没有指定到的项目，则该权限不会被变动，例如上面的例子中，由于仅以 - 拿掉 x 则其他两个保持当时的值不变。多多操作一下，你就会知道如何修改权限。这在某些情况下面很好用，举例来说，你想要教一个朋友如何让一个程序可以拥有执行的权限，但你又不知道该文件原本的权限是什么，此时，利用【chmod a+x filename】，就可以让该程序拥有执行的权限了，是否很方便呢？
## 5.2.3 目录与文件的权限意义

# 5.3 Linux 目录配置

在了解了每个文件的相关种类与属性，以及了解了如何修改文件属性与权限的相关信息后，再来要了解的就是，为什么每个 Linux 发行版它们的配置文件、执行文件、每个目录内放置的东西，其实都差不多？原来是有一套标准依据，我们下面就来看一看。

### 5.3.1 Linux 目录配置的依据 —— FHS

|  | 可分享（shareable） | 不可分享（unshareable） |
| --- | --- | --- |
| 不变（static） | /usr（软件存放处） | /etc（配置文件） |
| | /opt（第三方辅助软件） | /boot（启动与内核文件） |
| 可变动（variable）| /var/mail（用户邮箱） | /var/run（程序相关） |
| | /var/spool/news（新闻组） | /var/lock（程序相关） |

事实上，FHS 针对目录树架构仅定义出三层目录下面应该放置什么数据而已，分别是下面这三个目录的定义：
- /（root, 根目录）：与启动系统有关
- /usr（unix software resource）：与软件安装/执行有关
- /var（variable）：与系统运行过程有关

为什么要定义出这三层目录？其实是有意义的，每层目录下面应该要放置的目录也都有特定的规定。由于我们尚未介绍完整的 Linux 系统，所以下面的介绍你可能会看不懂。没关系，先有个概念即可，等到你将基础篇全部看完后，就重头将基础篇再看一篇，到时候你就会豁然开朗了。
>这个 root 在 Linux 里面的意义很多很多，多到让人搞不懂那是啥玩意儿。如果以账号的角度来看，所谓的 root 指的是系统管理员的身份，如果以目录的角度来看，所谓的 root 意即指的是根目录，就是 /，要特别留意。

#### ◆ 根目录（/）的意义与内容

根目录是整个系统最重要的一个目录，因为不但所有的目录都是由根目录衍生出来，同时**根目录也与启动、还原、系统修复等操作有关**。由于系统启动时需要特定的启动软件、内核文件、启动所需程序、函数库等文件数据，若系统出现错误时，根目录也必须要包含有能够修复文件系统的程序才行。因为根目录这么重要，所以在 FHS 的要求方面，它希望根目录不要放在非常大的分区内，因为越大的分区你会放入越多的数据，如此一来根目录所在的分区就可能会有较多发生错误的机会。
因此 FHS 标准建议：**根目录（/）所在分区应该越小越好，且应该程序所安装的软件最好不要与根目录放在同一个分区内，保持根目录越小越好。如此不但性能较佳，根目录所在的文件系统也较不容易发生问题**。
有鉴于上述的说明，因此 FHS 定义出根目录（/）下面应该要有下面这些子目录的存在才好，即使没有物理目录，FHS 也希望至少有链接（link）目录存在才好。













