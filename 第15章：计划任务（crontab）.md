# 15.1 什么是计划任务
## 15.1.1 Linux 计划任务的种类：at、cron

从上面的说明当中，我们可以很清楚地发现两种计划任务的方式。
◆ 一种是例行性的，就是每隔一定的周期要来办的事项。
◆ 一种是突发性的，就是这次做完以后就没有的那一种。
	那么在 Linux 下面如何实现这两个功能？那就得使用 at 与 crontab 这两个好东西。
 ◆ at：at 是个可以处理仅执行一次就结束的命令，不过要执行 at 时，必须要有 atd 这个服务（第 17 章）的支持才行。在某些新版的 Linux 发行版中，atd 可能默认并没有启动，那么 at 这个命令就会失效，不过我们的 CentOS 默认是启动的。
 ◆ crontab：crontab 这个命令所设置的任务将会循环地一直执行下去，可循环的时间为分钟、小时、每周、每月或每年等。crontab 除了可以使用命令执行外，亦可编辑 /etc/crontab 来支持，至于让 crontab 可以生效的服务则是 crond。
 下面我们先来谈一谈 Linux 的系统到底在做什么事情，怎么有若干计划任务在执行呢？然后再回来谈一谈 at 与 crontab 这两个好东西。

# 15.2 仅执行一次的计划任务

首先，我们先来谈谈单一计划任务的运行，那就是 at 这个命令的运行。

## 15.2.1 atd 的启动与 at 运行的方式

要使用单一计划任务时，我们的 Linux 系统上面必须要有负责这类计划的服务，那就是 atd 这个服务。不过并非所有的 Linux 发行版都默认启动，所以，某些时刻我们必须要手动将它启动才行。启动的方法很简单，就是这样：
```shell
[root@study ~]# systemctl restart atd # 重新启动 atd 这个服务。
[root@study ~]# systemctl enable atd # 让这个服务开机就自动启动。
[root@study ~]# systemctl status atd # 查看一下 atd 目前的状态
● atd.service - Deferred execution scheduler                                       
     Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; preset: enabled)                                                                           
     Active: active (running) since Fri 2026-05-15 21:51:18 CST; 43s ago           
       Docs: man:atd(8)                                                            
   Main PID: 110200 (atd)                                                          
      Tasks: 1 (limit: 4296)                                                       
     Memory: 256.0K (peak: 1.5M)                                                   
        CPU: 7ms                                                                   
     CGroup: /system.slice/atd.service                                             
             └─110200 /usr/sbin/atd -f                                             
May 15 21:51:18 VM-0-4-ubuntu systemd[1]: Starting atd.service - Deferred execution scheduler...                                                             
May 15 21:51:18 VM-0-4-ubuntu systemd[1]: Started atd.service - Deferred execution scheduler.
```
重点就是要看到上表中的特殊字体，包括【enabled】以及【running】时，这才是 atd 真的在运行的意思，这部分我们在第 17 章会谈及。

### ◆ at 的运行方式

既然是计划任务，那么应该会有产生任务的方式，并且将这些任务排进计划表中。OK，那么产生任务的方式是怎么执行的呢？事实上，**我们使用 at 这个命令来产生所要运行的任务，并将这个任务以文本文件的方式写入/var/spool/at/目录内，该任务便能等待 atd 这个服务的使用与执行了**，就这么简单。
不过，并不是所有的人都可以执行 at 计划任务。为什么？因为安全的原因，很多主机被所谓【劫持】后，最常发现的就是它们的系统当中多了很多的骇客程序，这些程序非常可能使用计划任务来执行或搜集系统信息，并定时地返回给骇客团体。所以，除非是你认可的账号，否则先不要让它们使用 at 目录。那怎么实现对 at 的管控呢？
我们可以利用/etc/at.allow 与/etc/at.deny 这两个文件来实现对 at 的使用限制。加上这两个文件后，at 的工作情况其实是这样的：
1. **先找寻/etc/at.allow 这个文件，写在这个文件中的用户才能使用 at，没有在这个文件中的用户则不能使用 at（即使没有写在 at.deny 当中）**。
2. **如果/etc/at.allow 不存在，就查找/etc/at.deny这个文件，写在这个 at.deny 中的用户则不能使用 at，而没有在这个 at.deny 文件中的用户，就可以使用 at**。
3. **如果两个文件都不存在，那么只有 root 可以使用 at 这个命令**。

