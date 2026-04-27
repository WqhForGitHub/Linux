# 6.1 目录与路径

由前一章 Linux 的文件权限与目录配置中通过 FHS 了解了 Linux 的树状目录概念之后，接下来就得实际地来搞定一些基本的路径问题了。这些目录的问题当中，最重要的莫过于前一章也谈过的绝对路径与相对路径的意义。绝对 / 相对路径的写法并不相同，要特别注意。此外，当你执行命令时，该命令是如何找到的？这与 PATH 这个变量有关，下面就让我们来谈谈。

## 6.1.1 相对路径与绝对路径

在开始目录的切换之前，你必须要先了解一下所谓的路径（PATH），有趣的是：什么是相对路径与绝对路径？虽然前一章已经针对这个问题提过一次，不过，这里不厌其烦地再次强调一下。

- **绝对路径**：路径的写法 “一定由根目录`/`写起”，例如：`/usr/share/doc`这个目录。
- **相对路径**：路径的写法 “不是由`/`写起”，例如由`/usr/share/doc`要到`/usr/share/man`下面时，可以写成：“`cd ../man`” 这就是相对路径的写法，相对路径意指相对于目前工作目录的路径。

### ◆ 相对路径的用途

那么相对路径与绝对路径有什么了不起呀？呵，那可真的是了不起。假设你编写了一个软件，这个软件共需要三个目录，分别是`etc`、`bin`、`man`这三个目录，然而由于不同的人喜欢安装在不同的目录之下，假设甲安装的目录是`/usr/local/packages/etc`、`/usr/local/packages/bin`及`/usr/local/packages/man`，不过乙却喜欢安装在`/home/packages/etc`、`/home/packages/bin`、`/home/packages/man`这三个目录中，请问如果需要用到绝对路径的话，那么是否很麻烦？是的，如此一来每个目录下的东西就很难对应起来，这个时候相对路径的写法就显得特别的重要了。

此外，如果你跟鸟哥一样，喜欢将路径的名字写得很长，好让自己知道哪个目录是在干什么的，例如：`/cluster/raid/output/taiwan2006/smoke`这个目录，而另一个目录在`/cluster/raid/output/taiwan2006/cctm`，那么我从第一个要到第二个目录去的话，怎么写比较方便？当然是 “`cd ../cctm`” 比较方便，对吧！

### ◆ 绝对路径的用途

但是对于文件名的正确性来说，绝对路径的正确度要比较好。一般来说，鸟哥会建议你，如果是在写程序（shell 脚本）来管理系统的条件下，务必使用绝对路径。怎么说？因为绝对路径的写法虽然比较麻烦，但是可以肯定这个写法绝对不会有问题。如果使用相对路径在程序当中，则可能由于你执行的工作环境不同，导致一些问题的发生。这个问题在计划任务（`at`与`cron`，第 15 章）当中尤其重要，且这个现象我们在 12 章 shell 脚本时，也会再次提醒你。

## 6.1.2 目录的相关操作

我们之前提到切换目录的命令是`cd`，还有哪些可以进行目录操作的命令呢？例如建立目录、删除目录之类，还有要先知道的就是有哪些比较特殊的目录？举例来说，下面这些就是比较特殊的目录，得要用力地记下来才行：

```bash
. 	代表此目录。
..	代表上一层目录。
- 	代表前一个工作目录。
~	代表目前使用者身份所在的家目录。
~account	代表 account 这个使用者的家目录（account 是个账号名称）。
```

需要特别注意的是：在所有目录下面都会存在的两个目录，分别是 “`.`” 与 “`..`” 分别代表此层与上层目录的意思。那么来思考一下下面这个例题：

>例题
>
>请问在 Linux 下面，根目录有没有上层目录（`..`）存在？
>
>答：若使用 “`ls -al`” 去查询，可以看到根目录下确实存在`. 与 ..`两个目录，再仔细查看，可发现这两个目录的属性与权限完全一致，这代表根目录的上一层（`..`）与根目录自己（`.`）是同一个目录。

下面我们就来谈一谈几个常见的处理目录的命令：

- `cd`：切换目录
- `pwd`：显示当前目录
- `mkdir`：建立一个新目录
- `rmdir`：删除一个空目录

### ◆ cd（change directory，切换目录）

我们知道 dmtsai 这个用户的家目录是`/home/dmtsai/`，而 root 家目录则是`/root/`，假设我以 root 身份在 Linux 系统中，那么简单说明一下这几个特殊目录的意义是：

```bash
[dmtsai@study ~]$ su - # 先切换身份成为 root 看看。
[root@study ~]# cd [相对路径或绝对路径]
# 最重要的就是目录的绝对路径与相对路径，还有一些特殊目录的符号。
[root@study ~]# cd ~dmtsai
# 代表进入 dmtsai 这个使用者的家目录，亦即/home/dmtsai。
[root@study dmtsai]# cd ~
# 表示回到自己的家目录，亦即是/root 这个目录。
[root@study ~]# cd
# 没有加上任何路径，也还是代表回到自己家目录的意思。
[root@study ~]# cd ..
# 表示去到目前的上层目录，亦即是/root 的上层目录的意思。
[root@study /]# cd -
# 表示回到刚刚的那个目录，也就是/root。
[root@study ~]# cd /var/spool/mail
# 这个就是绝对路径的写法。直接指定要去的完整路径名称。
[root@study mail]# cd ../postfix
# 这个是相对路径的写法，我们由/var/spool/mail 到/var/spool/postfix 就这样写。
```

