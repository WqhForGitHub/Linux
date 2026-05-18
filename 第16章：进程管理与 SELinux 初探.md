# 16.1 什么是进程（process）

在前面几章中，我们一直强调 Linux 下面所有命令与你能够执行的操作都与权限有关，而系统如何判断你的权限呢？当然就是第 13 章账号管理当中提到的 UID/GID 的相关概念，以及文件的属性相关性。再进一步来解释，你现在大概知道，在 Linux 系统当中：【触发任何一个事件时，系统都会将它定义成为一个进程，并且给予这个进程一个 ID，称为 PID，同时根据触发这个进程的用户与相关属性关系，给予这个 PID 一组有效的权限设置】。从此之后，这个 PID 能够在系统上面执行的操作就与这个 PID 的权限有关。
看这个定义似乎没有什么很奇怪的地方，不过，您得到了解什么叫做【触发事件】才行。我们在什么情况下会触发一个事件？而同一个事件可否被触发多次？呵呵，先来了解了解吧。

# 16.3 进程管理

本章一开始提到所谓的【进程】概念，包括进程的触发、子进程与父进程的相关性等。此外，还有那个【进程的依赖性】以及所谓的【僵尸进程】等需要说明。为什么进程管理这么重要呢？这是因为：
- 首先，本章一开始就谈到的，我们在操作系统时的各项任务其实都是经过某个 PID 来完成的（包括你的 bash 环境），因此，能不能执行某项任务，就与该进程的权限有关了。
- 再来，如果您的 Linux 系统是个很忙碌的系统，那么当整个系统资源快要被使用光时，您是否能够找出最耗系统的那个进程，然后删除该进程，让系统恢复正常？
- 此外，如果由于某个程序写的不好，导致在内存当中产生一个有问题的进程，您又该如何找出它，然后将它删除？
- 如果同时有五六项任务在您的系统当中运行，但其中有一项任务才是最重要的，该如何让那一项重要的任务被最优先执行？

所以，一个称职的系统管理员，必须要熟悉进程的管理流程才行，否则当系统发生问题时，还真是很难解决问题。下面我们会先介绍如何查看进程与进程的状态，然后再加以控制。

## 16.3.1 查看进程

既然进程这么重要，那么我们如何查看系统上面正在运行当中的进程呢？很简单，可以利用静态的 ps 或是动态的 top 命令，还可以利用 pstree 来查看进程树之间的关系。

### ◆ ps：将某个时间点的进程运行情况提取下来
```shell
[root@study ~]# ps aux <==查看系统所有的进程
[root@study ~]# ps -lA <==也是能够查看所有系统的进程
[root@study ~]# ps axjf <==连同部分进程树状态。
选项与参数：
-A：所有的进程均显示出来，与 -e 具有同样的效果。
-a：不显示与终端有关的所有进程。
-u：有效使用者（effective user）相关的进程。
x：通常与 a 这个参数一起使用，可列出较完整信息。
输出格式规划：
l：较长，较详细的将该 PID 的信息列出。
j：任务的格式（jobs format）。
-f：做一个更为完整的输出。
```
鸟哥个人认为 ps 这个命令的 man page 不是很好看，因为很多不同的 UNIX 都使用这个 ps 来查看进程状态。由于要符合不同版本的需求，所以这个 man page 写得非常庞大。因此，通常鸟哥都会建议你，直接背两个比较不同的选项，**一个是只能看自己 bash 进程的【ps -l】，另一个则是可以查看所有系统运行的进程的【ps aux】**。注意，你没看错，是【ps aux】，没有那个减号（-）。先来看看如何查看自己 bash 进程的状态：

