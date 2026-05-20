# 10.1 认识 BASH 这个 Shell

我们在第 1 章中提到了：管理整个计算机硬件的其实是操作系统的内核（kernel），这个内核是需要被保护的。所以我们一般用户就只能通过 Shell 来跟内核沟通，以让内核完成我们所想要实现的任务。那么系统有多少 Shell 可用呢？为什么我们要使用 bash？下面分别来谈一谈。

## 10.1.1 硬件、内核与 Shell

这应该是个蛮有趣的话题：什么是 Shell？相信只要摸过计算机，对于操作系统（不论是 Linux、UNIX 或是 Windows）有点概念的朋友们大多听过这个名词，因为只要有操作系统那么就离不开 Shell 这个东西。不过，在讨论 Shell 之前，我们先来了解一下计算机的运行情况吧。举个例子来说：**当你要计算机播放出来音乐的时候，你的计算机需要什么东西？**
1. 硬件：当然就是需要你的硬件有声卡这个设备，否则怎么会有声音
2. 内核管理：操作系统的内核可以支持这个芯片组，当然还需要提供芯片的驱动程序
3. 应用程序：需要用户（就是你）输入发生声音的命令
这就是基本的一个输出声音所需要的步骤，也就是说，你必须要输入一个命令之后，硬件才会通过你执行的命令来工作。那么硬件如何知道你执行的命令？那就是内核（kernel）的管理工作了，也就是说，**我们必须要通过 Shell 将我们输入的命令与内核沟通，好让内核可以控制硬件来正确无误地工作**。基本上，我们可以通过下图来说明下：
![硬件、内核与用户的相关性图例](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC10%E7%AB%A0%EF%BC%9A%E8%AE%A4%E8%AF%86%E4%B8%8E%E5%AD%A6%E4%B9%A0BASH/%E7%A1%AC%E4%BB%B6%E3%80%81%E5%86%85%E6%A0%B8%E4%B8%8E%E7%94%A8%E6%88%B7%E7%9A%84%E7%9B%B8%E5%85%B3%E6%80%A7%E5%9B%BE%E4%BE%8B.png)
我们在第 0 章内的操作系统小节曾经提到过，**操作系统其实是一组软件，由于这组软件在控制整个硬件与管理系统的活动监测。如果这组软件能被用户随意操作，若用户应用不当，将会使得整个系统崩溃**。因为操作系统管理的就是整个硬件功能嘛，所以当然不能够随便被一些没有管理能力的终端用户随意使用。
但是我们总是需要让用户使用操作系统的，所以就有了在操作系统上面发展的应用程序。用户可以通过应用程序来指挥内核，让内核完成我们所需要的硬件任务。如果考虑如第零章所提供的操作系统图例，我们可以发现应用程序其实是在最外层，就如同鸡蛋的外壳一样，因此这个东西也就被称呼为壳程序（shell）。
其实壳程序的功能只是提供用户操作系统的一个界面，因此这个壳程序需要可以调用其他软件才好。我们在第 4 章到第 9 章提到过很多命令，包括 man、chmod、chown、vi、fdisk、mkfs 等命令，这些命令都是独立的应用程序，但是我们可以通过壳程序（就是命令行模式）来操作这些应用程序，让这些应用程序调用内核来执行所需的任务，这样对于壳程序是否有了一定的概念了？
>也就是说，只要能够操作应用程序的软件都能够称为壳程序。狭义的壳程序指的是命令行方面的软件，包括本章要介绍的 bash 等，广义的壳程序则包括图形用户界面模式的软件，因为图形用户界面模式其实也能够操作各种应用程序来调用内核工作，不过在本章中，我们主要还是在使用 bash。

## 10.1.2 为何要学命令行模式的 Shell？

**命令行模式的 shell 是很不好学的，但是学了之后好处多多**。所以，在这里鸟哥要先对您进行一些心理建设，先来了解一下为啥学习 shell 是有好处的，这样你才会有信心继续玩下去。