通过这个说明，我们知道/etc/at.allow 是管理较为严格的方式，而/etc/at.deny 则较为松散（因为账号没有在该文件中，就能够执行 at 了）。在一般的 Linux 发行版当中，由于假设系统上的所有用户都是可信任的，因此系统通常会保留一个空的/etc/at.deny文件，允许所有人使用 at 命令（您可以自行检查一下该文件）。不过，万一你不希望某些用户使用 at 的话，将那个用户的账号写入/etc/at.deny 即可，一个账号写一行。

## 15.2.2 实际运行单一计划任务

单一计划任务的执行使用 at 命令，这个命令的运行非常简单，将 at 加上一个时间即可。基本的语法如下：
```shell
[root@study ~]# at [-mldv] TIME
[root@study ~]# at -c 任务号码
选项与参数：
-m：当 at 的任务完成后，即使没有输出信息，亦发 email 通知使用者该任务已完成。
-l：at -l 相当于 atq，列出目前系统上面的所有该使用者的 at 计划。
-d：at -d 相当于 atrm，可以取消一个在 at 计划中的任务。
-v：可以使用较明显的时间格式列出 at 计划中的任务列表。
-c：可以列出后面接的该项任务的实际命令内容。
TIME：时间格式，这里可以定义出【什么时候要执行 at 这项任务】的时间，格式有：
HH:MM                           ex> 04:00
	在今日的 HH:MM 时刻执行，若该时刻已超过，则明天的 HH:MM 执行此任务。
HH:MM YYY-MM-DD                 ex> 04:00 2015-07-30
	强制规定在某年某月某日的某时刻执行。
HH:MM[am|pm] [Month] [Date]     ex> 04pm July 30
	也是一样，强制在某年某月某日的某时刻执行。
HH:MM[am|pm] + number [minutes|hours|days|weeks]
	ex> now + 5 minutes         ex> 04px + 3 days
	就是说，在某个时间点【再加几个时间后】才执行。
```

### ◆ at 任务的管理

那么万一我执行了 at 之后，才发现命令输入错误，该如何是好？使用 atq 与 atrm 将它删除。
```shell
[root@study ~]# atq
[root@study ~]# atrm （jobnumber）
范例一：查询目前主机上面有多少的 at 计划任务？
[root@study ~]# atq
3  Tue Aug 4 23:00:00 2015 a root
# 上面说的是：【在 2015/08/04 的 23:00 有一项任务，该项任务命令执行者为 root】
# 而且该项任务的任务号码（jobnumber）为 3 号。
范例二：将上述的第 3 个任务删除。
[root@study ~]# atrm 3
[root@study ~]# atq
# 没有任何信息，表示该任务被删除了。
```
如此一来，你可以利用 atq 来查询，利用 atrm 来删除错误的命令，利用 at 来直接执行单一计划任务，很简单。不过，有个问题需要处理一下。**如果你是在一个非常忙碌的系统下运行 at，能不能指定你的任务在系统较闲的时候才执行呢？** 这是可以的，那就使用 batch 命令。

### ◆ batch：系统有空时才执行后台任务