cd 是 Change Directory 的缩写，这是用来切换工作目录的命令，注意目录名称与 cd 命令之间存在一个空格。当登录 Linux 系统后，每个账号都会在自己账号的家目录中，那回到上一层目录可以用 “`cd ..`”。利用相对路径的写法必须要确认你目前的路径才能正确地去到想要去的目录。例如上表当中最后一个例子，你必须要确认你是在`/var/spool/mail`当中，并且知道在`/var/spool`当中有个 mqueue 的目录才行，这样才能使用`cd ../postfix`进入正确的目录，否则就要直接输入`cd /var/spool/postfix`。

其实，我们的提示字符，亦即那个`[root@study ~]#`当中，就已经有指出当前目录了，刚登录时会到自己的家目录，而家目录还有一个符号，那就是 “`~`”。例如上面的例子可以发现，使用 “`cd ~`” 可以回到自己的家目录里面。另外，针对 cd 的使用方法，如果仅输入`cd`时，代表的就是 “`cd ~`” 的意思，亦即回到自己的家目录。而那个 “`cd -`” 比较难以理解，请自行多做几次练习，就会明白了。

>还是要一再地提醒，我们的 Linux 的默认命令行模式（bash shell）具有文件补齐功能，你要常常利用 [Tab] 按键来自动补全目录路径。这可是个好习惯，可以避免你按错键盘输入错字。

### ◆ pwd（显示目前所在的目录）

```bash
[root@study ~]# pwd [-P]
选项与参数：
-P：显示出真正的路径，而非使用链接（link）路径。

范例：单纯显示出目前的工作目录
[root@study ~]# pwd
/root  <== 显示出工作目录。
范例：显示出真正的目录，而非链接文件本身的目录名而已。
[root@study ~]# cd /var/mail  <==注意，/var/mail是一个链接文件。
[root@study mail]# pwd
/var/mail  <==pwd只显示目前的工作目录。
[root@study mail]# pwd -P
/var/spool/mail  <==怎么回事？有没有加-P差很多。
[root@study mail]# ls -ld /var/mail
lrwxrwxrwx. 1 root root 10 May  4 17:51 /var/mail -> spool/mail
# 看到这里应该知道为啥了吧？因为/var/mail是链接文件，链接到/var/spool/mail。
# 所以，加上pwd -P的选项后，不会显示链接文件的路径，而是显示正确的完整路径。
```

pwd 是 Print Working Directory 的缩写，也就是显示目前所在目录的命令，例如在上面最后的目录是`/var/mail`，但是提示字符仅显示 mail，如果你想要知道目前所在的目录，可以输入 pwd 即可。此外，由于很多的软件所使用的目录名称都相同，例如`/usr/local/etc/`和`/etc`，但是通常 Linux 仅列出最后面那一个目录而已，这个时候你就可以使用 pwd 来知道你的所在目录。

其实有趣的是那个`-P`的选项。它可以让我们取得正确的目录名称，免得搞错目录，造成损失。显示的。如果你使用的是 CentOS 7.x 的话，刚好`/var/mail`是`/var/spool/mail`的链接文件，通过到`/var/mail`执行`pwd -P`就能够知道这个选项的意义。

### ◆ mkdir（建立新目录）

```bash
[root@study ~]# mkdir [-mp] 目录名称

选项与参数：
-m：设置文件的权限。直接设置，不使用默认权限（umask）。
-p：帮助你直接将所需要的目录（包含上层目录）递回创建。

范例：请到 /tmp 下面尝试建立数个新目录看看：
[root@study ~]# cd /tmp
[root@study tmp]# mkdir test  <==建立一名为test的新目录。
[root@study tmp]# mkdir test1/test2/test3/test4
mkdir: cannot create directory ‘test1/test2/test3/test4’: No such file or directory
# 话说，系统告诉我们，不可能建立这个目录，就是没有目录才要建立的，见鬼哦？
[root@study tmp]# mkdir -p test1/test2/test3/test4
# 原来是要建test4上层先建test3的原因，加了这个-p的选项，可以自行帮你建立多层目录。

范例：建立权限为 rwx--x--x 的目录。
[root@study tmp]# mkdir -m 711 test2
[root@study tmp]# ls -ld test*
drwxr-xr-x. 2 root root  6 Jun  4 19:03 test
drwxr-xr-x. 3 root root 18 Jun  4 19:04 test1
drwx--x--x. 2 root root  6 Jun  4 19:05 test2
# 仔细看上面的权限部分，如果没有加上-m来强制设置属性，系统会使用默认属性。
# 那么你的默认属性是什么？这要通过下面的umask才能了解。
```

如果想要建立新的目录的话，那么就使用 mkdir（make directory）吧！不过，在默认的情况下，你如果想要建立新的目录的话，那么例如，假如你要建立一个目录为`/home/bird/testing/test1`，那么首先必须要有`/home/bird`这个目录，然后`/home/bird/testing`都必须要存在，才可以建立`/home/bird/testing/test1`这个目录。假如没有`/home/bird/testing`时，就没有办法建立 test1 的目录。

不过，现在有个更简单且有效的方法，那就是加上`-p`这个选项，你可以直接执行：`mkdir -p /home/bird/testing/test1`，则系统会自动帮你将`/home/bird/`、`/home/bird/testing/`依序地建立起目录。并且，如果该目录本来就已经存在时，系统也不会显示错误信息。挺快乐的吧！不过鸟哥不建议常用 -p 这个选项，因为担心如果你打错字，那么目录名称就会变得乱七八糟。