#### • 仅查看自己的 bash 相关进程：ps -l
```shell
范例一：将目前属于您自己这次登录的 PID 与相关信息列示出来（只与自己的 bash 有关）。
[root@study ~]# ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD          
1 S     0  996254  996253  0  80   0 -  4183 do_pol pts/1    00:00:00 sudo         
4 S     0  996255  996254  0  80   0 -  2163 do_wai pts/1    00:00:00 bash         
4 R     0 1011622  996255  0  80   0 -  2729 -      pts/1    00:00:00 ps
# 还记得鸟哥说过，非必要不要使用 root 直接登录吧？从这个 ps -l 的分析，你也可以发现，
# 鸟哥其实是使用 sudo 才转成 root 的身份，否则连测试机，鸟哥都是使用一般账号登录。
```
系统整体运行的进程是非常多的，但使用 ps -l 仅会列出与你的操作环境（bash）有关的进程，即最上层的父进程会是你自己的 bash 而没有扩展到 systemd（后续会介绍）这个进程中。那么 ps -l 显示来的数据有哪些？我们就来观察看看：
- F：代表这个进程标识（process flags），说明这个进程的权限，常见号码有：
	- 若为 4 表示此进程的权限为 root.
	- 若为 1 表示此子进程仅执行复制（fork）而没有实际执行（exec）。
- S：代表这个进程的状态（STAT），主要的状态有：
	- R（Running）：该进程正在运行中。
	- S（Sleep）：该进程目前正在睡眠状态（idle），但可以被唤醒（signal）。
	- D：不可被唤醒的睡眠状态，通常这个进程可能在等待 I/O 的情况（ex>打印）。
	- T：停止状态（stop），可能是在任务控制（后台暂停）或跟踪（traced）状态。
	- Z（Zombie）：僵尸状态，进程已经终止但却无法被删除至内存中。
- UID/PID/PPID：代表【此进程被该 UID 所拥有/进程的 PID 号码/此进程的父进程 PID 号码】。
- C：代表 CPU 使用率，单位为百分比。
- PRI/NI：Priority/Nice 的缩写，代表此进程被 CPU 所执行的优先级，数值越小代表该进程越快被 CPU 执行。详细的 PRI 与 NI 将在下一小节说明。
- ADDR/SZ/WCHAN：都与内存有关，ADDR 是 kernel function，指出该进程在内存的哪个部分，如果是个 running 的进程，一般就会显示【-】，SZ 代表此进程用掉多少内存，WCHAN 表示目前进程是否运行，同样的，若为 - 表示正在运行中。
- TTY：登录者的终端位置，若为远程登录则使用动态终端接口名称（pts/n）。
- TIME：使用的 CPU 时间，注意，是此进程实际花费 CPU 运行的时间，而不是系统时间。
- CMD：就是 command 的缩写，表示造成此进程的触发进程的命令是什么。

所以你看到的 ps -l 输出信息中，它说明的是：【bash 的进程属于 UID 为 0 的用户，状态为睡眠（sleep），之所以为睡眠，是因为它触发了 ps（状态为 run）。此进程的 PID 为 996255，优先执行顺序为 80，执行 bash 所获取的终端接口为 pts/1，运行状态为等待（wait）】，这样已经够清楚了吧？您自己尝试解析一下 ps 那一行代表的意义是什么？
接下来让我们使用 ps 来查看一下系统内所有的进程状态。‘

#### • 查看系统所有进程：ps aux

```shell
范例二：列出目前所有的正在内存当中的进程。
[root@study ~]# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND         
root           1  0.0  0.3  22664 13928 ?        Ss   May15   0:09 /sbin/init showopts                                                                           
root           2  0.0  0.0      0     0 ?        S    May15   0:00 [kthreadd]      
root           3  0.0  0.0      0     0 ?        S    May15   0:00 [pool_workqueue_release]                                                           
root           4  0.0  0.0      0     0 ?        I<   May15   0:00 [kworker/R-rcu_g]                                                                             
root           5  0.0  0.0      0     0 ?        I<   May15   0:00 [kworker/R-rcu_p]                      
```
你会发现 ps -l 与 ps aux 显示的项目并不相同。在 ps aux 显示的项目中，各字段的意义为：
- USER：该进程属于所属用户账号
- PID：该进程的进程 ID
- %CPU：该进程使用掉的 CPU 资源百分比
- %MEM：该进程所占用的物理内存百分比
- VSZ：该进程使用掉的虚拟内存量（KB）
- RSS：该进程占用的固定的内存量（KB）
- TTY：该进程是在哪个终端上面运行，若与终端无关则显示？（问号）？另外，tty1~tty6 是本机上面的登录进程，若为 pts/0 等，则表示是由网络连接进入主机的进程
- STAT：该进程目前的状态，状态显示与 ps -l 的 S 标识相同（R/S/T/Z）
- START：该进程被触发启动的时间
- TIME：该进程实际使用 CPU 运行的时间
- COMMAND：该进程的实际命令是什么

