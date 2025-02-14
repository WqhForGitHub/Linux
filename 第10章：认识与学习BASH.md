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