另外，有个地方你必须要先有概念，那就是默认权限。我们可以利用`-m`来强制设置一个新目录相关的权限，例如上表当中，我们给予`-m 711`来给予新的目录`drwx--x--x`的权限。不过，如果没有使用`-m`选项时，那么默认的新建目录权限又是什么？这个跟`umask`有关，我们在本章后面会加以介绍。

### ◆ rmdir（删除 “空” 的目录）

```bash
[root@study ~]# rmdir [-p] 目录名称

选项与参数：
-p：连同上层 “空” 的目录也一起删除。

范例：将于mkdir范例中建立的目录（/tmp下面）删除掉。
[root@study tmp]# ls -ld test*  <==看看有多少目录存在？
drwxr-xr-x. 2 root root 6 Jun  4 19:03 test
drwxr-xr-x. 3 root root 18 Jun 4 19:04 test1
drwxr--x--x. 2 root root 6 Jun  4 19:05 test2
[root@study tmp]# rmdir test  <==可直接删除掉，没问题。
[root@study tmp]# rmdir test1  <==因为尚有内容，所以无法删除。
rmdir: failed to remove 'test1': Directory not empty
[root@study tmp]# rmdir -p test1/test2/test3/test4
[root@study tmp]# ls -ld test*  <==您看看，下面的输出中 test 与 test1 不见了。
drwx--x--x. 2 root root 6 Jun  4 19:05 test2
# 使用-p选项，主机会将test1/test2/test3/test4一次删除，不过要注意，这个rmdir仅能 “删除空目录”。
```

如果想要删除旧有的目录时，就使用`rmdir`。例如将刚刚建立的`test`删掉，使用`[rmdir test]`即可。请注意，目录需要一层一层的删除才行，而且被删除的目录里面必定不能存在其他的目录或文件，这也是所谓的空目录（empty directory）的意思。那如果要将所有目录下的东西都删除？这个时候就必须使用`[rm -r test]`。不过，还是使用`rmdir`比较安全，你也可以尝试以`-p`选项来删除上层空的目录。

## 6.1.3 关于执行文件路径的变量：$PATH

经过前一章 FHS 的说明后，我们知道查看文件属性的命令`ls`完整文件名为：`/bin/ls`（这是绝对路径），那你会不会觉得很奇怪：“为什么我可以在任何地方执行`/bin/ls`这个命令？” 为什么我在任何目录下输入`ls`就一定可以显示出一些信息而不会说找不到该`/bin/ls`命令？这是因为环境变量`PATH`的帮助所致。

当我们在执行一个命令的时候，举例来说`ls`好了，系统会依照`PATH`的设置去每个`PATH`定义的目录下查找文件名为`ls`的可执行文件，如果在`PATH`定义的目录中含有多个文件名为`ls`的可执行文件，那么先查找到的同名命令先被执行。

现在，请执行`[echo $PATH]`来看看到底有哪些目录被定义出来了？`echo`有 “显示、打印” 的意思，而`PATH`前面加的`$`表示后面接的是变量，所以会显示出目前的`PATH`。

```bash
范例：先用 root 的身份列出查找的路径是什么？
[root@study ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

范例：用 dmtsai 的身份列出查找的路径是什么？
[root@study ~]# exit  <==由之前的 su - 离开，变回原本的账号，或再取得一个终端皆可。
[dmtsai@study ~]$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/dmtsai/.local/bin:/home/dmtsai/bin
# 记不得我们前一章说过，目前/bin是链接到/usr/bin当中。
```

`PATH`（一定是大写）这个变量的内容是由一堆目录所组成，每个目录中间用冒号（`:`）来隔开，每个目录有顺序之分。仔细看一下上面的输出，你可以发现到无论是`root`还是`dmtsai`都有`/bin`或`/usr/bin`这个目录在`PATH`变量内，所以当然就能够在任何地方执行`ls`来找到`/bin/ls`执行文件。因为`/bin`在 CentOS 7 当中就是链接的`/usr/bin`，所以这两个目录内容会一模一样。

我们用几个范例来让你了解一下，为什么`PATH`是那么重要的项目。

>例题
>
>假设你是`root`，如果你将`ls`由`/bin/ls`移动成为`/root/ls`（可用`【mv /bin/ls /root】`命令完成），然后你自己本身也在`/root`目录下，请问（1）你能不能直接输入`ls`来执行？（2）若不能，你该如何执行`ls`这个命令？（3）若要直接输入`ls`即可执行，又该如何进行？
>
>答：由于这个例题的重点是将某个执行文件移动到非正规目录去，所以我们先要进行下面的操作才行（务必先使用`su -`切换成为`root`的身份）：
>
>```bash
>[root@study ~]# mv /bin/ls /root
># mv为移动，可将文件在不同的目录间进行移动操作。
>```
>
>（1）接下来不论你在哪个目录下面输入任何与`ls`相关的命令，都没有办法顺利地执行`ls`。也就是说，你不能直接输入`ls`来执行，因为`/root`这个目录并不在`PATH`指定的目录中，所以，即使你在`/root`目录下，也不能够查找`ls`这个命令。
>
>（2）因为这个`ls`确实存在于`/root`下面，并不是被删除了。所以我们可以通过使用绝对路径或是相对路径直接指定这个执行文件，下面的两个方法都能够执行`ls`这个命令：
>
>```
>[root@study ~]# /root/ls  <==直接用绝对路径设定文件名。
>[root@study ~]# ./ls      <==因为在/root目录下，就用./ls来指定。
>```
>
>（3）如果想要让`root`在任何目录均可执行`/root`下面的`ls`，那么就将`/root`加入`PATH`当中即可。加入的方法很简单，就像下面这样：
>
>```bash
>[root@study ~]# PATH="$PATH:/root"
>```
>
>上面这个做法就能够将`/root`加入到执行文件查找路径`PATH`中了，不相信的话请您自行使用`【echo $PATH】`去查看。另外，除了`$PATH`之外，如果想要更明确地定义出变量的名称，可以使用大括号`${PATH}`来处理变量的调用。如果确定这个例题进行的没有问题，请将`ls`移回`/bin`下面，不然系统会挂。
>
>```
>[root@study ~]# mv /root/ls /bin
>```
>
>某些情况下，即使你已经将`ls`移回了`/bin`目录，不过系统还是会告知你无法处理`/root/ls`。很可能是因为指向参数被缓存的关系。不要紧张，只要注销（`exit`）再登录（`su -`）就可以继续快乐地使用`ls`命令了。

