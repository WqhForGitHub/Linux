从 CentOS 7.x 开始，传统的 init 已经被舍弃，取而代之的是 systemd，这家伙跟之前的 init 有什么差异？优缺点是什么？如何管理不同种类的服务类型？以及如何替换原本的【运行级别】等，都是很重要的改变。

# 17.1 什么是 daemon 与服务（service）

我们在第 16 章就曾经谈过服务这东西。当时的说明是：常驻内存中的进程且可以提供一些系统或网络功能，那就是服务，而服务一般的英文说法是 service。
但如果你常常上网去查看一些数据的话，尤其是 UNIX-like 的相关操作系统，应该常常看到请启动某某 daemon 来提供某某功能，那么 daemon 与 service 有关？否则为什么都能够提供某些系统或网络功能？此外，这个 daemon 是什么东西？daemon 的字面上的意思就是守护神、恶魔还真实有点奇怪。
简单地说，系统为了某些功能必须要提供一些服务（不论是系统本身还是网络方面），这个服务就称为 service。但是 service 的提供总是需要程序的运行吧，否则如何执行？所以完成这个 service 的程序我们就称呼它为 daemon。举例来说，完成周期性计划任务服务（service）的程序为 crond 这个 daemon。这样说比较容易理解了吧。
>你不必去区分什么是 daemon 与 service。事实上，你可以将这两者视为相同的东西。因为完成某个服务需要一个 daemon 在后台中运行，没有这个 daemon 就不会有 service，所以不需要分得太清楚。

一般来说，当我们以命令行或图形模式（非单人维护模式）完整启动进入 Linux 主机后，操作系统已经提供了很多的服务，包括打印服务、计划任务服务、邮件管理服务等。那么这些服务是如何被启动的呢？它们的工作状态如何？下面我们就来谈一谈。
>daemon 既然是一个程序执行后的进程，那么 daemon 所处的那个原本的程序通常是如何命令的（daemon 程序的命名方式）呢？每一个服务的开发者，在开发他们的服务时，都有特别的故事。不过，无论如何，这些服务的名称被建立之后，在 Linux 中使用时，通常在服务的名称之后会加上一个 d，例如计划任务命令建立的 at 与 cron 这两个服务，它的程序名会被取为 atd 与 crond，这个 d 代表的就是 daemon 的意思。所以，在第 16 章中，我们使用了 ps 与 top 来查看进程时，都会发现到很多的{xxx}d 的进程，通常那就是一些 daemon 的进程。

# 17.2 通过 systemctl 管理服务

基本上，systemd 这个启动服务的机制，主要是通过一个名为 sysytemctl 的命令来完成。跟以前 System V 需要 service、chkconfig、setup、init 等命令来协助不同，systemd 只有 systenctl 这个命令来处理而已，所以全部的操作都得要使用 systemctl。会不会很难？其实习惯了之后，鸟哥觉得 systemctl 还挺好用的。

## 17.2.1 通过 systemctl 管理单一服务（service unit）的启动/开机启动与查看状态