其实 batch 是利用 at 来执行命令的，只是加入一些控制参数而已。这个 batch 神奇的地方在于：**它是在 CPU 的任务负载小于 0.8 的时候，才执行你的工作任务**。那什么是任务负载 0.8 呢？这个任务负载的意思是：CPU 在单一时间点所负责的任务数量，而不是 CPU 的使用率。举例来说，如果我有一个程序需要一直使用 CPU 的运算功能，那么此时 CPU 的使用率可能到达 100%，但是 CPU 的任务负载则是趋近于【1】，因为 CPU 仅负责一个任务嘛。如果同时执行两个这样的程序呢？CPU 的使用率还是 100%，但是任务负载则变成了【2】，了解了吗？
所以也就是说，CPU 的任务负载大，代表 CPU 必须要在不同的任务之间执行频繁的任务切换。这样的 CPU 运行情况我们在第 0 章谈过，忘记的话请回去看看。因为一直切换任务，所以会导致系统忙碌。系统如果很忙碌，还要额外执行 at，不太合理，所以才有 batch 命令的产生。
在 CentOS 7 下面的 batch 已经不再支持时间参数了，因此 batch 可以拿来作为判断是否要立刻执行后台程序的根据。下面我们来实验一下 batch。为了产生 CPU 较高的任务负载，我们用了第 12 章里面计算 Pi 的脚本，连续执行 4 次这个程序，来模拟高负载，然后来玩一玩 batch：
```shell
范例一：请执行 Pi 的计算，然后在系统闲置时，执行 updatedb 的任务。
[root@study ~]# echo "scale=100000; 4*a（1）" | bc -lq &
[root@study ~]# echo "scale=100000; 4*a（1）" | bc -lq &
[root@study ~]# echo "scale=100000; 4*a（1）" | bc -lq &
[root@study ~]# echo "scale=100000; 4*a（1）" | bc -lq &
# 然后等待个大约数十秒的时间，之后再来确认一下任务负载的情况。
[root@study ~]# uptime
19:46:45 up 1 day,  5:24,  1 user,  load average: 0.00, 0.00, 0.00
[root@study ~]# batch
[root@study ~]# date;atq
```

# 15.3 循环执行的计划任务

相对于 at 是仅执行一次的任务，**循环执行的计划任务则是由 cron（crond）这个系统服务来控制的**。刚刚谈过 Linux 系统上面原本就有非常多的例行性计划任务，因此这个系统服务是默认启动的。另外，由于用户自己也可以执行计划任务，所以，Linux 也提供用户控制计划任务的命令（crontab）。

## 15.3.1 用户的设置

用户想要建立循环型计划任务时，使用的是 crontab 这个命令。不过，为了避免安全性的问题，与 at 同样的，我们可以限制使用 crontab 的用户账号。可以使用的配置文件有：
◆ /etc/cron.allow
将可以使用 crontab 的账号写入其中，不在这个文件内的用户则不可使用 crontab。
◆ /etc/cron.deny
将不可以使用 crontab 的账号写入其中，未记录到这个文件当中的用户，就可以使用 crontab。
与 at 很像。同样的，以优先级来说，/etc/cron.allow 比 /etc/cron.deny 要优先。而判断上面，这两个文件只选择一个来限制而已。因此，建议你只要保留一个即可，免得影响自己在设置上面的判断。一般来说，系统默认保留/etc/cron.deny，你可以将不想让它执行 crontab 的那个用户写入 /etc/cron.deny 当中，一个账号一行。
**当用户使用 crontab 这个命令来建立计划任务之后，该项任务就会被记录到 /var/spool/cron/ 中，而且是以账号来作为判断根据的**。举例来说，dmtsai 使用 crontab 后，它的任务会被记录到 /var/spool/cron/dmtsai。但请注意，**不要使用 vi 直接编辑该文件，因为可能由于输入语法错误，会导致无法执行 cron**。另外，cron 执行的每一项任务都会被记录到/var/log/cron这个日志文件中，所以，如果你的 Linux 不知道是否被植入木马时，也可以查找一下/var/log/cron 这个日志文件。
好了，那么我们就来聊一聊 crontab 的语法。
```shell
[root@study ~]# crontab [-u username] [-l|-e|-r]
选项与参数：
-u：操作指定用户的 crontab
-e：编辑 crontab 的任务内容。
-l：查看 crontab 的任务内容。
-r：删除所有的 crontab 的任务内容，若仅要删除一项，请用 -e 去编辑。
范例一：用 dmtsai 的身份在每天的12:00发信给自己。
[dmtsai@study ~]# crontab -e
# 此时会进入 vi 的编辑界面让您编辑任务，注意到，每项任务都是一行。
```
默认情况下，任何用户只要不被列入/etc/cron.deny当中，那么它就可以直接执行【crontab -e】去编辑自己的例行性命令。整个过程就如同上面提到的，会进入 vi 的编辑页面，然后以一个任务一行来编辑，编辑完毕之后输入【:wq】并存储后退出 vi 即可。而每项任务（每行）的格式都具有六个字段，这六个字段的意义为：
代表意义      分钟       小时      日期       月份       周               命令
数字范围     0~59     0~23     1~31     1~12     0~7      需要执行的命令