>例题
>
>如果我有两个`ls`命令在不同的目录中，例如`/usr/local/bin/ls`与`/bin/ls`那么当我执行`ls`的时候，哪个`ls`会被执行？
>
>答：那还用说，就找出`${PATH}`里面哪个目录先被查询，则那个目录下的命令就会被先执行。所以用`dmtsai`账号为例，它最先查找的是`/usr/local/bin`，所以`/usr/local/bin/ls`会先被执行。

>例题
>
>为什么`${PATH}`查找的目录不加入本目录（`.`）？加入本目录的查找不是也不错？
>
>答：如果在`PATH`中加入本目录（`.`）后，确实我们就能够在命令所在目录进行命令的执行。但是由于你的工作目录并非固定（常常会使用`cd`来切换到不同的目录），因此能够执行的命令会有变动（因为每个目录下面的可执行文件都不相同），这对用户来说并非好事。
>
>另外，如果有个别有用心的用户在`/tmp`下面做了一个命令，因为`/tmp`是大家都能够写入的环境，所以他当然可以这样做。假设该命令可能会窃取用户的一些数据，如果你使用`root`的身份来执行这个命令，那不是很糟糕？如果这个命令的名称又是经常会被用到的`ls`时，那 “中标” 的机率就更高了。
>
>所以，为了安全起见，不建议将 “`.`” 加入`PATH`的查找目录中，这一点同 Windows 的习惯不同。
>
>而由上面的几个例题我们也可以知道几件事情：
>
>◆ 不同身份用户默认的`PATH`不同，默认能够随意执行的命令也不同（如`root`与`dmtsai`）；
>
>◆ `PATH`是可以修改的；
>
>◆ 使用绝对路径或相对路径直接指定某个命令的文件名来执行，会比查找`PATH`来的正确；
>
>◆ 命令应该要放置到正确的目录下，执行才会比较方便；
>
>◆ 本目录（`.`）最好不要放到`PATH`当中。
>
>关于`PATH`更详细的变量说明，我们会在第三篇的 bash shell 中详细介绍。

# 6.2 文件与目录管理

谈了谈目录与路径之后，再来讨论一下关于文件的一些基本管理。文件与目录的管理上，不外乎显示属性、复制、删除文件及移动文件或目录等，由于文件与目录的管理在 Linux 当中是很重要的内容，尤其是每个人自己家目录的数据也都需要注意管理，所以我们来谈一谈有关文件与目录的一些基础管理部分。

## 6.2.1 文件与目录的查看：ls

### ◆ ls

```bash
[root@study ~]# ls [-aAdfFhilnrRSt] 文件名或目录名称
[root@study ~]# ls [--color={never,auto,always}] 文件名或目录名称
[root@study ~]# ls [--full-time] 文件名或目录名称
选项与参数：
-a：全部的文件，连同隐藏文件（开头为.的文件）一起列出来（常用）；
-A：全部的文件，连同隐藏文件，但不包括.与..这两个目录；
-d：仅列出目录本身，而不是列出目录内的文件数据（常用）；
-f：直接列出结果，而不进行排序（ls默认会以文件名排序）；
-F：根据文件、目录等信息，给予附加数据结构，例如：
*：代表可执行文件；/：代表目录；=：代表 socket 文件；|：代表 FIFO 文件；
-h：将文件容量以人类较易读的方式（例如 GB、KB 等）列出来；
-i：列出inode号码，inode的意义下一章将会介绍；
-l：详细信息显示，包含文件的属性与权限等数据（常用）；
-n：列出 UID 与 GID 而非使用者与用户组的名称（UID 与 GID 会在账号管理提到）；
-r：将排序结果反向输出，例如：原本文件名由小到大，反向则为由大到小；
-R：连同子目录内容一起列出来，等于该目录下的所有文件都会显示出来；
-S：以文件容量大小排序，而不是用文件名排序；
-t：以时间排序，而不是用文件名；
--color=never：不要依据文件特性给予颜色显示；
--color=always：显示颜色；
--color=auto：让系统自行依据设置来判断是否给予颜色；
--full-time：以完整时间模式（包含年、月、日、时、分）输出；
--time={atime,ctime}：输出access time或change time（ctime），而非内容修改时间（modification time）。
```

