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
## 16.3.2 进程的管理

进程之间是可以互相控制的。举例来说，你可以关闭、重新启动服务器软件，服务器软件本身是个进程，你既然可以让它关闭或启动，当然就可以控制该进程。**那么进程是如何互相管理的呢？其实是通过给予该进程一个信号（signal）去告知该进程你想要让它做什么**，因此这个信号就很重要。
我们也在本章之前的 bash 任务管理当中提到过，要给予某个已经存在于后台中的任务某些操作时，直接给予一个信号给该任务号码即可。那么到底有多少信号呢？你可以使用 kill -l（小写的 L）或 man 7 signal 来查询。主要信号的代号、名称及内容如下：

| 代号  | 名称      | 内容                                                                              |
| --- | ------- | ------------------------------------------------------------------------------- |
| 1   | SIGHUP  | 启动被终止的进程，可让该 PID 重新读取自己的配置文件，类似重新启动                                             |
| 2   | SIGINT  | 相当于用键盘输入[ctrl]-c 来中断一个进程的运行                                                     |
| 9   | SIGKILL | 代表强制中断一个进程的执行，如果该进程执行到一半，那么尚未完成的部分可能会有【半成品】产生，类似 vim 会有 .filename.swp 保留下来      |
| 15  | SIGTERM | 以正常的方式结束来终止该进程。由于是正常的终止，所以后续的操作会将它完成。不过，如果该进程已经发生问题，就是无法使用正常的方法终止时，输入这个信号也是没有用的 |
| 19  | SIGSTOP | 相当于用键盘输入[ctrl]-z 来暂停一个进程的运行                                                     |
上面仅是常见的信号而已，更多的信号信息请自行 man 7 signal 吧。一般来说，你只要记得【1、9、15】这三个号码的意义即可。那么我们如何发送一个信号给某个进程呢？就通过 kill 或 killall。下面分别来看看：
### ◆ kill -signal PID

kill 可以帮我们将这个信号传送给某个任务（%jobnumber）或是某个 PID（直接输入数字）。要再次强调的是：**kill 后面直接加数字与加上 %number 的情况是不同的**，这个很重要。因为任务管理中有 1 号任务，但是 PID 1 号则是专指【systemd】这个进程。你怎么可以将 systemd 关闭呢？关闭 systemd，你的系统就宕掉了，所以务必记得那个%是专门用于进行任务管理的。我们就活用一下 kill 与刚刚上面提到的 ps 来做个简单的练习。
了解了这个用法以后，如果未来你想要将某个莫名其妙的登录者的连接删除的话，就可以使用 pstree -p 找到相关进程，然后再用 kill -9 删除该进程，该条连接就会被踢掉了。这样很简单吧。
### ◆ killall -signal 命令名称

由于 kill 后面必须要加上 PID（或是 job number），所以，通常 kill 都会配合 ps、pstree 等命令，因为我们必须要找到相对应的那个进程 PID。但是，如此一来，很麻烦，有没有可以利用【执行命令的名称】来给予信号的呢？举例来说，能不能直接将 rsyslogd 这个进程给予一个 SIGHUP 的信号？当然可以，用 killall。
```shell
[root@study ~]# killall [-iIe] [command name]
选项与参数：
-i：interactive 及互动的意思，若需要删除时，会出现提示字符给使用者。
-e：exact 的意思，表示【后面接的 command name 要一致】，但整个完整的命令不能超过 15 个字符。
-I：命令名称（可能含参数）忽略大小写。
范例一：给予 rsyslogd 这个命令启动的 PID 一个 SIGHUP 的信号。
[root@study ~]# killall -1 rsyslogd
# 如果用 ps aux 仔细看一下，若包含所有参数，则 /usr/sbin/rsyslogd -n 才是最完整的。
范例二：强制终止所有以 httpd 启动的进程（其实并没有此进程在系统内）。
[root@study ~]# killall -9 httpd
范例三：依次询问每个 bash 进程是否需要被终止运行。
[root@study ~]# killall -i -9 bash
# 具有互动的功能，可以询问你是否要删除 bash 这个进程，要注意，若没有 -i 的参数，
# 所有的 bash 都会被这个 root 给杀掉，包括 root 自己的 bash。
```
总之，要删除某个进程，我们可以使用 PID 或是启动该进程的命令名称，而如果要删除某个服务呢？呵呵，最简单的方法就是利用 killall，因为它可以将系统当中所有以某个命令名称启动的进程全部删除。举例来说，上面的范例二当中，系统内所有以 httpd 启动的进程，就会通通被删除。

