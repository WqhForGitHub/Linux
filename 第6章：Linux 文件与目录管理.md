# 1. 目录与路径

```bash
.  代表此层目录
.. 代表上一层目录
-  代表前一个工作目录
~  代表目前使用者身份所在的家目录
~account 代表 account 这个使用者的家目录 (account 是个账号名称)
```



下面我们就来谈一谈几个常见的处理目录的命令：

* cd：切换目录
* pwd：显示当前目录
* mkdir：建立一个新目录
* rmdir：删除一个空目录



## cd (change directory，切换目录)

```bash
[root@study ~]# cd ~dmtsai
# 代表进入 dmtsai 这个使用者的家目录，亦即 /home/dmtsai。

[root@study dmtsai]# cd ~
# 表示回到自己的家目录，亦即是 /root 这个目录。

[root@study ~]# cd 
# 没有加上任何路径，也还是代表回到自己家目录的意思。

[root@study ~]# cd ..
# 表示去到目前的上层目录，亦即是 /root 的上层目录的意思。

[root@study /]# cd -
# 表示回到刚刚的那个目录，也就是 /root。

[root@study ~]# cd /var/spool/mail
# 这个就是绝对路径的写法。直接指定要去的完整路径名称。

[root@study mail]# cd ../postfix
# 这个是相对路径的写法，我们由 /var/spool/mail 到 /var/spool/postfix 就这样写。
```



