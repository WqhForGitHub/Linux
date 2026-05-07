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

### ◆ cat
```shell
[root@study ~]# cat [-AbEnTv]

选项与参数
-A: 相当于 -vET 的整合选项，可列出一些特殊字符而不是空白而已。
-b：列出行号，仅针对非空白行做行号显示，空白行不标行号。
-E：将结尾的换行符 $ 显示出来。
-n：打印出行号，连同空白行也会有行号，与 -b 的选项不同。
-T：将 [tab] 按键以 ^I 显示出来。
-v：列出一些看不出来的特殊字符。

范例一：查看 /etc/issue 这个文件的内容。
[root@study ~]# cat /etc/issue
\S
Kernel \r on an \m

范例二：承上题，如果还要打印行号？
[root@study ~]# cat -n /etc/issue
1 \S
2 Kernel \r on an \m
3
# 所以这个文件有三行，看到了吧，可以列出行号。这对于大文件要找某个特定的行时，有点用处。
# 如果不想要显示空白行的行号，可以使用 [cat -b /etc/issue]，自己测试看看。

范例三：将 /etc/man_db.conf 的内容完整的显示出来（包含特殊字符）。
[root@study ~]# cat -A /etc/man_db.conf
# $
......（中间省略）......
MANPATH_MAP^I/bin^I^I^I/usr/share/man$
MANPATH_MAP^I/usr/bin^I^I^I/usr/share/man$
MANPATH_MAP^I/sbin^I^I^I/usr/share/man$
MANPATH_MAP^I/usr/sbin^I^I^I/usr/share/man$
......（下面省略）......
# 上面的结果限于篇幅，鸟哥删除掉很多数据。另外，输出的结果并不会有特殊字体，
# 鸟哥上面的特殊字体是要让您发现差异点在哪里，基本上，在一般的环境中，
# 使用 [tab] 与空格键的效果差不多，都是一堆空白。我们无法知道两者的差别。
# 此时使用 cat -A 就能够发现哪些空白的地方是啥鬼东西了。[tab] 会以 ^I 表示，换行符则是以 $ 表示。
# 所以你可以发现每一行后面都是 $，不过换行符在 Windows/Linux 则不太相同，Windows 的换行符是 ^M$。
# 这部分我们会在第九章 vim 软件的介绍时，再次说明。
```
嘿嘿，Linux 里面有猫命令？不是的，cat 是 Concatenate（串联）的简写，主要的功能是将一个文件的内容连续打印在屏幕上面。例如上面的例子中，我们将 /etc/issue 打印出来，如果加上 -n 或 -b 的话，则每一行前面还会加上行号。
鸟哥个人比较少用 cat。毕竟当你的文件内容的行数超过 40 行以上，根本来不及在屏幕上看到结果。所以，配合等一下要介绍的 more 或是 less 来执行比较好。此外，如果是一般的 DOS 文件时，就需要特别留意一些奇怪的符号了，例如换行与 `[Tab]` 等要显示出来，就得加入 -A 之类的选项。
### ◆ tac（反向列示）
```shell
[root@study ~]# tac /etc/issue
Kernel \r on an \m
\S
# 与刚刚上面的范例一比较，是由最后一行先显示。
```
tac 这个好玩了。怎么说？详细看一下，cat 与 tac，有没有发现？对，tac 刚好是将 cat 反写过来，所以它的功能就跟 cat 相反，cat 是由第一行到最后一行连续显示在屏幕上，而 tac 则是由最后一行到第一行反向在屏幕上显示出来，很好玩吧。
### ◆ nl（添加行号打印）
```shell
[root@study ~]# nl [-bnw] 文件
选项与参数
-b：指定行号指定的方式，主要有两种：
	-b a：表示不论是否为空行，也同样列出行号（类似 cat -n）
	-b t：如果有空行，空的那一行不要列出行号（默认值）
-n：列出行号表示的方法，主要有三种：
	-n ln：行号在屏幕的最左方显示
	-n rn：行号在自己栏位的最右方显示，且不加 0
	-n rz：行号在自己的栏位的最右方显示，且加 0
-w：行号栏位的占用的字符数

范例一：用 nl 列出 /etc/issue 的内容。
[root@study ~] nl /etc/issue
[root@study ~] nl -b a /etc/issue
[root@study ~] nl -b a -n rz /etc/issue
[root@study ~] nl -b a -n rz -w 3 /etc/issue
```
nl 可以将输出的文件内容自动地加上行号，其默认地结果与 cat -n 有点不太一样，nl 可以将行号做比较多的显示设计，包括位数与是否自动补齐 0 等的功能。
## 2.可翻页查看

