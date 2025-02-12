# 6.1 目录与路径



## 2. 目录的相关操作

```powershell
.  代表此层目录
.. 代表上一层目录
-  代表前一个工作目录
~  代表目前使用者身份所在的家目录
~account 代表 account 这个使用者的家目录 (account 是个账号名称)
```



* cd：切换目录
* pwd：显示当前目录
* mkdir：建立一个新目录
* rmdir：删除一个空目录



## cd (change directory，切换目录)

```powershell
[root@study ~]# cd ~dmtsai
代表进入 dmtsai 这个使用者 的家目录，亦即 /home/dmtsai。

[root@study dmtsai]# cd ~
表示回到自己的家目录，亦即是 /root 这个目录。

[root@study ~]# cd 
没有加上任何路径，也还是代表回到自己家目录的意思。

[root@study ~]# cd ..
表示去到目前的上层目录，亦即是 /root 的上层目录的意思。

[root@study /]# cd -
表示回到刚刚的那个目录，也就是 /root。

[root@study ~]# cd /var/spool/mail
这个就是绝对路径的写法。直接指定要去的完整路径名称。

[root@study mail]# cd ../postfix
这个是相对路径的写法，我们由 /var/spool/mail 到 /var/spool/postfix 就这样写。
```





## pwd（显示目前所在的目录）

```powershell
[root@study ~]# pwd [-P]
选项与参数：
-P: 显示出真正的路径，而非使用链接路径

单纯显示出目前的工作目录
[root@study ~]# pwd
/root
```



## mkdir （建立新目录）

```powershell
[root@study ~]# mkdir [-mp] 目录名称
选项与参数：
-m：设置文件的权限。直接设置，不使用默认权限（umask）
-p：帮助你直接将所需要的目录（包括上层目录）递归创建

[root@study ~]# cd /tmp
[root@study tmp]# mkdir test
[root@study tmp]# mkdir -p test1/test2/test/test4 

设置权限为 rwx--x--x 的目录
[root@study tmp]# mkdir -m 711 test2
```



## rmdir（删除空的目录）

```powershell
[root@study ~]# rmdir [-p] 目录名称
选项与参数：
-p：连同上层空的目录也一起删除

[root@study ~]# cd /tmp 
[root@study tmp]# rmdir -p test1/test2/test3/test4
```



## 关于执行文件路径的变量：$PATH

```powershell
[root@study ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```





# 6.2 文件与目录管理



## 1. 文件与目录的查看：ls

```powershell
[root@study ~]# ls [-aADfFhilmrRSt] 文件名或目录名称
[root@study ~]# ls [--color={never,auto,always}] 文件名或目录名称
[root@study ~]# ls [--full-time] 文件名或目录名称

选项与参数：
-a：全部的文件，连同隐藏文件（开头为 . 的文件）一起列出来
-d：仅列出目录本身，而不是列出目录内的文件数据
-l：详细信息展示，包含文件的属性与权限等数据                                    
```