### ◆ 命令行模式的 shell：大家都一样

鸟哥常常听到这个问题：【我干嘛要学习 shell？不是已经有很多的工具可以提供我设置我的主机了吗？我为何要花这么多时间去学命令？不是以 X Window 按一按几个按钮就可以搞定了吗？】唉，还是得一再地强调，X Window 还有 Web 接口的设置工具例如 Webmin 是真的好用的家伙，它真的可以帮助我们很简单地设置好我们的主机，甚至是一些很高级的设置都可以帮我们搞定。
但是鸟哥在前面章节里面也已经提到过相当多次了，X Window 与 Web 接口的工具，它的界面虽然易用，功能虽然强大，但毕竟它是将所有利用到的软件都整合在一起的一组应用程序而已，并非是一个完整的应用程序，所以某些时候当你升级或是使用其他程序管理模块（例如 tarball 而非 rpm 文件等）时，就会造成设置的困扰。甚至不同的 Linux 发行版所设计的 X Window 界面也都不相同，这样也造成学习方面的困扰。
命令行模式的 shell  就不同了，几乎各家 Linux 发行版使用的 bash 都是一样的。如此一来，你就能够轻轻松松的转换不同的 Linux 发行版，就像武侠小说里面提到的【一法通，万法通。】

### ◆ 远程管理：命令行模式就是比较快

此外，Linux 的管理常常需要通过远程联机。而联机时**命令行模式的传输速度一定比较快，而且，较不容易出现掉线或是信息外流的问题**，因此，shell 真的是得学习的一项工具。而且它可以让您更深入 Linux，更了解它，而不是只会按一按鼠标而已，所谓【天助自助者】多摸一点命令行模式的东西，会让你与 Linux 更亲近。

### ◆ Linux 的任督二脉：shell 是也

有些朋友也很可爱，常会说：我学这么多干什么？又不常用，也用不到，嘿嘿，有没有听过【书到用时方恨少？】当你的主机一切安然无恙的时候，您当然会觉得好像学这么多的东西一点帮助也没有。万一，某一天真的不幸它被黑了，您该如何是好？是直接重新安装？还是先追踪入侵来源后进行漏洞的修补？或是干脆就关站好了？这当然涉及很多的考虑，但以鸟哥的观点来看，多学一点总是好的，尤其我们可以有备而无患嘛。甚至学得不精也没有关系，了解概念也就 OK，毕竟没有人要您一定要背这么多的内容，了解概念就很了不起了。
此外，**如果你真的有心想要将您的主机管理得好，那么良好的 shell 程序编写是一定需要的**。就鸟哥自己来说，鸟哥管理的主机虽然还不算多，只有区区不到 10 台，但是如果每台主机都要花上几十分钟来查看它的日志文件信息以及相关的信息，那么鸟哥可能会疯掉。基本上，也太没有效率了。这个时候，如果能够借由 shell 提供的数据流重定向以及管道命令，呵呵，那么鸟哥分析登录信息只要花费不到 10 分钟就可以看完所有的主机之重要信息了，相当好用。
由于学习 shell 的好处真的是多多。所以，如果你是个系统管理员，或有心想要管理系统的话，那么 shell 与 shell 脚本这个东西真的有必要看一看，因为它就像打通任督二脉，任何武功都能随你应用。
## 10.1.3 系统的合法 shell 与 /etc/shells 功能