前面提到的 nl 与 cat、tac 等，都是一次性地将数据一口气显示到屏幕上面，那有没有可以进行一页一页翻动的命令？让我们可以一页一页的观察，才不会前面的数据看不到。有，那就是 more 与 less。
### ◆ more（一页一页翻动）
```shell
[root@study ~]# more /etc/man_db.conf
```
仔细地给它看到上面的范例，如果 more 后面接的文件内容行数大于屏幕输出的行数时，就会出现类似上面的图例。重点在最后一行，最后一行会显示出目前显示的百分比，而且还可以在最后一行输入一些有用的命令。在 more 这个程序的运行过程中，你有几个按键可以使用：
- 空格键（space）：代表向下翻一页
- Enter：代表向下翻一行
- /字符串：代表在这个显示的内容当中，向下查找字符串这个关键词
- :f：立即显示出文件名以及目前显示的行数
- q：代表立刻离开 more，不再显示该文件内容
- b 或 [ctrl]-b：代表往回翻页，不过这操作只对文件有用，对管道无用。
要离开 more 这个命令的显示工作，可以按下 q 就能够离开。而要向下翻页，使用空格键即可。比较有用的是查找字符串的功能，举例来说，我们使用 more /etc/man_db.conf 来观察该文件，若想要在该文件内查找 MANPATH 这个字符串时，可以这样做：
```shell
[root@study ~]# more /etc/man_db.conf
```
如同上面的说明，输入了 / 之后，光标就会跑到最下面一行，并且等待你的输入，你输入了字符串并按下 [enter] 之后，more 就会开始向下查找该字符串，而重复查找同一个字符串，可以直接按下 n 即可。最后，不想要看了，就按下 q 即可离开 more。
### ◆ less（一页一页翻动）
```shell
[root@study ~]# less /etc/man_db.conf
```
less 的用法比起 more 又更加有弹性，在 more 的时候，我们并没有办法向前面翻，只能往后面看，但若使用了 less 时，就可以使用 [pageup]、[pagedown] 等按键的功能来往前往后翻看文件，你看是不是更容易观看一个文件的内容了。
除此之外，在 less 里面可以拥有更多的查找功能。不止可以向下查找，也可以向上查找，实在是很不错，基本上，可以输入的命令有：
- 空格键：向下翻动一页
- [pagedown]：向下翻动一页
- [pageup]：向上翻动一页
- /字符串：向下查找字符串的功能
- ?字符串：向上查找字符串的功能
- n：重复前一个查找（与/或?有关）
- N：反向的重复前一个查找（与/或?有关）
- g：前进到这个数据的第一行
- G：前进到这个数据的最后一行去（注意大小写）
- q：离开 less 这个程序
查看文件内容还可以进行查找的操作，看，less 是否很不错？其实 less 还有很多功能，详细的使用方式请使用 man less 查询一下。
你是否会觉得 less 使用的画面与环境与 man page 非常类似？没错，因为 man 这个命令就是调用 less 来显示说明文件的内容，现在你是否觉得 less 很重要？
## 3. 数据截取

