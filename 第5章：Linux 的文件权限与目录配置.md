# 5.2 Linux 文件权限概念



## 1. Linux 文件属性

```powershell
[root@study ~]# ls -al
total 84
dr-xr-x---. 10 root root  4096 Feb  6 17:31 .
dr-xr-xr-x. 19 root root  4096 Feb  6 17:40 ..
drwxr-xr-x   5 root root  4096 Feb  9 11:22 app
-rw-------   1 root root 12717 Feb 10 18:52 .bash_history
-rw-r--r--.  1 root root    18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root   176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root   176 Dec 29  2013 .bashrc
drwx------   3 root root  4096 Aug 18  2017 .cache
-rw-r--r--.  1 root root   100 Dec 29  2013 .cshrc
drwx------   3 root root  4096 Sep 22 12:28 .docker
drwxr-xr-x   3 root root  4096 Sep 22 12:40 mongo
drwxr-xr-x   2 root root  4096 Aug 18  2017 .pip
drwxr-----   3 root root  4096 Sep 21 18:17 .pki
-rw-r--r--   1 root root    64 Aug 18  2017 .pydistutils.cfg
drwxr-xr-x   3 root root  4096 Feb  6 17:37 redis
drwx------   2 root root  4096 Sep 21 16:56 .ssh
-rw-r--r--.  1 root root   129 Dec 29  2013 .tcshrc
-rw-------   1 root root   616 Oct  8 22:36 .viminfo
```





## 2. 如何修改文件属性与权限

* **`chgrp：修改文件所属用户组`**
* **`chown：修改文件拥有者`**
* **`chmod：修改文件的权限，SUID、SGID、SBIT 等的特性`**



### chgrp：修改文件所属用户组

```powershell
  [root@study ~]# chgrp [-R] dirname/filename ..
选项与参数：
-R：进行递归修改，亦即连同于子目录下的所有文件、目录都更新成为这个用户组之意，常常用在修改某一目录内所有的文件之情况


[root@study ~]# chgrp users initial-setup-ks.cfg
```





### chown：修改文件拥有者

```powershell
[root@study ~]# chown [-R] 账号名称 文件或目录
[root@study ~]# chown [-R] 账号名称:用户组名称 文件或目录
选项与参数：
-R：进行递归修改，亦即连同子目录下的所有文件都修改

将 initial-setup-ks.cfg 的拥有者为 bin 这个账号
[root@study ~]# chown bin initial-setup-ks.cfg

将 initial-setup-ks.cfg 的拥有者与用户组改回为 root
[root@study ~]# chown root:root initial-setup-ks.cfg
```





### chmod：修改文件的权限

#### 数字类型修改文件权限

**`Linux 文件的基本权限就有 9 个，分别是拥有者（owner）、所属群组（group）、其他人（others）三种身份各有自己的读（read）、写（write）、执行（execute）权限`** 。

**`r: 4`** 

**`w: 2`** 

**`x: 1`** 

**`每种身份（owner、group、others）各自的三个权限（r、w、x）数字是需要累加的，例如当权限为：[-rwxrwx---] 数字则是`** ：

**`owner = rwx = 4 + 2 + 1 = 7`** 

**`group = rwx = 4 + 2 + 1 = 7`** 

**`others = --- = 0 + 0 + 0 = 0`** 



```powershell
[root@study ~]# chmod [-R] xyz 文件或目录
选项与参数：
xyz：就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加
-R：进行递归修改，亦即连同子目录下的所有文件都会修改

.bashrc 这个文件所有的权限都设置启用，那么就执行：
[root@study ~]# chmod 777 .bashrc
```