## 16.3.3 关于进程的执行顺序

我们知道 Linux 是多人多任务的环境，由 top 命令的输出结果我们也发现，系统同时间有非常多的进程在运行，只是绝大部分的进程都在休眠（sleeping）状态。想一想，如果所有的进程同时被唤醒，那么 CPU 应该要先处理哪个进程呢？也就是说，哪个进程被执行的优先级比较高？这就要考虑到进程的优先级（Priority）与 CPU 调度。
>CPU 调度与前一章的计划任务并不一样。CPU 调度指的是每个进程被 CPU 运行的规则，而计划任务则是将某个进程安排在某个时间再交由系统执行。CPU 调度与操作系统较具有相关性。
### ◆ Priority 与 Nice 值

我们知道 CPU 一秒可以运行多达数 G 的指令次数，通过内核的 CPU 调度可以让各进程被 CPU 切换运行，因此每个进程在一秒钟内或多或少都会被 CPU 执行部分的指令。如果进程都是集中在一个队列中等待 CPU 的运行，而不具有优先级之分，也就是像我们去游乐场玩热门游戏需要排队一样，每个人都是照顺序来。你玩过一遍后还想再玩（没有执行完毕），请到后面继续排队等待。情况有点像下图这样：
下图中假设 pro1、pro2 是紧急的进程，pro3、pro4 是一般的进程。在这样的环境中，由于不具有优先级，唉，pro1、pro2 还是要继续等待而没有优待。如果 pro3、pro4 的任务又臭又长，那么紧急的 pro1、pro2 就要等待个老半天才能够完成，真麻烦。所以，我们要为进程分优先级。如果优先级较高则运行次数可以较多，而不需要与较低优先级的进程抢位置。我们可以将进程的优先级与 CPU 调度执行的关系用下图来解释：
![并没有优先级的进程队列示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC16%E7%AB%A0%EF%BC%9A%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86%E4%B8%8E%20SELinux%20%E5%88%9D%E6%8E%A2/%E5%B9%B6%E6%B2%A1%E6%9C%89%E4%BC%98%E5%85%88%E7%BA%A7%E7%9A%84%E8%BF%9B%E7%A8%8B%E9%98%9F%E5%88%97%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

![具有优先级的进程队列示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC16%E7%AB%A0%EF%BC%9A%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86%E4%B8%8E%20SELinux%20%E5%88%9D%E6%8E%A2/%E5%85%B7%E6%9C%89%E4%BC%98%E5%85%88%E7%BA%A7%E7%9A%84%E8%BF%9B%E7%A8%8B%E9%98%9F%E5%88%97%E7%A4%BA%E6%84%8F%E5%9B%BE.png)
如上图所示，高优先权的 pro1、pro2 可以被使用两次，而较不重要的 pro3、pro4 则运行次数较少，如此一来 pro1、pro2 就可以较快被完成。要注意，上图仅是示意图，并非较优先者一定会被运行两次。为了实现上述功能，我们 Linux 给予进程一个所谓的【优先级（priority，PRI）】，这个 **PRI 值越低代表越优先的意思。不过这个 PRI 值是由内核动态调整的，用户无法直接调整 PRI 值**。先来看看 PRI 在哪里出现。
```shell
[root@study ~]# ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD          
1 S     0 1303535 1303534  0  80   0 -  4184 do_pol pts/1    00:00:00 sudo         
4 S     0 1303536 1303535  0  80   0 -  2163 do_wai pts/1    00:00:00 bash         
4 R     0 1386829 1303536  0  80   0 -  2729 -      pts/1    00:00:00 ps
# 你应该要好奇，怎么我的 NI 已经是 10 了？还记得刚刚 top 的测试吗？我们在那边就有改过一次。
```
由于 PRI 是内核动态调整的，我们用户也无权去干涉 PRI。如果要调整进程的优先级，就要通过 nice 值了，nice 值就是上表的 NI。一般来说，PRI 与 NI 的相关性如下：
```shell
PRI（new）= PRI（old）+ nice
```
不过你要特别留意到，如果原本的 PRI 是 50，并不是我们给予一个 nice = 5，就会让 PRI 变成 55。因为 PRI 是系统【动态】决定的，所以，虽然 nice 值可以影响 PRI，但最终的 PRI 仍是要经过系统分析后才会决定的。另外，nice 值是有正负的，而既然 PRI 越小则越早被执行，所以，当 nice 值为负值时，那么该进程就会降低 PRI 值，即会变得较优先被处理。此外，你必须要留意到：
- nice 值可调整的范围为 -20 ~ 19
- root 可随意调整自己或它人进程的 nice 值，且范围为 -20~19
- 一般用户仅可调整自己进程的 nice 值，且范围仅为 0~19（避免一般用户抢占系统资源）
- 一般用户仅可将 nice 值越调越高，例如本来 nice 为 5，则未来仅能调整到大于 5
这也就是说，要调整某个进程的优先级，就是【调整该进程的 nice 值】。那么如何给予某个进程 nice 值呢？有两种方式，分别是：
- **一开始执行进程就立即给予一个特定的 nice 值，用 nice 命令**
- **调整某个已经存在的 PID 的 nice 值，用 renice 命令**
### ◆ nice：新执行的命令即给予新的 nice 值
```shell
[root@study ~]# nice [-n 数字] command
选项与参数：
-n：后面接一个数值，数值的范围-20 ~ 19。
范例一：用 root 给一个 nice 值为 -5，用于执行 vim，并查看该进程。
[root@study ~]# nice -n -5 vim &
[1] 1395878
[root@study ~]# ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD          
1 S     0 1303535 1303534  0  80   0 -  4184 do_pol pts/1    00:00:00 sudo         
4 S     0 1303536 1303535  0  80   0 -  2163 do_wai pts/1    00:00:00 bash         
4 T     0 1395878 1303536  0  75  -5 -  6180 do_sig pts/1    00:00:00 vim          
4 R     0 1396277 1303536  0  80   0 -  2729 -      pts/1    00:00:00 ps
# 原本的 bash PRI 为 90，所以 vim 默认应为 90，不过由于给予 nice 为 -5，
# 因此 vim 的 PRI 降低了，PRI 与 NI 各减 5。但不一定每次都是正好相同，因为内核会动态调整。
[root@study ~]# kill -9 %1 <==测试完毕将 vim 关闭。
```
如同前面所说，nice 用来调整进程的执行优先级，这里只是一个执行的范例罢了。通常什么时候要将 nice 值调大？举例来说，一般是系统的后台任务中，某些比较不重要的进程的运行，例如备份任务。由于备份任务相当地消耗系统资源，这个时候就可以将备份命令的 nice 值调大一些，可以使系统的资源分配得更为公平。
### ◆ renice：已存在进程的 nice 重新调整
```shell
[root@study ~]# renice [number] PID
选项与参数：
PID：某个进程的 ID。
范例一：找出自己的 bash PID，并将该 PID 的 nice 调整到 -5。
[root@study ~]# ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD          
1 S     0 1303535 1303534  0  80   0 -  4184 do_pol pts/1    00:00:00 sudo         
4 S     0 1303536 1303535  0  80   0 -  2163 do_wai pts/1    00:00:00 bash         
4 T     0 1395878 1303536  0  75  -5 -  6180 do_sig pts/1    00:00:00 vim          
4 R     0 1399278 1303536  0  80   0 -  2729 -      pts/1    00:00:00 ps
[root@study ~]# renice -5 1303535
1303535 (process ID) old priority 0, new priority -5
[root@study ~]# ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD          
1 S     0 1303535 1303534  0  75  -5 -  4184 do_pol pts/1    00:00:00 sudo         
4 S     0 1303536 1303535  0  80   0 -  2163 do_wai pts/1    00:00:00 bash         
4 T     0 1395878 1303536  0  75  -5 -  6180 do_sig pts/1    00:00:00 vim
4 R     0 1399695 1303536  0  80   0 -  2729 -      pts/1    00:00:00 ps
```
如果要调整的是已经存在的某个进程的话，就要使用 renice 了。使用的方法很简单，renice 后面接上数值及 PID 即可。因为后面接的是 PID，所以你务必要以 ps 或其他进程的查看命令去找出 PID 才行。
由上面这个范例当中我们也看得出来，虽然修改的是 bash 那个进程，但是该进程所触发的 ps 命令当中的 nice 也会继承而为 -5，了解了吧。整个 nice 值可以在父进程-->子进程之间进行传递。另外，除了 renice 之外，其实那个 top 命令同样也可以调整 nice 值。

## 16.3.4 查看系统资源信息

除了系统的进程之外，我们还必须就系统的一些资源进行检查。举例来说，我们使用 top 可以看到很多系统的资源对吧。那么，还有没有其他工具可以查看？当然有，下面这些工具命令可以玩一玩。
### ◆ free：查看内存使用情况
```shell
[root@study ~]# free [-b|-k|-m|-g|-h] [-t] [-s N -c N]
选项与参数：
-b：直接输入 free 时，显示的单位是 KBytes，我们可以使用 b（Bytes）、m（MBytes）、k（KBytes）及 g（GBytes）来显示单位，也可以直接让系统自己指定单位（-h）。
-t：在输出的最终结果，显示物理内存与 swap 的总量。
-s：可以让系统不断刷新显示数据，对于系统查看挺有效。
-c：与 -s 同时处理，让 free 列出几次的意思。
范例一：显示目前系统的内存容量。
[root@study ~]# free -m
               total        used        free      shared  buff/cache   available
Mem:            3659         561        2039           2        1301        3098   
Swap:           1987           0        1987
```
仔细看看，我的系统当中有 2848MB 左右的物理内存，我的 swap 有 1GB 左右，那我使用 free -m 以 MBytes 来显示时，就会出现上面的信息。Mem 那一行显示的是物理内存的量，Swap 则是内存交换分区的量。total 是总量，used 是已被使用的量，free 则是剩余可用的量。后面的 shared、buffers、cached 则是在已被用的量当中，用来作为缓冲及缓存的。这些 shared、buffers、cached 的使用量中，在系统比较忙碌时，可以被发布而继续利用。因此后面就有一个 available（可用的）数值。
请看上面范例一的输出，我们可以发现这台测试机根本没有什么特别的服务，但是竟然有 706MB 左右的 cache，因为鸟哥在测试过程中还是读、写、执行了很多的文件嘛。这些文件就会被系统暂时缓存下来，等待下次运行时可以更快速地取出。也就是说，系统是【很有效率地将使所有的内存用光光】，目的是为了让系统的读写性能加速。
很多朋友都会问到这个问题【我的系统明明很轻松，为何内存会被用光光？】现在了解了吧？被用光是正常的，而需要注意的反而是 swap 的量。一般来说，swap 最好不要被使用，尤其 swap 最好不要被使用超过 20% 以上。如果您发现 swap 的用量超过 20%，那么，最好增加物理内存。因为，swap 的性能跟物理内存实在差很多，而系统会使用到 swap，绝对是因为物理内存不足了才会这样做的。如此，了解了吧。
>Linux 系统为了提高系统性能，会将最常用的活是最近使用到的文件数据缓存（cache）下来，这样未来系统要使用该文件时，就可以直接由内存中查找取出，而不需要重新读取硬盘，速度上面当然就加快了。因此，物理内存被用光是正常的。

### ◆ uname：查看系统与内核相关信息

```shell
[root@study ~]# uname [-asrmpi]
选项与参数：
-a：所有系统相关的信息，包括下面的数据都会被列出来。
-s：系统内核名称。
-r：内核的版本。
-m：本系统的硬件架构，例如 i686 或 x86-64 等。
-p：CPU 的类型，与 -m 类似，只是显示的是 CPU 的类型。
-i：硬件的平台（x86）。
范例一：输出系统的基本信息。
[root@study ~]# uname -a
Linux VM-0-4-ubuntu 6.8.0-101-generic #101-Ubuntu SMP PREEMPT_DYNAMIC Mon Feb  9 10:15:05 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
```
这个东西我们前面使用过很多次了。uname 可以列出目前系统的内核版本、硬件结构以及 CPU 类型等信息。

### ◆ uptime：查看系统启动时间与任务负载

这个命令很单纯，就是显示出目前系统已经运行的时间，以及 1、5、15 分钟内的平均负载情况。还记得 top 吧？没错，这个 uptime 可以显示出 top 界面的最上面一行。
```shell
[root@study ~]# uptime
21:30:44 up 4 days,  7:08,  1 user,  load average: 0.00, 0.01, 0.00
# top 这个命令已经谈过相关信息，不再聊。
```

### ◆ netstat：追踪网络或 socket 文件

netstat 也是挺好玩的，其实这个命令经常被用在网络监控方面。不过，在进程管理方面也是需要了解的。基本上，netstat 的输出分为两大部分，分别是网络与系统自己的进程相关性部分。这个命令的执行如下所示：
```shell
[root@study ~]# netstat -[atunlp]
选项与参数：
-a：将目前系统上所有的连接、监听、socket 信息都列出来。
-t：列出 tcp 网络封包的信息。
-u：列出 udp 网络封包的信息。
-n：不以进程的服务名称，以端口号（port number）来显示。
-l：列出目前正在网络监听（listen）的服务。
-p：列出该网络服务的进程 PID。
范例一：列出目前系统已经建立的网络连接与 unix socket 状态。
[root@study ~]# netstat
Active Internet connections (w/o servers)                                          
Proto Recv-Q Send-Q Local Address           Foreign Address         State          
tcp        0      0 VM-0-4-ubuntu:ssh       115.190.52.176:60176    ESTABLISHED    
tcp        0      0 VM-0-4-ubuntu:ssh       115.190.52.176:49206    ESTABLISHED    
tcp        0      0 VM-0-4-ubuntu:ssh       115.190.52.176:60172    ESTABLISHED    
tcp        0      0 VM-0-4-ubuntu:36440     169.254.0.55:5574       ESTABLISHED    
tcp        0      0 VM-0-4-ubuntu:35280     169.254.0.138:8186      ESTABLISHED
Active UNIX domain sockets (w/o servers)                                           
Proto RefCnt Flags       Type       State         I-Node   Path                    
unix  3      [ ]         SEQPACKET  CONNECTED     15371                            
unix  3      [ ]         STREAM     CONNECTED     10894    /run/dbus/system_bus_socket
```
上面的结果显示了两个部分，分别是网络的连接以及 Linux 上面的 socket 进程相关性部分。我们先来看看因特网连接情况的部分：
- Proto：网络的封包协议，主要分为 TCP 与 UDP 封包，相关数据请参考服务器篇。
- Recv-Q：非由用户进程连接到此 socket 的复制的总 Bytes 数
- Send-Q：非由远程主机传送过来的 acknowledged 总 Byte 数
- Local Address：本地端的 IP:port 情况
- Foreign Address：远程主机的 IP:port 情况
- State：连接状态，主要有建立（ESTABLISED）及监听（LISTEN）
我们看上面仅有一条连接的数据，它的意义是：【通过 TCP 封包的连接，远程的 172.16.220.234:48300 连接到本地端的 172.16.15.100:ssh，这条连接状态是建立（ESTABLISHED）的状态】，至于更多的网络环境说明，就要到鸟哥的另一本书服务器篇查看。
除了网络上的连接之外，Linux 系统上面的进程还可以接收不同进程所发送来的信息，那就是 Linux 上面的 socket 文件（socket file）。我们在第 5 章的文件种类曾提到过 socket 文件，但当时未谈到进程的概念，所有没有深入讨论。socket 文件可以沟通两个进程之间的信息，因此进程可以获取对方传送过来的数据。由于有 socket 文件，因此类似 X Window 这种需要通过网络连接的软件，目前新的发行版就以 socket 来进行窗口接口的连接沟通了。上表中 socket 文件的输出字段有：
- Proto：一般就是 unix
- RefCnt：连接到此 socket 的进程数量
- Flags：连接的标识
- Type：socket 存取的类型。主要有确认连接的 STREAM 与不需确认的 DGRAM 两种
- State：若为 CONNECTED 则表示多个进程之间已经建立连接
- Path：连接到此 socket 的相关进程的路径，或是相关数据输出的路径
以上表的输出为例，最后那三行在 /tmp/.xx 下面的数据，就是 X Window 图形界面的相关进程。而 PATH 指向的就是这些进程要交换数据的 socket 文件。好，那么 netstat 可以帮我们执行什么任务呢？很多，我们先来看看，利用 netstat 去看看我们的哪些进程启动了哪些网络【后门】呢？
```shell
范例二：找出目前系统上已在监听的网络连接及其 PID。
[root@study ~]# netstat -tulnp
Active Internet connections (only servers)                                         
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name                                                                   
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      521/systemd-resolve                                                                
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      521/systemd-resolve                                                                
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1/init                                                                             
tcp6       0      0 :::22                   :::*                    LISTEN      1/init                                                                             
udp        0      0 127.0.0.54:53           0.0.0.0:*                           521/systemd-resolve                                                                
udp        0      0 127.0.0.53:53           0.0.0.0:*                           521/systemd-resolve                                                                
udp        0      0 10.4.0.4:68             0.0.0.0:*                           4481/systemd-networ                                                                
udp        0      0 127.0.0.1:323           0.0.0.0:*                           5792/chronyd                                                                       
udp6       0      0 ::1:323                 :::*                                5792/chronyd
# 除了可以列出监听网络的界面与状态之外，最后一个栏位还能够显示此服务的
# PID 号码以及进程的命令名称，例如上面的 1326 就是该 PID。
范例三：将上述的 0.0.0.0:57808 那个网络服务关闭的话？
[root@study ~]# kill -9 743
[root@study ~]# killall -9 avahi-daemon
```
很多朋友常常有疑问，那就是，我的主机目前到底开了几个门（ports）。其实，不论主机提供什么样的服务，一定要有相对应的程序在主机上面执行才行。举例来说，我们鸟园的 Linux 主机提供的就是 WWW 服务，那么我的主机当然有一个进程在提供 WWW 的服务。那就是 Apache 这个软件所提供的。所以，当我执行了这个程序之后，我的系统自然就可以提供 WWW 的服务了。那如何关闭？关闭该程序所触发的那个进程就好了，例如上面的范例三所提供的例子。不过，这个是非正规的做法，正规的做法请查看下一章的说明。

### ◆ dmesg：分析内核产生的信息

系统在启动的时候，内核会去检测系统的硬件，你的某些硬件到底有没有被识别，就与这个时候的检测有关。但是这些检测的过程不是没有显示在屏幕上，就是在屏幕上一闪而逝。能不能把内核检测的信息识别出来看看？可以使用 dmesg。
不管是启动的时候还是系统运行过程中，只要是内核产生的信息，都会被记录到内存的某个保护区域中。dmesg 这个命令就能够在该区域的信息读出来。因为信息实在太多了，所以执行时可以加入这个管道命令【| more】来使界面暂停。
```shell
范例一：输出所有的内核启动时的信息。
[root@study ~]# dmesg | more
范例二：查找启动的时候，硬盘的相关信息是什么？
[root@study ~]# dmesg | grep -i vda
[    0.512955] virtio_blk virtio1: [vda] 125829120 512-byte logical blocks (64.4 GB/60.0 GiB)                                                                       
[    0.533482]  vda: vda1 vda2                                                     
[    2.131880] EXT4-fs (vda2): mounted filesystem 9842d3d6-a839-4127-bda7-f19137effe71 ro with ordered data mode. Quota mode: none.                          
[    3.680550] EXT4-fs (vda2): re-mounted 9842d3d6-a839-4127-bda7-f19137effe71 r/w.                                                                               
[    8.948914] EXT4-fs (vda2): resizing filesystem from 2620672 to 15728123 blocks 
[   10.059765] EXT4-fs (vda2): resized filesystem to 15728123
```
由范例二就知道我这台主机的硬盘是什么格式了。

### ◆ vmstat：检测系统资源变化

如果你想要动态地了解一下系统资源的运行，那么这个 vmstat 确实可以玩一玩。vmstat 可以检测【CPU/内存/磁盘 I/O 状态】等，如果你想要了解一个繁忙的系统到底是哪个环节最累人，可以使用 vmstat 分析看看。下面是常见的选项与参数说明：
```shell
[root@study ~]# vmstat [-a] [延迟[总计检测次数]] <==CPU/内存等信息
[root@study ~]# vmstat [-fs] <==内存相关
[root@study ~]# vmstat [-S 单位] <==设置显示数据的单位
[root@study ~]# vmstat [-d] <==与磁盘有关
[root@study ~]# vmstat [-p 分区] <==与磁盘有关
选项与参数：
-a：使用 inactive/active（活动与否）替换 buffer/cache 的内存输出信息。
-f：开机到目前为止，系统复制（fork）的进程数。
-s：将一些事件（启动至目前为止）导致的内存变化情况列表说明
-S：后面可以接单位，让显示的数据有单位，例如 K/M 替换 Bytes 的容量
-d：列出磁盘的读写总量统计表
-p：后面列出分区，可显示该分区的读写总量统计表
范例一：统计目前主机 CPU 状态，每秒一次，共计三次
[root@study ~]# vmstat 1 3
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu------- 
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu                                                                                 
 1  0      0 2016308 105472 1298832    0    0     1    56  527    5  1  0 99  0  0  0                                                                                 
 0  0      0 2016308 105472 1298872    0    0     0     0  756 1515  1  1 98  0  0  0                                                                                 
 0  0      0 2016308 105472 1298872    0    0     0     0  441  796  1  1 99  0  0  0
```
利用 vmstat 甚至可以执行追踪。你可以使用类似【vmstat 5】代表每 5 秒钟更新一次，且无穷地更新。直到你按下[ctrl]-c 为止。如果你想要实时地知道系统资源地运行状态，这个命令就必须知道。那么上面的表格各项字段的意义是什么？基本说明如下：
- 进程字段（procs）的项目分别为：
r：等待运行中的进程数量
b：不可被唤醒的进程数量
这两个项目越多，代表系统越忙碌（因为系统太忙，所以很多进程就无法被执行或一直在等待二无法被唤醒之故）。
- 内存字段（memory）项目分别为：
swpd：虚拟内存被使用的容量
free：未被使用的内存容量
buff：用于缓冲存储器
cache：用于高速缓存
这部分则与 free 命令是相同的
- 内存交换分区（swap）的项目分别为：
si：由磁盘中将进程取出的容量
so：由于内存不足而将没用的进程写入到磁盘的 swap 的容量
如果 si/so 的数值太大，表示内存中的数据常常得再磁盘与内存之间传输，系统性能会很差
- 磁盘读写（I/O）的项目分别为：
bi：由磁盘读入的区块数量
bo：写入到磁盘中的区块数量
这部分的值越高，代表系统的 I/O 越忙碌
- 系统（system）的项目分别为：
in：每秒被中断的进程次数
cs：每秒执行的事件切换次数
这两个数值越大，代表系统与外接设备的沟通越频繁。这些接口设备包括磁盘、网卡等。
- CPU 的项目分别为：
us：非内核层的 CPU 使用状态
sy：内核层所使用的 CPU 状态
id：闲置的状态
wa：等待 I/O 所耗费的 CPU 状态
st：被虚拟机（virtual machine）所使用的 CPU 状态（2.6.11 以后才支持
由于鸟哥的机器是测试机，所以并没有什么 I/O 或是 CPU 忙碌的情况。如果改天你的服务器非常忙碌时，记得使用 vmstat 去看看，到底是哪个部分的资源被使用的最为频繁。一般来说，如果 I/O 部分很忙碌的话，你的系统会变得非常慢。让我们再来看看，磁盘的部分该如何查看：
```shell
范例二：系统上面所有的磁盘的读写状态
[root@study ~]# vmstat -d
disk- ------------reads------------ ------------writes----------- -----IO------    
       total merged sectors      ms  total merged sectors      ms    cur    sec    
loop0     11      0      28       1      0      0       0       0      0      0    
loop1      0      0       0       0      0      0       0       0      0      0    
loop2      0      0       0       0      0      0       0       0      0      0    
loop3      0      0       0       0      0      0       0       0      0      0    
loop4      0      0       0       0      0      0       0       0      0      0    
loop5      0      0       0       0      0      0       0       0      0      0    
loop6      0      0       0       0      0      0       0       0      0      0    
loop7      0      0       0       0      0      0       0       0      0      0    
vda    17666   4267 1255638   15291 2080008 2370130 48588672 1488550      0    718 
sr0     4149      0  292592    1170      0      0       0       0      0      1
```
详细的各字段就请诸位查看一下 man vmstat，反正与读写有关，这样了解了吗？


































