在 Linux 系统当中，这个`ls`命令可能是最常被执行的吧！因为我们随时都要知道文件或是目录的相关信息，不过，我们 Linux 的文件所记录的信息实在是太多了，`ls`没有需要全部都列出来，所以当你只有执行`ls`时，默认显示的只有：非隐藏文件的文件名、以文件名进行排序及文件名代表的颜色显示如此而已。举例来说，你执行`【ls /etc】`之后，只有经过排序的文件名，并以蓝色显示目录及白色显示一般文件，如此而已。

那如果我还想要加入其他的显示信息时，可以加入上面提到的那些有用的选项，举例来说，我们之前一直用到的`-l`这个显示详细信息，以及将隐藏文件也一起列示出来的`-a`选项等。下面则是一些常用的范例，实际试做看看：

```bash
范例一：将家目录下的所有文件列出来（含属性与隐藏文件）。
[root@study ~]# ls -al ~
total 56
dr-xr-x---.  5 root root 4096 Jun  4 19:49 .
dr-xr-xr-x. 17 root root 4096 May  4 17:56 ..
-rw-------.  1 root root 1816 May  4 17:57 anaconda-ks.cfg
-rw-------.  1 root root 6798 Jun  4 19:53 .bash_history
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 29  2013 .bashrc
-rw-rw-rw-.  1 root root    0 Jun  3 20:04 .bashrc.test
drwx------.  4 root root   29 May  6 00:14 .cache
drwxr-xr-x.  3 root root   17 May  6 00:14 .config
# 这个时候你会看到，以开头的几个文件，以及目录文件（.、..、.config等）。
# 不过，目录文件文件名都是以深蓝色显示，有点不容易看清楚。

范例二：承上题，不显示颜色，但显示出该文件名代表的类型（type）
[root@study ~]# ls -alF --color=never ~
total 56
dr-xr-x---.  5 root root 4096 Jun  4 19:49 ./
dr-xr-xr-x. 17 root root 4096 May  4 17:56 ../
-rw-------.  1 root root 1816 May  4 17:57 anaconda-ks.cfg
-rw-------.  1 root root 6798 Jun  4 19:53 .bash_history
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 29  2013 .bashrc
-rw-rw-rw-.  1 root root    0 Jun  3 20:04 .bashrc.test
drwx------.  4 root root   29 May  6 00:14 .cache/
drwxr-xr-x.  3 root root   17 May  6 00:14 .config/
# 注意看到显示结果的第一行，嘿，知道为何我们会用./*command之类的命令吧？
# 因为，.代表的是【当前目录下】的意思，至于什么是 FIFO/Socket？请参考前一章的介绍。
# 另外，那个.bashrc时间仅写 2013，能否知道详细时间？

范例三：完整的显示文件的修改时间（modification time）。
[root@study ~]# ls -al --full-time ~
total 56
dr-xr-x---.  5 root root 4096 2015-06-04 19:49:54.520684829 +0800 .
dr-xr-xr-x. 17 root root 4096 2015-05-04 17:56:38.880000000 +0800 ..
-rw-------.  1 root root 1816 2015-05-04 17:57:02.326000000 +0800 anaconda-ks.cfg
-rw-------.  1 root root 6798 2015-06-04 19:53:41.451684829 +0800 .bash_history
-rw-r--r--.  1 root root  176 2013-12-29 10:26:31.000000000 +0800 .bash_logout
-rw-r--r--.  1 root root  176 2013-12-29 10:26:31.000000000 +0800 .bash_profile
-rw-r--r--.  1 root root  176 2013-12-29 10:26:31.000000000 +0800 .bashrc
-rw-rw-rw-.  1 root root    0 2015-06-03 20:04:16.916684829 +0800 .bashrc.test
drwx------.  4 root root   29 2015-05-06 00:14:56.960764950 +0800 .cache
drwxr-xr-x.  3 root root   17 2015-05-06 00:14:56.975764950 +0800 .config
# 请仔细看，上面的【时间】栏位变了，变成较为完整的格式。一般来说，ls -al仅列出目前短格式的时间，
# 有时不会列出年份，借由--full-time可以查看到比较正确的完整时间格式。
```

其实`ls`的用法还有很多，包括查看文件`inode`号码的`ls -i`选项，以及用来进行文件排序的`-S`选项，还有用来查看不同时间的操作的`--time=atime`等选项（更多时间说明请参考本章后面`touch`的说明）。而这些选项的存在都是因为 Linux 文件系统记录了很多有用的信息的缘故。那么 Linux 的文件系统中，这些与权限、属性有关的数据放在哪里？放在`inode`里面。关于这部分，我们会在下一章继续为你作比较深入的介绍。

无论如何`ls`是最常被使用到的功能还是那个`-l`的选项，为此很多 Linux 发行版在默认的情况中，已经将`ll`（L 的小写）设置成为`ls -l`的意思。其实，那个功能是 Bash shell 的`alias`功能，也就是说，我们直接输入`ll`就等于是输入`ls -l`，关于这部分，我们会在后续 Bash shell 时再次强调。

## 6.2.2 复制、删除与移动：cp、rm、mv

要复制文件，请使用 `cp（copy）` 这个命令即可，不过，`cp` 这个命令的用途可多了，除了单纯的复制之外，还可以建立链接文件（就是快捷方式），比对两文件的新旧而予以更新，以及复制整个目录等的功能。至于移动目录与文件，则使用 `mv（move）`，这个命令也可以直接拿来做重命名（rename）的操作。至于删除吗？那就是 `rm（remove）` 这个命令，下面我们就来看一看。

