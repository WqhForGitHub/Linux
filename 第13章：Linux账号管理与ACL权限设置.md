# 13.1 Linux 的账号与用户组

管理员的工作中，相当重要的一环就是【管理账号】，因为整个系统都是你在管理，并且所有一般用户的账号申请，都必须要通过你的协助才行，所以你就必须要了解一下如何管理好一个服务器主机的账号。在管理 Linux 主机的账号时，我们必须先来了解一个 Linux 到底是如何辨别每一个用户的.

## 13.1.1 用户标识符：UID 与 GID

虽然我们登录 Linux 主机的时候，输入的是我们的账号，但是其实 Linux 主机并不会直接认识你的【账号名称】，它仅认识 ID（ID 就是一组号码）。由于计算机仅认识 0 与 1，所以主机对于数字比较有概念，账号只是为让人们容易记忆而已，而你的 ID 与账号的对应就在 /etc/passwd 当中。
>如果你曾经在网络上下载过 tarball 类型的文件，那么应该不难发现，在解压缩之后的文件中，文件拥有者的字段竟然显示【不明的数字】。奇怪吧？这没什么好奇怪的，因为说实在话，Linux 它真的只认识代表你身份的号码而已。

那么到底有几种 ID 呢？还记得我们在第 5 章提到过，每一个文件都具有【 拥有人与拥有人组 】的属性吗？没错，每个登录的用户至少都会获取两个 ID，一个是用户 ID（User ID，简称 UID），一个是用户组 ID（Group ID，简称 GID）。
那么文件如何判别它的拥有者与用户组呢？其实就是利用 UID 与 GID。每一个文件都会有所谓的拥有者 ID 与拥有人组 ID，当我们有要显示文件属性的需求时，系统会根据 /etc/passwd 与 /etc/group 的内容，找到 UID 与 GID 对应的账号与组名再显示出来。我们可以做个小实验，你可以用 root 的身份 vim /etc/passwd，然后将你的一般身份的用户 ID 随便改一个号码，然后再到你的一般身份的目录下看看原先该账号拥有的文件，你会发现该文件的拥有人变成了【数字】。呵呵，这样可以理解了吗？来看看下面的例子：
```shell
# 1. 先查看一下，系统里面有没有一个名为 dmtsai 的用户？
[root@study ~]# id ubuntu
uid=1000(ubuntu) gid=1001(ubuntu) groups=1001(ubuntu),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),101(lxd),1000(netdev) <==确定有这个账号
```
你一定要了解的是，上面的例子仅是在说明 UID 与账号的对应性。**在一台正常运行的 Linux 主机环境下，上面的操作不可随便进行**，这是因为系统上已经有很多的数据被建立存在了，随意修改系统上某些账号的 UID 很可能会导致某些程序无法运行，甚至导致系统无法顺利运行，因为权限的问题。所以，了解了之后，请赶快回到 /etc/passwd 里面，将数字改回来。
>举例来说，如果上面的测试最后一个步骤没有将 2000 改回原本的 UID，那么当 dmtsai 下次登录时将没有办法进入自己的家目录。因为它的 UID 已经改为 2000，但是它的家目录（home/dmtsai）却记录的是 1000，由于权限是 700，因此它将无法进入原本的家目录，是否觉得非常严重啊？

## 13.1.2 用户账号

Linux 系统上面的用户如果需要登录主机以获取 shell 的环境来工作时，它需要如何进行呢？首先，它必须要在计算机前面利用 tty1~tty6 的终端提供的登录接口，并输入账号与密码后才能够登录。如果是通过网络的话，那至少用户就得要学习 ssh 这个功能（服务器篇再来谈）。那么你输入账号密码后，系统帮你处理了什么呢？
1. **先查找 /etc/passwd 里面是否有你输入的账号？如果没有则退出，如果有的话则将该账号对应的 UID 与 GID（在 /etc/group 中）读出来，另外，该账号的家目录与 shell 设置也一并读出。**
2. **再来则是核对密码表。这时 Linux 会进入 /etc/shadow 里面找出对应的账号与 UID，然后核对一下你刚刚输入的密码与里面的密码是否相符？**
3. **如果一切都 OK 的话，就进入 shell 管理的阶段**。
大致上的情况就像这样，所以当你要登录你的 Linux 主机的时候，那个 /etc/passwd 与 /etc/shadow 就必须要让系统读取（这也是很多攻击者会将特殊账号写到 /etc/passwd 里面去的缘故）。所以，如果你要备份 Linux 系统的账号的话，那么这两个文件就一定需要备份才行呦。
由上面的流程我们也知道，跟用户账号有关的有两个非常重要的文件，一个是管理用户 UID 与 GID 重要参数的 /etc/passwd，另一个则是专门管理密码相关数据的 /etc/shadow。那这两个文件的内容就非常值得进行研究。下面我们会简单地介绍这两个文件，详细的说明可以参考 man 5 passwd 及 man 5 shadow。

### ◆ /etc/passwd 文件结构

这个文件的构造是这样的：**每一行都代表一个账号，有几行就代表有几个账号在你的系统中，不过需要特别留意的是，里面很多账号本来就是系统正常运行所必须的，我们可以简称它为系统账号，例如 bin、daemon、adm、nobody 等，这些账号请不要随意删除**。这个文件的内容有点像这样：
>鸟哥在接触 Linux 之前曾经碰过 Solaris 系统（1999 年），当时鸟哥啥也不清楚。由于【听说】UNIX 上面的账号越复杂会导致系统越危险，所以鸟哥就将 /etc/passwd 上面的账号全部删除到只剩下 root 与鸟哥自己用的一般账号，结果你猜发生什么事？那就是请 SUN 的工程师来维护系统。
```shell
[root@study ~]# head -n 4 /etc/passwd
root:x:0:0:root:/root:/bin/bash <==等一下作为下面说明用。
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin                                    
bin:x:2:2:bin:/bin:/usr/sbin/nologin                                               
sys:x:3:3:sys:/dev:/usr/sbin/nologin
```
我们先来看一下每个 Linux 系统都会有的第 1 行，就是 root 这个系统管理员那一行。你可以明显地看出来，每一行使用【:】分隔开，共有七个东西分别是：
#### 1. 账号名称
就是账号，提供给对数字不太敏感的人类使用来登录系统的，需要用来对应 UID，例如 root 的 UID 对应就是 0（第三字段）。
#### 2. 密码
早期 UNIX 系统的密码就是放在这字段上，但是因为这个文件的特性是**所有的程序都能够读取**，这样一来很容易造成密码数据被窃取，因此后来就将这个字段的密码数据改放到 /etc/shadow 中了，所以这里你会看到一个【x】，呵呵。
#### 3. UID
这个就是用户标识符。通常 Linux 对于 UID 有几个限制需要说给您了解一下：

