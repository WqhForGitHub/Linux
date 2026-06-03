# 12.1 什么是 shell 脚本

什么是 shell 脚本（shell script，程序化脚本）呢？就字面上的意义，我们将它分为两部分。对于【shell】部分，我们在第 10 章的 BASH 当中已经提过了，那是在命令行模式下面让我们与系统沟通的一个工具接口。那么【script】是啥？字面上，script 是【脚本、剧本】的意思，整句话是说，shell 脚本是针对 shell 所写的【剧本】。
什么是东西？其实，shell **脚本是利用 shell 的功能所写的一个【程序（program）】。这个程序是使用纯文本文件，将一些 shell 的语法与命令（含外部命令）写在里面，搭配正则表达式、管道命令与数据流重定向等功能，以达到我们所想要的处理目的**。
所以，简单地说 shell 脚本就像是早期 DOS 时代的批处理文件（.bat），最简单的功能就是将许多命令集合写在一起，让用户很轻易地就能够用 one touch 的方法去处理复杂的操作（执行一个文件“shell 脚本”，就能够一次执行多个命令）。而且 shell 脚本更提供数组、循环、条件与逻辑判断等重要功能，让用户也可以直接用 shell 来编写程序，而不必使用类似 C 等传统程序语言来编写。
这么说你可以了解了吗？是的，shell 脚本可以简单地被看成是批处理文件，也可以被说成是一个程序语言，且这个程序语言由于都是利用 shell 与相关工具命令，所以不需要编译即可执行。此外，它还拥有不错的的除错（debug）工具，能够帮助系统管理员快速地管理好主机。

## 12.1.1 为什么要学习 shell 脚本

这是个好问题：【我为什么一定要学 shell 脚本呢？我又不从事 IT 工作，没有写程序的概念，那我干嘛还要学 shell 脚本？不要学可不可以？】呵呵，如果对你而言，只是想【会用】Linux 而已，那么，不需要学 shell 脚本也还无所谓，这部分先给它跳过去，等到有空的时候，再来好好地看一看。但是，如果你是真的想要玩清楚 Linux 的来龙去脉，那么 shell 脚本就不可不知，为什么呢？因为：
### ◆ 自动化管理的重要根据

不用鸟哥说你也知道，管理一台主机真不是件简单的事情，每天要进行的任务就有查询日志文件、跟踪流量、监控用户使用主机状态、主机各项硬件设备状态、主机软件更新查询等，更不要说得应付其他用户的突然要求了。而这些工作的进行可以分为：（1）手动处理，或是（2）写个简单的程序来帮你每日【自动处理分析】。你觉得哪种方式比较好？当然是让系统自动工作比较好，对吧。这就需要良好的 shell 脚本来帮忙。

## 12.1.2 第一个脚本的编写与执行