在开始本小节之前，鸟哥要先来跟大家报告一下，那就是：**一般来说服务的启动有两个阶段，一个是【开机的时候设置要不要启动这个服务】，以及【你现在要不要启动这个服务】**，这两者之间有很多的差异。举个例子，假如我们现在要【立刻停止 atd 这个服务】时，正确的方法（不要用 kill）要怎么处理？
```shell
[root@study ~]# systemctl [command] [unit]
command 只要有：
start：立刻启动后面接的 unit。
stop：立刻关闭后面接的 unit。
restart：立刻重新启动后面接的 unit，亦即执行 stop 再 start 的意思。
reload：不关闭后面接的 unit 的情况下，重新加载配置文件，让设置生效。
enable：设置下次开机时，后面接的 unit 会被启动。
disable：设置下次开机时，后面接的 unit 不会被启动。
status：目前后面接的这个 unit 的状态，会列出有没有正在执行、开机默认执行与否、登录等信息等。
is-active：目前有没有正在运行中。
is-enable：开机时有没有默认要启用这个 unit。
范例一：看看目前 atd 这个服务的状态是什么。
[root@study ~]# systemctl status atd.service
● atd.service - Deferred execution scheduler                                       
     Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; preset: enabled)                                                                           
     Active: active (running) since Fri 2026-05-15 21:51:18 CST; 2 weeks 2 days ago                                                                                
       Docs: man:atd(8)                                                            
   Main PID: 110200 (atd)                                                          
      Tasks: 1 (limit: 4296)                                                       
     Memory: 164.0K (peak: 1.5M swap: 104.0K swap peak: 104.0K)                    
        CPU: 49ms                                                                  
     CGroup: /system.slice/atd.service                                             
             └─110200 /usr/sbin/atd -f                                             
Notice: journal has been rotated since unit was started, output may be incomplete.
# 重点在第二、三行，
# Loaded：这行在说明，开机的时候这个 unit 会不会启动，enabled 为开机启动，disabled 开机不会启动。
# Active：现在这个 unit 的状态是正在执行（running）或没有执行（dead）。
# 后面几行则是说明这个 unit 程序的 PID 状态以及最后一行显示这个服务的日志文件信息。
# 日志文件信息格式为：【时间】【信息发送主机】【哪一个服务的信息】【实际信息内容】
# 所以上面的显示信息是：这个 atd 默认开机就启动，而且现在正在运行的意思。
范例二：正常关闭这个 atd 服务。
[root@study ~]# systemctl stop atd.service
○ atd.service - Deferred execution scheduler                                       
     Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled; preset: enabled)                                                                           
     Active: inactive (dead) since Mon 2026-06-01 18:00:43 CST; 3s ago             
   Duration: 2w 2d 20h 9min 24.290s                                                
       Docs: man:atd(8)                                                            
    Process: 110200 ExecStart=/usr/sbin/atd -f (code=exited, status=0/SUCCESS)     
   Main PID: 110200 (code=exited, status=0/SUCCESS)                                
        CPU: 49ms                                                                  
Jun 01 18:00:43 VM-0-4-ubuntu systemd[1]: Stopping atd.service - Deferred execution scheduler...                                                             
Jun 01 18:00:43 VM-0-4-ubuntu systemd[1]: atd.service: Deactivated successfully.   
Jun 01 18:00:43 VM-0-4-ubuntu systemd[1]: Stopped atd.service - Deferred execution scheduler.                                                                         
Notice: journal has been rotated since unit was started, output may be incomplete.
# 目前这个 unit 下次开机还是会启动，但是现在处于关闭状态中。同时，
# 最后两行为新增加的登录信息，告诉我们目前的系统状态。
```
上面的范例中，我们已经关闭了 atd，这样做才是对的。不应该使用 kill 的方式来关闭一个正常的服务。否则 systemctl 会无法继续监控该服务，那就比较麻烦了。而使用 systemctl status atd 的输出结果中，第 2、3 两行很重要，因为那个是告知我们该 unit 下次开机会不会默认启动，以及目前启动的状态，相当重要。最下面是这个 unit 的日志文件，如果你的这个 unit 曾经出错过，查看这个地方也是相当重要的。
那么现在问个问题，你的 atd 现在是关闭的，未来重新启动后，这个服务会不会再次启动？答案是当然会。因为上面出现的第 2 行中，它是 enabled，这样理解所谓的【现在的状态】跟【开机时默认的状态】两者的差异了吗？
好，再回到 systemctl status atd.service 的第 3 行，不是有个 Active 的 daemon 现在状态吗？除了 running 跟 dead 之外，有没有其他的状态？有的，基本上有如下几个常见的状态。
- active（running）：正有一个或多个进程正在系统中运行的意思，举例来说，正在运行中的 vsftpd 就是这种模式。
- active（exited）：仅执行一次就正常结束的服务，目前并没有任何进程在系统中执行。举例来说，开机或是挂载时才会执行一次的 quotaon 功能，就是这种模式。quotaon 不需一直运行，只需执行一次之后，就交给文件系统去自行处理。通常用 bash shell 写的小型服务，大多是属于这种类型（无须常驻内存）。
- active（waiting）：正在运行当中，不过还需等待其他的事件发生才能继续运行。举例来说，打印的队列相关服务就是这种状态。虽然正在启动中，不过，也需要真的有队列进来（打印作业）这样它才会继续唤醒打印机服务来进行下一步的打印功能。
- inactive：这个服务目前没有运行的意思。
既然 daemon 目前的状态就有这么多种了，那么 daemon 的默认状态有没有可能除了 enable/disable 之外，还有其他的情况？当然有。
- enabled：这个 daemon 将在开机时被运行。
- disabled：这个 daemon 在开机时不会被运行。
- static：这个 daemon 不可以自己启动（不可 enable），不过可能会被其他的 enabled 的服务来唤醒（依赖属性的服务）。
- mask：这个 daemon 无论如何都无法被启动，因为已经被强制注销（非删除）。可通过 systemctl unmask 方式改回默认状态。
## 17.2.2 通过 systemctl 查看系统上所有的服务