知道什么是 shell 之后，那么我们来了解一下 Linux 使用的是哪一个 shell？什么，哪一个？难道说 shell 不就是一个 shell 吗？哈哈，那可不，在早年的 UNIX 年代发展者众多，所以 shell 依据发展者的不同就有许多版本，例如常听到的 Bourne shell（sh）、在 Sun 里面默认的 C shell、商业上常用的 K shell，还有 TCSH 等，每一种 Shell 都各有其特点。至于 Linux 使用的这一种版本就称为【Bourne Again Shell（简称 bash）】，这个 shell 是 Bourne shell 的增强版本，也是基准于 GNU 的架构下发展出来的呦。
在介绍 shell 的优点之前，先来说一说 shell 的简单历史吧：第一个流行的 shell 是由 Steven Bourne 发展出来的，为了纪念它所以就称为 Bourne shell，或直接简称为 sh，而后来另一个广为流传的 shell 是由伯克利大学的 Bill Joy 设计依附于 BSD 版的 UNIX 系统中的 shell，这个 shell 的语法有点类似 C 语言，所以才得名为 C shell，简称为 csh，由于在学术界 Sun 主机势力相当庞大，而 Sun 是主要的 UNIX 分支之一，所以 C shell 也是另一个很重要而且流传很广的 shell 之一。
>由于 Linux 由 C 语言编写，很多程序员使用 C 来开发软件，因此 C shell 相对的就很热门了。另外，还记得我们在第 1 章提到的吧？Sun 公司的创始人就是 Bill Joy，而 BSD 最早就是 Bill Joy 发展出来的。

那么目前我们的 Linux（以 CentOS 7.x 为例）有多少我们可以使用的 shells 呢？你可以检查一下 /etc/shells 这个文件，至少就有下面这几个可以用的 shells（鸟哥省略了重复的 shell 了，包括 /bin/sh 等于 /usr/bin/sh）：
- /bin/sh（已经被 /bin/bash 所替换）
- /bin/bash（就是 Linux 默认的 shell）
- /bin/tcsh（整合 C Shell，提供更多的功能）
- /bin/csh（已经被 /bin/tcsh 所替换）
虽然各家 shell 的功能都差不多，但是在某些语法的执行方面则有所不同，因此建议你还是得要选择某一种 shell 来熟悉一下较佳。Linux 默认就是使用 bash，所以最初你只要学会 bash 就非常了不起了。另外，咦？**为什么我们系统上合法的 shell 要写入 /etc/shells 这个文件？** 这是因为系统某些服务在运行过程中，会去检查用户能够使用的 shells，而这些 shell 的查询就是借由 /etc/shells 这个文件。
举例来说，某些 FTP 网站会去检查用户的可用 shell，而如果你不想要让这些用户使用 FTP 以外的主机资源时，可能会给予该用户一些怪怪的 shell，让用户无法以其他服务登录主机，这个时候，你就得将那些怪怪的 shell 写到 /etc/shells 当中了。举例来说，我们的 CentOS 7.x 的 /etc/shells 里面就有个 /sbin/nologin 文件的存在，这个就是我们说的怪怪的 shell。
那么，再想一想，我这个用户什么时候可以取得 shell 来工作？还有，我这个用户默认会取得哪一个 shell？还记得我们在第 4 章的在终端界面登录 Linux 小节当中提到的登录操作吧？当我登录的时候，系统就会给我一个 shell 让我来工作，而这个登录取得的 shell 就记录在 /etc/passwd 这个文件内，这个文件的内容是啥？
```shell
[dmtsai@study ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash                                                    
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
```
如上所示，在每一行的最后一个数据，就是你登录后可以取得的默认的 shell。那你也会看到，root 是 /bin/bash，不过系统账号 bin 于 daemon 等，就使用那个怪怪的 /sbin/nologin，关于用户这部分的内容，我们留在第 13 章的账号管理时提供更多的说明。

## 10.1.4 Bash shell 的功能

既然 /bin/bash 是 Linux 默认的 shell，那么总是得了解一下这个玩意儿吧。bash 是 GNU 计划中重要得工具软件之一，目前也是 Linux Linux 发行版的标准 shell。bash 主要兼容于 sh，并且依据一些用户需求而加强的 shell 版本。不论你使用是的哪个 Linux 发行版，你都难逃需要学习 bash 的宿命。那么这个 shell 有什么好处，干嘛 Linux 要使用它作为默认的 shell 呢？bash 主要的优点有下面几个：

### ◆ 历史命令（history）