一般来说，ps aux 会依照 PID 的顺序来排序显示，我们还是以 PID 为 14836 的那行来说明。该行的意义为【root 执行的 bash PID 为 14836，占用了 0.1% 的内存容量，状态为休眠（S），该进程启动的时间为 8 月 4 号，因此启动太久了，所以没有列出实际的时间点，且获取的终端环境为 pts/0】，与 ps aux 看到的其实是同一个进程。这样可以理解吗？让我们继续使用 ps 来查看一下其他信息。
```shell
范例三：以范例一的显示内容，显示出所有的进程
[root@study ~]# ps -lA
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD          
4 S     0       1       0  0  80   0 -  5666 ep_pol ?        00:00:09 systemd      
1 S     0       2       0  0  80   0 -     0 kthrea ?        00:00:00 kthreadd     
1 S     0       3       2  0  80   0 -     0 kthrea ?        00:00:00 pool_workqueue_release                                                             
# 你会发现每个栏位与 ps -l 的输出情况相同，但显示的进程则包括系统所有的进程。
范例四：列出类似进程树的进程显示。
[root@study ~]# ps axjf
 PPID     PID    PGID     SID TTY        TPGID STAT   UID   TIME COMMAND           
0       2       0       0 ?             -1 S        0   0:00 [kthreadd]      
2       3       0       0 ?             -1 S        0   0:00  \_ [pool_workqueue_release]                                                           
2       4       0       0 ?             -1 I<       0   0:00  \_ [kworker/R-rcu_g] 
2       5       0       0 ?             -1 I<       0   0:00  \_ [kworker/R-rcu_p] 
2       6       0       0 ?             -1 I<       0   0:00  \_ [kworker/R-slub_] 
2       7       0       0 ?             -1 I<       0   0:00  \_ [kworker/R-netns]
```
看出来了吧？其实鸟哥是以网络连接进入虚拟机来执行一些测试的，所以你会发现其实进程之间是有相关性的。不过，其实还可以使用 pstree 来完全查看这个进程树。从上面的例子来看，鸟哥是通过 sshd 提供的网络服务获取一个进程，该进程提供 bash 给我使用，而我通过 bash 再去执行 ps axjf，这样可以看得懂了吗？其他各字段的意义请 man ps（虽然真的很难 man 出来）。
```shell
范例五：找出与 cron 与 rsyslog 这两个服务有关的 PID 号码。
[root@study ~]# ps aux | egrep '（cron|rsyslog）'
root     1024473  0.0  0.0   6676  2464 pts/1    S+   16:57   0:00 grep -E --color=auto （cron|rsyslog）
# 所以号码是 742 及 1338 这两个，就是这样找的。
```
除此之外，我们必须要知道的是【僵尸（zombie）】进程是什么？通常，造成僵尸进程的原因在于该进程应该已经执行完毕，或是应该要终止了，但是该进程的父进程却无法完整地将该进程结束掉，而造成该进程一直存在内存当中。如果你发现在某个进程的 CMD 后面接上了`<defunct>` 时，就代表该进程是僵尸进程，例如：
```shell
apache 8683 0.0 0.9 83384 9992 ? Z 14:33 0:00 /usr/sbin/httpd <defunct>
```
系统不稳定的时候就容易造成所谓的僵尸进程，可能是因为程序写得不好，或是用户的操作习惯不良等所造成的。如果你发现系统中有很多僵尸进程时，记得，要找出该进程的父进程，然后好好做个追踪，好好进行主机的环境优化，看看有什么地方需要改善，不要只是直接将它 kill 掉。不然的话，万一它一直产生，那可就麻烦了。
事实上，通常僵尸进程都已经无法管理，而直接交给 systemd 这个进程来负责，偏偏 systemd 是系统第一个执行的进程，它是所有进程的父进程。我们是无法杀掉该进程的（杀掉它，系统就死掉了），所以，如果产生僵尸进程，而系统过一阵子还没有办法通过内核非经常性的特殊处理来将该进程删除时，那你只好通过 reboot 的方式来将该进程 kill 掉。