上一小节谈到的是单一服务的启动、关闭、查看，以及依赖服务要注销的功能。那系统上面有多少的服务存在？这个时候就得要通过 list-units 及 list-unit-files 来查看。详细用法如下：
```shell
[root@study ~]# systemctl [command] [--type=TYPE] [--all]
command：
	list-units：依据 unit 显示目前有启动的 unit，若加上 --all 才会列出没启动的
	list-unit-files：依据 /usr/lib/systemd/system/内的文件，将所有文件列表说明。
--type=TYPE：就是之前提到的 unit 类型，主要有 service、socket、target 等。
范例一：列出系统上面有启动的 unit。
[root@study ~]# systemctl
 UNIT  LOAD   ACTIVE SUB       DESCRIPTION
  proc-sys-fs-binfmt_misc.automount                                                                               loaded active running   Arbitrary Executable File Formats File System Automount Point
  sys-devices-pci0000:00-0000:00:01.1-ata1-host0-target0:0:1-0:0:1:0-block-sr0.device                             loaded active plugged   QEMU_DVD-ROM config-2                                                                                  
  sys-devices-pci0000:00-0000:00:05.0-virtio0-net-eth0.device                                                     loaded active plugged   Virtio network device       
  sys-devices-pci0000:00-0000:00:06.0-virtio1-block-vda-vda1.device                                               loaded active plugged   /sys/devices/pci0000:00/0000:00:06.0/virtio1/block/vda/vda1                        
  sys-devices-pci0000:00-0000:00:06.0-virtio1-block-vda-vda2.device                                               loaded active plugged   /sys/devices/pci0000:00/0000:00:06.0/virtio1/block/vda/vda2                        
  sys-devices-pci0000:00-0000:00:06.0-virtio1-block-vda.device                                               loaded active plugged   /sys/devices/pci0000:00/0000:00:06.0/virtio1/block/vda
Legend: LOAD   → Reflects whether the unit definition was properly loaded.         
        ACTIVE → The high-level unit activation state, i.e. generalization of SUB. 
        SUB    → The low-level unit activation state, values depend on unit type. 
241 loaded units listed. Pass --all to see loaded but inactive units, too.         
To show all installed unit files use 'systemctl list-unit-files'.
# 列出的项目中，主要的意义是：
# UNIT：项目的名称，包括各 unit 的类别（看副文件名）。
# LOAD：开机时是否会被加载，默认 systemctl 显示的是有加载的项目而已。
# ACTIVE：目前的状态，须与后续的 SUB 搭配，就是我们用 systemctl status 查看时，active 的项目。
# DESCRIPTION：详细描述。
# cups 比较有趣，因为刚刚被我们玩过，所以 ACTIVE 竟然是 failed 的，被玩死了。
# 另外，systemctl 都不加参数，其实默认就是 list-units 的意思。                                                              
范例二：列出所有已经安装的 unit 有哪些。
[root@study ~]# systemctl list-unit-files
```
使用 systemctl list-unit-files 会将系统上所有的服务通通显示出来，而不像 list-units 仅以 unit 分类作大致的说明。至于 STATE 状态就是前两个小节谈到的开机是否会加载的那个状态项目。主要有 enabled、disabled、mask、static 等。
假设我不想要知道这么多的 unit 项目，我只想要知道 service 这种类别的 daemon 而已，而且不论是否已经启动，通通要显示出来。那该如何是好？
```shell
[root@study ~]# systemctl list-units --type=service --all
# 只剩下 *.service 的项目才会出现。
范例一：查询系统上是否有以 cpu 为名的服务。
[root@study ~]# systemctl list-units --type=service --all | grep cpu
```




