bash 的功能里面，鸟哥个人认为相当棒的一个就是它能记录使用过的命令，这功能真的相当的棒。因为我只要在命令行按【上下键】就可以找到前后一个输入的命令。而在很多 Linux 发行版里面，默认的命令记录条目可以到达 1000 个，也就是说，你曾经执行过的命令几乎都被记录下来。
这么多的命令记录在哪里？在你的家目录内的 .bash_history，不过，需要留意的是，~/.bash_history **记录的是前一次登录以前所执行过的命令，而至于这一次登录所执行的命令都被缓存在内存中，当你成功的注销系统后，该命令才会记录到 .bash_history 当中**。
这有什么优点？最大的好处就是可以查询曾经做过的操作，如此可以知道你的执行步骤，那么就可以追踪你曾执行过的命令，刚好你的命令又跟系统有关（例如直接输入 MySQL 的密码在命令行上面），那你的服务器可就有危险了。到底记录命令的条数越多还是越少好？这部分是见仁见智，没有绝对的答案。

### ◆ 命令与文件补全功能：（[Tab] 按键的好处）

还记得我们在第 4 章内的重要的几个热键小节当中提到的 [Tab] 这个按键吗？这个按键的功能就是在 bash 里面才有的。常常在 bash 环境中使用 [Tab] 是个很好的习惯，因为至少可以让你 1) **少打很多字** 2) **确定输入的数据是正确的**。使用 [Tab] 按键的时机根据 [Tab] 接在命令后或参数后而有所不同，我们再复习一次：
- [Tab] 接在一串命令的第一个字的后面，则为命令补全
- [Tab] 接在一串命令的第二个字的后面，则为【文件补全】
- 若安装 bash-completion 软件，则再某些命令后面使用 [Tab] 按键时，可以进行【选项/参数的补齐】功能
所以说，如果我想要知道我的环境当中所有以 c 为开头的命令？就按下【`c[Tab][Tab]`】就好，是的，真的是很方便的功能，所以，**有事没事**，在 bash shell 下面，多按几次[Tab]是一个不错的习惯。

### ◆ 命令别名设置功能：（alias）

假如我需要知道这个目录下面的所有文件（包含隐藏文件）及所有的文件属性，那么我就必须要执行【ls -al】这样的命令，唉，真麻烦，有没有更快的替换方式？呵呵，就使用命令别名。例如鸟哥最喜欢直接以 lm 这个自定义的命令来替换上面的命令，也就是说，lm 会等于 ls -al 这样的一个功能，嘿，那么要如何做？就使用 alias 即可。你可以在命令行输入 alias 就可以知道目前的命令别名有哪些了，也可以直接执行命令来设置别名：
```shell
alias lm='ls -al'
```

### ◆ 任务管理、前台、后台控制：（job control、foreground、background）

这部分我们在第 16 章 Linux 过程控制中再提及。使用前、后台的控制可以让任务进行的更为顺利，至于任务管理（jobs）的用途则更广，可以让我们随时将任务丢到后台中执行，而不怕不小心使用了[Ctrl]+c 来停掉该程序，真是不错。此外，也可以在单一登录的环境中，达到多任务的目的。

### ◆ 程序化脚本：（shell scripts）

在 DOS 年代还记得将一堆命令写在一起的所谓的批处理文件吧？在 Linux 下面的 shell 脚本则发挥更为强大的功能，可以将你平时管理系统常需要执行的连续命令写成一个文件，该文件并且可以通过交互式的方式来进行主机的检测工作，也可以借由 shell 提供的环境变量及相关命令来进行设计。哇，整个设计下来几乎就是一个小型的程序语言了。该脚本的功能真的是超乎鸟哥的想象之外，以前在 DOS 下面需要程序语言才能写的东西，在 Linux 下面使用简单的 shell 脚本就可以帮你完成了，真的厉害，这部分我们在第 12 章再来谈。

### ◆ 通配符：（Wildcard）