```powershell
[root@study ~]# ls -al ~
total 84
dr-xr-x---. 10 root root  4096 Feb  6 17:31 .
dr-xr-xr-x. 19 root root  4096 Feb  6 17:40 ..
drwxr-xr-x   5 root root  4096 Feb  9 11:22 app
-rw-------   1 root root 12636 Feb 10 17:26 .bash_history
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

不显示颜色，但显示出该文件名代表的类型（type）
[root@study ~]# ls -alF --color=never ~
total 84
dr-xr-x---. 10 root root  4096 Feb  6 17:31 ./
dr-xr-xr-x. 19 root root  4096 Feb  6 17:40 ../
drwxr-xr-x   5 root root  4096 Feb  9 11:22 app/
-rw-------   1 root root 12636 Feb 10 17:26 .bash_history
-rw-r--r--.  1 root root    18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root   176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root   176 Dec 29  2013 .bashrc
drwx------   3 root root  4096 Aug 18  2017 .cache/
-rw-r--r--.  1 root root   100 Dec 29  2013 .cshrc
drwx------   3 root root  4096 Sep 22 12:28 .docker/
drwxr-xr-x   3 root root  4096 Sep 22 12:40 mongo/
drwxr-xr-x   2 root root  4096 Aug 18  2017 .pip/
drwxr-----   3 root root  4096 Sep 21 18:17 .pki/
-rw-r--r--   1 root root    64 Aug 18  2017 .pydistutils.cfg
drwxr-xr-x   3 root root  4096 Feb  6 17:37 redis/
drwx------   2 root root  4096 Sep 21 16:56 .ssh/
-rw-r--r--.  1 root root   129 Dec 29  2013 .tcshrc
-rw-------   1 root root   616 Oct  8 22:36 .viminfo

完整的显示文件的修改时间
[root@study ~]# ls -al --full-time ~
total 84
dr-xr-x---. 10 root root  4096 2025-02-06 17:31:25.317247346 +0800 .
dr-xr-xr-x. 19 root root  4096 2025-02-06 17:40:12.626039955 +0800 ..
drwxr-xr-x   5 root root  4096 2025-02-09 11:22:07.040449602 +0800 app
-rw-------   1 root root 12636 2025-02-10 17:26:34.029249000 +0800 .bash_history
-rw-r--r--.  1 root root    18 2013-12-29 10:26:31.000000000 +0800 .bash_logout
-rw-r--r--.  1 root root   176 2013-12-29 10:26:31.000000000 +0800 .bash_profile
-rw-r--r--.  1 root root   176 2013-12-29 10:26:31.000000000 +0800 .bashrc
drwx------   3 root root  4096 2017-08-18 11:59:43.439890649 +0800 .cache
-rw-r--r--.  1 root root   100 2013-12-29 10:26:31.000000000 +0800 .cshrc
drwx------   3 root root  4096 2024-09-22 12:28:45.976792353 +0800 .docker
drwxr-xr-x   3 root root  4096 2024-09-22 12:40:59.365055714 +0800 mongo
drwxr-xr-x   2 root root  4096 2017-08-18 12:00:32.471660122 +0800 .pip
drwxr-----   3 root root  4096 2024-09-21 18:17:03.511918487 +0800 .pki
-rw-r--r--   1 root root    64 2017-08-18 12:00:32.469660300 +0800 .pydistutils.cfg
drwxr-xr-x   3 root root  4096 2025-02-06 17:37:38.061185369 +0800 redis
drwx------   2 root root  4096 2024-09-21 16:56:25.663037854 +0800 .ssh
-rw-r--r--.  1 root root   129 2013-12-29 10:26:31.000000000 +0800 .tcshrc
-rw-------   1 root root   616 2024-10-08 22:36:05.838667574 +0800 .viminfo
```



## 2. 复制、删除与移动：cp、rm、mv                                                                          



### cp（复制文件或目录）

```powershell
[root@study ~]# cp [-adfilprsu] 源文件（source）目标文件（destination）
[root@study ~]# cp [options] source1 source2 source3 ... directory
选项与参数：
-a：相当于 -dr --preserve=all 的意思
-i：若目标文件（destination）已经存在时，在覆盖时会先询问操作的进行
-p：连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性
-r：递归复制，用于目录的复制操作

如果源文件有两个以上，则最后一个目标文件一定要是目录才行
```



```powershell
用 root 身份，将家目录下的 .bashrc 复制到 /tmp 下，并更名为 bashrc
[root@study ~]# cp ~/.bashrc /tmp/bashrc
[root@study ~]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite `/tmp/bashrc`? n  n 为不覆盖，y 为覆盖

切换目录到 /tmp，并将 /var/log/wtmp 复制到 /tmp 且观察属性
[root@study ~]# cd /tmp
[root@study tmp]# cp /var/log/wtmp .  

如果你想要将文件的所有特性都一起复制过来该怎么办？可以加上 -a，如下所示：
[root@study tmp]# cp -a /var/log/wtmp wtmp_2


-r 可以复制目录，但是，文件与目录的权限可能会被改变
[root@study tmp]# cp -r /etc/ /tmp
```



### rm（删除文件或目录）

```powershell
[root@study ~]# rm [-fir] 文件或目录
选项与参数：
-f：就是 force 的意思，忽略不存在的文件，不会出现警告信息
-i：交互模式，在删除前会询问使用者是否操作
-r：递归删除，最常用于目录的删除，这是非常危险的选项

如果加上 -i 的选项就会主动询问，避免你删除到错误的文件名
[root@study ~]# cd /tmp
[root@study tmp]# rm -i bashrc
rm：remove regular file `bashrc`? y
```