### ◆ top：动态查看进程的变化

相对于 ps 是选取一个时间点的进程状态，top 则可以持续检测进程运行的状态。使用方式如下：
```shell
[root@study ~]# top [-d 数字] | top [-bnp]
选项与参数：
-d：后面可以接秒数，就是整个进程界面更新的秒数，默认是 5 秒。
-b：以批量的方式执行 top，还有更多的参数可以使用，通常会搭配数据流重定向来将批量的结果输出为文件。
-n：与 -b 搭配，意义是，需要执行几次 top 的输出结果。
-p：指定某些个 PID 来执行查看监测而已。
在 top 执行过程当中可以使用的按键命令：
	?：显示在 top 当中可以输入的按键命令
	P：以CPU 的使用排序显示
	M：以 Memory 的使用排序显示
	N：以 PID 来排序
	T：由该进程使用的 CPU 时间累积（TIME+）排序
	k：给予某个 PID 一个信号（signal）
	r：给予某个 PID 重新制订一个 nice 值
	q：退出 top 的按键
```
其实 top 的功能非常多，可以用的按键也非常多，可以参考 man top 的部分说明文件，鸟哥这里仅列出了一些自己常用的选项而已。接下来让我们实际查看一下如何使用 top 与 top 的界面。
```shell
范例一：每两秒钟更新一次 top，查看整体信息。
[root@study ~]# top -d 2
```
top 也是个挺不错的进程查看工具，但与 ps 的静态结果输出不同，top 这个进程可以持续地监测整个系统地进程任务状态。在默认的情况下，每次更新进程资源的时间为 5 秒，不过，可以使用 -d 来执行修改。top 主要分为两部分界面，上面的界面为整个系统的资源使用状态，基本上总共有六行，显示的内容依序是：
- 第一行（top...）：这一行显示的信息分别为：
	- 目前的时间，即 00:53:59 这个项目
	- 开机到目前为止所经过的时间，即 up 6:07，这个项目
	- 已经登录系统的用户人物，即 3 users，这个项目
	- 系统在 1、5、15 分钟的平均任务负载。我们在第 15 章谈到的 batch 任务方式为负载小于 0.8 就是这个负载。代表的是 1、5、15 分钟，系统平均要负责运行几个进程（任务）的意思。数值越小代表系统越闲置，若高于 1 就要注意你的系统进程是否太过频繁了。
- 第二行（Tasks...）：显示的是目前进程的总量与个别进程在什么状态（running、sleeping、stopped、zomble）。需要注意的是最后的 zombie 那个数值，如果不是 0，赶紧好好看看到底是哪个 process 变成僵尸了吧？
- 第三行（%Cpus...）：显示的是 CPU 的整体负载，每个项目可使用?（问号）查看。需要特别注意的是 wa 项目，那个项目代表的是 I/O wait，通常你的系统会变慢都是 I/O 产生的问题比较大。因此这里要注意这个项目耗用 CPU 的资源。另外，如果是多内核的设备，可以按下数字键【1】来切换成不同 CPU 的负载率。
- 第四行与第五行：表示目前的物理内存与虚拟内存（Mem/Swap）的使用情况。再次重申，要注意的是 swap 的使用量要尽量的少，如果 swap 被用得很多，表示系统的物理内存实在不足。
- 第六行：这个是当在 top 进程当中输入命令时，显示状态的地方。