除了完整的字符串之外，bash 还支持许多的通配符来帮助用户查询与命令执行。举例来说，想要知道/usr/bin 下面有多少以 X 为开头的文件吗？使用：【ls -l /usr/bin/X*】就能够知道，此外，还有其他可供利用的通配符，这些都能够加快用户的操作。总之，bash 这么好，不学吗？怎么可能，快来学吧。

















































# 10.2 Shell 的变量功能
​                                                               

## 2. 变量的使用与设置：echo、变量设置规则、unset

### 变量的使用：echo 

```powershell
[root@study ~]$ echo $variable
[root@study ~]$ echo $PATH
[root@study ~]$ echo ${PATH}
```



### 变量的设置规则



* **`变量与变量内容以一个等号 = 来连接，如下所示：`** 

  myname=VBird

* **`等号两边不能直接接空格，如下所示为错误：`**

  myname = VBird 或 myname=VBird Tsai

* **`变量名称只能是英文字母与数字，但是开头字符不能是数字，如下为错误：`** 

  2myname=VBird

* **`变量内容若有空格可使用双引号（""）或单引号（''）将变量内容结合起来`** 

  var="lang is $LANG" 则 echo $var 可得 lang is zh_CN.UTF-8

* **`单引号内的特殊字符则仅为一般字符（纯文本），如下所示：`** 

  var='lang is $LANG' 则 echo $var 可得 lang is $LANG

* **`可用转义符（\）将特殊符号（如 Enter、$、\、空格、' 等）变成一般字符，如：`** 

  myname=VBird\ Tsai

* **`在一串命令的执行中，还需要借由其他额外的命令所提供的信息时，可以使用反单引号（``）或 $（命令）。`** 

  version=$(uname -r) 再 echo $version 可得 3.10.0-229.el7.x86-64

* **`若该变量为扩增变量内容时，则可用 "$变量名称" 或 ${变量} 累加内容，如下所示：`** 

  PATH="$PATH":/home/bin 或 PATH=${PATH}:/home/bin

* **`若该变量需要在其他子程序执行，则需要以 export 来使变量变成环境变量`** 

​	export PATH

* **`通常大写字符为系统默认变量，自行设置变量可以使用小写字符，方便判断。`** 
* **`取消变量的方法为使用 unset 变量名称，例如取消 myname 的设置：`** 

​	unset myname

**`举个例子：`** 

```powershell
[root@study ~]# 12name=VBird
-bash: 12name=VBird: command not found

[root@study ~]# name = VBird
-bash: name: command not found 有空格

[root@study ~]# name=VBird

取消刚刚设置的 name 这个变量内容
[root@study ~]# unset name
```



## 3. 环境变量的功能



### 用 env 观察环境变量与常见环境变量说明

```   powershell
列出目前的 shell 环境下的所有环境变量与其内容
[root@study ~]# env
```



### 用 set 观察所有变量（含环境变量与自定义变量）

```powershell
[root@study ~]# set
```



### export：自定义变量转成环境变量

```powershell
[root@study ~]# export 变量名称
```





## 4. 影响显示结果的语系变量（locale）

**`整体系统默认的语系定义在 /etc/locale.conf`** 





## 5. 变量的有效范围

**`环境变量 = 全局变量`** 

**`自定义变量 = 局部变量`**                                                                                                                                                                                                                                                           





# 10.3 命令别名与历史命令



## 1. 命令别名设置：alias、unalias

```powershell
[root@study ~]# alias lm='ls -al | more'
```



**`如何知道目前有哪些的命令别名？就使用 alias`** 

```powershell
[root@study ~]# alias
```



**`删除别名，使用 unalias`**

```powershell
[root@study ~]# unalias lm
```





## 2. 历史命令：history

```powershell
[root@study ~]# history [n]
[root@study ~]# history [-c]
[root@study ~]# history [-raw] histfiles
选项与参数：
n：数字，意思是要列出最近的 n 条命令行表的意思
-c：将目前的 shell 中的所有 history 内容全部清除
-a：将目前新增的 history 命令新增入 histfiles 中，若没有加 histfiles，则默认写入 ~/.bash_history
-r：将 histfiles 的内容读到目前这个 shell 的 history 记录中
-w：将目前的 history 记录内容写入 histfiles 中
```



