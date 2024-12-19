变量的使用：echo 

```powershell
[root@study ~]$ echo $variable
[root@study ~]$ echo $PATH
[root@study ~]$ echo ${PATH}
```



2. 取消刚刚设置的 myname 这个变量内容：unset

```powershell
[root@study ~]$ myname=VBird
[root@study ~]$ echo ${myname}
VBird
[root@study ~]$ unset myname
```



5. 数据流重定向
+ 标准输入（stdin）：代码为 0 ，使用 < 或 << ;
+ 标准输出（stdout）：代码为 1，使用 > 或 >>;
+ 标准错误输出（stderr）：代码为 2，使用 2> 或 2>>;

```powershell
[root@study ~]$ ll /
[root@study ~]$ ll / > rootfile <== 屏幕并无任何信息
[root@study ~]$ ll rootfile <== 有一个新文件被建立了
```

总结：1. 该文件（rootfile）若不存在，系统会自动地将它建立起来。

    2. 当这个文件存在的时候，那么系统就会先将这个文件内容清空，然后再将数据写入。

    3. 也就是若以 > 输出到一个已存在的文件中，这个文件就会被覆盖掉。

+ 1> ：以覆盖的方法将 正确的数据 输出到指定的文件或设备上
+ 1>>：以累加的方法将 正确的数据 输出到指定的文件或设备上
+ 2>：以覆盖的方法将 错误的数据 输出到指定的文件或设备上
+ 2>>：以累加的方法将 错误的数据 输出到指定的文件或设备上

```powershell
[root@study ~]$ find /home -name .bashrc > list_right 2> list_error
```

总结：将正确的与错误的数据分别存入不同的文件中。



<h2 id="UrNep">管道命令</h2>
+ 管道命令仅会处理标准输出，对于标准错误会予以忽略
+ 管道命令必须要能够接受来自前一个命令的数据成为标准输入继续处理才行

例如：less、more、head、tail 等都是可以接受标准输入的管道命令

```powershell
[root@study ~]$ ls -al /etc | less
```



<h3 id="rQhNV">选取命令：cut、grep</h3>
| **              ****cut**<br/>这个命令可以将一段信息的某一段给它切出来，处理的信息是以行为单位。<br/>[root@study ~]$ cut -d '分隔字符' -f fields <br/>[root@study ~]$ cut -c 字符区间<br/>选项与参数：<br/>-d：后面接分隔字符，与 -f 一起使用<br/>-f：根据 -d 的分隔字符将一段信息划分成为数段，用 -f 取出第几段的意思<br/>-c：以字符的单位取出固定字符区间 |
| --- |


```powershell
[root@study ~]$ echo ${PATH}

[root@study ~]$ echo ${PATH} | cut -d ':' -f 5
```