我们可以将输出的数据作一个最简单的截取，那就是取出文件前面几行（head）或取出后面几行（tail）文字的功能。不过，要注意的是 head 与 tail 都是以行为单位来进行数据截取的。
### ◆ head（取出前面几行）
```shell
[root@study ~]# head [-n number] 文件
选项与参数：
-n：后面接数字，代表显示几行的意思。

[root@study ~]# head /etc/man_db.conf

# 默认的情况下，显示前面十行，若要显示前 20 行，就得要这样。
[root@study ~]# head -n 20 /etc/man_db.conf

范例：如果后面 100 行的数据都不打印，只打印 /etc/man_db.conf 的前面几行，该如何是好？
[root@study ~]# head -n -100 /etc/man_db.conf
```
head 的英文意思就是头，那么这个东西的用法自然就是显示出一个文件的前几行，没错，就是这样。若没有加上 -n 这个选项时，默认只显示十行，若只要一行？那就加入 head -n 1 filename 即可。
另外那个 -n 选项后面的参数较有趣，如果接的是负数，例如上面范例的 -n -100 时，代表列出前面所有行数，但不包括后面 100 行。举例来说 CentOS 7.1 的 /etc/man_db.conf 共有 131 行，则上述的命令 head -n -100 /etc/man_db.conf 就会列出前面 31 行，后面 100 行不会打印出来了。这样说，比较容易懂了吧？
### ◆ tail（取出后面几行）
```shell
[root@study ~]# tail [-n number] 文件
选项与参数：
-n：后面接数字，代表显示几行的意思。
-f：表示连续刷新显示后面所接文件中的内容，要等到按下 [ctrl]-c 才会结束。

[root@study ~]# tail /etc/man_db.conf

# 默认的情况下，显示最后的十行。若要显示最后的 20 行，就得要这样：
[root@study ~]# tail -n 20 /etc/man_db.conf

范例一：如果不知道 /etc/man_db.conf 有几行，却只想列出 100 行以后的数据时？
[root@study ~]# tail -n +100 /etc/man_db.conf

范例二：持续检测 /var/log/messages 的内容。
[root@study ~]# tail -f /var/log/messages
<== 要等到输入 [ctrl]-c 之后才会结束执行 tail 这个命令。
```
有 head 自然就有 tail（尾巴），没错，这个 tail 的用法跟 head 的用法类似，只是显示的是后面几行。默认也是显示十行，若要显示非十行，就加 -n number 的选项即可。
范例一的内容就有趣啦，其实与 head -n =xx 有异曲同工之妙。当执行 tail -n +100 /etc/man_db.conf 代表该文件从 100 行以后就会被列出来，同样，在 man_db.conf 共有 131 行，因此第 100~131 行就会被列出来，前面的 99 行都不会被显示出来。
至于范例二，由于 /var/log/messages 随时会有数据写入，你想要让该文件有数据写入时就立刻显示到屏幕上，就利用 -f 这个选项，它可以一直刷新显示 /var/log/messages 这个文件，新加入的数据都会被显示到屏幕上，直到你按下 [ctrl]-c 才会结束 tail 这个命令的执行，由于 messages 必须要 root 权限才能看，所以该范例得要使用 root 来查询。
## 4. 非纯文本文件：od

我们上面提到的都是在查看纯文本文件的内容。那么万一我们想要查看非文本文件，举例来说，例如 `/usr/bin/passwd` 这个执行文件的内容时，又该如何去读出信息呢？事实上，由于执行文件通常是二进制文件（binary file），使用上面提到的命令来读取它的内容时，确实会产生类似乱码的数据。那怎么办？没关系，我们可以利用 od 这个命令来读取。
```shell
[root@study ~]# od [-t TYPE] 文件
选项与参数：
-t：后面可以接各种【类型（TYPE）】的输出，例如：
	a：利用默认的字符来输出
	c：使用 ASCII 字符来输出
	d[size]：利用十进制（decimal）来输出数据，每个整数占用 size Bytes
	f[size]：利用浮点数值（floating）来输出数据，每个数占用 size Bytes
	o[size]：利用八进制（octal）来输出数据，每个整数占用 size Bytes
	x[size]：利用十六进制（hexdecimal）来输出数据，每个整数占用 size Bytes

范例一：请将 /usr/bin/passwd 的内容使用 ASCII 方式来显示。
[root@study ~]# od -t c /usr/bin/passwd
```
## 5. 修改文件时间或创建新文件：touch

我们在介绍 ls 这个命令时，提到每个文件在 Linux 下面都会记录许多的时间参数，其实是有三个主要的变动时间，那么三个时间的意义是什么？
### ◆ 修改时间（modification time, mtime） 

当该文件的【内容数据】变更时，就会更新这个时间，内容数据指的是文件的内容，而不是文件的属性或权限。

### ◆ 状态时间（status time, ctime）

当该文件的【状态（status）】改变时，就会更新这个时间，举例来说，像是权限与属性被更改了，都会更新这个时间。

### ◆ 读取时间（access time, atime）

当【该文件的内容被读取】时，就会更新这个读取时间（access），举例来说，我们使用 cat 去读取 /etc/man_db.conf，就会更新该文件的 atime。