### mv（移动文件与目录，或重命名）

```powershell
[root@study ~]# mv [-fiu] source destination
[root@study ~]# mv [options] source1 source2 source3 ... directory
选项与参数：
-f：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖
-i：若目标文件（destination）已经存在时，就会询问是否覆盖
-u：若目标文件已经存在时，且 source 比较新，才会更新（update）

复制一文件，建立一目录，将文件移动到目录中
[root@study ~]# cd /tmp
[root@study tmp]# cp ~/.bashrc bashrc
[root@study tmp]# mkdir mvtest
[root@study tmp]# mv bashrc mvtest

将刚刚的目录名称更名为 mvtest2
[root@study tmp]# mv mvtest mvtest2

再建立两个文件，再全部移动到 /tmp/mvtest2 当中
[root@study tmp]# cp ~/.bashrc bashrc1
[root@study tmp]# cp ~/.bashrc bashrc2
[root@study tmp]# mv bashrc1 bashrc2 mvtest2
```





## 3. 获取路径的文件名与目录名称

```powershell
取得最后的文件名
[root@study ~]# basename /etc/sysconfig/network
network

取得目录名
[root@study ~]# dirname /etc/sysconfig/network
/etc/sysconfig
```





# 6.3 文件内容查看 



## 1. 直接查看文件内容



### cat

```powershell
[root@study ~]# cat [-AbEnTv]
选项与参数：
-n：打印出行号，连同空白行也会有行号

[root@study ~]# cat -n /etc/issue 
```



### tac（反向列示）

```powershell
[root@study ~]# tac /etc/issue
```



## 2. 可翻页查看



### more（一页一页翻动）

```powershell
[root@study ~]# more /etc/man_db.conf
```

在 more 这个程序的运行过程中，你有几个按键可以使用：

* **`空格键（space）：代表向下翻一页`** 
* **`Enter：代表向下翻一行`** 
* **`/字符串：代表在这个显示的内容当中，向下查找字符串这个关键词`** 
* **`q：代表立刻离开 more，不再显示该文件内容`** 
* **`b：代表往回翻页`**  



### less（一页一页翻动）

```powershell
[root@study ~]# less /etc/man_db.conf
```

可以输入的命令有：

* **`空格键：向下翻动一页`**
* **`[pagedown]：向下翻动一页`**
* **`[pageup]：向上翻动一页`** 
* **`/字符串：向下查找字符串的功能`** 
* **`?字符串：向上查找字符串的功能`** 
* **`n：重复前一个查找`** 
* **`N：反向的重复前一个查找`** 
* **`g：前进到这个数据的第一行`** 
* **`G：前进到这个数据的最后一行去`** 
* **`q：离开 less 这个程序`** 



## 5. 修改文件时间或创建新文件：touch

```powershell
[root@study ~]# touch [-acdmt] 文件
选项与参数：
-a：仅自定义 access time
-c：仅修改文件的时间，若该文件不存在则不建立新文件
```





# 6.4 文件与目录的默认权限与隐藏权限



## 1. 文件默认权限：umask

基本上，umask 就是指定目前用户在建立文件或目录时候的权限默认值，那么如何得知或设置 umask？它的指定条件以下面的方式来指定：

```powershell
[root@study ~]# umask
0022  一般与权限有关的的后面三个数字

[root@study ~]# umask -S
u=rwx,g=rx,o=rx
```

   

1. 若用户建立为文件则默认没有可执行（x）权限，即只有 rw 这两个项目，也就是最大为 **`666`** ，默认权限如下：

```powershell
-rw-rw-rw-
```

2. 若用户建立为目录，则由于 x 与是否可以进入此目录有关，因为默认为所有权限均开放，即 **`777`** ，默认权限如下：

```powershell
drwxrwxrwx
```



要注意的是，umask 的数字指的是 **`该默认值需要减掉的权限`** 。如果以上面的例子来说明的话，因为 umask 为 022，所以 user 并没有被拿掉任何权限，不过 group 与 others 的权限被拿掉了 2（也就是 w 这个权限），那么当用户：

* **`建立文件时：（-rw-rw-rw-）- （-----w--w-）= -rw-r--r--`** 
* **`建立目录时：（drwxrwxrwx）-（d----w--w-）= drwxr-xr-x`**  