| ID 范围 | 该 ID 用户特性 |
| --- | --- |
| 0（系统管理员） | 当 UID 是 0 时，代表这个账号是【系统管理员】，所以当你要让其他的账号名称也具有 root 的权限时，将该账号的 UID 改为 0 即可。这也就是说，一台系统上面的系统管理员不见得只有 root，不过，很不建议有多个账号的 UID 是 0，容易让系统管理员混乱 |
| 1~999（系统账号） | 保留给系统使用的 ID，其实除了 0 之外，其他的 UID 权限与特性并没有不一样。默认 1000 以下的数字留给系统作为保留账号只是一个习惯。 由于系统上面启动的网络服务或后台服务希望使用较小的权限去运行，因此不希望使用 root 的身份去执行这些服务，所以我们就得要提供这些运行中程序的拥有者账号才行。这些系统账号通常是不可登录的，所以才会有我们在第 10 章提到的 /sbin/nologin 这个特殊的 shell 存在。<br>根据系统账号的由来，通常这类账号又大概被区分为两种：<br> - 1~200：由 Linux 发行版自行建立的系统账号 <br> - 201~999：若用户由系统账号需求时，可以使用的账号 UID |
| 1000~60000（可登录账号） | 给一般用户使用。事实上，目前的 Linux 内核（3.10.x 版）已经可以支持到 4394967295（2^32-1）这么大的 UID 号码 |
上面这样说明可以了解了吗？是的，UID 为 0 的时候，就是 root，所以请特别留意一下你的 /etc/passwd 文件。
#### 4. GID
这个与 /etc/group 有关，其实 /etc/group 的概念与 /etc/passwd 差不多，只是它是用来规范组名与 GID 的对应而已。
#### 5. 用户信息说明栏
这个字段基本上并没有什么重要用途，只是用来解释这个账号的意义而已。不过，如果您提供使用 finger 的功能时，这个字段可以提供很多的信息，本章后面的 chfn 命令会解释这里的说明。
#### 6. 家目录
这是用户的家目录，以上面为例，root 的家目录在 /root，所以当 root 登录之后，就会立刻跑到 /root 目录里面。呵呵，如果你个账号的使用空间特别的大，你想要将该账号的家目录移动到其他的硬盘去该怎么做？没有错，可以在这个字段进行修改。默认的用户家目录在 /home/yourIDname。
#### 7. shell
我们在第 10 章 BASH 中提到很多次，当用户登录系统后就会获取一个 shell 来与系统的内核沟通以进行用户的操作任务。那为何默认 shell 会使用 bash 呢？就是在这个字段指定的。这里比较需要注意的是，有一个 shell 可以使账号在登录时无法获得 shell 环境，那就是 /sbin/nologin 这个东西。这也可以用来制作纯 pop 邮件账号的数据。

### ◆ /etc/shadow 文件结构

我们知道很多程序的运行都与权限有关，而权限与 UID 和 GID 有关。因此各程序当然需要读取 /etc/passwd 来了解不同账号的权限，因此 /etc/passwd 的权限需设置为 -rw-r--r-- 这样的情况。虽然早期的密码也有加密过，但却放置到 /etc/passwd 的第二个字段上，这样一来很容易被有心人士所窃取，加密过的密码也能够通过暴力破解法去 trial and error（试误）找出来。
因为这样的关系，所以后来发展出将密码移动到 /etc/shadow 这个文件分隔开的技术，而且还加入很多的密码限制参数在 /etc/shadow 里面。在这里，我们先来了解一下这个文件的构造。鸟哥的 /etc/shadow 文件有点像这样：
```shell
[root@study ~]# head -n 4 /etc/shadow
root:*:20512:0:99999:7:::                                                          
daemon:*:19836:0:99999:7:::                                                        
bin:*:19836:0:99999:7:::                                                           
sys:*:19836:0:99999:7:::
```
基本上，shadow 同样以【:】作为分隔符，如果数一数，会发现共有九个字段，这九个字段的用途是这样的：
#### 1. 账号名称
由于密码也需要与账号对应，因此，这个文件的第一栏就是账号，必须要与 /etc/passwd 相同才行。
#### 2. 密码
这个字段内的数据才是真正的密码，而且是经过编码的密码（摘要）。你只会看到有一些特殊符号的字母。需要特别留意的是，虽然这些加密过的密码难被破解，但是【很难】不等于【不会】，所以这个文件的默认权限是【-rw-------】或是【---------】，即只有 root 才可以读写。你得随时注意，不要不小心修改了这个文件的权限。
另外，由于各种密码编码的技术不一样，因此不同的编码系统会造成这个字段的长度不相同。举例来说，旧式的 DES、MD5 摘要算法产生的密码长度就与目前常用的 SHA 不同。SHA 的密码长度明显比较长些。由于固定的摘要算法产生的密码是特定的，因此【当你修改这个字段后，该密码就会失效（算不出来）】。很多软件通过这个功能，在此字段前加上 ! 或 * 修改密码字段，就会让密码【暂时失效】。
#### 3. 最近修改密码的日期
这个字段记录了【修改密码那一天】的日期，不过，很奇怪呀，在我的例子中怎么会是 16559 呢？呵呵，这个是因为计算 Linux 日期的时间是以 1970 年 1 月 1 日作为 1 而累加的日期，1971 年 1 月 1 日则为 366。得注意一下这个数据，上述得 16559 指的就是 2015-05-04 这一天。了解了么？而想要了解该日期可以使用本章后面的 chage 命令。至于想知道某个日期的累积日数，可使用如下的程序计算：
```shell
[root@study ~]# echo $(( $(date --date="2015/05/04" + %s) / 86400+1))
16559
```
上述命令中，2015/05/04 为你想要计算的日期，86400 为每一天的秒数，%s 为 1970/01/01 以来的累积总秒数。由于 bash 仅支持整数，因此最终需要加上 1 补齐 1970/01/01 当天。
#### 4. 密码不可被修改的天数（与第三字段相比）
第四个字段记录了这个账号的密码在最近一次被更改后需要经过几天才可以再被修改。如果是 0 的话，表示密码随时可以修改，这个限制是为了怕密码被某些人一改再改而设计的。如果设置为 20 天的话，那么当你设置了密码之后，20 天之内都无法再修改这个密码。
#### 5. 密码需要重新修改的天数（与第三字段相比）
经常修改密码是个好习惯。为了强制要求用户修改密码，这个字段可以指定在最近一次更改密码后，在多少天数内需要再次修改密码才行。**你必须要在这个天数内重新设置你的密码，否则这个账号的密码将会【变为过期特性】**。而如果像上面的 99999（计算为 273 年）的话，那就表示密码的修改没有强制性之意。
#### 6. 密码需要修改期限前的警告天数（与第五字段相比）
当账号的密码有效期限快要到的时候（第五字段），系统会根据这个字段的设置，发出【警告】信息给这个账号，提醒它【再过 n 天你的密码就要过期了，请尽快重新设置你的密码】。如上面的例子，则是密码到期之前的 7 天之内，系统会警告该用户。
#### 7. 密码过期后的账号宽限时间（密码失效日）（与第五字段相比）
密码有效日期为【更新日期（第三字段）】+【重新修改日期（第五字段）】，过了该期限后用户依旧没有更新密码，那该密码就算过期了。虽然密码过期但是该账号还是可以用力啊执行其他的任务，包括登录系统获取 bash。**不过如果密码过期了，那当你登录系统时，系统会强制要求你必须要重新设置密码才能登录继续使用，这就是密码过期特性**。
那这个字段的功能是什么呢？是在密码过期几天后，如果用户还是没有登录更改密码，那么这个扎账号的密码将会【失效】，即该帐号再也无法使用该密码登录。要注意**密码过期与密码失效并不相同**。
#### 8. 账号失效日期
这个日期跟第三个字段一样，都是使用 1970 年以来的总天数。这个字段表示：**这个账号在此字段规定的日期之后，将无法再使用**。就是所谓的【账号失效】，此时不论你的密码是否过期，这个【账号】都不能再被使用。这个字段会被使用通常应该是在【收费服务】的系统中，你可以规定一个日期让该账号不能再使用。
#### 9. 保留
最后一个字段是保留的，看以后有没有新功能加入。