这是个有趣的现象，举例来说，我们来看一看你自己的 /etc/man_db.conf 这个文件的时间吧。
```shell
[root@study ~]# date;

[root@study ~]# ls -l /etc/man_db.conf

[root@study ~]# ls -l --time=atime /etc/man_db.conf

[root@study ~]# ls -l --time=ctime /etc/man_db.conf
```
看到了吗？**在默认情况下，ls 显示出来的是该文件的 mtime，也就是这个文件的内容上次被修改的时间**。至于鸟哥的系统是在 5 月 4 号的时候安装，因此，这个文件被产生导致状态被修改的时间就回溯到那个时间点了（ctime）。而还记得刚刚我们使用的范例当中，有使用到 man_db.conf 这个文件，所以，它的 atime 就会变成刚刚使用的时间了。
文件的时间是很重要的，因为，如果文件的时间错误的话，可能会造成某些程序无法顺利的运行。那么万一我发现了一个文件来自未来，该如何让该文件的时间变成【现在】的时刻呢？很简单，就用 【touch】这个命令即可。
>嘿嘿，不要怀疑系统时间会来自未来，很多时候会有这个问题。举例来说，在安装过后系统时间可能会被改变，因为中国时区在国际标准时间格林威治时间，GMT 的右边，所以会比较早看到阳光，也就是说中国时间比 GMT 时间快了 8 小时。如果安装不当，我们的系统可能会快 8 小时，你的文件就有可能来自 8 小时后了。

至于某些情况下，由于 BIOS 的设置错误，导致系统时间跑到未来时间，并且你又建立了某些文件，等你将时间改回正确的时间时，该文件不就变成来自未来了吗？
```shell
[root@study ~]# touch [-acdmt] 文件
选项与参数：
-a：仅自定义 access time
-c：仅修改文件的时间，若该文件不存在则不建立新文件
-d：后面可以接受自定义的日期而不用目前的日期，也可以使用 --date="日期或时间"
-m：仅修改 mtime
-t：后面可以接收自定义的时间而不用目前的时间，格式为 [YYYYMMDDhhmm]

范例一：新建一个空文件并观察时间
[dmtsai@study ~]# cd /tmp
[dmtsai@study tmp]# touch testtouch
[dmtsai@study tmp]# ls -l testtouch
# 注意到，这个文件的大小是0。在默认的状态下，如果 touch 后面有接文件，
# 则该文件的三个时间（atime/ctime/mtime）都会更新为目前的时间。若该文件不存在
# 则会主动的建立一个新的空文件，例如上面这个例子。


[dmtsai@study tmp]# touch -d "2 days ago" bashrc

[dmtsai@study tmp]# touch -t 2014061520202 bashrc
```
通过 touch 这个命令，我们可以轻易地自定义文件地日期与时间，并且也可以建立一个空文件。不过，要注意的是，即使我们复制一个文件时，复制所有的属性，但也没有办法复制 ctime 这个属性。ctime 可以记录这个文件最近的状态（status）被改变的时间。无论如何，还是要告知大家，我们平时看的文件属性中，比较重要的还是 mtime。我们关心的常常是这个文件的内容是什么时候被修改，了解了吗？
无论如何，touch 这个命令最常被使用的情况是：
◆ 建立一个空文件。
◆ 将某个文件日期自定义为目前（mtime 与 atime）

# 6.4 文件与目录的默认权限与隐藏权限

## 1. 文件认默权限：umask

