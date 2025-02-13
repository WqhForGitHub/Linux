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

​                                                                                                                                                                          
