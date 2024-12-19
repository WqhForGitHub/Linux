<h2 id="PWCA9">2 Linux 文件权限概念</h2>
<h3 id="xp5nf">Linux 文件属性</h3>
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



<h3 id="Ujujy">如何修改文件属性与权限</h3>
chgrp：修改文件所属用户组

chown：修改文件拥有者

chmod：修改文件的权限，SUID、SGID、SBIT 等的特性





---

<h2 id="kSzjh">3 Linux 目录配置</h2>