```powershell
[root@study ~]# !number
[root@study ~]# !command
[root@study ~]# !!
选项与参数：
number：执行第几条命令的意思
command：由最近的命令向前查找，命令串开头为 command 的那个命令，并执行
!!：就是执行上一个命令（相当于按上键后，按回车）



[root@study ~]# !66（执行第 66 条命令）
[root@study ~]# !! 执行上一条命令
[root@study ~]# !al 执行最近以 al 为开头的命令
```







# 10.4 Bash shell 的操作环境



## 1. 路径与命令查找顺序

1. **`以相对/绝对路径执行命令，例如 /bin/ls 或 ./ls`** 
2. **`由 alias 找到该命令来执行`** 
3. **`由 bash 内置的命令来执行`** 
4. **`通过 $PATH 这个变量的顺序查找到的第一个命令来执行`** 





## 2. bash 的登录与欢迎信息：/etc/issue、/etc/motd

```powershell
[root@study ~]# cat /etc/issue
\S
Kernel \r on an \m
```



**`你想要让用户登录后取得一些信息，例如你想要让大家都知道的信息，那么可以将信息加入 /etc/motd 里面`**





# 10.5 数据流重定向



## 1. 什么是数据流重定向



### standard output 与 standard error output

* **`标准输入（stdin）：代码为 0，使用 < 或 <<`** 
* **`标准输出（stdout）：代码为 1，使用 > 或 >>`** 
* **`标准错误输出（stderr）：代码为 2，使用 2> 或 2>>`** 

```powershell
观察你的系统根目录（/）下各目录的文件名、权限与属性，并记录下来
[root@study ~]# ll /
[root@study ~]# ll / > ~/rootfile
[root@study ~]# ll ~/rootfile
```

该文件的建立方式是：

* **`该文件（本例中是 ~/rootfile）若不存在，系统会自动地将它建立起来`** 
* **`当这个文件存在的时候，那么系统就会先将这个文件内容清空，然后再将数据写入`** 
* **`也就是若以 > 输出到一个已存在的文件中，这个文件就会被覆盖掉`** 

如果我想要将数据累加而不想要将旧的数据删除，如何做？利用两个大于的符号（>>）就好。

```powershell
[root@study ~]# ll / >> ~/rootfile
```

该文件的建立方式是：

* **`~/rootfile 不存在时系统会主动建立这个文件`** 
* **`若该文件已存在，则数据会在该文件的最下方累加进去`** 



上面谈到的是标准输出的正确数据，那如果是标准错误的错误数据？那就通过 2> 及 2>>，同样的覆盖（2>）与累加（2>>）的特性。我们刚刚才谈到 stdout 代码是 1，而 stderr 代码是 2，所以这个 2> 是很容易理解的，而如果仅存在 > 时，则代表默认的代码是 1，也就是说：

* **`1>：以覆盖的方法将正确的数据输出到指定的文件或设备上`** 
* **`1>>：以累加的方法将正确的数据输出到指定的文件或设备上`** 
* **`2>：以覆盖的方法将错误的数据输出到指定的文件或设备上`** 
* **`2>>：以累加的方法将错误的数据输出到指定的文件或设备上`** 



**`如果想要将正确的与错误的数据分别存入不同的文件中需要怎么做？`** 

```                                 powershell
将 stdout 与 stderr 分别存到不同的文件中
[root@study ~]# find /home -name .bashrc > list_right 2> list_error
```



### /dev/null 垃圾桶黑洞设备与特殊写法

```powershell
将错误的数据丢弃，屏幕上显示正确的数据
[root@study ~]# find /home -name .bashrc 2> /dev/null
```



**`如果我们需要将正确与错误数据通通写入同一个文件中，这个时候就得要使用特殊的写法了。我们同样用下面的案例来说明：`** 