比较有趣的是那个【周】，周的数字为 0 或 7 时，都代表【星期天】的意思。另外，还有下面这些特殊字符：

| 特殊字符 | 代表意义 |
| --- | --- |
| `*（星号）` | 代表任何时刻都接受的意思。举例来说，范例一内那个日、月、周都是 `*`，就代表着【不论何月、何日的星期几的 12:00 都执行后续命令】的意思 |
| `,（逗号）`  | 代表分隔时段的意思。举例来说，如果要执行的任务是 3:00 与 6:00 时，就会是：<br> 0 3,6 * * * command <br> 时间参数还是有五栏，不过第二栏是 3、6，代表 3 与 6 都适用 |
| `-（减号）` | 代表一段时间范围内，举例来说，8 点到 12 点之间的每小时的 20 分都执行一项任务：<br> 20 8-12 * * * command <br> 仔细看到第二栏变成 8-12，代表 8、9、10、11、12 都适用的意思 |
| `/n（斜线）` | 那个 n 代表数字，亦即是【每隔 n 单位间隔】的意思，例如每五分钟执行一次，则：<br> `*/5 * * * * command`<br> 很简单吧，用 `*` 与/5 来搭配，也可以写成 0-59/5，相同意思 |

## 15.3.2 系统的配置文件：/etc/crontab、/etc/cron.d/*

这个【crontab -e】是针对用户的 cron 来设计的，如果要执行【系统的例行性任务】时，该怎么办？是否还是需要用 crontab -e 来管理你的计划任务？当然不需要，你只要编辑 /etc/crontab 这个文件就可以。有一点需要特别注意，那就是 crontab -e 这个 crontab 其实是 /usr/bin/crontab 这个执行文件，但是 /etc/crontab 可是一个【纯文本文件】，你可以用 root 的身份编辑一下这个文件。
基本上，cron **这个服务的最低检测限制是【分钟】，所以【cron 会每分钟去读取一次 /etc/crontab 与 /var/spool/cron 里面的数据内容】**。因此，只要你编辑完 /etc/crontab 这个文件，并且将它保存之后，那么 cron 的设置就自动地会来执行了。
>在 Linux 下面的 crontab 会自动帮我们每分钟重新读取一次 /etc/crontab 的计划任务列表。但是由于某些原因是在其他的 UNIX 系统中，由于 crontab 是读到内存当中的，所以在你修改完/etc/crontab之后，可能并不会马上执行，这个时候请重新启动 crond 这个服务：【systemctl restart crond】。