如同前面讲到的，shell 脚本其实就是纯文本文件，我们可以编辑这个文件，然后让这个文件来帮我们一次执行多个命令，或是利用一些运算与逻辑判断来帮我们完成某些功能。所以，要编辑这个文件的内容时，当然就需要具备执行 bash 命令的相关知识。执行命令需要注意的事项在第 4 章的开始执行命令小节内已经提过，有疑问请自行回去翻阅，在 shell 脚本的编写中还需要注意下面的事项：
1. **命令是从上而下、从左而右地分析与执行**
2. **命令的执行就如同第 4 章内提到的：命令、选项与参数间的多个空格都会被忽略掉**
3. **空白行也将被忽略掉，并且[Tab]按键所产生的空白同样视为空格键**
4. **如果读取到一个 Enter 符号（CR），就尝试开始执行该行（或该串）命令**
5. **至于如果一行的内容太多，则可以使用【`\[Enter]`】来扩展至下一行**
6. **【#】可做为注释，任何加在 # 后面的数据将全部被视为注释文字而被忽略**
如此一来，我们在脚本内所编写的程序，就会被一行一行地执行。现在我们假设你写的这个程序文件名是 /home/dmtsai/shell.sh，那如何执行这个文件？很简单，可以有下面几个方法：
- **直接命令执行：shell.sh 文件必须要具备可读与可执行（rx）的权限，然后：**
- **绝对路径：使用 /home/dmtsai/shell.sh 来执行命令**
- **相对路径：假设工作目录在 /home/dmtsai/，则使用 ./shell.sh 来执行**
- **变量【PATH】功能：将 shell.sh 放在 PATH 指定的目录内，例如：~/bin/**
- **以 bash 程序来执行：通过【bash shell.sh】或【sh shell.sh】来执行**
反正重点就是要让那个 shell.sh 内的命令可以被执行的意思。唉？那我为何需要使用【./shell.sh】来执行命令？忘记了吗？回去查看一下第 10 章内的命令查找顺序，你就会知道原因了。同时，由于 CentOS 默认用户家目录下的 ~/bin 目录会被设置到 ${PATH} 内，所以你也可以将 shell.sh 建立在 /home/dmtsai/bin/下面（~/bin 目录需要自行设置），此时，若 shell.sh 在 ~/bin 内且具有 rx 的权限，那么直接输入 shell.sh 即可执行该脚本程序。
那为何【sh shell.sh】也可以执行呢？这是因为 /bin/sh 其实就是 /bin/bash（链接文件），使用 sh shell.sh 亦即告诉系统，我想要直接以 bash 的功能来执行 shell.sh 这个文件内的相关命令的意思。所以此时你的 shell.sh 只要有 r 的权限即可被执行，而我们也可以利用 sh 的参数，如 -n 及 -x 来检查与跟踪 shell.sh 的语法是否正确。

### ◆ 编写第一个脚本

在武侠世界中，不论是哪个门派，学武功都要从扫地与蹲马步做起，那么学程序呢？呵呵，肯定是由【显示 Hello World】这段文字开始。OK，那么鸟哥就先写一个脚本给大家看一看：
```shell
[dmtsai@study ~]# mkdir bin; cd bin
[dmtsai@study ~]# vim hello.sh
# !/bin/bash
# Program:                                                                         
#       This program shows "Hello World!" in your screen.                          
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
echo -e "Hello World~ \a \n"                                                       
exit 0
```
在本章当中，请将所有编写的脚本放置到你的家目录的 ~/bin 内，未来比较好管理。上面的写法当中，鸟哥主要将整个程序的编写分成数段，大致是这样：

#### 1. 第一行 #!/bin/bash 在声明这个脚本使用的 shell 名称

因为我们使用的是 bash，所以，必须要以【#!/bin/bash】来声明这个文件内使用 bash 的语法。这样以【#!】开头的行被称为 shebang 行。那么当这个程序被执行时，它就能够加载 bash 的相关环境配置文件（一般来说就是非登录 shell 的 ~/.bashrc），并且执行 bash 来使我们下面的命令能够执行。这很重要，在很多错误的情况中，如果没有设置好这一行，那么该程序很可能会无法执行，因为系统可能无法判断该程序需要使用什么 shell 来执行。
#### 2. 程序内容的说明

整个脚本当中，除了第一行的【#!】是用来声明 shell 的之外，其他的 # 都是【注释】用途。所以上面的程序当中，第二行以下就是用来说明整个程序的基本数据。一般来说，建议你一定要养成习惯，说明该脚本的：1. 内容与功能 2. 版本信息 3. 作者与联络方式 4. 建文件日期 5. 历史记录等。这将有助于未来程序的改写与调试。

#### 3. 主要环境变量的声明

建议务必要将一些重要的环境变量设置好，鸟哥个人认为，PATH 与 LANG（如果有使用到输出相关的信息时）是当中最重要的。如此一来，我们这个程序在进行时，可以直接执行一些外部命令，而不必写绝对路径，比较方便。

#### 4. 主要程序部分

将主要的程序写好即可，在这个例子当中，就是 echo 那一行。

#### 5. 执行结果告知（定义返回值）

是否记得我们在第 10 章里面要讨论一个命令的执行成功与否，可以使用 $? 这个变量来观察？**那么我们也可以利用 exit 这个命令来让程序中断，并且返回一个数值给系统**。在我们这个例子当中，鸟哥使用 exit 0，这代表退出脚本并且返回一个 0 给系统，所以我执行完这个脚本后，若接着执行 echo $? 则可得到 0 的值。聪明的读者应该也知道了，呵呵，利用这个 exit n（n 是数字）的功能，我们还可以自定义错误信息，让这个程序变得更加的聪明。
接下来通过上面介绍的执行方法来执行看看结果吧。
```shell
[dmtsai@study ~]# sh hello.sh
Hello World !
```
你会看到屏幕是这样，而且应该还会听到【咚】的一声，为什么？还记得前一章提到的 printf 吧？用 echo 接着那些特殊的按键也可以发生同样的事情。不过，echo 必须要加上 -e 的选项才行。呵呵，在你写完这个小脚本之后，你就可以大声地说：【我也会写程序了！】哈哈很简单有趣吧。
另外，你也可以利用：【chmod a+x hello.sh; ./hello.sh】来执行这个脚本。

## 12.1.3 建立 shell 脚本的良好编写习惯

一个良好习惯的养成是很重要的。大家在刚开始编写程序的时候，最容易忽略这部分，认为程序写出来就好了，其他的不重要。其实，如果程序的说明能够更清楚，那么对你自己也会有很大的帮助。
举例来说，鸟哥为了自己的需求，曾经编写了不少的脚本来帮我进行主机 IP 的检测、日志文件分析与管理、自动上传下载重要配置文件等。不过，早期就是因为太懒了，管理的主机又太多，常常同一个程序在不同的主机上面进行更改，最后到底哪一个才是最新的都记不起来。而且，重点是我到底改了哪里？为什么做那样的修改？都忘得一干二净，真要命。
所以，后来鸟哥在写程序的时候，通常会比较仔细地将程序的设计过程给记录下来，而且还会记录一些历史记录。如此一来，好多了，至少很容易知道我修改了哪些数据，以及程序修改的理念与逻辑概念等，在维护上面是轻松很多很多的。
另外，在一些环境的设置上面，毕竟每个人的环境都不相同，为了取得较佳的执行环境，我都会自行先定义好一些一定会被用到的环境变量，例如 PATH 这个玩意儿。这样比较好，所以说，建议你一定要养成良好的脚本编写习惯，在每个脚本的文件头处记录好：
- 脚本的功能
- 脚本的版本信息
- 脚本的作者与联络方式
- 脚本的版权声明方式
- 脚本的 History（历史记录）
- 脚本内较特殊的命令，使用【绝对路径】的方式来执行
- 脚本运行时需要的环境变量预先声明与设置

# 12.2 简单的 shell 脚本练习

在第一个 shell 脚本编写完毕之后，相信你应该具有基本的编写功力了。接下来，在开始更深入的程序概念之前，我们先来玩一些简单的小范例好了。下面的范例中，完成结果的方式相当的多，建议你先自行编写看看，写完之后再与鸟哥写的内容比对，这样才能更加深概念。好，我们就一个一个来玩吧。

## 12.2.1 简单范例

下面的范例都很简单，但在很多脚本程序中都会用到，值得参考看看。

### ◆ 交互式脚本：变量内容由用户决定

很多时候我们需要用户输入一些内容，好让程序剋顺利运行。大家应该都有安装过软件的经验，安装的时候，它不是会问你【要安装到哪个目录去】吗？那个让用户输入数据的操作，就是让用户输入变量内容。
你应该还记得在第 10 章 bash 中，我们曾学到一个 read 命令吧？现在，请你以 read 命令的用途，编写一个脚本，它可以让用户输入：1. first name 与 2. last name，最后在屏幕上显示：【Your full name is：】的内容：
```shell
[dmtsai@study ~]# vim showname.sh
# !/bin/bash                                                                       
# Program":                                                                        
#       User inputs his first name and last name. Program shows his full name.     
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbib:~/bin           
export PATH                                                                        
read -p "Please input your first name: " firstname # 提示使用者输入。
read -p "Please input your last name: " lastname # 提示使用者输入。
echo -e "\nYour full name is: ${firstname} ${lastname}" # 结果由屏幕输出。
```
将上面那个 showname.sh 执行一下，你就能够发现用户自己输入的变量可以让程序所使用，并且将它显示到屏幕上。接下来，如果想要制作一个每次执行都会根据日期而变化结果的脚本呢？

### ◆ 随日期变化，利用 date 建立文件

想象一个状况，假设我的服务器内有数据库，数据库每天的数据都不太一样，因此当我备份时，希望将每天的数据都备份成不同的文件名，这样才能够让旧的数据也能够保存下来不被覆盖。哇，不同文件名，这真困扰？难道要我每天去修改脚本吗？
不需要，考虑到每天的【日期】并不相同，所以将文件名取成类似 backup.2015-07-16.data，不就可以每天一个不同文件名了吗？呵呵，确实如此。那个 2015-07-16 怎么来的？那就是重点。接下来出个相关的例子：假设我想要建立三个空文件（通过 touch），文件名最开头由用户输入决定，假设用户输入 filename，而今天的日期是 2015/07/16，我想要以前天、昨天、今天的日期来建立这些文件，即 filename_20150714、filename_20150715、filename_20150716，该如何是好？
```shell
[dmtsai@study bin]# vim create_3_filename.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       Program creates three files, which named bu user's input and date command. 
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
# 1. 让使用者输入文件名称，并取得 fileuser 这个变量。                                   
echo -e "I will use 'touch' command to create 3 files." # 纯粹显示信息。
read -p "Please input your filename: " fileuser # 提示使用者输入。        
# 2. 为了避免使用者随意按 Enter，利用变量功能分析文件名是否有设置？                        
filename=${fileuser:-"filename"} # 开始判断有否配置文件名。             
# 3. 开始利用 date 命令来取得所需要的文件名了。                                         
date1=$(date --date='2 days ago' + %Y%m%d) # 前两天的日期。                          
date2=$(date --date='1 days ago' + %Y%m%d) # 前一天的日期。                          
date3=$(date + %Y%m%d) # 今天的日期。                                                
file1=${filename}${date1} # 下面三行在配置文件名。                                    
file2=${filename}${date2}                                                          
file3=${filename}${date3}                                                          
# 4. 将文件名建立吧！                                                                
touch "${file1}" # 下面三行在建立文件  
touch "${file2}"                                                                   
touch "${file3}"
```
在上面的范例中，鸟哥使用了很多在第 10 章介绍过的概念：包括【$(command)】参数的信息获取、变量的设置功能、变量的累加以及利用 touch 命令辅助。这个 create_3_filename.sh，你可以执行两次：一次直接在 [Enter] 来查看文件名是啥？一次可以输入一些字符，这样可以判断你的脚本是否设计正确。

### ◆ 数值运算：简单的加减乘除

各位看官应该还记得，我们可以使用 declare 来定义变量的类型吧？当变量定义成为整数后才能够进行加减运算。此外，我们也可以利用【$((计算式))】来进行数值运算。可惜的是 bash shell 里面默认仅支持到整数的数据而已。OK，那我们来玩玩看，如果我们要用户输入两个变量，然后将两个变量的内容相乘，最后输出相乘的结果，那可以怎么做？
```shell
[dmtsai@study ~]# vim multiplying.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       User inputs 2 integer numbers; program will cross these two numbers.       
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
echo -e "You SHOULD input 2 numbers, I will multiplying them! \n"                  
read -p "first number: " firstnu                                                   
read -p "second number: " secnu                                                    
total=$((${firstnu}*${secnu}))                                                     
echo -e "\nThe result of ${firstnu} x ${secnu} is ==> ${total}"
```
在数值的运算上，我们既可以使用【`declare -i total=${firstnu}*${secnu}`】，也可以使用上面的方式来进行。基本上，鸟哥比较建议使用这样的方式来进行运算：
```shell
var=$((运算内容))
```
不但容易记忆，而且也方便得多，因为两个小括号内可以加上空格符，未来你可以使用这种方式来计算。至于数值运算上的处理，则有 +、-、/、%。那个 % 是取余数，举例来说，13 对 3 取余数，结果是 13=4*+1，所以余数是 1，就是：
```shell
[dmtsai@study ~]# echo $((13 % 3))
1
```
这样了解了吧？另外，如果你想要计算含有小数点的数据时，其实可以通过 bc 这个命令的协助，例如可以这样做：
```shell
[dmtsai@study ~]# echo "123.123*55.9" | bc
6882.575
```
了解了 bc 的妙用之后，来让我们测试一下如何计算 Pi 这个东西？

### ◆ 数值运算：通过 bc 计算 Pi（圆周率）

其实计算 Pi 时，小数点以下位数可以无限制地扩展下去，而 bc 提供了一个运算 Pi 的函数，要使用该函数必须通过 bc -l 来调用才行。也因为这个小数点的位数可以无限扩展运算的特性存在，所以我们可以通过下面这个小脚本来让用户输入一个【小数点位数】，以让 Pi 能够更准确。
```shell
[dmtsai@study ~]# vim cal_pi.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       User input a scale number to calculate pi number.                          
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
echo -e "This program will calculate pi value. \n"                                 
echo -e "You should input a float number to calculate pi value.\n"                
read -p "The scale number (10~10000)? " checking                                   
num=${checking:-"10"}                                                              
echo -e "Starting calcuate pi value. Be patient."                                  
time echo "scale=${num}; 4*a(1)" | bc -lq
```
上述数据中，那个 `4*a(1)` 是 bc 主动提供的一个计算 Pi 的函数，至于 scale 就是要 bc 计算几个小数点位数的意思。scale 的数值越大，代表 Pi 要被计算得越精确，当然用掉得时间就会越多。因此，你可以尝试输入不同的数值看看，不过，最好不要超过 5000，因为会算很久。如果要让你的 CPU 随时保持在高负载，这个程序运行下去你就会知道有多消耗 CPU。
>鸟哥的实验室中，为了要确认虚拟机的效率问题，很多时候需要保持虚拟机在高负载的状态。鸟哥的学生就是让这个程序在系统中运行，但是将 scale 调高一些，这样计算就要花比较多的时间，用以达到 CPU 高负载的状态。

## 12.2.2 脚本的执行方式差异（source、sh script、./script）

不同的脚本执行方式会造成不一样的结果，尤其对 bash 的环境影响很大。脚本的执行除了前面小节谈到的方式之外，还可以利用 source 或小数点（.）来执行。那么这种执行方式有何不同？当然是不同的，让我们来说说。

### ◆ 利用直接执行的方式来执行脚本

当使用前一小节提到的直接命令执行（不论是绝对路径/相对路径还是${PATH}内），或是利用 bash（或 sh）来执行脚本时，该脚本都会使用一个新的 bash 环境来执行脚本内的命令。也就是说，使用这种执行方式时，其实脚本是在子进程的 bash 内执行的。我们在第 10 章 BASH 内谈到 export 的功能时，曾经就父进程和子进程谈过一些概念性的问题，重点在于【**当子进程完成后，在子进程内的各项变量或操作将会结束而不会传回到父进程中**】，这是什么意思？
我们通过刚刚提到过的 showname.sh 这个脚本来说明。这个脚本可以让用户自行设置两个变量，分别是 firstname 与 lastname。想一想，如果你直接执行该命令时，该命令帮你设置的 firstname 会不会生效？看一下下面的执行结果：
```shell
[dmtsai@study ~]# echo ${firstname} ${lastname}
<==确认了，这两个变量并不存在。
[dmtsai@study ~]# sh showname.sh
Please input your first name: VBird <==这个名字是鸟哥自己输入的。  
Please input your last name: Tsai 
Your full name is: VBird Tsai <==看吧，在脚本运行中，这两个变量有生效。
[dmtsai@study ~]# echo ${firstname}${lastname}
<==事实上，这两个变量在父进程的 bash 中还是不存在的。
```
上面的结果你应该会觉得很奇怪，怎么我已经利用 showname.sh 设置好的变量竟然在 bash 环境下面无效。怎么回事？如果将进程相关性绘制成图的话，我们以下图来说明，当你使用直接执行的方法来处理时，系统会给予一个新的 bash 让我们来执行 showname.sh 里面的命令，因此你的 firstname、lastname 等变量其实是在下图中的子进程 bash 内执行的，当 showname.sh 执行完毕后，子进程 bash 内的所有数据便被删除，因此上表的练习中，在父进程下面 echo ${firstname} 时，就看不到任何东西了，这样可以理解吗？

### ◆ 利用 source 来执行脚本：在父进程中执行

如果你使用 source 来执行命令那就不一样了，同样的脚本我们来执行看看：
```shell
[dmtsai@study ~]# source shoename.sh
Please input your first name: VBird                                                
Please input your last name: Tsai                                                  
Your full name is: VBird Tsai
[dmtsai@study ~]# echo ${firstname}${lastname}
VBirdTsai <==嘿嘿，有数据产生。
```
竟然生效了，没错，因为 source 对脚本的执行方式可以使用下面的图例来说明，showname.sh 会在父进程中执行，因此各项操作都会在原本的 bash 内生效。这也是为啥你不注销系统而要让某些写入 ~/.bashrc 的设置生效时，需要使用【source ~/.bashrc】而不能使用【bash ~/,bashrc】是一样的。
![showname.sh 在子进程当中运行的示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC12%E7%AB%A0%EF%BC%9A%E5%AD%A6%E4%B9%A0%20shell%20%E8%84%9A%E6%9C%AC/showname.sh%20%E5%9C%A8%E5%AD%90%E8%BF%9B%E7%A8%8B%E5%BD%93%E4%B8%AD%E8%BF%90%E8%A1%8C%E7%9A%84%E7%A4%BA%E6%84%8F%E5%9B%BE.png)
![showname.sh 在父进程当中运行的示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC12%E7%AB%A0%EF%BC%9A%E5%AD%A6%E4%B9%A0%20shell%20%E8%84%9A%E6%9C%AC/showname.sh%20%E5%9C%A8%E7%88%B6%E8%BF%9B%E7%A8%8B%E5%BD%93%E4%B8%AD%E8%BF%90%E8%A1%8C%E7%9A%84%E7%A4%BA%E6%84%8F%E5%9B%BE.png)
# 12.3 善用判断式

在第 10 章中，我们提到过 $? 这个变量所代表的意义，此外，也通过 && 及 || 来作为前一个命令执行返回值对于后一个命令是否要进行的根据。第 10 章的讨论中，如果想要判断一个目录是否存在，当时我们使用的是j ls 这个命令搭配数据流重定向，最后配合 $? 来决定后续的命令进行是否，但是否有更简单的方式可以来进行【条件判断】呢？有的，那就是【test】这个命令。
## 12.3.1 利用 test 命令的测试功能

当我要检测系统上面某些文件或是相关的属性时，利用 test 这个命令来工作真是好用得不得了。举例来说，我要检查 /dmtsai 是否存在时，使用：
```shell
[dmtsai@study ~]# test -e /dmtsai
```
执行结果并不会显示任何信息，但最后我们可以通过$?或&&及||来展现整个结果，例如我们将上面的例子改写成这样：
```shell
[dmtsai@study ~]# test -e /dmtsai && echo "exist" || echo "Not exist"
Not exist <==结果显示不存在。
```
最终的结果可以告知我们是【exist】还是【Not exist】，那我知道 -e 是测试一个【东西】在不在，如果还想要测试一下该文件名是啥玩意儿时，还有哪些参数可以来判断呢？呵呵，有下面这些东西。
### 1. 关于某个文件名的【文件类型】判断，如 test -e filename 表示存在否

| 测试的参数 | 代表意义                               |
| ----- | ---------------------------------- |
| -e    | 该【文件名】是否存在                         |
| -f    | 该【文件名】是否存在且为文件（file）               |
| -d    | 该【文件名】是否存在且为目录（directory）          |
| -b    | 该【文件名】是否存在且为一个 block device 设备     |
| -c    | 该【文件名】是否存在且为一个 character device 设备 |
| -S    | 该【文件名】是否存在且为一个 socket 文件           |
| -p    | 该【文件名】是否存在且为一个 FIFO（pipe）文件        |
| -L    | 该【文件名】是否存在且为一个链接文件                 |
### 2. 关于文件的权限检测，如 test -r filename 表示可读否（但 root 权限常有例外）

| 测试的参数 | 代表意义                         |
| ----- | ---------------------------- |
| -r    | 检测该文件名是否存在且具有【可读】的权限         |
| -w    | 检测该文件名是否存在且具有【可写】的权限         |
| -x    | 检测该文件名是否存在且具有【可执行】的权限        |
| -u    | 检测该文件名是否存在且具有【SUID】的属性       |
| -g    | 检测该文件名是否存在且具有【SGID】的属性       |
| -k    | 检测该文件名是否存在且具有【Sticky bit】的属性 |
| -s    | 检测该文件名是否存在且为【非空文件】           |

### 3. 两个文件之间的比较，如：test file1 -nt file2

| 测试的参数 | 代表意义                                                                     |
| ----- | ------------------------------------------------------------------------ |
| -nt   | （newer than）判断 file1 是否比 file2 新                                         |
| -ot   | （older than）判断 file1 是否比 file2 旧                                         |
| -ef   | 判断 file1 与 file2 是否为同一文件，可用在判断 hard link 的判定上。主要意义在判定，两个文件是否均指向同一个 inode |

### 4. 关于两个整数之间的判定，例如 test n1 -eq n2

| 测试的参数 | 代表意义                              |
| ----- | --------------------------------- |
| -eq   | 两数值相等（equal）                      |
| -ne   | 两数值不等（not equal）                  |
| -gt   | n1 大于 n2（greater than）            |
| -lt   | n1 小于 n2（less than）               |
| -ge   | n1 大于等于 n2（greater than or equal） |
| -le   | n1 小于等于 n2（less than or equal）    |

### 5. 判定字符串的数据

| 测试的参数             | 代表意义                                             |
| ----------------- | ------------------------------------------------ |
| test -z string    | 判定字符串是否为 0？若 string 为空字符串，则为 true                |
| test -n string    | 判定字符串是否非为 0？若 string 为空字符串，则为 false<br>注：-n 亦可省略 |
| test str1 == str2 | 判定 str1 是否等于 str2，若相等，则返回 true                   |
| test str1 != str2 | 判定 str1 是否不等于 str2，若相等，则返回 false                 |

### 6. 多重条件判定，例如：test -r filename -a -x filename

| 测试的参数 | 代表意义                                                                   |
| ----- | ---------------------------------------------------------------------- |
| -a    | （and）两条件同时成立。例如 test -r file -a -x file，则 file 同时具有 r 与 x 权限时，才返回 true |
| -o    | （or）两条件任何一个成立。例如 test -r file -o -x file，则 file 具有 r 或 x 权限时，就可返回 true |
| !     | 反相状态，如 test ! -x file，当 file 不具有 x 时，返回 true                           |
OK，现在我们就利用 test 来帮我们写几个简单的例子。首先，让用户输入一个文件名，我们判断：
1. 这个文件是否存在，若不存在则给予一个【Filename does not exist】的信息，并中断程序
2. 若这个文件存在，则判断它是个文件或目录，结果输出【Filename is regular file】或【Filename is directory】
3. 判断一下，执行者的身份对这个文件或目录所拥有的权限，并输出权限数据。
你可以先自行写写看，然后再跟下面的结果讨论讨论，注意利用 test 与 && 还有 || 等标志。
```shell
[dmtsai@study ~]# vim file_path.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       User input a filename, program will check the flowing:                    
#       1.) exist? 2.) file/directory? 3.) file permissions                        
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
# 1. 让使用者输入文件名，并且判断使用者是否真的有输入字符？                                
echo -e "Please input a filename, I will check the filename's type and permission. \n\n"                                                                              
read -p "Input a filename: " filename                                              
test -z ${filename} && echo "You MUST input a filename." && exit 0                 # 2. 判断文件是否存在？若不存在则显示信息并结果脚本。                                     
test ! -e ${filename} && echo "The filename '${filename}' DO NOT exist" && exit 0  
# 3. 开始判断文件类型与属性。                                                         
test -f ${filename} && filetype="regulare file"
test -d ${filename} && filetype="directory"                                        
test -r ${filename} && perm="readable"                                             
test -w ${filename} && perm="${perm} writable"                                     
test -x ${filename} && perm="${perm} executable"                                   
# 4. 开始输出信息。                                                                 
echo "The filename: ${filename} is a ${filetype}"                                  
echo "And the permissions for you are: ${perm}"
```
执行这个脚本后，它会根据你输入的文件名来进行检查，先看是否存在，再看文件或目录类型，最后判断权限。但是必须要注意的是，**由于 root 在很多权限的限制上面都是无效的，所以使用 root 执行这个脚本时，常常会发现与 ls -l 观察到的结果并不相同**。所以，建议使用一般用户来执行这个脚本看看。

## 12.3.2 利用判断符号[]

除了我们很喜欢使用的 test 之外，其实，我们还可以利用判断符号【[]】（就是中括号）来进行数据的判断。举例来说，如果我想要知道${HOME} 这个变量是否为空，可以这样做：
```shell
[dmtsai@study ~]# [ -z "${HOME}" ]; echo $?
```
使用中括号必须要特别注意，因为中括号用在很多地方，包括通配符与正则表达式等。所以如果要在 bash 的语法当中使用中括号作为 shell 的判断式时，必须要注意**中括号的两端需要有空格符来分隔**。假设空格键使用【 】符号来表示，那么，在这些地方你都需要有空格：
```shell
[ "$HOME" == "$MAIL"]
```
>你会发现鸟哥在上面的判断式当中使用了两个等号【==】，其实在 bash 当中使用一个等号与两个等号的结果是一样的，不过在一般常用程序的写法中，一个等号代表【变量的设置】，两个等号则是代表【逻辑判断（是与否之意）】。由于我们在中括号内重点在于【判断】而非【设置变量】，因此鸟哥建议您还是使用两个等号较佳。

上面的例子在说明，两个字符串 ${HOME} 与 ${MAIL} 是否相同，相当于 test ${HOME} == ${MAIL}。而如果没有空白分隔，例如 `[${HOME}==${MAIL}]`，我们的 bash 就会显示错误信息，这可要很注意，所以说，你最好要注意：
- **在中括号[]内的每个组件都需要有空格来分隔**
- **在中括号内的变量，最好都以双引号括号起来**
- **在中括号内的常数，最好都以单或双引号括号起来**
为什么要这么麻烦？直接举例来说，假如我设置了 name="VBird Tsai"，然后这样判定：
```shell
[dmtsai@study ~]# name="VBird Tsai"
[dmtsai@study ~]# [ ${name} == "VBird" ]
-bash: [: ==: unary operator expected
```
见鬼了，怎么会发生错误？bash 还跟我说错误是由于【太多参数（too many arguments）】，所致，为什么？因为 ${name} 如果没有双引号括起来，那么上面的判定式会变成：
```shell
[ VBird Tsai == "VBird" ]
```
上面肯定不对嘛，因为一个判断式仅能有两个数据的比对，上面 VBird 与 Tsai 还有 "VBird" 就有三个数据，这不是我们要的，我们要的应该是下面这个样子：
```shell
[ "VBird Tsai" == "VBird" ]
```
这可是差很多的。另外，中括号的使用方法与 test 几乎一模一样，只是中括号比较常用在条件判断式 if...then...if 的情况中。好，那我们也使用中括号的判断来做一个小案例好了，案例设置如下：
1. 当执行一个程序的时候，这个程序会让用户选择 Y 或 N
2. 如果用户输入 Y 或 y 时，就显示【OK，continue】
3. 如果用户输入 N 或 n 时，就显示【Oh，interrupt】
4. 如果不是 Y/y/N/n 之内的其他字符，就显示【I don't know what your choice is】。
利用中括号、&& 与 || 来继续吧。
```shell
[dmtsai@study ~]# vim ans_yn.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       This program shows the user's choice                                       
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
read -p "Please input（Y/N）: " yn                                                 
[ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "OK, continue" && exit 0              
[ "${yn}" == "N" -o "${yn}" == "n" ] && echo "Oh, interrupt!" && exit 0            echo "I don't know what your choice is" && exit 0
```
由于输入正确（Yes）的方法有大小写之分，不论输入大写 Y 或小写 y 都是可以的，此时判断式内就得要有两个判断才行。由于是任何一个成立即可（大写或小写的 y），所以这里使用 -o（或）连接两个判断，很有趣吧。利用这个字符串判别的方法。我们就可以很轻松地将用户想要进行的工作分门别类。接下来，我们再来谈一些其他有的没有的东西吧。

## 12.3.3 shell 脚本的默认变量（$0、$1...）

我们知道命令可以带有选项与参数，例如 ls -al 可以查看包含隐藏文件的所有属性与权限。那么 shell 脚本能不能在脚本文件名后面带有参数呢？很有趣，举例来说，如果你想要重新启动系统的网络，可以这样做：
```shell
[dmtsai@study ~]# file /etc/init.d/network
# 使用 file 来查询后，系统告知这个文件是个 bash 的可执行脚本
[dmtsai@study ~]# /etc/init.d/network restart
```
restart 是重新启动的意思，上面的命令可以【重新启动 /etc/init.d/network 这个程序】。唔，那么如果你在 /etc/init.d/network 后面加上 stop 呢？没错，就可以直接关闭该服务了，那么神奇？没错，如果你要根据程序的执行给予一些变量去进行不同的任务时，本章一开始是使用 read 的功能，但 read 功能的问题是你得要手动由键盘输入一些判断式。如果通过命令后面接参数，那么一个命令就能够处理完毕而不需要手动再次输入一些变量操作，这样执行命令会比较简单方便。
脚本是怎么完成这个功能的呢？其实脚本针对参数已经设置好了一些变量名称，对应如下：
```shell
/path/to/scriptname opt1 opt2 opt3 opt4
$0 $1 $2 $3 $4
```
这样够清楚了吧？执行的脚本文件名为 $0 这个变量，第一个接的参数就是 $1。所以，只要我们在脚本里面善用 $1 的话，就可以很简单地立即执行某些命令功能了。除了这些数字的变量之外，我们还有一些较为特殊的变量可以在脚本内使用来调用这些参数。
- $#：代表后接的参数【个数】，以上表为例这里显示为【4】
- $@：代表【"$1" "$2" "$3" "$4"】之意，每个变量是独立的（用双引号括起来）
- `$*`：代表【"$1c$2c$3c$4"】，其中 c 为分隔字符，默认为空格，所以本例中代表【"$1 $2 $3 $4"】之意
那个`$@`与`$*` 基本上还是有所不同，不过，一般使用情况下可以直接记忆`$@`。好了，来做个例子吧。假设我要执行一个可以携带参数的脚本，执行该脚本后屏幕会显示如下数据：
- 程序的文件名是什么
- 共有几个参数
- 若参数的个数小于 2 则告知用户参数数量太少
- 全部的参数内容是什么
- 第一个参数是什么
- 第二个参数是什么
```shell
[dmtsai@study ~]# vim how_paras.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       Program shows the script name, parameters...                               
# History:                                                                         
# 2015/07/16 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
echo "The script name is ==> ${0}"                                                 
echo "Total parameter number is ==> $#"                                            
[ "$#" -lt 2 ] && echo "The number of parameter is less than 2. Stop here." && exit 0                                                                             
echo "Your whole parameter is ==> '$@'"                                            
echo "The 1st parameter ==> ${1}"                                                  
echo "The 2nd parameter ==> ${2}"
```
执行结果如下：
```shell
[dmtsai@study ~]# sh how_parse.sh theone haha quot
The script name is ==> how_paras.sh <==文件名。
Total parameter number is ==> 3 <==果然有三个参数。
Your whole parameter is ==> 'theone haha quot' <==参数的内容全部。
The 1st parameter ==> theone <==第一个参数
The 2nd parameter ==> haha <==第二个参数
```

### ◆ shift：造成参数变量号码偏移

除此之外，脚本后面所接的变量是否能够进行偏移（shift）呢？什么是偏移啊？我们直接以下面的范例来说明好了，用范例说明比较好解释。我们将 how_paras.sh 的内容稍作变化一下，用来显示每次偏移后参数的变化情况：
```shell
[dmtsai@study ~]# vim shift_paras.sh
# !/bin/bash                                                                       
# Program:                                                                         
#       Program shows the effect of shift function.                                
# History:                                                                         
# 2009/02/17 VBird First release                                                   
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin            
export PATH                                                                        
echo "Total parameter number is ==> $#"                                            
echo "Your whole parameter is ==> '$@'"                                            
shift # 进行第一次【一个变量的shift】。                                                
echo "Total parameter number is ==> $#"                                            
echo "Your whole parameter is ==> '$@'"                                            
shift 3 # 进行第二次【三个变量的 shift】。                                             
echo "Total parameter number is ==> $#"                                            
echo "Your whole parameter is ==> '$@'"
```
这脚本的执行结果如下：
```shell
[dmtsai@study ~]# sh shift_paras.sh one two three four five six <==给予六个参数。
Total parameter number is ==> 6 <==最原始的参数变量情况。
Your whole parameter is ==> 'one two three four five six'
Total parameter number is ==> 5 <==第一次偏移，看下面发现第一个 one 不见了。
Your whole parameter is ==> 'two three four five six'
Total parameter number is ==> 2 <==第二次偏移掉三个，two three four 不见了。
Your whole parameter is ==> 'five six'
```
光看结果你就可以知道，那个 shift 会移动变量，而且 shift 后面可以接数字，代表拿掉最前面的几个参数的意思。上面的执行结果中，第一次进行 shift 后它的显示情况是【two three four five six】，所以就剩下五个。第二次直接拿掉三个，就变成【five six】。这样这个案例可以了解了吗？理解 shift 的功能了吗？
上面这几个例子都很简单吧？几乎都是利用 bash 的相关功能而已，不难，下面我们就要使用条件判断式来分别设置一些功能，好好看一看。








































































