## 13.1.3 关于用户组：有效与初始用户组，groups，newgr

认识了与账号相关的两个文件 /etc/passwd 与 /etc/shadow 之后，你或许还是会觉得奇怪，那么用户组的配置文件在哪里呢？还有，在 /etc/passwd 的第四栏不是所谓的 GID 吗？那又是啥？呵呵，此时就需要了解 /etc/group 与 /etc/gshadow。

### ◆ /etc/group 文件结构

这个文件就是在记录 GID 与组名的对应记录，鸟哥测试机的 /etc/group 内容有点像这样：
```shell
[root@study ~]# head -n 4 /etc/group
root:x:0:                                                                          
daemon:x:1:                                                                        
bin:x:2:                                                                           
sys:x:3:
```
这个文件每一行代表一个用户组，也是以冒号【:】作为字段的分隔符，共分为四栏，每一字段的意义是：
#### 1. 组名
就是组名。同样用来给人使用，基本上需要与第三字段的 GID 对应。
#### 2. 用户组密码
通常不需要设置，这个设置通常是给【用户组管理员】使用，目前很少有这个机会设置用户组管理员。同样，密码已经移动到 /etc/gshadow 中，因此这个字段只会存在一个【x】而已。
#### 3. GID
就是用户组 ID。我们 /etc/passwd 第四个字段使用的 GID 对应的用户组名，就是由这里对应出来的。
#### 4. 此用户组支持的账号名称
我们知道一个账号可以加入多个户组，如果某个账号想要加入此用户组时，将该账号填入这个字段即可。举例来说，如果我想要让 dmtsai 与 alex 也加入 root 这个用户组，那么在第一行的最后面加上【dmtsai,alex】，注意不要有空格，使其成为【root:x:0:dmtsai,alex】就可以。
谈完了 /etc/passwd、/etc/shadow、/etc/group 之后，我们可以使用一个简单的示意图来了解一下 UID/GID 与密码之间的关系，图例如下。
![账号相关文件之间的UID/GID与密码相关性示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC13%E7%AB%A0%EF%BC%9ALinux%E8%B4%A6%E5%8F%B7%E7%AE%A1%E7%90%86%E4%B8%8EACL%E6%9D%83%E9%99%90%E8%AE%BE%E7%BD%AE/%E8%B4%A6%E5%8F%B7%E7%9B%B8%E5%85%B3%E6%96%87%E4%BB%B6%E7%A4%BA%E6%84%8F%E5%9B%BE.png)
其实重点是 /etc/passwd，其他相关的数据都是根据这个文件的字段去找寻出来的。右图中，root 的 UID 是 0，而 GID 也是 0，去找 /etc/group 可以知道 GID 为 0 时的组名就是 root。至于密码的寻找中，会找到 /etc/shadow 与 /etc/passwd 内同账号名称的那一个，就是密码相关数据。
至于 /etc/group 比较重要的特色在于第四栏，因为每个用户都可以拥有多个支持的用户组，这就好在学校念书的时候，我们可以加入多个社团一样。不过这里你或许会觉得奇怪，那就是：**【假如我同时加入多个用户组，那么我在作业的时候，到底是以哪个用户组为准呢？】** 下面我们就来谈一谈这个【有效用户组】的概念。
>请注意，新版的 Linux 中，初始用户组的用户群已经不会加入第四个字段，例如我们知道 root 这个账号的主要用户组为 root，但是在上面的范例中，你已经不会看到 root 这个【用户】的名称在 /etc/group 的 root 那一行的第四个字段内，这点还请留意一下。

### ◆ 有效用户组（effective group）与初始用户组（initial group）