### ◆ cp（复制文件或目录）

```bash
[root@study ~]# cp [-adfilprsu] 源文件（source）目标文件（destination）
[root@study ~]# cp [options] source1 source2 source3 .... directory
选项与参数：
-a：相当于 -dr --preserve=all 的意思，至于 dr 请参考下列说明（常用）；
-d：若源文件为链接文件的属性（link file），则复制链接文件属性而非文件本身；
-f：为强制（force）的意思，若目标文件已经存在且无法开启，则删除后再尝试一次；
-i：若目标文件（destination）已经存在时，在覆盖时会先询问操作再进行（常用）；
-l：进行硬链接（hard link）的链接文件建立，而非复制文件本身；
-p：连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性（备份常用）；
-r：递归复制，用于目录的复制操作（常用）；
-s：复制成为符号链接文件（symbolic link），亦即 “快捷方式” 文件；
-u：若 destination 比 source 旧才更新 destination，或 destination 不存在的情况下才复制；
--preserve=all：除了 -p 的权限相关参数外，还加入 SELinux 的属性、links、xattr 等也复制；
最后要注意的是：如果源文件有两个以上，则最后一个目标文件一定要是 “目录” 才行。
```

复制（`cp`）这个命令是非常重要的，不同身份者执行这个命令会有不同的结果产生，尤其是那个 `-a`、`-p` 的选项，对于不同身份来说，差异则非常大。下面的练习中，有的身份为 `root`，有的身份为一般账号（在我这里用 `dmtsai` 这个账号），练习时请特别注意身份的差别。好，开始来做复制的练习与观察：

```bash
范例一：用 root 身份，将家目录下的 .bashrc 复制到 /tmp 下，并更名为 bashrc。
[root@study ~]# cp ~/.bashrc /tmp/bashrc
[root@study ~]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite ‘/tmp/bashrc’? n  <==n不覆盖，y为覆盖。
# 重复两次操作，由于 /tmp 下面已经存在 bashrc 了，加上 -i 选项后，
# 则在覆盖前会询问使用者是否确定，可以按下 n 或 y 来二次确认。

范例二：切换目录到 /tmp，并将 /var/log/wtmp 复制到 /tmp 且观察属性。
[root@study ~]# cd /tmp
[root@study tmp]# cp /var/log/wtmp .  <==想要复制到目前的目录，最后的.不要忘。
[root@study tmp]# ls -l /var/log/wtmp wtmp
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
-rw-r--r--. 1 root root 28416 Jun 11 19:01 wtmp
# 注意上面的特殊字体，在不加任何选项的情况下，文件的某些属性 / 权限会改变。
# 这是个很重要的特性，要注意，还有，连文件建立的时间也不一样了。
# 那如果你想要将文件的所有特性都一起复制过来该怎么办？可以加上 -a，如下所示：
[root@study tmp]# cp -a /var/log/wtmp wtmp_2
[root@study tmp]# ls -l /var/log/wtmp wtmp_2
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 wtmp_2
# 了解了吧！整个数据特性完全一模一样，真是不赖，这就是 -a 的特性。
```

这个 `cp` 的功能很多，由于我们常常会进行一些数据的复制，所以也会常常用到这个命令。一般来说，我们如果去复制别人的数据（当然，该文件你必须要有 `read` 的权限才行）时，总是希望复制到的数据最后是我们自己的，所以，在默认的条件中，`cp` 的源文件与目标文件的权限是不同的，目标文件的拥有者通常会是命令操作者本身。举例来说，上面的范例二中由于我是 `root` 的身份，因此复制过来的文件拥有者与用户组就改变成了 `root` 所有。

由于具有这个特性，因此当我们在进行备份的时候，某些需要特别注意的特殊权限文件，例如密码文件（`/etc/shadow`）以及一些配置文件，就不能直接以 `cp` 来复制，而必须要加上 `-a` 或是 `-p` 等可以完整复制文件权限的选项才行。另外，如果你想要复制文件给其他的用户，也必须要注意到文件的权限（包含读、写、执行以及文件拥有者等），否则，其他人还是无法针对你给予的文件进行自定义的操作。

```bash
范例三：复制 /etc/ 这个目录下的所有内容到 /tmp 下面。
[root@study tmp]# cp /etc /tmp
cp: omitting directory ‘/etc’  <==如果是目录则不能直接复制，要加上 `-r` 的选项。
[root@study tmp]# cp -r /etc /tmp
# 还是要再次的强调，-r 是可以复制目录，但是，文件与目录的权限可能会被改变。
# 所以，也可以利用 【cp -a /etc /tmp】 来执行命令，尤其是在备份的情况下。

范例四：将范例一复制的 bashrc 建立一个符号链接文件（symbolic link）。
[root@study tmp]# ls -l bashrc
-rw-r--r--. 1 root root 176 Jun 11 19:01 bashrc  <==先观察一下文件情况。
[root@study tmp]# cp -s bashrc bashrc_slink
[root@study tmp]# cp -l bashrc bashrc_hlink
[root@study tmp]# ls -l bashrc*
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc		<==与原始文件不太一样了。
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc_hlink
lrwxrwxrwx. 1 root root   6 Jun 11 19:06 bashrc_slink -> bashrc
```

范例四可有趣了，使用 `-l` 及 `-s` 都会建立所谓的链接文件（link file），但是这两种链接文件却有不一样的情况。这是怎么一回事？那个 `-l` 就是所谓的硬链接（hard link），至于 `-s` 则是符号链接（symbolic link），简单来说，`bashrc_slink` 是一个快捷方式，这个快捷方式会链接到 `bashrc`。所以你会看到文件名右侧会有个指向（`->`）的符号。

