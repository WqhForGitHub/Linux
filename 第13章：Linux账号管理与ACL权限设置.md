# 13.1 Linux 的账号与用户组



## 1. 用户标识符：UID 与 GID

 账号只是为了让人们容易记忆而已，而你的 ID 与账号的对应就在 /etc/passwd 当中。每个登录的用户至少都会获取两个 ID，一个是用户 ID（User ID，简称 UID），一个是用户组 ID（Group ID，简称 GID）。





## 2. 用户账号



### /etc/passwd

**`这个文件的构造是这样的：每一行都代表一个账号，有几行就代表有几个账号在你的系统中，不过需要特别留意的是，里面很多账号本来就是系统正常运行所必须的，我们可以简称它为系统账号，例如 bin、daemon、adm、nobody 等，这些账号请不要随意删除。`** 

```powershell
[root@study ~]# head -n 4 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
```

一共有 7 个字段分别是：

1. **`账号名称`** 

   root 的 UID 对应就是 0

   

2. **`密码`** 

   放在 /etc/shadow

   

3. **`UID`** 

   

| ID 范围      | 该 ID 用户特性 |
| ------------ | -------------- |
| 0            |                |
| 1 ~ 999      |                |
| 1000 ~ 60000 |                |



4. **`GID`** 

   这个与 /etc/group 有关，其实 /etc/group 的概念与 /etc/passwd 差不多，只是它是用来规范组名与 GID 的对应而已。

   

5. **`用户信息说明栏`** 

6. **`家目录`** 

   默认的用户家目录在 /home/yourIDname

7. **`shell`** 



### /etc/shadow

```powershell
[root@study ~]# head -n 4 /etc/shadow
root:$6$J6ghH/GTuu4/$jcmjxvuA2Nx3p3cpOYv/ZQsaiSOqsTGPmrWAT5k/HhcbjtddD6.9EpyJQ8y7FO3AIskuBt9S.EJb/VwncPE4Q1:19987:0:99999:7:::
bin:*:17110:0:99999:7:::
daemon:*:17110:0:99999:7:::
adm:*:17110:0:99999:7:::
```

一共有 9 个字段分别是：

1. **`账号名称`** 

2. **`密码`** 

3. **`最近修改密码的日期`** 

   修改密码的那一天

4. **`密码不可被修改的天数`** 

   这个字段记录了这个账号的密码在最近一次被更改后需要经过几天才可以再被修改。如果是 0 的话，表示密码随时可以修改。如果设置为 20 天的话，那么当你设置了密码之后，20 天之内都无法再修改这个密码

5. **`密码需要重新修改的天数`** 

   上面的 99999（计算为 273 年）的话，那就表示密码的修改没有强制性之意

6. **`密码需要修改期限前的警告天数`** 

7. **`密码过期后的账号宽限时间`** 

   密码过期几天后，如果用户还是没有登录更改密码，那么这个账号的密码将会失效，即该账号再也无法使用该密码登录。要注意密码过期与密码失效并不相同。

8. **`账号失效日期`** 

   这个字段表示这个账号在此字段规定的日期之后，将无法再使用

9. **`保留`** 



## 3. 关于用户组：有效与初始用户组，groups，newgr



### /etc/group

```powershell
[root@study ~]# head -n 4 /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
```

1. **`组名`** 
2. **`用户组密码`** 
3. **`GID`** 
4. **`此用户组支持的账号名称`**  



### /etc/gshadow

**`建立用户组管理员`**

```powershell
[root@study ~]# head -n 4 /etc/group
root:::
bin:::
daemon:::
sys:::
```

1. **`组名`** 
2. **`密码栏，同样的，开头为 ! 表示无合法密码，所以无用户组管理员`** 
3. **`用户组管理员的账号`** 
4. **`有加入该用户组支持的所属账号`**



# 13.2 账号管理



## 1. 新增与删除用户：useradd、相关配置文件、passwd、usermod、userdel



### useradd

```powershell
[root@study ~]# useradd [-u UID] [-g 初始用户组] [-G 次要用户组] [-mM] [-c 说明蓝] [-d 家目录绝对路径] [-s shell] 使用者账号名

[root@study ~]# useradd vbird1
```



### passwd