OK，那么现在我们知道如何建立或是改变一个目录或文件的属性了，不过，你知道当你建立一个新的文件或目录时，它的默认权限会是什么吗？呵呵，那就与 umask 这个玩意儿有关了，那么 umask 是在做什么？基本上，umask 就是指**目前用户在建立文件或目录时候的权限默认值**，那么如何得知或设置 umask？它的指定条件以下面的方式来指定：
```shell
[root@study ~]# umask
0022 <== 与一般权限有关的是后面三个数字

[root@study ~]# umask -S
u=rwx,g=rx,o=rx
```
查看的方式有两种，一种可以直接输入 umask，就可以看到数字类型的权限设置值，一种则是加入 -S（Symbolic）这个选项，就会以符号类型的方式来显示出权限了。奇怪的是，怎么 umask 会有四组数字？不是只有三组吗？是没错，第一组是特殊权限用的，我们先不要理它，所以先看后面三组即可。
在默认权限的属性上，目录与文件是不一样的，从第 5 章我们知道 x 权限对于目录是非常重要的。但是一般文件的建立则不应该有执行的权限，因为一般文件通常是用于数据的记录，当然不需要执行的权限了。因此，默认的情况如下：
- 若用户建立为文件则默认没有可执行（x）权限，即只有 rw 这两个项目，也就是最大为 666，默认权限如下：
```shell
-rw-rw-rw
```
- 若用户建立为目录，则由于 x 与是否可以进入此目录有关，因此默认为所有权限均开放，即 777，默认权限如下：
```shell
drwxrwxrwx
```
要注意的是，umask 的数字指的是该**默认值需要减掉的权限**。因为 r、w、x 分别是 4、2、1，所以，当要拿掉能写的权限，就是输入 2，而如果能拿掉能读的权限，也就是 4，那么要拿掉读与写的权限，也就是 6，而要拿掉执行与写入的权限，也就是 3。这样了解吗？请问你，5 是什么？呵呵，就是读与执行的权限。
如果以上面的例子来说明的话，因为 umask 为 022，所以 user 并没有被拿掉任何权限，不过 group 与 others 的权限被拿掉了 2（也就是 w 这个权限），那么当用户：
- 建立文件时：（-rw-rw-rw-）-（-----w--w-）=> -rw-r--r--
- 建立目录时：（drwxrwxrwx）-（d----w--w-）=> drwxr-xr-x
不相信吗？我们就来测试看看吧。
```shell
[root@study ~]# umask
0022

[root@study ~]# touch test1
[root@study ~]# mkdir test2
[root@study ~]# ll -d test*
-rw-r--r-- 1 root root    0 May  7 19:08 test1
drwxr-xr-x 2 root root 4096 May  7 19:08 test2/
```
呵呵，看见了吧，确定新建文件的权限是没有错的。
### ◆ umask 的利用与重要性：课题制作
想象一下状况，如果你跟你的同学在同一台主机里面工作时，因为你们两个正在进行同一个课题，老师也帮你们两个的账号建立好了相同用户组的状态，并且将 /home/class/ 目录做为你们两个人的课题目录。想象一下，有没有可能你所制作的文件你的同学无法编辑？果真如此的话，那就伤脑筋了。
这个问题经常发生。举上面的案例来说，你看一下 test1 的权限数值是什么？644，意思是**如果 umask 制定为 022，那新建的数据只有用户自己具有 w 的权限，同用户组的人只有 r 这个可读的权限而已，并无法修改**。这样要怎么共同制作课题，您说是吧。
所以，当我们需要新建文件给同用户组的用户共同编辑时，那么 umask 的用户组就不能拿掉 2，这个 w 的权限。所以，umask 就得是 002 之类的才可以。这样新建的文件才能够是 -rw-rw-r-- 的权限样式，那么如何设置 umask 呢？很简单，直接在 umask 后面输入 002 就好。
```shell
[root@study ~]# umask 002
[root@study ~]# touch test3
[root@study ~]# mkdir test4
[root@study ~]# ll -d test[34] # 中括号[ ]代表中间有个指定的字符，而不是任意字符的意思。
```
所以说，这个 umask 对于新建文件与目录的默认权限是很有关系的。这个概念可以用来任何服务器上面，尤其是未来在你搭配文件服务器（file server），举例来说，SAMBA 服务器或是 FTP 服务器时，都是很重要的概念，这牵涉到你的用户是否能够将文件进一步利用的问题，不要等闲视之。
>关于 umask 与权限的计算方式中，教科书喜欢使用二进制的方式来进行逻辑与和逻辑否的计算，不过，鸟哥还是比较喜欢使用符号方式来计算，联想上面比较容易一点。

但是，有的书籍或是 BBS 上面的朋友，喜欢使用文件默认属性 666 与目录默认属性 777 来与 umask 进行相减的计算，这是不好的。以上面例题来看，如果使用默认属性相加减，则文件变成：666 - 003 = 663，即 -rw-rw--wx，这可是完全不对的。想想看，原本文件就已经去除 x 的默认的属性，怎么可能突然间冒出来了？所以，这个地方要特别小心。
在默认的情况下，root 的 umask 会拿掉比较多的属性，root 的 umask 默认是 002，这是基于安全的考虑，至于一般身份用户，通常它们的 umask 为 002，即保留用户组的写入权力。其实，关于默认 umask 的设置可以参考 /etc/bashrc 这个文件的内容，不过，不建议修改该文件，你可以参考第 10 章 bash shell 提到的环境参数配置文件（~/.bashrc）的说明。
## 2. 文件隐藏属性
## 3. 文件特殊权限：SUID、SGID、SBIT
## 4. 观察文件类型：file

















