至于 `bashrc_hlink` 文件与 `bashrc` 的属性与权限完全一模一样，与尚未进行链接前的差异则是第二栏的 link 数量由 1 变成了 2。鸟哥这里先不介绍硬链接，因为硬链接涉及 inode 的相关知识，我们下一章谈到文件系统（filesystem）时再来讨论这个问题。

```bash
范例五：若 ~/.bashrc 比 /tmp/bashrc 新，才复制过来。
[root@study tmp]# cp -u ~/.bashrc /tmp/bashrc
# 这个 -u 的特性，是在目标文件与源文件有差异时，才会复制的。所以，常被用于备份的工作当中。

范例六：将范例四造成的 bashrc_slink 复制成为 bashrc_slink_1 与 bashrc_slink_2。
[root@study tmp]# cp bashrc_slink bashrc_slink_1
[root@study tmp]# cp -d bashrc_slink bashrc_slink_2
[root@study tmp]# ls -l bashrc_slink*
lrwxrwxrwx. 2 root root   6 Jun 11 19:06 bashrc_slink -> bashrc
-rw-r--r--. 1 root root 176 Jun 11 19:09 bashrc_slink_1           <==与原始文件相同。 
lrwxrwxrwx. 1 root root   6 Jun 11 19:10 bashrc_slink_2 -> bashrc <==是链接文件。
# 这个例子也是很有趣，原本复制的是链接文件，但是却将链接文件的实际文件复制过来了。
# 也就是说，如果没有加上任何选项时，cp 复制的是原始文件，而非链接文件的属性。
# 若要复制链接文件的属性，就得要使用 -d 的选项了，如 bashrc_slink_2 所示。

范例七：将家目录的 .bashrc 及 .bash_history 复制到 /tmp 下面。
[root@study tmp]# cp ~/.bashrc ~/.bash_history /tmp
# 可以将多个文件一次复制到同一个目录，最后面一定是目录。
```

>例题
>
>你能否使用 `dmtsai` 的身份，完整地复制 `/var/log/wtmp` 文件到 `/tmp` 下面，并更名为 `dmtsai_wtmp`？
>
>答：实际做的结果如下：
>
>```bash
>[dmtsai@study ~]$ cp -a /var/log/wtmp /tmp/dmtsai_wtmp
>[dmtsai@study ~]$ ls -l /var/log/wtmp /tmp/dmtsai_wtmp
>-rw-rw-r--. 1 root  utmp  28416 6月 11 18:56 /var/log/wtmp
>-rw-rw-r--. 1 dmtsai dmtsai 28416 6月 11 18:56 /tmp/dmtsai_wtmp
>```
>
>由于 `dmtsai` 的身份并不能随意修改文件的拥有者与用户组，因此虽然能够复制 `wtmp` 的相关权限与时间等属性，但是与拥有者、用户组相关，原本 `dmtsai` 身份无法进行的操作，即使加上 `-a` 选项，也是无法完成完整权限的复制。

总之，由于 `cp` 有种种的文件属性与权限的特性，所以，在复制时，你必须要清楚地了解到：

- 是否需要完整的保留源文件的信息？
- 源文件是否为符号链接文件（symbolic link file）？
- 源文件是否为特殊的文件，例如 FIFO、socket 等？
- 源文件是否为目录？

### ◆ rm（删除文件或目录）

```bash
[root@study ~]# rm [-fir] 文件或目录
选项与参数：
-f：就是 force 的意思，忽略不存在的文件，不会出现警告信息。
-i：交互模式，在删除前会询问使用者是否操作。
-r：递归删除，最常用于目录的删除，这是非常危险的选项。

范例一：将刚刚在 cp 范例中建立的 bashrc 删除掉。
[root@study ~]# cd /tmp
[root@study tmp]# rm -i bashrc
rm: remove regular file ‘bashrc’? y
# 如果加上 -i 的选项就会主动询问，避免你删除到错误的文件。

范例二：通过通配符 * 的帮忙，将 /tmp 下面开头为 bashrc 的文件名通通删除。
[root@study tmp]# rm -i bashrc*
# 注意那个星号，代表的是 0 到无穷多个任意字符，很好用的东西。

范例三：将 cp 范例中所建立的 /tmp/etc/ 这个目录删除掉。
[root@study tmp]# rmdir /tmp/etc
rmdir: failed to remove ‘/tmp/etc’: Directory not empty  <==删不掉，因为这不是空的目录。
[root@study tmp]# rm -r /tmp/etc
rm: descend into directory ‘/tmp/etc’? y
rm: remove regular file ‘/tmp/etc/fstab’? y
rm: remove regular file ‘/tmp/etc/crypttab’? ^C  <==按下【ctrl+c】中断。
（中间省略）...
# 因为身份是 root，默认已经加入了 -i 的选项，所以你要一直按 y 才会删除。
# 如果不想要继续按 y，可以按下【ctrl+c】来终止正在的工作。
# 这是一种保护的操作，以确保要删除的目录是不要的，可以这样操作。
[root@study tmp]# \rm -r /tmp/etc
# 在命令前加上反斜线，可以忽略 alias 的指定选项，至于 alias 我们在 bash 再谈。
# 拜托，这个范例很可怕，你不要删错了，删除 /etc 系统会挂掉。

范例四：删除一个带有 - 开头的文件。
[root@study tmp]# touch ./-aaa-  <==touch 这个命令可以建立空文件。
[root@study tmp]# ls -l
-rw-r--r--. 1 root root  0 Jun 11 19:22 -aaa-  <==文件大小为 0，所以是空文件。
[root@study tmp]# rm -aaa-
rm: invalid option -- 'a' <== 因为 - 是选项嘛，所以系统读错了。
Try 'rm ./-aaa-' to remove the file '-aaa-'. <== 新的 bash 有给建议的。
Try 'rm --help' for more information.
[root@study tmp]# rm ./-aaa-
```