```powershell
[root@study ~]# passwd [--stdin] [账号名称] 
[root@study ~]# passwd [-l] [-u] [--stdin] [-S] [-n 日数] [-x 日数] [-w 日数] [-i 日期] 账号
选项与参数：
--stdin：可以通过来自前一个管道的数据，作为密码输入，对 shell 脚本有帮助
-l：是 Lock 的意思，会将 /etc/shadow 第二栏最前面加上 ！使密码失效
-u：与 -l 相对，是 Unlock 的意思
-S：列出密码相关参数，即 shadow 文件内的大部分信息
-n：后面接天数，shadow 的第 4 栏位，多久不可修改密码天数
-x：后面接天数，shadow 的第 5 栏位，多久内必须要修改密码
-w：后面接天数，shadow 的第 6 栏位，密码过期前的警告天数
-i：后面接日期，shadow 的第 7 栏位，密码失效日期


请 root 设置 vbird2 密码
[root@study ~]# passwd vbird2


用 vbird2 登录后，修改 vbird2 自己的密码
[vbird2@study ~]# passwd


管理 vbird2 的密码使具有 60 天修改、密码过期 10 天后账号失效的设置
[root@study ~]# passwd -S vbird2
[root@study ~]# passwd -x 60 -i 10 vbird2
```





### chage

```powershell
[root@study ~]# chage [-ldEImMW] 账号名
选项与参数：
-l：列出该账号的详细密码参数
-d：后面接日期，修改 shadow 第三栏位（最近一次修改密码的日期），格式 YYYY-MM-DD
-E：后面接日期，修改 shadow 第八栏位（账号失效日），格式 YYYY-MM-DD
-I：后面接天数，修改 shadow 第七栏位（密码失效日期）
-m：后面接天数，修改 shadow 第四栏位（密码最短保留天数）
-M：后面接天数，修改 shadow 第五栏位（密码多久需要进行修改）
-W：后面接天数，修改 shadow 第六栏位（密码过期前警告日期）
```



### usermod

```powershell
[root@study ~]# usermod [-cdegGlsuLU] username
选项与参数：
-c：后面接账号说明，即 /etc/passwd 第五栏的说明栏，可以加入一些账号的说明
-d：后面接账号的家目录，即修改 /etc/passwd 的第六栏
-e：后面接日期，格式是 YYY-MM-DD 也就是在 /etc/shadow 内的第八个栏位的内容
-f：后面接天数，为 shadow 的第七栏位
-g：后面接初始用户组，修改 /etc/passwd 的第四个栏位，亦即是 GID 的栏位
-G：后面接次要用户组，修改这个使用者能够支持的用户组，修改的是 /etc/group
-a：与 -G 合用，可增加次要用户组的支持
-l：后面接账号名称，亦即是修改账号名称，/etc/passwd 的第一栏
-s：后面接 shell 的实际文件，例如 /bin/bash 或 /bin/csh
-u：后面接 UID 数字，即 /etc/passwd 第三栏的数据
-L：暂时将使用者的密码冻结，让它无法登录，其实仅修改 /etc/shadow 的密码栏
-U：将 /etc/shadow 密码栏的感叹号（！）拿掉，解锁
```



### userdel

```powershell
[root@study ~]# userdel [-r] username
选项与参数：
-r：连同使用者的家目录也一起删除


删除 vbird2，连同家目录一起删除
[root@study ~]# userdel -r vbird2
```





## 3. 新增与删除用户组



### groupadd

```powershell
[root@study ~]# groupadd [-g gid] [-r] 用户组名称
选项与参数：
-g：后面接某个特定的 GID，用来直接设置某个 GID
-r：建立系统用户组


新建一个用户组，名称为 group1
[root@study ~]# groupadd group1
```



### groupmod

```powershell
[root@study ~]# groupmod [-g gid] [-n group_name] 用户组名
选项与参数：
-g：修改既有的 GID 数字
-n：修改既有的用户组名称


将刚刚上个命令建立的 group1 名称改为 mygroup，GID 为 201
[root@study ~]# groupmod -g 201 -n mygroup group1
```



### groupdel

```powershell
[root@study ~]# groupdel [groupname]


将刚刚的 mygroup 删除
[root@study ~]# groupdel mygroup
```





​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           

​	



​                                                                                                                                                    

​              