还记得每个用户在它的 /etc/passwd 里面的第四栏有所谓的 GID 吧？那个 GID 就是所谓的【初始用户组（initial group）】。也就是说，当用户一登录系统，立即就会拥有这个用户组的相关权限。举例来说，我们上面提到 dmtsai 这个用户的 /etc/passwd 与 /etc/group 还有 /etc/gshadow 相关的内容如下：
```shell
[root@study ~]# grep root /etc/passwd /etc/group /etc/gshadow
/etc/passwd:root:x:0:0:root:/root:/bin/bash                                        
/etc/group:root:x:0:                                                               
/etc/gshadow:root:*::
```
仔细看上面这个表格，在 /etc/passwd 里面，dmtsai 这个用户所谓的用户组为 GID=1000，查找一下 /etc/group 得到 1000 是那个名为 dmtsai 的用户组，这就是初始用户组。因为是初始用户组用户一登录就会主动获取，不需要在 /etc/group 的第四个字段写入该账号。
但是非初始用户组的其他用户组可就不同了。举上面这个例子来说，我将 dmtsai 加入 users 这个用户组当中，由于 users 这个用户组并非是 dmtsai 的初始用户组，因此，我必须要在 /etc/group 这个文件中，找到 users 那一行，并且将 dmtsai 这个账号加入第四栏，这样 dmtsai 才能够接加入 users 这个用户组。
那么在这个例子当中，因为我的 dmtsai 账号同时支持 dmtsai、wheel 与 users 这三个用户组，因此，在读取、写入、执行文件时，针对用户组部分，只要是 users、wheel 与 dmtsai 这三个用户组拥有的功能，dmtsai 这个用户都能够拥有。这样了解了吗？不过，这是针对已经存在的文件而言，如果今天我要建立一个新的文件或是新的目录，请问一下，新文件的用户组是 dmtsai、wheel 还是 users 呢？呵呵，这就要检查一下当时的有效用户组了（effective group）。
### ◆ groups：有效与支持用户组的观察
如果我以 dmtsai 这个用户的身份登录后，该如何知道我所有支持的用户组？很简单，直接输入 groups 就可以了。注意，是 groups 有加 s，结果像这样：
```shell
[dmtsai@study ~]# groups
dmtsai wheel users
```
在这个输出的信息中，可知道 dmtsai 这个用户同时属于 dmtsai、wheel 及 users 这三个用户组，而且，**第一个输出的用户组即为有效用户组**（effective group）。也就是说，我的有效用户组为 dmtsai，此时，如果我以 touch 去建立一个新文件，例如【touch test】，那么这个文件的拥有者为 dmtsai，而且用户组也是 dmtsai。
```shell
[dmtsai@study ~]# touch test
[dmtsai@study ~]# ll test
-rw-rw-r--. 1 dmtsai dmtsai 0 Jul 20 19:54 test
```
这样是否可以了解什么是有效用户组了呢？通常有效用户组的作用是新建文件。那么有效用户组是否能够变换？
### ◆ newgrp：有效用户组的切换
那么如何修改有效用户组呢？要使用 newgrp。不过使用 newgrp 是有限制的，那就是**你想要切换的用户组必须是你已经有支持的用户组**。举例来说，dmtsai 可以在 dmtsai、wheel、users 这三个用户组间切换为有效用户组，但是 dmtsai 无法切换有效用户组成为 sshd，使用的方式如下：
```shell
[dmtsai@study ~]# newgro users
[dmtsai@study ~]# groups
users wheel dmtsai
[dmtsai@study ~]# touch test2
[dmtsai@study ~]# ll test*
-rw-rw-r--. 1 dmtsai dmtsai 0 Jul 20 19:54 test
-rw-r--r--. 1 dmtsai users 0 Jul 20 19:56 test2
[dmtsai@study ~]# exit # 注意，记得退出 newgrp 的环境。
```
此时，dmtsai 的有效用户组就成为 users 了。我们额外来讨论一下 newgrp 这个命令。这个命令可以修改目前用户的有效用户组，而且是**另外以一个 shell 来提供这个功能**。所以，以上面的例子来说，dmtsai 这个用户目前是以另一个 shell 登录的，而且新的 shell 给予 dmtsai 有效 GID 为 users。如果以图例来看就是如下图所示：
![newgrp 的运行示意图](https://linux-1257950569.cos.ap-guangzhou.myqcloud.com/%E9%B8%9F%E5%93%A5%E7%9A%84%20Linux%20%E7%A7%81%E6%88%BF%E8%8F%9C%EF%BC%88%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AF%87%EF%BC%89%E7%AC%AC%E5%9B%9B%E7%89%88/%E7%AC%AC13%E7%AB%A0%EF%BC%9ALinux%E8%B4%A6%E5%8F%B7%E7%AE%A1%E7%90%86%E4%B8%8EACL%E6%9D%83%E9%99%90%E8%AE%BE%E7%BD%AE/newgrp%E7%9A%84%E8%BF%90%E8%A1%8C%E7%A4%BA%E6%84%8F%E5%9B%BE.png)
虽然用户的环境设置（例如环境变量等其他数据）不会有影响，但是用户的【用户组权限】将会重新被计算。需要注意，由于是新获取一个 shell，因此如果你想要回到原本的环境中，请输入 exit 回到原本的 shell。
既然如此，也就是说，只要我的用户有支持的用户组就是能够切换成为有效用户组。好了，那么如何让一个账号加入不同的用户组就是问题的所在。你要加入一个用户组有两个方式，一个方式是通过系统管理员（root）利用 usermod 帮你加入，另一个方式，如果 root 太忙了而且你的系统有设置用户组管理员，那么你可以通过用户组管理员以 gpasswd 帮你加入它所管理的用户组中，详细的方法留待下一小节再来介绍。

### ◆ /etc/gshadow
刚刚讲了很多关于【有效用户组】的概念，也提到了 newgrp 这个命令的用法，但是如果 /etc/gshadow 这个设置没有搞懂的话，那么 newgrp 是无法操作的。鸟哥测试机的 /etc/gshadow 的内容有点像这样：
```shell
[root@study ~]# head -n 4 /etc/gshadow
root:*::                                                                           
daemon:*::                                                                         
bin:*::                                                                            
sys:*::
```
这个文件内同样还是使用冒号【:】来作为字段的分隔字符，而且你会发现，这个文件几乎与 /etc/group 一模一样。是这样没错，不过，要注意的大概就是第二个字段吧，第二个字段是密码栏，如果密码栏上面是【!】或为空时，表示该用户组不具有用户组管理员。至于第四个字段也就是支持的账号名称，这四个字段的意义为：
1. 组名
2. 密码栏，同样的，开头为 ! 表示无合法密码，所以无用户组管理员。
3. 用户组管理员的账号（相关信息在 gpasswd 中介绍）。
4. 有加入该用户组支持的所属账号（与 /etc/group 内容相同）。
以系统管理员的角度来说，这个 gshadow 最大的功能就是**建立用户组管理员**。那么什么是用户组管理员呢？由于系统上面的账号可能会很多，但是我们 root 可能平时太忙碌，所以当有用户想要加入某些用户组时，root 或许会没有空管理。此时如果能够建立用户组管理员的话，那么**该用户组管理员就能够将那个账号加入自己管理的用户组中**，可以免去 root 的忙碌。不过，由于目前有类似 sudo 之类的工具，所以这个用户组管理员的功能已经很少使用了，我们会在后续的 gpasswd 中介绍这方面的内容。

# 13.2 账号管理

好，既然要管理账号，当然是由新增与删除用户开始。下面我们就分别来谈一谈如何新增、删除与修改用户的相关信息吧。

## 13.2.1 新增与删除用户：useradd、相关配置文件、passwd、usermod、userdel

要如何在 Linux 的系统新增一个用户呢？呵呵，真是太简单了，我们登录系统时会输入（1）账号与（2）密码，所以建立一个可用的账号同样的也需要这两个数据。账号可以使用 useradd 来新建，密码则使用 passwd 这个命令设置。这两个命令的执行方法如下：
### ◆ useradd
```shell
[root@study ~]# useradd [-u UID] [-g 初始用户组] [-G 次要用户组] [-mM] [-c 说明栏] [-d 家目录绝对路径] [-s shell] 使用者账号名
选项与参数：
-u：后面接的是 UID，是一组数字，直接指定一个特定的 UID 给这个账号。
-g：后面接的用户组就是上面提到的初始用户组，该用户组的 GID 会被放到 /etc/passwd 的第四个栏位内。
-G：后面接的用户组则是该账号还可加入的用户组，这个选项与参数会修改 /etc/group 内的相关内容。
-M：强制，不要建立使用者家目录。（系统账号默认值）
-m：强制，要建立使用者家目录。（一般账号默认值）
-c：这个就是 /etc/passwd 的第五栏的说明内容，可以随便我们设置的。
-d：指定某个目录成为家目录，而不要使用默认值，务必使用绝对路径。
-r：建立一个系统账号，这个账号的 UID 会有限制。（参考 /etc/login.defs）
-s：后面接一个 shell，若没有指定则默认是 /bin/bash。
-e：后面接一个日期，格式为【YYYY-MM-DD】此选项可写入 shadow 第八栏位，亦即账号失效日的设置选项。
-f：后面接 shadow 的第七栏位选项，指定密码是否户失效，0 为立刻失效，-1 为永远不失效（密码只会过期而强制于登录时重新设置而已）。

范例一：完全参考默认值建立一个使用者，名称为 vbird1。
[root@study ~]# useradd vbird1
[root@study ~]# ll -d /home/vbird1

[root@study ~]# grep vbird1 /etc/passwd /etc/shadow /etc/group
/etc/passwd:vbird1:x:1002:1003::/home/vbird1:/bin/sh                               
/etc/shadow:vbird1:!:20587:0:99999:7:::                                            
/etc/group:vbird1:x:1003: <==默认会建立一个与账号一模一样的用户组名
```
其实系统已经帮我们规定好非常多的默认值了，所以我们可以简单地使用【useradd 账号】来建立用户。CentOS 这些默认值主要会帮我们处理几个选项：
- 在 /etc/passwd 里面建立一行与账号相关的数据，包括建立 UID/GID/家目录等
- 在 /etc/shadow 里面将此账号的密码相关参数写入，但是尚未有密码
- 在 /etc/group 里面加入一个与账号名称一模一样的组名
- 在 /home 下面建立一个与账号同名的目录作为用户家目录，且权限为 700。
由于在 /etc/shadow 内仅会有密码参数而不会有加密过的密码数据，因此我们在建立用户账号时，还需要使用【passwd 账号】来设置密码才算是完成了用户建立的流程。如果由于特殊需求而需要修改用户相关参数时，就得要通过上述表格中的选项来进行建立了，参考下面的案例：
```shell
范例二：假设我已知道我的系统当中有个用户组名称为 users，且 UID 1500 并不存在，请用 users 为初始用户组，以及 uid 为 1500 来建立一个名为 vbird2 的账号。
[root@study ~]# useradd -u 1500 -g users vbird2

[root@study ~]# grep vbird2 /etc/passwd /etc/shadow /etc/group
/etc/passwd:vbird2:x:1500:100::/home/vbird2:/bin/sh                                
/etc/shadow:vbird2:!:20587:0:99999:7:::
# 看一下，UID 与初始用户组确实修改成我们需要的了。
```
在这个范例中，我们建立的是指定一个已经存在的用户组作为用户的初始用户组，因为用户组已经存在，**所以在 /etc/group 里面就不会主动建立与账号同名的用户组了**。此外，我们也指定了特殊的 UID 来作为用户的专属 UID。了解了一般账号后，我们来看看啥是系统账号（system account）吧。
```shell
范例三：建立一个系统账号，名称为 vbird3。
[root@study ~]# useradd -r vbird3

[root@study ~]# grep vbird3 /etc/passwd /etc/shadow /etc/group
/etc/passwd:vbird3:x:999:987::/home/vbird3:/bin/sh                                 
/etc/shadow:vbird3:!:20587::::::                                                   
/etc/group:vbird3:x:987:
```
我们在谈到 UID 的时候曾经说过一般账号应该是 1000 号以后，那用户自己建立的系统账号则一般是小于 1000 号以下。所以在这里我们加上 -r 这个选项以后，系统就会主动将账号与账号同名用户组的 UID/GID 都指定小于 1000 以下，在本案例中则是使用 699（UID）与 699（GID）。此外，由于系统账号主要是用来执行系统所需服务的权限设置，所以**系统账号默认都不会主动建立家目录**。
由这几个范例我们也会知道，使用 useradd 建立用户账号时，其实会修改不少地方，至少我们就知道下面几个文件：
- 用户账号与密码参数方面的文件：/etc/passwd、/etc/shadow
- 用户用户组相关方面的文件：/etc/group、/etc/gshadow
- 用户的家目录：/home/账号名称
那请教一下，你有没有想过，为什么【useradd vbird1】会主动在 /home/vbird1 建立起用户的家目录呢？家目录内有什么数据且来自哪里？为何默认使用的是 /bin/bash 这个 shell 呢？为什么密码字段已经都规范好了（0:99999:7 那一串）？呵呵，这就得要说明一下 useradd 所使用的参考文件。
### ◆ useradd 参考文件

其实 useradd 的默认值可以使用下面的方法查看：
```shell
[root@study ~]# useradd -D
GROUP=100                                                                          
HOME=/home                                                                         
INACTIVE=-1                                                                        
EXPIRE=                                                                            
SHELL=/bin/sh                                                                      
SKEL=/etc/skel                                                                     
CREATE_MAIL_SPOOL=no                                                               
LOG_INIT=yes                                                                       
```
### ◆ passwd
刚刚我们讲到了，使用 useradd 建立了账号之后，在默认的情况下，该账号是暂时被锁定的。也就是说，该账号是无法登录的。你可以去看一看 /etc/shadow 内的第二个字段。那该如何是好？怕什么？直接给它设置新密码就好了嘛，对吧，设置密码就使用 passwd。
```shell
[root@study ~]# passwd [--stdin] [账号名称] <== 所有人均可使用来改自己的密码。
[root@study ~]# passwd [-l] [-u] [--stdin] [-S] [-n 日数] [-x 日数] [-w 日数] [-i 日期] 账号 <==root 功能。
选项与参数：
--stdin：可以通过来自前一个管道的数据，作为密码输入，对 shell 脚本有帮助。
-l：是 Lock 的意思，会将 /etc/shadow 第二栏最前面加上!使密码失效。
-u：与 -l 相对，是 Unlock 的意思。
-S：列出密码相关参数，即 shadow 文件内的大部分信息。
-n：后面接天数，shadow 的第 4 栏位，多久不可修改密码天数。
-x：后面接天数，shadow 的第 5 栏位，多久内必须要修改密码。
-w：后面接天数，shadow 的第 6 栏位，密码过期前的警告天数。
-i：后面接【日期】，shadow 的第 7 栏位，密码失效日期。

范例一：请 root 设置 vbird2 密码。
[root@study ~]# passwd vbird2
Changing password for user vbird2.
New UNIX password: <== 这里直接输入新的密码，屏幕不会有任何反应。
BAD PASSWORD: The password is shorter than 8 characters <==密码太简单或过短的错误。
Retype new UNIX password: <==再输入一次同样的密码。
passwd: all authentication tokens updated successfully. <==竟然还是成功修改了。
```
root 果然是最伟大的人物。当我们要设置用户密码时，通过 root 来设置即可。root 可以设置各式各样的密码，系统几乎一定会接受。所以您看看，如同上面的范例一，明明鸟哥输入的密码太短了，但是系统依旧可接受 vbird2 这样的密码设置。这个是 root 帮忙设置的结果，那如果是用户自己要改密码呢？包括 root 也是这样修改的。
```shell
范例二：用 vbird2 登录后，修改 vbird2 自己的密码。
[vbird2@study ~]# passwd <==后面没有加账号，就是改自己的密码。
Changing password for user vbird2.
Changing password for vbird2
（current）UNIX password: <==这里输入【原有的旧密码】。
New UNIX password: <== 这里输入新设的密码。
BAD PASSWORD: The password fails the dictionary check - it is based on a dictionary word
# 同样的，密码设置在字典里面找的到该字符，所以也是不建议，无法通过。
New UNIX password: <== 这里再想个新的密码来输入吧。
Retype new UNIX password: <==通过密码验证，所以重复这个密码的输入。
passwd: all authentication tokens updated successfully. <==有无成功看关键字。
```
passwd 的使用真的要很注意，尤其是 root 先生。鸟哥在课堂上每次讲到这里，说到要帮自己的一般账号建立密码时，经常有一小部分学生会忘记加上账号名，结果就变成修改 root 自己的密码，最后，root 密码就这样不见了。唉，**要帮一般账号建立密码需要使用【passwd 账号】的格式，使用【passwd】表示修改自己的密码**。拜托，千万不要改错。
与 root 不同的是，一般账号在修改密码时需要先输入自己的旧密码（即 current 那一行），然后再输入新密码（New 那一行）。要注意的是，密码的规范是非常严格的，尤其新的 Linux 发行版大多使用 PAM 模块来进行密码的校验，包括太短、密码与账号相同、密码为字典常见字符串等，都会被 PAM 模块检查出来而拒绝修改密码。此时会再重复出现【New】这个关键词，那时请再想个新密码。若出现【Retype】才是你的密码被接受了，重复输入新密码并且看到【successfully】这个关键词时才是修改密码成功。
>与一般用户不同的是，root 不需要知道旧密码就能够帮用户或 root 自己建立新密码。但如此一来的困扰，就是如果你的亲密爱人老是告诉你【我的密码真难记，帮我设置简单一点】时，千万不要妥协，这是为了系统安全。

为何用户设置自己的密码会这么麻烦？这是因为密码的安全性。如果密码设置太简单，一些有心人士就能够很简单地猜到你的密码，如此一来人家就可能使用你的一般账号登录你的主机或使用其他主机资源，对主机的维护会造成困扰。所以新的 Linux 发行版使用较严重的 PAM 模块来管理密码，这个模块的机制写在 /etc/pam.d/passwd 当中。而**该文件与密码有关的测试模块就是使用 pam_cracklib.so，这个模块会校验密码相关的信息，并且替换 /etc/login.defs 内的 PASS_MIN_LEN 的设置**。关于 PAM，我们在本章后面继续介绍，这里先谈一下，理论上，你的密码最好符合如下要求：
- 密码不能与账号相同
- 密码尽量不要选用字典里面会出现的字符串
- 密码需要超过 8 个字符
- 密码不要使用个人信息，如身份证、手机号码、其他电话号码等
- 密码不要使用简单的关系式，如 1+1=2、lamvbird 等
- 密码尽量使用大小写字符、数字、特殊字符（$、-、_ 等）的组合。
为了方便系统管理，新版的 passwd 还加入了很多创意选项，鸟哥个人认为最好用的大概就是这个【--stdin】了。举例来说，你想要帮 vbird2 修改密码成为 abc543CC，可以这样执行命令。
```shell
范例三：使用 standard input 建立用户的密码。
[root@study ~]# echo "abc543CC" | passwd --stdin vbird2
Changing password for user vbird2.
passwd: all authentication tokens updated successfully.
```
这个操作会直接更新用户的密码而不用再次手动输入。好处是方便处理，缺点是这个密码会保留在命令历史中，未来若系统被攻击，人家可以在 /root/.bash_history 找到这个密码。所以这个操作通常仅在通过 shell 脚本大量建立用户账号时使用。要注意的是，这个选项并不存在所有 Linux 发行版中，请通过 man passwd 确认你使用的 Linux 发行版是否支持此选项。
如果你想要让 vbird2 的密码具有相当的规则，举例来说你要让 vbird2 每 60 天需要修改密码，密码过期后 10 天未使用就声明账号失效，那该如何处理？
```shell
范例四：管理 vbird2 的密码使具有 60 天修改、密码过期 10 天后账号失效的设置。
[root@study ~]# passwd -S vbird2
vbird2 PS 2015-07-20 0 ***** 7 -1（Password set, SHA512 crypt.）
# 上面说明密码建立时间（2015-07-20）、0 最小天数、99999 修改天数、7 警告日数与密码不会失效（-1）
[root@study ~]# passwd -x 60 -i 10 vbird2
[root@study ~]# passwd -S vbird2
vbird2 PS 2015-07-20 0 60 7 10（Password set, SHA512 crypt.）
```
那如果我想要让某个账号暂时无法使用密码登录主机？举例来说，vbird2 这家伙最近老是在主机上乱来，所以我想要暂时让它无法登录的话，最简单的方法就是让它的密码变成不合法（shadow 第 2 字段长度变掉），处理的方法就更简单。
```shell
范例五：让 vbird2 的账号失效，查看完毕后再让它失效。
[root@study ~]# passwd -l vbird2
[root@study ~]# passwd -S vbird2
vbird2 L 2026-05-15 0 60 7 10（Password locked.）
# 嘿嘿，状态变成【LK，Lock】了，无法登录
[root@study ~]# grep vbird2 /etc/shadow
vbird2:!$y$j9T$6J1RlLBDOtEr78CZHw9C0.$PZkUULH2U63uDvqIsC5iVjq5.pw2E6Gi2zCKpBkKCa5:20588:0:60:7:10::
# 其实只是在这里加上!而已。
[root@study ~]# passwd -u vbird2
[root@study ~]# grep vbird2 /etc/shadow
vbird2:$y$j9T$6J1RlLBDOtEr78CZHw9C0.$PZkUULH2U63uDvqIsC5iVjq5.pw2E6Gi2zCKpBkKCa5:20588:0:60:7:10::
# 密码栏位恢复正常
```
是否很有趣？可以自行管理一下你的账号的密码相关参数，接下来让我们用更简单的方法来查看密码参数。

### ◆ chage

```shell
[root@study ~]# chage [-ldEImMW] 账号名
选项与参数：
-l：列出该账号的详细密码参数
-d：后面接日期，修改 shadow 第三栏位（最近一次修改密码的日期），格式 YYYY-MM-DD
-E：后面接日期，修改 shadow 第八栏位（账号失效日），格式 YYYY-MM-DD
-I：后面接天数，修改 shadow 第七栏位（密码失效日期）
-m：后面接天数，修改 shadow 第四栏位（密码最短保留天数）
-M：后面接天数，修改 shadow 第五栏位（密码多久需要进行修改）
-W：后面接天数，修改 shadow 第六栏位（密码过期前警告日期）
范例一：列出 vbird2 的详细密码参数。
[root@study ~]# chage -l vbird2
Last password change                                    : May 15, 2026             
Password expires                                        : Jul 14, 2026             
Password inactive                                       : Jul 24, 2026             
Account expires                                         : never                    
Minimum number of days between password change          : 0                        
Maximum number of days between password change          : 60                       
Number of days of warning before password expires       : 7
```
我们在 passwd 的介绍中谈到了处理 vbird2 这个账号的密码属性流程，使用 passwd -S 却无法看到很清楚的说明，但使用 chage 可就明白多了。如上表所示，我们可以清楚地知道 vbird2 地详细参数。如果想要修改其他地设置值，就自己参考上面的选项，或自行 man chage 一下吧。
chage 有一个功能很不错，如果你想要让【用户在第一次登录时，强制它们一定要修改密码后才能够使用系统资源】，可以利用如下的方法来处理。
```shell
范例二：建立一个名为 vbird2 的账号，该账号第一次登录后使用默认密码，但必须要修改过密码后，使用新密码才能够登录系统使用 bash 环境。
[root@study ~]# useradd vbird2
[root@study ~]# echo "vbird2" | passwd --stdin vbird2
[root@study ~]# chage -d 0 vbird2
[root@study ~]# chage -l vbird2 | head -n 3
Last password change                                    : password must be changed 
Password expires                                        : password must be changed 
Password inactive                                       : password must be changed
# 此时此账号的密码建立时间会被改为 1970/1/1，所以会有问题。
```
非常有趣吧，你会发现 vbird2 这个账号在第一次登录时可以使用与账号同名的密码登录，但登录时就会被要求立刻修改密码，修改密码完成后就会被踢出系统，再次登录时就能够使用新密码登录了。这个功能对学校老师非常有帮助。因为我们不想要知道学生的密码，那么在初次上课时就可以使用与学号相同的账号密码给学生，让他们登录时自行设置自己的密码，如此一来既能够避免其他同学随意使用别人的账号，也能够保证学生知道如何修改自己的密码。

### ◆ usermod

```shell
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
如果你仔细地比对，会发现 usermod 的选项与 useradd 非常类似，这是因为 usermod 也是用来微调 useradd 增加的用户参数。不过 usermod 还是有新增的选项，那就是 -L 与 -U。不过这两个选项其实与 passwd 的 -l 以及 -u 是相同的，而且也不见得会存在于所有的 Linux 发行版当中。接下来，让我们谈谈一些修改参数的实例吧。
```shell
范例一：修改使用者 vbird2 的说明栏，加上【VBird test】的说明。
[root@study ~]# usermod -c "VBirds test" vbird2
[root@study ~]# grep vbird2 /etc/passwd
vbird2:x:1002:1003:VBirds test:/home/vbird2:/bin/sh
范例二：使用者 vbird2 这个账号在 2015/12/31 失效。
[root@study ~]# usermod -e "2015-12-31" vbird2
[root@study ~]# chage -l vbird2 | grep 'Account expires'
Account expires                                         : Dec 31, 2015
范例三：我们建立 vbird3 这个系统账号时并没有设置家目录，请建立它的家目录。
[root@study ~]# ll -d ~vbird3
ls: cannot access /home/vbird3: No such file or directory <==确认一下，确实没有家目录的存在。
[root@study ~]# cp -a /etc/skel /home/vbird3
[root@study ~]# chown -R vbird3:vbird3 /home/vbird3
[root@study ~]# chmod 700 /home/vbird3
```

### ◆ userdel

这个功能就太简单了，目的在删除用户的相关数据，而用户的数据有：
- 用户账号/密码相关参数：/etc/passwd、/etc/shadow
- 用户组相关参数：/etc/group、/etc/gshadow
- 用户个人文件数据：/home/username、/var/spool/mail/username
这个命令的语法非常简单：
```shell
[root@study ~]# userdel [-r] username
选项与参数：
-r：连同使用者的家目录也一起删除。
范例一：删除 vbird2，连同家目录一起删除。
[root@study ~]# userdel -r vbird2
```
执行这个命令的时候要小心了。通常我们要删除一个账号的时候，可以手动将 /etc/passwd 与 /etc/shadow 里面的该账号取消。一般而言，如果该账号只是【暂时不可用】的话，那么将 /etc/shadow 里面的账号失效日期（第八字段）设置为 0 就可以让该账号无法使用，但是所有跟该账号相关的数据都会留下来。使用 userdel 的时机通常是【你真的确定不要让该用户在主机上面使用任何数据了】。
另外，如果用户在系统上面操作过一阵子了，那么该用户其实在系统内可能会含有其他文件。举例来说，他的邮箱（mailbox）或是计划任务（crontab，第 15 章）之类的文件。所以，如果想要将某个账号完整删除，最好在执行 userdel -r username 之前，先用【find / -user username】查出整个系统内属于 username 的文件，然后再加以删除吧。

## 13.2.2 用户功能

useradd、usermod、userdel 都是系统管理员所能够使用的命令，如果我是一般身份用户，那么我是否除了密码之外，就无法修改其他的数据？当然不是。这里我们介绍几个一般身份用户常用的账号数据修改与查询命令。

### ◆ id

id 这个命令可以查询某人或自己的相关 UID/GID 等信息，它的参数也不少，不过，都不需要记，反正使用 id 时就全部都列出来了。另外，也回想一下，我们在前一章谈到循环时，就用过这个命令：
```shell
[root@study ~]# id [username]
范例一：查看 root 自己相关 ID 信息。
[root@study ~]# id
uid=0(root) gid=0(root) groups=0(root)
范例二：查看一下 vbird1 吧
[root@study ~]# id vbird1
uid=1002(vbird2) gid=1003(vbird2) groups=1003(vbird2)
[root@study ~]# id vbird100
id: vbird100: no such user <== id 这个命令也可以用来判断系统上面有无某账号。
```

## 13.2.3 新增与删除用户组

OK，了解了账号的新增、删除、修改与查询后，再来我们可以聊一聊用户组的相关内容了。基本上，用户组的内容都与这两个文件有关：/etc/group、/etc/gshadow。用户组的内容其实很简单，都是上面两个文件的新增、修改与删除而已。不过，如果再加上有效用户组的概念，那么 newgrp 与 gpasswd 则不可不知。

### ◆ groupadd

```shell
[root@study ~]# groupadd [-g gid] [-r] 用户组名称
选项与参数：
-g：后面接某个特定的 GID，用来直接设置某个 GID
-r：建立系统用户组，与 /etc/login.defs 内的 GID_MIN 有关。
范例一：新建一个用户组，名称为 group1。
新建一个用户组，名称为 group1
[root@study ~]# groupadd group1
/etc/group:group1:x:1004:                                                          
/etc/gshadow:group1:!::
# 用户组的 GID 也是会由 1000 以上最大 GID+1 来决定。
```
曾经有某些版本的教育培训手册谈到，为了让用户的 UID/GID 成对，它们建议**新建与用户私有用户组无关的其他用户组时，使用小于 1000 的 GID 为宜**。也就是说，如果要建立用户组的话，最好能够使用【groupadd -r 用户组名】的方式。不过，这见仁见智，看你自己的抉择。

### ◆ groupmod

跟 usermod 类似的，这个命令仅是在进行 group 相关参数的修改而已。
```shell
[root@study ~]# groupmod [-g gid] [-n group_name] 用户组名
选项与参数：
-g：修改既有的 GID 数字
-n：修改既有的用户组名称
范例一：将刚刚上个命令建立的 group1 名称改为 mygroup，GID 为 201。
[root@study ~]# groupmod -g 201 -n mygroup group1
[root@study ~]# grep mygroup /etc/group /etc/gshadow
/etc/group:group1:x:1004:                                                          
/etc/gshadow:group1:!::
```
不过，还是那句老话，不要随意修改 GID，容易造成系统资源的错乱。

### ◆ groupdel

呼呼，groupdel 自然就是用在删除用户组，用法很简单：
```shell
[root@study ~]# groupdel [groupname]
范例一：将刚刚的mygroup删除。
[root@study ~]# groupdel mygroup
范例二：若要删除 vbird1 这个用户组的话？
[root@study ~]# groupdel vbird1
```
为什么 mygroup 可以删除，但是 vbird1 就不能删除？愿因很简单，【有某个账号（/etc/passwd）的初始用户组使用该用户组】。如果查看一下，你会发现在 /etc/passwd 内的 vbird1 第四栏的 GID 就是 /etc/group 内的 vbird1 那个用户组的 GID。所以，当然无法删除，否则 vbird1 这个用户登录系统后，就会找不到 GID，那可是会造成很大的困扰。那么如果硬要删除 vbird1 这个用户组呢？你【必须要确认 /etc/passwd 内的账号没有任何人使用该用户组作为初始用户组】才行。所以，你可以：
- 修改 vbird1 的 GID。
- 删除 vbird1 这个用户。
### ◆ gpasswd：用户组管理员功能

如果系统管理员太忙碌了，导致某些账号想要加入某个选项时找不到人帮忙，这个时候可以建立【用户组管理员】。什么是用户组管理员？就是让某个用户组具有一个管理员，这个用户组管理员可以管理哪些账号可以加入/移出该用户组。那要如何【建立一个用户组管理员】？就得要通过 gpasswd。
```shell
# 关于系统管理员（root）做的操作
[root@study ~]# gpasswd groupname
[root@study ~]# gpasswd [-A user1,...] [-M user3,...] groupname
[root@study ~]# gpasswd [-rR] groupname
选项与参数：
  ：若没有任何参数时，表示设置 groupname 密码（/etc/gshadow）
-A：将 groupname 的管理权交由后面的使用者管理（该用户组的管理员）。
-M：将某些账号加入这个用户组当中。
-r：将 groupname 的密码删除。
-R：让 groupname 的密码栏失效。
# 关于用户组管理（Group administrator）做的操作。
[someone@study ~]# gpasswd [-ad] user groupname
选项与参数：
-a：将某位使用者加入到 groupname 这个用户组当中。
-d：将某位使用者删除出 groupname 这个用户组当中。
范例一：建立一个新用户组，名称为 testgroup 且用户组交由 vbird1 管理。
[root@study ~]# groupadd testgroup <==先建立用户组。
[root@study ~]# gpasswd testgroup <==给这个用户组一个密码。
New Password:
Re-enter new password:
# 输入两次密码就对了。
[root@study ~]# gpasswd -A vbird1 testgroup <==加入用户组管理员为 vbird1。
[root@study ~]# grep testgroup /etc/group /etc/gshadow

范例二：以 vbird1 登录系统，并且让它加入 vbird1、vbird3 成为 testgroup 成员。
[vbird1@study ~]# id
uid=1003（vbird1）gid=1004（vbird1）groups=1004（vbird1）...
#看得出来，vbird1 尚未加入 testgroup 用户组。
[vbird1@study ~]# gpasswd -a vbird1 testgroup
[vbird1@study ~]# gpasswd -a vbird3 testgroup
[vbird1@study ~]# grep testgroup /etc/group
testgroup:x:1503:vbird1:vbird3
```
很有趣的一个小实验吧，我们可以让 testgroup 成为一个可以公开的用户组，然后建立起用户组管理员，用户组管理员可以有多个。在这个案例中，我将 vbird1 设置为 testgroup 的用户组管理员，所以 vbird1 就可以自行增加用户组成员。然后，该用户组成员就能够使用 newgrp。















​                                                             



​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           

​	



​                                                                                                                                                    

​              