这是删除的命令（remove），要注意的是，通常在 Linux 系统下，为了怕文件被 `root` 误删，所以很多 Linux 发行版都已经默认加入了 `-i` 这个选项。而如果要连目录下的东西都一起删除的话，例如子目录里面还有子目录时，那就要使用 `-r` 这个选项。不过，使用 `rm -r` 这个命令之前，请千万注意了，因为该目录或文件肯定会被 `root` 删除。因为系统不会再次询问你是否要删除，所以那是个超级严重的命令，得特别注意。不过，如果你确定该目录不要了，那么使用 `rm -r` 来递归删除是不错的方式。

另外，范例四也是很有趣的例子，我们在之前就谈过，文件名最好不要使用 “`-`” 号开头，因为 “`-`” 后面接的是选项，因此，单纯的使用 `【rm -aaa-】` 系统的命令就会误判，那如果使用后面会谈到的正则表达式时，还是会出问题。所以，只能用避过首位字符是 “`-`” 的方法，就是加上本目录 “`./`” 即可。如果 `man rm` 的话，其实还有一种方法，那就是 `【rm -- -aaa-】` 也可以。

### ◆ mv（移动文件与目录，或重命名）

```bash
[root@study ~]# mv [-fiu] source destination
[root@study ~]# mv [options] source1 source2 source3 .... directory
选项与参数：
-f：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖。
-i：若目标文件（destination）已经存在时，就会询问是否覆盖。
-u：若目标文件已经存在，且 source 比较新，才会更新（update）。

范例一：复制一文件，建立一目录，将文件移动到目录中。
[root@study ~]# cd /tmp
[root@study tmp]# cp ~/.bashrc bashrc
[root@study tmp]# mkdir mvtest
[root@study tmp]# mv bashrc mvtest
# 将某个文件移动到某个目录去，就是这样做。

范例二：将刚刚的 mvtest 目录更名为 mvtest2。
[root@study tmp]# mv mvtest mvtest2  <==这样就重命名了。
# 其实在 Linux 下面还有个有趣的命令，名称为 rename，
# 该命令专职进行多个文件名的同时重命名，并非针对单一文件名修改，与 mv 不同，请 man rename。

范例三：再建立两个文件，再全部移动到 /tmp/mvtest2 当中。
[root@study tmp]# cp ~/.bashrc bashrc1
[root@study tmp]# cp ~/.bashrc bashrc2
[root@study tmp]# mv bashrc1 bashrc2 mvtest2
# 注意到这边，如果有多个源文件或目录，则最后一个目标文件一定是【目录】。
# 意思是说，将所有的文件移动到该目录的意思。
```

这是移动（move）的意思，当你要移动文件或目录的时候，这个命令就很重要。同样，你也可以使用 `-u`（update）来测试新旧文件，看看是否需要移动。另外一个用途就是修改文件名，我们可以很轻易地使用 `mv` 来修改一个文件的文件名。不过，在 Linux 中有个 `rename` 命令，可以用来更改大量文件的文件名，你可以利用 `man rename` 来查看一下，也是挺有趣的命令。

## 6.2.3 获取路径的文件名与目录名称

每个文件的完整文件名包含了前面的目录与最终的文件名，而每个文件名的长度都可以到达 255 个字符。那么你怎么知道哪个是文件名？哪个是目录名？嘿嘿，就是利用反斜线（`/`）来辨别。其实，获取文件名或是目录名称，一般的用途应该是在写程序的时候用来判断之用，所以，这部分的命令可以用在第三篇内的 shell 脚本里。下面我们简单地以几个范例来谈一谈 `basename` 与 `dirname` 的用途。

# 6.3 文件内容查看

如果我们要查看一个文件的内容时，该如何是好？这里有相当多有趣的命令可以来分享一下：最常使用的显示文件内容的命令可以说是 `cat` 与 `more` 及 `less` 了。此外，如果我们要查看一个很大的文件（好几百 MB 时），但是我们只需要后面的几行字而已，那么该如何是好？呵呵，用 `tail` 呀。此外，`tac` 这个命令也可以达到这个目的。好了，说说各个命令的用途。

- cat 由第一行开始显示文件内容。
- tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写。
- nl 显示的时候，同时输出行号。
- more 一页一页地显示文件内容。
- less 与 more 类似，但是比 more 更好的是，它可以往前翻页。
- head 只看前面几行。
- tail 只看后面几行。
- od 以二进制的方式读取文件内容。
## 1. 直接查看文件内容

直接查看一个文件的内容可以使用 cat/tac/nl 这几个命令。

- cat
```shell
[root@study ~]# cat [-AbEnTv]

选项与参数
-A: 相当于 -vET 的整合选项，可列出一些特殊字符而不是空白而已。
-b：列出行号，仅针对非空白行做行号显示，空白行不标行号。
```
















[^1]: 
