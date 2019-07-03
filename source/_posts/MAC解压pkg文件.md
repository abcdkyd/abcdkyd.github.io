---

title: MAC解压pkg文件
date: 2019-07-03 13:55:41
tags: MAC
categories: 笔记

---

```
xar -xf Setup.pkg
cat ./Payload | cpio -i
```

### cpio用法

cpio 指令可以将文件复制到 存档包（archives），也可以从存档包（archives）复制出文件。缺省情况下，cpio从标准输入读取输入数据，向标准输出写入输出数据。

归档包（archives）是一种包含其他文件和有关信息的文件。 有关信息包括：

1. 文件名,
2. 属主,
3. 时间戳(timestamp),
4. 和访问权限。

cpio支持下列的归档格式:

1. binary,
2. old ASCII,
3. new ASCII, crc,
4. HPUX binary,
5. HPUX old ASCII,
6. old tar,
7. POSIX.1 tar。

默认情况下，cpio创建二进制格式存档，以便与旧的cpio程序兼容。从档案中提取时，cpio会自动识别正在读取的归档类型，并可以读取在具有不同字节顺序的计算机上创建的归档。

cpio 有三种操作模式，某些选项只能用在对应的操作模式上;

#### Copy-out 模式

> 在copy-out模式下，cpio将文件复制到存档（archives）中。它从标准输入读取文件名列表（每行一个文件名），并将存档（archives）写入标准输出。生成文件名列表的典型方法是使用find命令; 您应该使用 -depth 选项，减少因为进入没有访问权限的目录而引起的麻烦。

```
cpio {-o|--create} [-0acvABLV] [-C bytes] [-H format] [-M message] \
    [-O [[user@]host:]archive] [-F [[user@]host:]archive] \
    [--file=[[user@]host:]archive] [--format=format] [--message=message]\
    [--null] [--reset-access-time] [--verbose] [--dot] [--append] \
    [--block-size=blocks] [--dereference] [--io-size=bytes] \
    [--rsh-command=command] [--help] [--version] < name-list [> archive]
```

#### Copy-in 模式

> 在copy-in模式中, cpio 从归档包里读取文件, 或者列出归档包里的内容。它从标准输入读入归档包。任何非选项的命令行参数都被认为是shell globbing的模式; 只有存档中的名称与这些模式中的一个或多个匹配的文件才会从存档中复制。和在shell中不同的是，不是使用`.`作为通配符，而是使用`/`作为通配符。如果没有给出任何模式，则提取所有文件

```
cpio {-i|--extract} [-bcdfmnrtsuvBSV] [-C bytes] [-E file] [-H format] [-M message] \
    [-R [user][:.][group]] [-I [[user@]host:]archive] [-F [[user@]host:]archive] \
    [--file=[[user@]host:]archive] [--make-directories] [--nonmatching] \
    [--preserve-modification-time] [--numeric-uid-gid] [--rename] [-t|--list] \
    [--swap-bytes] [--swap] [--dot] [--unconditional] [--verbose] [--block-size=blocks] \
    [--swap-halfwords] [--io-size=bytes] [--pattern-file=file] [--format=format] \
    [--owner=[user][:.][group]] [--no-preserve-owner] [--message=message] \
    [--force-local] [--no-absolute-filenames] [--absolute-filenames] [--sparse] \
    [--only-verify-crc] [--to-stdout] [--quiet] [--rsh-command=command] [--help] \
    [--version] [pattern...] [< archive]
```

#### Copy-pass 模式

> 在copy-pass模式中, cpio把文件从一棵目录树复制到另一棵, 它结合了 copy-in 和 copy-out 的操作, 但不使用归档包。 cpio从标准输入读取欲复制的文件名列表; 并复制它们复制到目标目录（非选项的参数都被认为是目标目录）。

```
cpio {-p|--pass-through} [-0adlmuvLV] [-R [user][:.][group]] [--null] \
     [--reset-access-time] [--make-directories] [--link] [--quiet] \
    [--preserve-modification-time] [--unconditional] [--verbose] [--dot] \
    [--dereference] [--owner=[user][:.][group]] [--no-preserve-owner] \
    [--sparse] [--help] [--version] destination-directory < name-list
```

备份会使用到的选项与参数：

```
  -o ：将数据 copy 输出到文件或装置上 
  -B ：让默认的 Blocks 可以添加至 5120 bytes ，默认是 512 bytes ！ 
　  　 这样的好处是可以让大文件的储存速度加快(请参考 i-nodes 的观念) 
还原会使用到的选项与参数：
  -i ：将数据自文件或装置 copy 出来系统当中 
  -d ：自动创建目录！使用 cpio 所备份的数据内容不见得会在同一层目录中，因此我们
       必须要让 cpio 在还原时可以创建新目录，此时就得要 -d 选项的帮助！
  -u ：自动的将较新的文件覆盖较旧的文件！
  -t ：需配合 -i 选项，可用在"察看"以 cpio 创建的文件或装置的内容 
一些可共享的选项与参数：
  -v ：让储存的过程中文件名称可以在萤幕上显示 
  -c ：一种较新的 portable format 方式储存 
```