```powershell
将命令的数据全部写入名为 list 的文件中
[root@study ~]# find /home -name .bashrc &> list
```



### standard input：< 与 <<

```                                                                                                                                                                                           powershell
[root@study ~]# cat > catfile
testing
cat file test
这里按下 Ctrl + d 来退出


[root@study ~]# cat catfile
testing
cat file test
```



**`用某个文件的内容来替换键盘的敲击`**

```powershell
[root@study ~]# cat > catfile < ~/.bashrc
[root@study ~]# ll catfile ~/.bashrc
```



**`<< 它代表的是结束的输入字符，我要用 cat 直接将输入的信息输出到 catfile 中，且当由键盘输入 eof 时，该次输入就结束`**

```powershell
[root@study ~]# cat > catfile << "eof"
> This si a test.
> OK now stop
> eof 输入这关键词，立刻就结束而不需要输入 Ctrl + d

[root@study ~]# cat catfile
This si a test.
OK now stop
```



## 2. 命令执行的判断根据：;、&&、||



### cmd;cmd（不考虑命令相关性的连续命令执行）

```powershell
[root@study ~]# sync;sync; shutdown -h now
```





# 10.6 管道命令（pipe）

**`| 命令`**

```powershell
[root@study ~]# ls -al /etc | less
```



**`管道命令有两个比较需要注意的地方：`**

* **`管道命令仅会处理标准输出，对于标准错误会予以忽略`** 
* **`管道命令必须要能够接受来自前一个命令的数据成为标准输入继续处理才行`** 



## 1. 选取命令：cut、grep



### cut

**`这个命令可以将一段信息的某一段给它切出来，处理的信息是以行为单位`**

```powershell
[root@study ~]# cut -d '分隔字符' -f fields 用于有特定分隔字符
[root@study ~]# cut -c 字符区间 用于排列整齐的信息
选项与参数：
-d：后面接分隔字符，与 -f 一起使用
-f：根据 -d 的分隔字符将一段信息划分为数段，用 -f 取出第几段的意思
-c：以字符（characters）的单位取出固定字符区间


将 PATH 变量取出，我要找出第五个路径
[root@study ~]# echo ${PATH}
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

[root@study ~]# echo ${PATH} | cut -d ':' -f 5
/root/bin

将 PATH 变量取出，我要找出第三与第五个路径
[root@study ~]# echo ${PATH} | cut -d ':' -f 3,5
/usr/sbin:/root/bin


将 export 输出的信息，取得第 12 到 20 字符 
[root@study ~]# export | cut -c 12-20
HISTCONTR
HISTSIZE=
HOME="/ro
HOSTNAME=
LANG="en_
LESSOPEN=
LOGNAME="
LS_COLORS
MAIL="/va
OLDPWD
PATH="/us
PWD="/roo
SHELL="/b
SHLVL="1"
SSH_CLIEN
SSH_CONNE
SSH_TTY="
TERM="xte
USER="roo
XDG_RUNTI
XDG_SESSI
```



### grep

```powershell
[root@study ~]# grep [-acinv] [--color=auto] '查找字符' filename
选项与参数：
-a：将二进制文件以文本文件的方式查找数据
-c：计算找到 '查找字符' 的次数
-i：忽略大小写的不同，所以大小写视为相同
-n：顺便输出行号
-v：反向选择，亦即显示出没有 '查找字符' 内容的那一行
--color=auto：可以将找到的关键字部分加上颜色的显示


将 last 当中，有出现 root 的那一行就显示出来
[root@study ~]# last | grep 'root'



将 last 当中，没有 root 的那一行就显示出来
[root@study ~]# last | grep -v 'root'



在 last 的输出信息中，只要有 root 就取出，并且仅取第一栏
[root@study ~]# last | grep 'root' | cut -d ' ' -f 1



取出 /etc/man_db.conf 内含 MANPATH 的那几行
[root@study ~]# grep --color=auto 'MANPATH' /etc/man_db.conf
```





