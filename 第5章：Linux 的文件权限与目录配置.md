# 5.2 Linux 文件权限概念

## 1. Linux 文件属性

```powershell
[root@study ~]$ ls -al
total 72
dr-xr-x---.  9 root root 4096 Oct 10 18:35 .
dr-xr-xr-x. 18 root root 4096 Sep 21 16:56 ..
drwxr-xr-x   4 root root 4096 Sep 22 14:33 app
-rw-------   1 root root 7695 Nov 11 16:36 .bash_history
-rw-r--r--.  1 root root   18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 29  2013 .bashrc
drwx------   3 root root 4096 Aug 18  2017 .cache
-rw-r--r--.  1 root root  100 Dec 29  2013 .cshrc
drwx------   3 root root 4096 Sep 22 12:28 .docker
drwxr-xr-x   3 root root 4096 Sep 22 12:40 mongo
drwxr-xr-x   2 root root 4096 Aug 18  2017 .pip
drwxr-----   3 root root 4096 Sep 21 18:17 .pki
-rw-r--r--   1 root root   64 Aug 18  2017 .pydistutils.cfg
drwx------   2 root root 4096 Sep 21 16:56 .ssh
-rw-r--r--.  1 root root  129 Dec 29  2013 .tcshrc
-rw-------   1 root root  616 Oct  8 22:36 .viminfo
```



## 2. 如何修改文件属性与权限

### chgrp：修改文件所属用户组



### chown：修改文件拥有者



### chmod：修改文件的权限，SUID、SGID、SBIT 等的特性

```powershell
[root@study ~]# chmod [-R] xyz 文件或目录
选项与参数：
xyz：就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的增加。
-R: 进行递归修改，亦或连同子目录下的所有文件都会修改。
```

举例来说，如果要将 .bashrc 这个文件所有的权限都设置启用，那么就执行：

```powershell
[root@study ~]# ls -al .bashrc
-rw-r--r--. 1 root root 176 Dec 29 2013 .bashrc

[root@study ~]# chmod 777 .bashrc
[root@study ~]# ls -al .bashrc
-rwxrwxrwx. 1 root root 176 Dec 29 2013 .bashrc
```



## 3. 目录与文件的权限意义



### 权限对文件的重要性

* r (read)：可读取此文件的实际内容，如读取文本文件的文字内容等
* w (write)：可以编辑、新增或是修改该文件的内容（但不含删除该文件）
* x（exeute）：该文件具有可以被系统执行的权限



# 5.3 Linux 目录配置

   