废话少说，我们就来看一下这个 /etc/crontab 的内容。
```shell
[root@study ~]# cat /etc/crontab
# /etc/crontab: system-wide crontab                                                
# Unlike any other crontab you don't have to run the `crontab'                     
# command to install the new version when you edit this file                       
# and files in /etc/cron.d. These files also have username fields,                 
# that none of the other crontabs do.                                              
SHELL=/bin/sh                                                                      
# You can also override PATH, but by default, newer versions inherit it from the environment                                                                        
#PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin                 
# Example of job definition:                                                       
# .---------------- minute (0 - 59)                                                
# |  .------------- hour (0 - 23)                                                  
# |  |  .---------- day of month (1 - 31)                                          
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...                          
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat                                                        
# |  |  |  |  |                                                                    
# *  *  *  *  * user-name command to be executed                                   
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly                
25 6    * * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }                                                                 
47 6    * * 7   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.weekly; }                                                                
52 6    1 * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.monthly; }                                                               
#
```
看到这个文件的内容你大概就了解了吧，呵呵，没错，这个文件与刚刚我们执行 crontab -e 的内容几乎一模一样，只有几个地方不太相同：
- MAILTO=root
	这个选项是说，当 /etc/crontab 这个文件中的例行性工作的命令发生错误时，或是该任务的执行结果有标准输出/标准错误时，会将错误信息或是屏幕显示的信息传给谁？默认当然是由系统直接发一封 email 给 root。不过，由于 root 无法在客户端中以 POP3 之类的协议收信，因此，鸟哥通常都将这个 email 改成自己的账号，好让我随时了解系统的状况。例如：`MAILTO=dmtsai@my.host.name`
- PATH=
	还记得我们在第 10 章的 BASH 当中一直提到的执行文件路径问题吧？没错，这里就是输入执行文件的查找路径，使用默认的路径设置就已经足够了。
- 【分 时 日 月 周 身份 命令】7 个字段的设置
	这个 /etc/crontab 里面可以设置的基本语法与 crontab -e 不太相同。前面同样是分、时、日、月、周 5 个字段，但是在 5 个字段后面接的并不是命令，而是一个新的字段，那就是【执行后面那串命令的用户身份】是什么，这与用户的 crontab -e 不相同。由于用户自己的 crontab 并不需要指定身份，但 /etc/crontab 里面当然要指定身份。以上表的内容来说，系统默认的计划任务是以 root 的身份来执行的。

### ◆ crond 服务读取配置文件的位置

一般来说，crond 默认有 3 个地方会执行脚本配置文件，它们分别是：
- /etc/contab
- /etc/cron.d/*
- /var/spool/cron/*
这三个地方中，跟系统的运行有关系的两个配置文件是 /etc/crontab 文件以及 /etc/cron.d/* 目录内的文件，另外一个是跟用户自己的任务有关系的配置文件，就是放在 /var/spool/cron/ 里面的文件。我们已经知道了 /var/spool/cron 以及 /etc/crontab 的内容，那么现在就来看看 /etc/cron.d 里面的东西。
```shell
[root@study ~]# ls -l /etc/cron.d
total 16                                                                           
-rw-r--r-- 1 root root 201 Apr  8  2024 e2scrub_all                                
-rw------- 1 root root 110 May 15 14:23 sgagenttask                                
-rw-r--r-- 1 root root 396 Apr 23  2024 sysstat                                    
-rw------- 1 root root 156 May 15 14:23 yunjing
# 其实说真的，除了 /etc/crontab 之外，crond 的配置文件还不少，上面就有四个设置。
# 先让我们来看看 0hourly 这个配置文件的内容。
[root@study ~]# cat /etc/cron.d/e2scrub_all
30 3 * * 0 root test -e /run/systemd/system || SERVICE_MODE=1 /usr/lib/x86_64-linux-gnu/e2fsprogs/e2scrub_all_cron                                               
10 3 * * * root test -e /run/systemd/system || SERVICE_MODE=1 /sbin/e2scrub_all -A -r
# 看一看，内容跟/etc/crontab 几乎一模一样，但实际上是有设置值，就是最后一行。
```
如果你想要自己开发新的软件，该软件要拥有自己的 crontab 定时命令时，就可以将【分、时、日、月、周、身份、命令】的配置文件放置到 /etc/cron.d/ 目录下。在此目录下的文件是【crontab 的配置文件脚本】。
>以鸟哥来说，现在鸟哥正在开发一些虚拟化教室的软件，该软件需要定时清除一些垃圾防火墙规则。那鸟哥就会将要执行的时间与命令设计好，然后直接将设置写入到 /etc/cron.d/newfile 即可。未来如果这个软件要升级，直接将该文件覆盖成新文件即可，比起手动去分析 /etc/crontab 要单纯得多。


另外，请注意一下上面表格中提到的最后一行，每个整点的一分会执行【run-parts /etc/cron.hourly】这个命令，咦，那什么是 run-parts 呢？如果你去分析一下这个执行文件，会发现它就是 shell 脚本，run-parts **脚本会在大约 5 分钟内随机选一个时间来执行 /etc/cron.hourly 目录内的所有执行文件。因此，放在 /etc/cron.hourly/ 的文件，必须是能被直接执行的命令脚本，而不是分、时、日、月、周的设置值**，注意注意。
也就是说，除了自己指定分、时、日、月、周加上命令路径的 crond 配置文件之外，你也可以直接将命令放置到（或链接到）/etc/cron.hourly/ 目录下，这样该命令就会被 crond 在每小时的第 1 分钟开始后的 5 分钟内，随机取一个时间点来执行，你无须手动去指定分、时、日、月、周。
眼尖的朋友可能还会发现，除了可以直接将命令放到 /etc/cron.hourly/，让系统每小时定时执行之外，在 /etc/ 下面其实还有 /etc/cron.daily、/etc/cron.weekly/、/etc/cron.monthly/，这三个目录是代表每日、每周、每月各执行一次的意思吗？嘿嘿，厉害，没错，是这样。不过跟 /etc/cron.hourly/ 不太一样的是，那三个目录是由 anacron 所执行的。而 anacron 的执行方式则是放在 /etc/cron.hourly/0anacron 里面，跟前几代 anacron 是单独的服务不太一样。这部分留待下个小节再来讨论。
最后，让我们总结一下吧：
- 个人化的操作使用【crontab -e】：如果你是根据个人需求来建立例行计划任务，建议直接使用 crontab -e 来建立你的计划任务较佳。这样也能保障你的命令操作不会被大家看到（/etc/crontab 是大家都能读取的权限）。
- 系统维护管理使用【vim /etc/crontab】：如果你这个例行计划任务是系统的重要任务，为了让自己管理方便，同时容易追踪，建立直接写入 /etc/crontab 较佳。
- 自己开发软件使用【vim /etc/cron.d/newfile】：如果你是想要自己开发软件，那当然最好就是使用全新的配置文件，并且放置于 /etc/cron.d/ 目录内即可。
- 固定每小时、每日、每周执行的特别任务：如果与系统维护有关，还是建议放置到 /etc/crontab 中来集中管理较好。如果想要偷懒或是一定要在某个周期内执行的任务，也可以放置到上面谈到的几个目录内，直接写入命令即可。

## 15.3.3 一些注意事项

有的时候，我们以系统的 cron 来执行计划任务的建立时，要注意一些使用方面的特性。举例来说，如果我们有四个任务都是五分钟要执行一次的，那么是否这四个操作全部都在同一个时间点执行呢？如果同时执行，该四个操作又很耗系统资源，如此一来，每五分钟的某个时刻不是会让系统忙得要死？呵呵，此时好好地分配一些运行时间就 OK。所以，还需要注意以下内容：

### ◆ 资源分配不均的问题

大量使用 crontab 的时候，总是会有问题发生。最严重的问题就是【系统资源分配不均】。以鸟哥的系统为例，我会检测主机流量的信息，包括：
- 流量
- 区域内其他 PC 的流量监测
- CPU 使用率
- RAM 使用率
- 在线人数实时监测
如果每个流程都在同一个时间启动的话，那么在某个时段，系统会变得相当繁忙。所以，这个时候就必须要分别设置，我可以这样做：
```shell
[root@study ~]# vim /etc/crontab
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly                
25 6    * * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }                                                                 
47 6    * * 7   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.weekly; }                                                                
52 6    1 * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.monthly; }
```
看到了没？那个【,】分隔的时候，请注意，不要有空格符。（连续的意思）如此一来，则可以将每五分钟运行的流程分别在不同的时刻来执行，从而让系统的执行较为流畅。

### ◆ 取消不要的输出选项

另外一个困扰发生在【当有执行成果或是执行的选项中有输出的数据时，该数据将会 mail 给 MAILTO 设置的账号】。好，那么当有一个任务一直出错（例如 DNS 的检测系统当中，若 DNS 上层主机挂掉，那么你就会一直收到错误信息），怎么办呢》呵呵，还记得第 10 章谈到的数据流重定向吧？直接用【数据流重定向】将结果输出到 /dev/null 这个垃圾桶当中就好。

### ◆ 安全的校验

很多时候木马都是以计划任务命令的方式植入的，所以可以借由检查 /var/log/cron 的内容来观察是否有【非您设置的 cron 被执行了？】这个时候就需要小心一点。

### ◆ 周与日月不可同时并存

另一个需要注意的地方在于：【你可以分别以周或是日月为单位作为循环，但你不可使用几月几号且为星期几的模式任务】。这个意思是说，你不可以这样编写一个计划任务：
```shell
30 12 11 9 5 root echo "just test" <==这是错误的写法。
```
本来你以为 9 月 11 号且为星期五才会执行这项任务，无奈的是，系统可能会判定每个星期五做一次，或每年的 9 月 11 号分别执行，如此一来与你当初的规划就不一样了。所以，得要注意这个地方。
>根据某些人的说法，这个月日、周不可并存的问题已经在新版中被解决了，不过，鸟哥并没有实际去验证它，目前也不打算验证它。因为，周就是周，月日就月日，单一执行点就是单一执行点，无须使用 crontab 去设置固定的日期，您说是吧？

# 15.4 可唤醒停机期间的工作任务

想象一个环境，你的 Linux 服务器有一个任务是需要在每周的星期天凌晨 2 点执行，但是很不巧，星期六停电了，所以你得要星期一才能进公司去启动服务器。那么请问，这个星期天的计划任务还要不要执行？因为你开机的时候已经是星期一，所以星期天的任务当然不会被执行，对吧。
问题是，若该任务非常重要（例如例行备份），所以其实你还是希望在下个星期天之前的某天执行一下比较好，那你该怎么办？自己手动执行？如果你跟鸟哥一样是个记忆力超差的家伙，那么肯定【记不起来某个重要任务要执行】的，这时候就要靠 anacron 这个命令的功能了。这不命令可以主动帮你执行到了但却没有执行的计划任务。

## 15.4.1 什么是 anacron

anacron 并不是用来替换 crontab 的，anacron 存在的目的就在于我们上面提到的，用于处理非 24 小时运行的 Linux 系统所执行的 crontab，以及因为某些原因导致的超过时间而没有被执行的任务。
其实 anacron 默认会以一天、七天、一个月为期去检测系统未执行的 crontab 任务，因此对于某些特殊的使用环境非常有帮助。举例来说，如果你的 Linux 主机是放在公司给同事使用的，因为周末假日大家都不在从而没有必要开启，所以你的 Linux 每周末都会关机凌天。但是 crontab 大多在每天的凌晨以及周日的早上执行各项任务，偏偏你又关机了，系统很多 crontab 的任务就无法执行，此时 anacron 刚好可以解决这个问题。
那么 anacron 又是怎么知道我们的系统啥时关机的呢？这就要使用 anacron 读取的时间记录文件（timestamps）了。anacron 会去分析现在的时间与时间记录文件所加载的上次执行 anacron 的时间，两者比较后若发现有差异，那就是在某些时刻没有执行 crontab，此时 anacron 就会开始执行未执行的 crontab 任务了。

## 15.4.2 anacron 与 /etc/anacrontab

anacron 其实是一个程序并非一个服务，这个程序在 CentOS 当中已经进入 crontab 的任务列表，同时 anacron 会每小时被主动执行一次。咦？每小时？所以 anacron 的配置文件应该放置在 /etc/cron.hourly 吗？嘿嘿，您真内行，赶紧来看一看。
基本上，anacron 的语法如下：
```shell
[root@study ~]# anacron [-sfn] [job]..
[root@study ~]# anacron -u [job]..
选项与参数：
-s：开始连续地执行各项任务（job），会根据时间记录文件地数据判断是否执行。
-f：强制执行，而不去判断时间记录文件的时间戳。
-n：立刻执行未执行的任务，而不延迟（delay）等待时间
-u：仅更新时间记录文件的时间戳，不执行任何任务。
job：由 /etc/anacrontab 定义的各项任务名称。
```
在我们的 CentOS 中，anacron 其实每小时都会被抓出来执行一次，但是担心 anacron 误判时间参数，因此 /etc/cron.hourly/ 里面的 anacron 才会在文件名之前加个 0（0anacron），让 anacron 最先执行，就是为了让时间戳先更新，以避免 anacron 误判 crontab 尚未执行任何任务。





