至于 top 下半部分的画面，则是每个进程使用的资源情况，需要注意的是：
- PID：每个进程的 ID
- USER：该进程所属的用户
- PR：Priority 的简写，进程的优先执行顺序，越小则越早被执行
- NI：Nice 的简写，与 Priority 有关，也是越小则越早被执行
- %CPU：CPU 的使用率
- %MEM：内存的使用率
- TIME+：CPU 使用时间的累加

top 默认使用 CPU 使用率（%CPU）作为排序的依据。如果你想要使用内存使用率排序，则可以按下【M】，若要恢复则按下【P】即可。如果想要退出 top，则按下【q】。如果想要将 top 的结果输出成为文件时，可以这样做：
```shell
范例二：将 top 的信息执行 2 次，然后将结果输出到 /tmp/top.txt。
[root@study ~]# top -b -n 2 > /tmp/top.txt
# 这样一来，嘿嘿，就可以将 top 的信息存到/tmp/top.txt 文件中了。
```
这个命令很有趣，可以帮助你将某个时段 top 查看到的结果存成文件，可以在系统后台执行。由于是后台执行，与终端的屏幕大小无关，因此可以得到全部的进程界面。如果你想要查看的进程 CPU 与内存使用率都很低，结果老是无法在第一行显示时，该怎么办？我们可以仅查看单一进程。如下所示：
```shell
范例三：我们自己的 bash PID 可由$$变量获取，请使用 top 持续查看该 PID。
[root@study ~]# echo $$
1049846 <==就是这个数字，它是我们 bash 的 PID。
[root@study ~]# top -d 2 -p 1049846
top - 18:51:26 up 3 days,  4:28,  1 user,  load average: 0.00, 0.00, 0.02          
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie               
%Cpu(s):  0.5 us,  0.2 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st    
MiB Mem :   3659.9 total,   2099.4 free,    524.7 used,   1276.6 buff/cache        
MiB Swap:   1988.0 total,   1988.0 free,      0.0 used.   3135.2 avail Mem         
PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
1049846 root      20   0    8652   5584   3948 S   0.0   0.1   0:00.01 bash
```
看到没？只会有一个进程给你看，很容易查看吧，好，那么如果我想要在 top 下面执行一些操作？比方说，修改 NI 这个数值，可以这样做：
```shell
范例四：承上题，上面的 NI 值是 0，想要改成 10 的话？
# 在范例三的 top 界面当中直接按下 r 之后，会出现如下的界面。
top - 18:56:37 up 3 days,  4:33,  1 user,  load average: 0.00, 0.00, 0.00          
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie               
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st    
MiB Mem :   3659.9 total,   2090.8 free,    532.7 used,   1277.2 buff/cache        
MiB Swap:   1988.0 total,   1988.0 free,      0.0 used.   3127.2 avail Mem         
PID to renice [default pid = 1049846] 14836                                        
PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND     
1049846 root      20   0    8652   5584   3948 S   0.0   0.1   0:00.01 bash
```
完成上面的操作后，在状态栏会出现如下信息：
```shell
Renice PID 14836 to value 10                                                      PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
```
接下来你就会看到如下显示的画面。
```shell
top - 18:59:36 up 3 days,  4:36,  1 user,  load average: 0.00, 0.00, 0.00          
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie               
%Cpu(s):  0.3 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st    
MiB Mem :   3659.9 total,   2080.7 free,    542.5 used,   1277.5 buff/cache        
MiB Swap:   1988.0 total,   1988.0 free,      0.0 used.   3117.5 avail Mem         
PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND     
1049846 root      30  10    8652   5584   3948 S   0.0   0.1   0:00.03 bash
```
看到不同了吧？下面的地方就是修改之后所产生的效果。一般来说，如果鸟哥想要找出最消耗 CPU 资源的那个进程时，大多使用的就是 top 这个程序，然后强制以 CPU 使用资源来排序（在 top 当中按下 P 即可），就可以很快知道。一定要多多使用这个好用的东西。
### ◆ pstree
```shell
[root@study ~]# pstree [-A|U] [-up]
选项与参数：
-A：各进程树之间的连接以 ASCII 字符来连接。
-U：各进程树之间的连接以 Unicode 的字符来连接，在某些终端界面下可能会有错误。
-p：并同时列出每个进程的 PID。
-u：并同时列出每个进程的所属账号名称。
范例一：列出目前系统上面所有的进程树的相关性。
[root@study ~]# pstree -A
systemd-+-ModemManager---3*[{ModemManager}]                                        
        |-YDLive---8*[{YDLive}]                                                    
        |-YDService---23*[{YDService}]                                             
        |-acpid                                                                    
        |-2*[agetty]                                                               
        |-atd                                                                      
        |-barad_agent-+-barad_agent                                                
        |             `-barad_agent---3*[{barad_agent}]                            
        |-chronyd---chronyd                                                        
        |-cron                                                                     
        |-dbus-daemon                                                              
        |-multipathd---6*[{multipathd}]                                            
        |-networkd-dispat                                                          
        |-polkitd---3*[{polkitd}]                                                  
        |-rsyslogd---3*[{rsyslogd}]                                                
        |-sgagent---{sgagent}                                                      
        |-sh---8*[{sh}]                                                            
        |-sshd---sshd---sshd---bash---sudo---sudo---bash---pstree                  
        |-systemd---(sd-pam)                                                       
        |-systemd-journal                                                          
        |-systemd-logind                                                           
        |-systemd-network                                                          
        |-systemd-resolve                                                          
        |-systemd-udevd                                                            
        |-tat_agent---3*[{tat_agent}]                                              
        |-udisksd---5*[{udisksd}]                                                  
        |-unattended-upgr---{unattended-upgr}                                      
        `-upowerd---3*[{upowerd}]
# 注意一下，为了节省版面，所以鸟哥已经删去很多进程了。
范例二：承上题，同时显示 PID 与 users。
[root@study ~]# pstree -Aup
systemd(1)-+-ModemManager(4601)-+-{ModemManager}(4625)                             
           |                    |-{ModemManager}(4635)                             
           |                    `-{ModemManager}(4642)                             
           |-YDLive(7118)-+-{YDLive}(7121)                                         
           |              |-{YDLive}(7122)                                         
           |              |-{YDLive}(7123)                                         
           |              |-{YDLive}(7124)                                         
           |              |-{YDLive}(7125)                                         
           |              |-{YDLive}(7128)                                         
           |              |-{YDLive}(17785)                                        
           |              `-{YDLive}(631950)
# 在括号内（）内的即是 PID 以及该进程的 owner，一般来说，如果该进程的拥有者与父进程相同。
# 就不会列出，但是如果与父进程不一样，那就会列出该进程的拥有者，看上面 13927 就转变成了 dmtsai。
```
如果要找进程之间的相关性，这个 pstree 真是好用到不行。直接输入 pstree 就可以查到进程相关性，如上表所示，还会使用线段将相关性进程连接起来。一般连接符号使用 ASCII 码即可，有时因为语系问题会主动以 Unicode 的符号来连接。但如果终端无法支持该编码，可能会造成乱码问题，可以加上 -A 选项来解决此类线段乱码问题。
由 pstree 的输出我们也可以很清楚地知道，所有的进程都是依附在 systemd 这个进程下面的。仔细看一下，这个进程的 PID 是一号，因为它是由 Linux 内核所主动调用的第一个进程，所以 PID 就是一号了。这也是我们刚刚讨论僵尸进程时提到的，为啥出现僵尸进程时需要重新启动？因为 systemd 要重新启动，而重新启动 systemd 就是 reboot。
如果还想知道 PID 与所属用户，加上 -u 及 -p 两个参数即可。我们前面不是一直提到，如果子进程挂掉或是总砍不掉子进程时，该如何找到父进程吗？呵呵，用这个 pstree 就对了。




























