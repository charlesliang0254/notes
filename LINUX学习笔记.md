# 鸟哥的LINUX私房菜读书笔记

## 一、基础概念

计算机、计算机组成、RISC/CISC、x86/AMD、南北桥、外频倍频、总线宽度与字长

物理CPU、核心数、逻辑CPU、超线程

DRAM = Dynamic Random Access Memory
SRAM = Static Random Access Memory
SDRAM = Synchronous DRAM
DDR SDRAM = Double Date Rate SDRAM
ROM = Read Only Memory
flash
EEPROM = Electrically Erasable Programmable ROM

PCI=>AGP=>PCIe
D-sub、DVI、HDMI、DisplayPort

磁道、扇区、柱面
SATA、SAS、IDE、SCSI
USB、eSATA

POSIX：可移植操作系统接口

虚拟化技术：在一台物理主机上模拟多个逻辑上完全独立的硬件

Linux企鹅的名字——Tux

Linux内核版本：

- 奇偶版本：主次版本为奇数——开发中版本；主次版本为偶数——稳定版本
- 主线版本、长期维护版本

查看最新主线版本和长期维护版本：https://www.kernel.org/

## 二、主机规划与磁盘分区

LINUX中组件和设备都是以文件的形式存在的

|设备|文件名|
|---------------|:---------------|
|SCSI、SATA、USB硬盘驱动器 |/dev/sd[a-p]|
|U盘 |/dev/sd[a-p]|
|Virto接口 |/dev/vd[a-p]|
|软盘驱动器 |/dev/fd[0-7]|
|打印机 |/dev/lp[0-2]（25针打印机）<br/>/dev/usb/lp[0-15]（USB接口）|
|鼠标 |/dev/input/mouse[0-15]（通用）<br/>/dev/psaux（PS/2接口）<br/>/dev/mouse（当前鼠标）|
|CDROM、DVDROM |/dev/scd[0-1]（通用）<br/>/dev/sr[0-1]（通用,CENTOS比较常见）<br/>/dev/cdrom（当前CDROM）|
|磁带机 |/dev/ht0（IDE接口）<br/>/dev/st0（SATA/SCSI接口）<br/>/dev/tape（当前磁带）|
|IDE磁盘驱动器 |/dev/hd[a-d]（旧式系统才有）|

整块硬盘的第一个扇区最重要，存放主引导记录（MBR）、分区表、结束标志。

- MBR：可以安装启动引导程序的地方，有446B
- 分区表：记录整块硬盘分区状态，有64B，最多仅能有四组记录区，称为主分区或扩展分区。分区的作用是隔离数据和提升性能。
- 结束标志（55AA）

主分区、扩展分区、逻辑分区：

- 主分区：可直接使用，记录一个分区的信息，可以被格式化，可以用来安装操作系统
- 扩展分区：使用额外的扇区划分多个逻辑分区，不可直接使用，无法格式化
- 逻辑分区：由扩展分区划分的新的分区，可以被格式化

主分区和扩展分区之和不超过4，扩展分区最多只能有一个，逻辑分区的数量依操作系统而定，主分区的编号从1开始，逻辑分区的编号从5开始

GPT磁盘分区表：

- 因为已经存在4K扇区，所以使用逻辑区块地址（LBA）取代原有扇区概念
- 使用34个LBA块记录分区信息，LBA0为MBR兼容区块，LBA1为GPT表头记录LBA2-33为实际记录分区信息处

BIOS搭配MBR/GPT的启动流程：

- BIOS：启动主动执行的固件，识别第一个可启动的设备
- MBR：第一个可启动设备的第一个扇区内的主引导记录块，内含启动引导代码
- 启动引导程序（BootLoader）：一个可读取内核文件执行的软件
- 内核文件：开始启动操作系统

引导程序的作用：

- 提供不同的启动选项
- 加载内核文件
- 转交其它启动引导程序

每个分区都会有自己的启动分区，实际可启动的内核文件放在分区之中

引导程序只会识别自己系统分区内的可启动的内核文件以及其他启动引导程序

WIndows安装时会主动覆盖MBR和自己所在分区的启动扇区，Linux安装时可以设置将引导程序存放在MBR或者其他分区的启动扇区

UEFI：BIOS的替代方案

- 使用C语言编写
- 使用驱动程序和协议
- CPU保护模式
- 支持简化操作系统环境

目录树架构：以根目录为主，然后向下呈现为分支状的一种文件架构

挂载：利用一个目录当成进入点，将磁盘分区的数据放置在该目录下

## 三、LINUX安装

## 四、首次登录与在线求助

linux命令：

```sh
command [-options] param1 param2
```

语言：

```sh
# 显示语言
locale

# 修改语言
LANG=en_US.utf8
export LC_ALL=en_US.utf8

# 修改默认的语系
vi /etc/locale.conf
```

显示日期的命令：

```sh
date                # 显示为Sat Aug 28 09:21:54 CST 2021
date +%Y-%m-%d      # 显示为2021-08-27格式的日期
date +%F            # 显示为2021-08-27格式的日期
date +%H:%M:%S      # 显示为09:00:00格式的时间
date +%T            # 显示为09:00:00格式的时间

%%	百分号本身
%a	星期的英文（或其他语言，下同）缩写
%A	星期的完整英文
%b	月份的英文缩写
%B	月份的完整英文
%c	日期时间的英文
%C	世纪减一

```

显示日历的命令：

```sh
cal                 # 显示当月的日历
cal 2021			# 显示2021年的年历
cal 7 2021          # 显示2021年7月的日历
```

计算器：

```sh
bc                  # 进入计算器
quit                # 退出计算器
```

几个重要的热键：

```sh
TAB                    - 命令补全和文件补全
CTRL+C                 - 中断目前的程序
CTRL+D                 - 结束输入，EXIT
Shift+PageUp/PageDown  - 翻页
```

命令的帮助信息

- `--help`选项
- man页面
  - 注意代号1、5、8的含义，1-一般用户可用，5-配置文件或某些文件的格式，8-系统管理员可用
  - 按h查看热键
  - `man -f`/`whatis`
  - `man -k`/`apropos`
  - `man 数字`
- info页面
  - 拆分成页面的形式展示
  - 按h查看热键，n=next，p=previous
  - `*`表示菜单，可以按Enter跳转
- `usr/share/doc`

超简易的文本编辑器——nano

关机：

- `who` 查看登录的用户
- `netstat -a` 查看网络的联机状态
- `ps -aux` 查看后台运行的程序
- `sync` 数据同步写入磁盘
- 关机/重启命令：
  - `shutdown`：通知系统内的进程、通知系统中的一些服务关闭，支持设置关机时间和关机信息，建议使用
  - `reboot`、`halt`、`poweroff`：调用`sync`然后关机，不会通知进程和服务关闭，不建议使用
  - `systemctl`：其他命令关机都会调用`systemctl`
  - `init`

```sh
shutdown -H                   # 停机
shutdown -P                   # 停机并且掉电
shutdown -h                   # 等同于-P，除非指定--halt
shutdown -r                   # 重启
shutdown -k                   # 不关机/重启，发送消息给登录用户
shutdown -c                   # 取消关机/重启
shutdown -h 10 '十分钟后停机'   # 设置关机时间和关机信息

systemctl halt                # 停机
systemctl poweroff            # 停机&掉电
systemctl reboot              # 重启
systemctl suspend             # 挂起
```

## 五、文件权限与目录配置

用户、用户组、其他人、root

用户信息：`/etc/passwd`

用户口令：`/etc/shadow`

用户组信息：`/etc/group`

查看文件：即查看目录文件的内容

```sh
ls                # 查看文件名
ls -l             # 查看文件详细信息
ls -a             # 显示所有隐藏文件
ls -d             # 查看目录文件本身而不是它的内容
ls -hl            # 文件大小适合人类阅读方式表示
ls -Rl			  # 递归查看目录文件内容SUID
ls -l --full-time # 展示完整的时间

-rwxr-xr-x.   1      root     root        8968          Aug 28 12:21  a.out
文件的类型与权限 链接数  文件拥有者 文件所属用户组 文件大小（字节） 文件最后修改时间  文件名

文件的类型与权限：
第1位：d 目录 - 文件 l 链接文件 b 可按块随机读写设备 c 串行端口设备 s Socket文件 p 管道文件
第2-4位：文件拥有者具备的权限，依次为rwx，表示可读、可写、可执行,-表示不具有该项权限
第5-7位：文件所属用户组的权限，依次为rwx，表示可读、可写、可执行,-表示不具有该项权限
第8-10位：其他人的权限，依次为rwx，表示可读、可写、可执行,-表示不具有该项权限

如果文件名是以 . 开头的，那么这个文件就是一个隐藏文件
```

修改文件权限与属性：

- `chgrp`：修改文件所属用户组
- `chown`：修改文件拥有者
- `chmod`：修改文件的权限，SUID、SGID、SBIT等特性

```sh
chgrp [-R] GROUPNAME DIRNAME/FILE...        # 修改文件所属用户组，-R表示递归修改
chown [-R] USERNAME:GROUPNAME DIR/FILE...   # 修改文件拥有者和文件所属用户组
chown [-R] USERNAME DIR/FILE...            	# 修改文件拥有者
chmod [-R] OCTAL-MODE DIR/FILE...           # 修改文件权限
chmod [-R] MODE... DIR/FILE...              # 修改文件权限

# OCTAL-MODE: 
#     r=4 w=2 x=1 rwxr-xr-x=755 rw-r--r--=644
# MODE:
#     u=文件拥有者 g=文件所属用户组 o=其他人 a=所有人u+g+o
#     +（加入） -（移除） =（设置）
#     r w x
#
#     u+rwx 给文件拥有者添加读写执行权限 
#     g-x 移除用户组的执行权限 
#     o=r 其他人的权限黄设置为只读 
#     a+r 给所有人添加只读权限
```

文件的权限意义：

- 对于文件：r=可读 w=可写 x=可执行
- 对于目录：r=可读（查看目录中的文件列表） w=可写（增加和删除已有文件与目录，移动=删除+新增）x=能否进入或成为工作目录
- 一般对于目录来说，x权限>r权限，一般同时出现

Linux文件种类与扩展名：

- 常规文件
  - 纯文本文件：用户可以直接阅读的文件，可以使用`cat`命令查看文件内容
  - 二进制文件：操作系统可以直接执行的文件，用户无法阅读
  - 数据文件：具有特定格式的文件，能够使用`last`命令读出来，不能使用`cat`命令读出来
- 目录
- 链接文件：指的是软链接文件，相当于Wiindows操作系统中的快捷方式
- 设备与设备文件：与系统周边及存储相关的一些文件
  - 区块设备文件：表示块设备，以块为单位传输数据，传输数据的效率高
  - 字符设备文件：表示一些串行端口的接口设备，以字符为单位传输数据，传输数据的效率低
- 数据接口文件（sockets）：通常被用来在网络上交换数据
- 数据输送文件（FIFO，pipe）：管道文件

Linux文件扩展名只具有语义上的含义，对于操作系统没有任何含义。

Linux单一文件名上限是255字节，应当避免在文件名中使用特殊符号。

Linux目录配置的依据——FHS：

- 目录的四种交互状态：可分享/不可分享 x 不变的/可变的

  - 可分享：可以分享给其它系统挂载的目录
  - 不可分享：自己机器上面运行的设备文件或是与程序相关的socket文件等，不可分享给其他机器
  - 不变：有些数据不会经常发生变动，例如函数库、文件说明、系统管理员所管理的主机服务配置文件等
  - 可变动：经常修改的数据，如日志文件、一般用户可自行接收的新闻组等

|                    | 可分享（shareable）       | 不可分享（unshareable） |
| ------------------ | ------------------------- | ----------------------- |
| 不变（static）     | /usr（软件存放处）        | /etc（配置文件）        |
|                    | /opt（第三方辅助文件）    | /boot（启动与内核文件） |
| 可变动（variable） | /var/mail（用户邮箱）     | /var/run（程序相关）    |
|                    | /var/spool/news（新闻组） | /var/lock（程序相关）   |


- / （根目录，与启动系统有关） /usr（与软件安装、执行有关） /var（与系统运行过程有关）
- FHS建议根目录所在分区越小越好，应用程序所安装的软件不应和根目录在同一个分区中
- FHS定义 / （根目录）下应该有以下目录才好

| 目录                                | 应放置文件内容                                               |
| ----------------------------------- | ------------------------------------------------------------ |
| **第一部分：FHS要求必须存在的目录** |                                                              |
| `/bin`                              | 在单人维护模式下还能够被使用的命令                           |
| `/boot`                             | 启动过程中会使用到的文件                                     |
| `/dev`                              | 设备文件                                                     |
| `/etc`                              | 系统主要的配置文件放在这个目录<br/>`/etc/opt`（必要）：/opt中的第三方辅助软件的配置文件放在这里<br/>`/etc/x11`（建议）：X Window相关的配置文件放在这里<br/>`/etc/sgml`（建议）：与SGML格式有关的各项配置文件<br/>`/etc/xml`（建议）：与XML格式有关的各项配置文件 |
| `/lib`                              | 启动时以及在`/bin`或`/sbin`下面的命令会调用的函数库<br>`/lib/modules/`：可抽换式的内核相关模块（驱动程序） |
| `/media`                            | 可删除的设备，包括软盘、光盘、U盘等                          |
| `/mnt`                              | 暂时挂载某些额外的设备                                       |
| `/opt`                              | 第三方辅助软件放置的目录                                     |
| `/run`                              | 系统启动后产生的各项信息                                     |
| `/sbin`                             | root用户用来配置系统环境，启动、修复、还原系统所需要的命令   |
| `/srv`                              | 一些网络服务启动之后所需要的一些数据存放的位置               |
| `/tmp`                              | 临时文件存放位置                                             |
| `/usr`                              | Unix Software Resource，操作系统软件资源                     |
| `/var`                              | 运行时才会慢慢占用的目录，主要存放缓存、日志以及某些软件运行后产生的文件，包括程序文件（lock file、run file）、MySQL数据库文件等等 |
| **第二部分：FHS建议可以存在的目录** |                                                              |
| `/home`                             | 系统默认的用户家目录，`~`表示当前用户的家目录，`~liming`表示用户liming的家目录 |
| `/lib<qual>`                        | 用来存放与/lib不同格式的二进制函数库                         |
| /root                               | 系统管理员的家目录                                           |

Linux中几个常见的目录：

| 目录        | 应放置的文件内容                                             |
| ----------- | ------------------------------------------------------------ |
| /lost+found | 使用标准的ext2、ext3、ext4文件系统格式才会产生的一个目录，目的在于当文件系统发生错误时，将一些遗失的片段放置到这个目录下 |
| /proc       | 这个目录本身是一个虚拟文件文件系统（virtual filesystem），它放置的数据都是在内存中的，例如系统内核、进程信息（process）、外接设备的状态及网络状态等。因为这个目录下的数据都是在内存当中，所以本身不占任何硬盘空间。比较重要的文件如：/proc/cpuinfo、/proc/dma、/proc/ioports、/proc/net/*等 |
| /sys        | 这个目录同样是一个虚拟文件系统（virtual filesystem）主要用来记录内核与系统硬件信息相关的内容。包括目前已加载的内核模块与内核检测到的硬件设备信息等，这个目录同样不占硬盘空间 |

 注意在CENTOS7.x中`/sbin`、`/bin`、`/lib`已经被移动到了/usr下面



/usr的意义与内容：

usr = Unix Software Resource，操作系统软件资源，/usr的子目录建议有以下内容

| 目录                                | 应放置文件内容                                               |
| ----------------------------------- | ------------------------------------------------------------ |
| **第一部分：FHS要求必须存在的目录** |                                                              |
| `/usr/bin`                          | 一般用户能够使用的命令都放在这里，CentOS已经将全部的命令都放在这里，/usr/bin目录下不应该再有子目录 |
| `/usr/lib`                          | 与lib的功能相同，所以/lib目录也会链接于此                    |
| `/usr/local`                        | 系统管理员在本机安装自己下载的软件（非发行版默认提供者），建议安装到此目录，这样会比较便于管理 |
| `/usr/sbin`                         | 非系统正常运行所需要的系统命令，最常见的就是某些网络服务器软件中的服务命令（daemon），基本功能与/sbin差不多，因此目前/sbin目录也是链接到该目录的 |
| `/usr/share`                        | 主要防止只读的数据文件，当然也包括共享文件，在这个目录下放置的数据几乎是不分硬件架构均可读取的数据，因为几乎都是文本文件。在此目录下常见的还有这些子目录：<br>- /usr/share/man：在线帮助文档<br>- /usr/share/doc：软件说明文档<br>- /usr/share/zoneinfo：与时区有关的时区文件 |
| **第二部分：FHS建议可以存在的目录** |                                                              |
| `/usr/games`                        | 与游戏比较相关的数据放置处                                   |
| `/usr/include`                      | c/c++等程序语言的头文件与包含文件存放位置，当我们以Tarball方式（*.tar.gz的方式安装软件）安装某些应用程序时，会使用到里面的很多文件 |
| `/usr/libexec`                      | 某些不被一般用户使用的执行文件或脚本等，到会放置在此目录中   |
| `/usr/lib<qual>`                    | 与`/lib<qual>`功能相同，目前`/lib<qual>`就是链接到此目录     |
| `/usr/src`                          | 一般源代码建议放置的位置，内核源代码则建议放置在/usr/src/Linux目录下 |



/var意义与内容：

在系统运行后逐渐占用硬盘的文件的目录，包括缓存、日志文件及某些软件运行时产生的文件，包括程序文件（lock file、run file），或例如MySQL数据库的文件等。常见的子目录有

| 目录                                | 应放置的文件内容                                    |
| ----------------------------------- | --------------------------------------------------- |
| **第一部分：FHS要求必须存在的目录** |                                                     |
| `/var/cache`                        | 应用程序缓存                                        |
| `/var/lib`                          | 程序使用的数据文件的存放位置                        |
| `/var/lock`                         | 设备锁文件，用于控制设备的并发访问                  |
| `/var/log`                          | 日志文件存放的位置                                  |
| `/var/mail`                         | 个人电子邮箱目录                                    |
| `/var/run`                          | 某些程序或服务启动后，会将他们的PID放置在这个目录下 |
| `/var/spool`                        | 放置一些队列数据                                    |



Centos发行版的目录：

- 网络设置数据放在 `/etc/sysconfig/network-scripts`
- 个别目录的位置调整：

```sh
/bin => /usr/bin
/sbin => /usr/sbin
/lib => /usr/lib
/lib64 => /usr/lib64
/var/lock => /run/lock
/var/run => /run
```



目录树：linux中所有文件和目录构成的树形结构

- 目录树的根节点是 `/`
- 支持本地分区的树形结构，也可以使用网络上的文件系统。CPU和内存只关心逻辑上的文件系统，不关心文件到底是存在哪里的。
- 每一个文件的全路径名在整个目录树中都是唯一的

## 六、文件和目录管理

### 目录和路径

绝对路径和相对路径：

- 绝对路径：目录树中以根目录开始的路径
- 相对路径：目录树中以目前路径是相对路径
  - `.` 或 `./`代表当前的目录
  - `..`或`../`代表当前目录的双亲目录

特殊的目录：`. .. - ~ ~USERNAME`

切换目录

```sh
cd
```

显示当前目录

```sh
pwd      # 显示当前路径
pwd -P   # 显示真正的路径，而不是使用链接的路径
```

建立新目录

```sh
mkdir DIR          # 建立新目录
mkdir -p DIR       # 建立路径上没有的所有目录
mkdir -m MODE DIR  # 建立新目录并且设置目录的权限，注意MODE是类似于755这种，不是rwx这种
mkdir -mp MODE DIR # 建立路径上没有的所有目录并设置权限
```

删除“空”的目录

```sh
rmdir DIR           # 删除“空”的目录
rmdir -p DIR        # 删除路径上“空”的目录
```

环境变量PATH：有很多目录，用冒号分隔，执行命令时会先去PATH中检索；不同用户的PATH也是不同的；不建议在PATH中加入`.`

修改PATH：

```sh
PATH="${PATH}:某个目录"
```

### 文件与目录管理

`ls` ：文件和目录查看

常用的选项：

```
-a  全部的文件（包括隐藏文件）
-A  除 . 和 .. 外全部的文件（包括隐藏文件）
-d  列举目录本身
-h  将文件大小以易读方式展示
-i  列出inode号码
-R  递归查看目录
-S  以文件容量大小排序而不是文件名
-t  以时间排序而不是文件名
--full-time  以完整时间模式输出
--time=atime|ctime  输出access时间或改变权限属性时间
```

cp、rm、mv：复制、删除、移动

常用的选项：

```
cp - 复制文件
	cp [OPTION]... SOURCE... DIRECTORY

    -i  interactive 交互模式，覆盖前会先询问
    -f  force 强制赋值，覆盖时不会询问
    -r  递归复制
    -d  如果复制的源文件是链接文件，那么复制链接文件的属性而不是实际指向的文件
    -l  建立硬链接而不是复制文件
    -s  建立符号链接（软链接）而不是复制文件
    -a  相当于 -dr --preserve=all
    -p  连同文件的属性‘属主、时间戳一起复制过去，相当于 --preserve=mode,ownership,timestamps
    --preserve[=ATTR_LIST]  连同指定的属性一起复制，默认为mode、ownership、timestamps，其他的属性还包括 context、links、xattr、all

rm - 删除文件
	rm [OPTION]... FILE...

    -f  强制删除
    -i  交互模式，删除前会询问
    -r  递归删除（常用于目录的删除，使用起来比较危险）
    -d  删除空的目录

mv - 移动文件
    mv [OPTION]... SOURCE... DIRECTORY

    -f  强制移动文件，会覆盖目标文件
    -i  interactive交互模式，覆盖前会先询问
    -u  源文件比目标文件新时才会覆盖

```

注意：

- 复制权限时，如果当前用户本身的权限不足，那么即使加上了`-a`也不会起作用



basename：获取路径的文件名

dirname：获取路径的目录名



### 文件内容的查看

cat、tac、nl：直接查看文件的内容

```
cat - 显示文件的内容
	cat [OPTION]... [FILE]...

    -A  相当于 -vET
    -v  列出一些看不出来的特殊字符
    -E  将结尾的换行符显示出来
    -T  将TAB键以^I显示出来
    -n  显示行号
    -b  显示行号，但空行不列出行号

	cat -  显示标准输入的内容

tac - 反向显示文件的内容
	tac [OPTION]... [FILE]...

nl - 添加行号打印
	nl [OPTION]... [FILE]...

	-b  指定行号指定的方式
		-b a  不论是否为空行都指定行号，相当于cat -n
		-b t  空行不列出行号，相当于cat -b
	-n  指定行号的显示方式
		-n ln 行号居左显示
		-n rn 行号居右显示（默认）
		-n rz 行号居右显示，且左侧补零
	-w  行号栏位宽度
```

more、less：可翻页查看

```
more常见命令：
	h          帮助信息
	<space>    下一页
	<enter>    下一行
	/STRING    查询字符串
	:f         显示文件名和行数
	q          退出
	b ctrl+b   往回翻页
	
less常见命令：
	h          帮助信息
	q          退出
	e j        下一行
	y k        上一行
	f <space>  下一页
	b          上一页
	/PATTERN   向后搜索
	?PATTERN   向前搜索
	n          搜索下一条
	N          搜索上一条
	g          回到开始
	G          到达结尾
```

head、tail：数据截取

```
head - 取出前面几行
	head [OPTION]... [FILE]...
	
	-n  从头截取的行数
	-c  从头截取的字符数
	-q  始终不打印文件名信息
	-v  始终打印文件名信息
	
tail - 取出后面几行
	-n  从末尾截取的行数
	-f  表示持续刷新后面所接文件中的内容，直到ctrl+c退出
	-q  始终不打印文件名信息
	-v  始终打印文件名信息
	
截取文件的第11到20行
cat -n /etc/man_db.conf | head -n 20 | tail -n 10
```

od：显示非纯文本文件的内容

```

od [-t TYPE] FILE

-t  输出类型，TYPE可选值如下
    a		使用默认的字符来显示输出
    c		使用ascii字符来显示输出
    d[SIZE] 使用十进制数输出，每个整数占用SIZE个字节
    f[SIZE] 使用浮点数输出，每个浮点数占用SIZE个字节
    o[SIZE] 使用八进制输出，每个八进制数占用SIZE个字节
    u[SIZE] 无符号数，每个无符号数占SIZE个字节
    x[SIZE] 使用十六进制输出，每个十六进制数占用SIZE个字节

    SIZE是一个数，如果TYPE的值为doux，那么SIZE还可以是
        C sizeof(char)
        S sizeof(short)
        I sizeof(int)
        L sizeof(long)
    如果TYPE是f，SIZE还可以是
        F sizeof(float)
        D sizeof(double)
        L sizeof(long double)
```

touch：修改文件时间或者创建新文件

> 文件的三个时间：
>
> - 修改时间（modification time, mtime）：当文件内容而非文件属性变化时，mtime更新
> - 状态时间（status time, ctime）：当文件的状态改变了就会更新这个时间，例如权限和属性变化了
> - 读取时间（access time, atime）：当文件内容被读取时就会更新这个时间

```
touch [OPTION]... FILE...
-a  只修改atime
-m  只修改mtime
-t  指定要修改为的时间，格式为[YYYYMMDDhhmm[.ss]]
-c  仅修改时间，不创建文件
-d  指定要修改为的日期，支持人类易读的日期格式，如：
Sun, 29 Feb 2004 16:21:42 -0800
2004-02-29 16:21:42
next Thursday
```

### 文件的默认权限与隐藏权限

umask：查看和设置文件的默认权限

```sh
umask                  # 以八进制数形式查看文件权限
umask -S               # 以符号形式查看文件权限
umask 0022             # 以八进制数形式设置权限
umask u=rwx,g=rx,o=rx  # 以符号形式设置权限

文件权限 = 666 & !umask值
目录权限 = 777 & !umask值
（&表示按位与，!表示按位取反）
```

> 文件隐藏属性的支持程度因文件系统而异，不同的文件系统对隐藏权限的支持程度也是不同的
>
> 使用man命令查看各个文件系统对隐藏权限的支持情况再使用
>
> `man -5 btrfs`
>
> `man -5 ext`
>
> `man -5 xfs`

chattr：设置文件隐藏属性

lsattr：查看文件隐藏属性

文件特殊权限：SUID、SGID、SBIT

- Set SUID：

  - 使用：标志为`s`，出现在文件拥有者的`x`权限位上；对应的八进制码值为4

  - 功能：具有某个程序的`x`权限的用户在该程序运行过程中借用程序属主的权限。

    例如，`sudo`命令能够使用root用户权限执行一些操作就是使用了SUID权限。

  - 条件：

    - 二进制文件有效；
    - 执行者拥有`x`权限；
    - 仅在该程序执行过程中有效；
    - 借用程序的属主的权限

- Set GUID

  - 标志为`s`，出现在文件所属用户组的`x`权限位置上；对应的八进制码值为2

  - 作用在文件上：具有某个程序的<code>x</code>权限的用户在该程序运行过程中借用程序所属用户组的权限。

  - 作用在目录上：具有某个程序的`x`权限的用户如果具有`r`和`x`权限可进入该目录，用户在此目录下的有效用户组会变成该目录的用户组，若用户在此目录下具有`w`的权限（可新建文件）则用户所建立的新文件所属用户组与此目录的用户组相同

  - 条件：

    - 二进制文件和目录有效
    - 执行者拥有`x`权限；
    - 如果是二进制文件，仅在该程序执行过程中有效；
    - 借用程序所属用户组的权限

    > 初始用户组：/etc/passwd文件中用户所在行的第4个字段记录的就是初始用户组
    >
    > 有效用户组：用户当前所具有的用户组就是有效用户组
    >
    > 使用groups命令可以查看用户的有效用户组

- Sticky Bit

  - 标志为`t`，出现在其他人的`x`权限位置上；对应的八进制码值为1
  - 作用：这个属性加在目录上之后，只有文件创建者和root才能删除、更名、移动文件；加在普通文件上没有效果

```sh
# 使用八进制数值设置权限
chmod 4755 start1.sh		# 添加SUID权限，SUID只能用在文件上
chmod 2755 start2.sh		# 添加GUID权限，用在文件上
chmod 2755 dir1				# 添加GUID权限，用在目录上
chmod 1755 dir2				# 添加SBIT权限，SBIT权限只能用在目录上

# 使用符号添加权限
chmod u+s start1.sh			# 添加SUID权限，SUID只能用在文件上
chmod g+s start2.sh			# 添加GUID权限，用在文件上
chmod g+s dir1				# 添加GUID权限，用在目录上
chmod o+t dir2				# 添加SBIT权限，SBIT只能用在目录上
```

注意：如果没有给设置SUID、GUID、SBIT权限的目录/文件设置`x`，那么这些属性就不会起作用，会显示成大写的`S`和`T`



file：观察文件类型

file命令会测试每一个参数以辨别文件的类型，共有三种测试，按照**文件系统测试**、**magic测试**和**语言测试**的顺序执行。首个通过的测试决定了输出的文件类型。被打印出来的文件类型通常会包含`text`、`executable`、`data`关键字之一，分别表示这是一个文本文件、可执行文件或数据文件。

- **text**：只包含可打印字符和一些常见的控制字符，并且可以在一个ASCII终端上尽可能安全地被读取
- **executable**：文件中包含具备Unix或者其他内核可识别格式的程序编译结果
- **data**：具有其他含义的二进制数据（数据通常是二进制且不可打印的）
- 例外情况是一些熟知的包含二进制数据的文件格式（core文件、tar压缩文件）

> 注意：当修改magic文件或者程序本身时，请确保保留这些关键字。用户依赖于知道目录中所有的可阅读文本都有”text“关键字。

三种文件类型测试：

- 文件系统测试：基于`stat`系统调用的结果。程序会检查文件是否为空，或者是否是某些特殊文件的排序。如果在系统头文件中定义了适合当前正在运行系统的文件类型（sockets、symbolic links、FIFOs），那么它们就会直觉地被知晓。
- Magic测试：检查有没有固定格式的标志性数据。例如，`a.out`文件的格式就被定义在标准库的几个头文件中。这些文件中会在特定的位置定义魔法数字，一般位于文件的开始位置，告知操作系统这是一个二进制可执行文件并由此获知是哪一种文件类型。magic的概念已经用来对数据文件类型进行扩展，任何文件在固定偏移量的位置如果有这样的magic值，就能够使用这种方式来描述它的类型。识别这些文件类型的magic值的信息在/usr/share/misc/magic.mgc文件中，如果这种编译好的文件不存在，那就去/usr/share/misc/magic/目录下找。如果你的家目录下存在.magic.mgc文件或者.magic目录，那使用的就是你这个magic文件。
- 语言测试：如果文件系统测试和Magic测试都没有通过，文件会被测试是否为一个文本文件，程序会使用各种类型的字符集尝试匹配文件的内容。根据文件匹配的字符集的不同，文件会被认定为"text"或者”character data“。除此之外，如果文件中使用了不同于LF的行结束符，程序也会进行报告；内置的转义序列也会被报告。一旦，文件被认为是一个文本文件，则会进行语言测试，根据`<names.h>`中定义的特殊关键字判断文件属于哪一种语言的源代码。



### 命令与文件的查找

which：查找执行文件，只会查找PATH路径下的可执行文件，不能查找bash内置的命令。

```
-a  查询全部
```

whereis：由一些特定的目录中查找文件

```
-l  列出whereis命令查找的几个目录
-b  查询二进制文件
-m  只找在说明文件manual路径下的文件
-s  只找source源文件
-u  查找不在上述三类项目中的其他特殊文件
```

locate：从数据库中查询文件名，默认每天更新一次数据库。

- 数据库存放的位置：`/var/lib/mlocate`
- 配置文件：`/etc/updatedb.conf`
- 手动更新数据库：`updatedb`

```
-l  限制查询结果的条数
-c  不输出结果，输出结果的条数
-A  匹配所有模式
-b  只匹配文件名，不包括文件名前面的路径
-w  匹配全路径名
-e  保证查询的结果都是存在的
-i  忽略大小写
```

find：直接读取目录树，查找文件名

```
1.与时间有关的选项 -atime -ctime -mtime，以 -atime为例：
-mtime n  		n天之前这一天被修改过内容的文件
-mtime +n 		n天之前（不包括n天本身）被修改内容的文件
-mtime -n 		n天之内（包括n天本身）被修改内容的文件
-newer file		比指定文件更新的文件

2.与使用者和用户组名称有关的参数：
-uid UID			查询与指定用户ID有关的文件
-gid GID			查询与指定用户组ID有关的文件
-user USERNAME		查询与指定用户名有关的文件
-group GROUPNAME	查询与指定用户组名有关的文件
-nouser				查询无主文件
-nogroup			查询无用户组文件

3.与文件属性相关的参数
-name FILENAME		查询指定文件名的文件
-size [+-]SIZE		查询指定大小的文件
						+表示比SIZE大，-表示比SIZE小
						SIZE可以携带单位：c 字节 k 千字节
-type TYPE			查询指定类型的文件，
						f 普通文件 
						b和c 设备文件 
						d 目录 
						l 链接文件 
						s socket文件 
						p FIFO文件
-perm MODE			查询权限等于MODE的文件
-perm -MODE			查询权限包含MODE的文件
-perm /MODE			查询文件权限包含任一MODE的文件

4.额外可以执行的操作
-exec COMMAND		额外的命令可以处理查询到的结果
-print				将结果打印到屏幕上，这是默认的操作
```

## 七、linux磁盘与文件系统管理

### 文件系统

文件的实际内容存放在数据区块，文件属性存放在inode节点，此外还有超级区块记录整个文件系统的整体信息（如inode与数据区块的总量、使用量、剩余量）

索引式文件系统：在inode节点中记录全部数据区块的位置，与之相对的是FAT这种顺序存取的文件系统

文件系统一开始就会规划好inode与数据区块，除非重新格式化或使用resize2fs命令，一般不变。

区块群组：linux ext2文件系统区分多个区块群组，每个区块群组拥有独立的inode、数据区块、超级区块系统

- 数据区块：放置数据，ext2中支持1K、2K、4K数据区块，不同数据区块支持的最大单一文件大小和最大文件系统总容量也不同。原则上区块的大小与数量在格式化完成后不能再修改；每个区块中最多只能放置一个文件的数据；一个文件可能占用多个数据区块；文件小于区块时会产生内碎片
- inode表：记录文件的读写属性、文件属主与用户组、文件大小、ctime&atime&mtime、定义文件特性的标识、该文件真正内容的指向。每个inode固定大小为128B或256B，每个文件只会占用1个inode，文件系统能够建立文件的数量与inode节点个数相关，读取文件先找到inode并校验访问权限。
  - 一个inode表中有1个文件属性记录区域、12个直接指向、1个间接指向、1个双间接指向、1个三间接指向
  - 直接指向：直接记录一个数据区块的编号，12x1K=12K
  - 间接指向：使用额外的一个区块记录数据区块的编号，一个1K的数据区块可以记录256个区块编号，256x1K=256K
  - 双间接指向：第一层区块指向第二层区块，第二层区块指向数据区块，总共256x256x1K=64M
  - 三间接指向：第一层区块指向第二层区块，第二层区块指向第三层区块，总共256x256x256x1K=16G
- 超级区块：记录数据区块与inode的总量、未使用与已使用的inode与数据区块的数量、数据区块与inode的大小、文件系统的挂载时间、最近一次写入数据的时间、最近一次检验磁盘（fsck）的时间、有效位（表示当前文件系统是否已经挂载）。一个文件系统应该只有一个超级区块，后续区块群组中的超级区块主要用作备份
- 文件系统描述说明：描述每个区块群组的开始与结束位置，说明每个区块（超级区块、对照表、inode对照表、数据区块）分别介于哪一个区块之间
- 区块对照表：记录每一个区块是否为空
- inode对照表：记录每一个inode是否使用
- dumpe2fs：查询ext系列超级区块信息的命令

```txt
dumpe2fs [-bh] 设备文件名

-b  列出保留为坏道的部分（一般用不到）
-h  仅列出超级区块的数据，不列出其他区块的数据
```

目录：创建目录时，文件系统会分配一个inode和至少一个区块。inode记录目录文件属性，区块记录文件名与文件名占用的inode号码。

文件：创建一般文件时，文件系统分配一个inode和若干区块

目录树：inode本身不记录文件名，文件名的记录是在目录的区块中

ext2/ext3/ext4文件的存取与日志式文件系统的功能

新建文件的操作：

1、校验用户读写目录的权限

2、查找未使用inode节点，写入新文件的属性

3、查找未使用的区块，写入数据，更新inode的区块指向数据

4、将刚刚写入的inode与区块数据同步更新inode对照表与区块对照表，并更新超级区块的内容

日志式文件系统：

为了避免文件系统不一致的情况发生，在文件系统中规划出一个区块专门用来记录读写文件时的步骤

 Linux文件系统的运行

当系统加载一个文件到内存后，如果该文件没有被修改过，则在内存区段文件数据会被设置为CLEAN；如果内存中的文件数据被更改过了，此时内存中的数据会被设置为DIRTY。系统会不定时地将内存中标记为DIRTY的数据写入硬盘中

常用文件会被加载入内存缓冲区，可以手动使用syn将内存中的数据回写入磁盘，正常关机时会调用sync命令，不正常关机时会导致数据尚未回写，重启后将花费时间进行磁盘校验，甚至导致文件系统损坏。

挂载：文件系统与目录树的结合操作称之为挂载。挂载点一定是目录，该目录是进入文件系统的入口

传统文件系统、日志式文件系统、网络文件系统

查看linux支持的文件系统：

```sh
ls -l /lib/modules/$(uname -r)/kernel/fs
```

系统目前已经加载到内存中支持的文件系统

```sh
cat /proc/filesystems
```

VFS：Virtual Filesystem Switch，Linux内核功能，用来读取文件系统



XFS文件系统：

日志式文件系统

主要规划为三个部分：数据区、文件系统活动登录区、实时运行区

- 数据区（data section）：分为多个存储区群组，每个存储区群组包含超级区块、inode、数据区块以及剩余空间的管理机制。与ext的区别在于，区块与inode有多种不同的容量可供设置，inode与区块是动态产生的，并不是格式化后就固定下来的
- 文件系统活动登录区（log section）：记录文件系统的变化，类似于ext2的日志区
- 实时运行区（realtime section）：建立文件时，在该区段找若干extent区块，放置文件；分配完毕后，再写入到data section的inode与区块中。

使用xfs_info命令可以观察xfs超级区块中的内容：

```txt
第1行		isize	inode的数量	agcount	存储区群组的个数	agsize	每个存储区群组的区块数
第2行		sectsz	逻辑扇区的容量
第4行		bsize	区块的容量	blocks	区块的数量
第5行		sunit	swidth	与磁盘阵列的stripe相关性较高
第7行		log = internal	表示登录区的位置在文件系统内，而不是在外部设备中
第9行		extsz	realtime区域中extent的容量
```

### 文件系统简单操作

df：列出文件系统整体磁盘使用量

```txt

-a	列出所有的文件系统，包括系统特有的/proc等文件系统
-k	以KB单位显示文件系统容量
-m	以MB单位显示文件系统容量
-h	以人类易读方式显示文件系统容量
-H	使用1000进制替换1024进制
-T	连同硬盘分区的文件系统名称也列出
-i	不用磁盘容量，而以inode的数量来显示

输出结果列：
Filesystem	代表该文件系统是在哪个硬盘分区，所以列出设备名称
1k-blocks	说明下面的数字单位是1K，可以使用-h -m来改变容量单位
Used		已使用存储空间
Available	剩余存储空间
Use%		磁盘使用百分比
Mounted on	磁盘挂载点

/proc目录是挂载在内存当中的，不占据磁盘的存储空间
/dev/shm是利用内存虚拟出来的磁盘空间，访问速度很快，但是重启后数据会丢失
```

du：查看文件系统磁盘使用量

```txt
-a	列出所有的文件和目录容量，默认只统计目录下面的文件
-h	以易读方式展示文件和目录容量
-s	仅列出总量，不列出当前目录下的每一个文件的容量
-S  不包括子目录下的总量
-k  以KB为单位查看
-m  以MB为单位查看
```

硬链接与符号链接：

- 硬链接：目录文件中新增文件名到inode节点的关联，不产生新的inode节点。硬链接不能跨文件系统，不能链接目录。

- 符号链接：建立一个独立的文件，让数据的读取指向另外一个文件

ln：创建一个硬链接或符号链接

```txt
ln 参数 源文件 目标文件
ln默认创建的是硬链接

-s  创建一个符号链接文件
-f  强制创建链接，如果目标文件存在会先删除之

```

目录的链接数量：

- 初始情况下链接数量为2，一个是目录本身，指向当前目录的(.)

- 子目录会维护一个指向当前目录的(..)，每增加一个子目录，目录的链接数量会加1

### 磁盘分区、格式化、检验与挂载

挂载磁盘的基本操作：

1、硬盘分区

2、格式化

3、检验

4、挂载目录

lsblk：列出系统上所有的磁盘

```txt
-d  仅列出磁盘，不列出分区
-f  同时列出磁盘内的文件系统的名称
-i  使用acsii输出，不使用复杂的编码
-m  同时输出该设备在/dev下面的权限信息
-p  列出该设备的全路径文件名，而不是仅仅列出设备文件名
-t  列出硬盘的详情，包括磁盘阵列机制、预读写的数据量大小


NAME        设备的文件名
MAJ:MIN     内核识别设备主要就是通过这两个代码实现的，分别是主要与次要设备代码
RM          是否为可卸载设备（Removable Device）
SIZE        指容量
RO          只读设备
TYPE        类型，磁盘（DISK）、分区（PARTITION）、只读存储器（ROM）
MOUNTPOINT  挂载点
```

blkid：列出设备的UUID，实际测试需要root用户权限支持

parted：列出磁盘的分区表类型和分区信息

```txt
parted /dev/sda print   查看硬盘的分区信息，包括硬盘是使用GPT分区还是MBR分区的
```

磁盘分区：fdisk命令适用于MBR分区，gdisk命令适用于GPT分区

gdisk：GPT分区可以使用gdisk命令操作，谨慎使用w命令

```txt
p   查看硬盘的分区信息
      Number            分区编号
      Start（sector）   每一个分区开始扇区号码位置
      End（sector）     每一个分区结束扇区号码位置
      Size              分区的容量
      Code              在分区内可能的文件系统类型，Linux为8300，swap为8200，仅供参考并非实际，可以通过l命令查阅Code对应的文件系统名称
      Name              文件系统的名称等


      Size = （End - Start + 1）* Logical sector size，注意Start和End是闭区间

w   执行、写入硬盘的分区操作
n   新增分区：输入分区编号，输入开始扇区号码，输入结束扇区号码，也可以使用+-相对位置来表示，最后输入HEX CODE或者GUID
l   显示所有可用的分区类型
d   删除一个硬盘分区
      【注意】不要操作正在使用中的分区
```

partprobe：更新linux内核的分区表信息，如果针对正在使用的硬盘进行分区，那么内核不会立即更新分区表，可以使用该命令更新分区表信息或者直接重新启动

fdisk：MBR分区可以使用fdisk命令操作，类似于fdisk

区别在于：

1、使用m命令显示提示信息

2、有时会使用柱面作为分区的最小单位

3、MBR分区有主分区、扩展分区、逻辑分区的限制

mkfs：磁盘格式化，mkfs = make filesystem，对于xfs文件系统使用命令格式化，对于ext4文件系统使用mkfs.ext4命令格式化，



```txt
 [-b bsize] [-d data_section_options] [-i inode_options] [-l log_section_options] [-L label] [-f] [-r realtime_section_options] device

-b  区块容量，单位是字节，可选值从512到64K
-d  data section相关参数值
        agcount=数值    设置需要几个存储群组（AG）的意思，与CPU相关
        agsize=数值     每个存储群组（AG）的容量，通常agcount与agsize只设置其中一个
        name=名称       指定包含在文件系统中的特殊文件的名称，在这种情况下，log section必须指定为internal的
        file=[0|1]      指定name子选项给定的文件是否为regular文件，file=0指设备文件（special file），file=1指普通文件（regular file），只有一个“file”相当于“file=1”（如虚拟磁盘会用到file=1这种情况）
        size=数值       data section的容量，当指定file=1时该子选项必须指定，否则只在文件系统占据的存储空间小于设备文件时指定即可
        su=数值         当有RAID时，指定数据条带单元（stripe unit）的大小，单位是字节，可以添加k、m、g等量词后缀
        sw=数值         当有RAID时，指定数据条带宽度（stripe width），单位是数据条带单元的个数，即存放数据的物理磁盘的个数
        sunit=数值     当有RAID时，指定数据条带单元（stripe unit）的大小，单位是扇区数
        swidth=数值     当有RAID时，置顶数据条带宽度（stripe width），单位是扇区数
-f  强制格式化
-i  与inode有关的设置
        size=数值       inode节点的容量
        internal=[0|1]  log设备是否为内置
        logdev=device   log设备
-L  这个文件系统的标头名称
-r  指定realtime section的相关配置
        extsize         就是realtime section中extent块的容量，一般情况下不进行设置，但是如果是RAID，最好设置与swidth相同的值
```

参见第14章RAID

agcount的值与CPU相关，如果服务器有4个物理内核，但是使用Intel超线程技术，系统可以模拟出8个CPU，那么agcount就可以设置为8

XFS与RAID性能优化：

- 如果有两个线程的CPU数量agcount最好指定为2

- su最后设置为与RAID数据条带（stripe）保持一致，如果stripe指定为256K，那么su可以指定为256K

- 如果RAID磁盘阵列有8个物理磁盘，其中1个是校验盘，则可以指定sw为7

- sunit = su / 扇区大小 = 256K / 512B/sector = 512 sector

- swidth = sunit * sw = 512 sector * 7 = 3584 sector

```sh
# 最终的命令
 -f -d agcount=2,sunit=512,swidth=3584 -r extsize=1792k /dev/vda4
```

mkfs.ext4：ext4文件系统格式化分区

```txt
mkfs.ext4 [-b size] [-L label] 设备名称

-b  设置区块的大小，支持1K、2K、4K
-L  设备的标识符
```

xfs_repair：当xfs文件系统错乱时才使用这个命令

```txt
xfs_repair [-fnd] 设备名称

-f    表示是一个regular文件而不是一个设备文件special file
-n    no modify模式，单纯检查并不修改文件系统的数据
-d    高位选项，通常用在单人维护模式下，针对根目录进行检查和修复的操作
```

fsck.ext4：ext4文件系统的检验

```txt
-p    一律使用y回复
-f    即使文件系统没有发现unclean标识，也要强制进行检查
-D    优化文件系统的目录，这个选项会导致该命令优化全部目录：如果文件系统支持目录索引则重建目录，或者针对小目录或使用传统线性目录的文件系统进行排序和压缩
-b    后面接超级区块的位置，一般情况下不使用，但是如果超级区块因故损坏时，通过这个参数可以利用文件系统内备份的超级区块来尝试恢复，一般来说超级区块备份在：
      1K区块放在8193,2K区块放在16384,4K区块放在32768
```

找出文件系统的另一个超级区块，并据此检验系统：

```sh
dumpe2fs -h /dev/vda5 | grep 'BLocks per group'
# Blocks per group: 32768
# 看起来每个区块群组会有32768个区块，区块编号从0开始，因此第二个超级区块就应该在32768上

fsck.ext4 -b 32768 /dev/vda5
```

注意，被检查的硬盘分区务必不能挂载到系统上，即需要在卸载的状态上

挂载前的准备工作：

- 单一文件系统不应该重复挂载在不同的挂载点中

- 单一目录不能重复挂载多个文件系统

- 要作为挂载点的目录，理论上应该都是空目录才可以。如果作为挂载点的目录不空，那么原来目录中的内容就会隐藏不见，除非将挂载的新文件系统卸载掉，原来的内容才会出现

mount：挂载文件系统命令

```txt
mount -a
mount [-l]
mount [-t 文件系统] LABEL='' 挂载点
mount [-t 文件系统] UUID='' 挂载点
mount [-t 文件系统] 设备文件名 挂载点

选项与参数：
-a  依照配置文件/etc/fstab的数据，将所有未挂载的磁盘都挂载上来
-l  单纯地输入mount会显示挂载信息，使用-l选项可以显示LABEL列
-t  欲挂载的文件系统的类型，如xfs、ext3、ext4、reiserfs、vfat、iso9660、nfs、cifs、smbfs
-n  默认情况下,系统会将实际挂载的情况实时地写入/etc/mtab，使用该选项强制不进行写入
-o  后面可以接一些挂载时额外的参数
      async，sync：此文件系统是否使用同步写入或非同步的内存机制，参考文件系统的运行方式，默认是非同步的
      atime，noatime：是否修改文件的读取时间，为了性能某些时刻可以使用noatime
      ro，rw：挂载文件系统成为只读（ro）或可读写（rw）
      auto，noauto：是否允许此文件系统以mount -a自动挂载
      dev,nodev：是否允许此文件系统可建立设备文件
      suid，nosuid：是否允许此文件系统含有suid/sgid的文件格式
      exec，noexec：是否允许此文件系统上拥有可执行二进制文件
      user，nouser：是否允许此文件系统让任何使用者执行mount
      defaults：默认值为rw、suid、dev、exec、auto、nouser、async
      remount：重新挂载，这在系统出错，或者重新更新参数时很有用

CentOS7中可以省略-t选项，系统会通过分析超级区块搭配linux自己的驱动程序测试挂载，测试成功后就使用选中类型的文件系统挂载。
系统参考/etc/filesystems和/proc/filesystems这两个文件指定哪些类型的文件系统需要进行上述挂载测试

查看系统是否具有相关文件系统类型的驱动程序
ls /lib/modules/$(uname -r)/kernel/fs/文件系统类型
```

常见文件系统的挂载

```sh
# xfs/ext4/vfat等文件系统的挂载
blkid 设备文件名
mount UUID=设备UUID 挂载点

# cd或者dvd的挂载
blkid
mkdir /data/cdrom
mount /dev/sr0 /data/cdrom

# 挂载U盘，注意U盘的文件系统不能是NTFS
blkid
mkdir /data/usb
mount -o codepage=950.iocharset=utf8 UUID=设备UUID /data/usb
df /data/usb

# 重新挂载根目录
mount -o remount,rw,auto /

# 额外挂载目录
mount --bind /var /data/var
```

umount:卸载文件系统

```txt
umount [-fn] 设备文件名或挂载点
-f  强制卸载，可用在类似网络文件系统无法读取到的情况
-l  立即卸载文件系统，比-f还要强制
-n  不更新/etc/mtab的情况下卸载
```

mknod：创建块设备文件（block special file）或字节设备文件（character special file）

```txt
mknod 设备文件名 设备类型 主要设备代码 次要设备代码

设备类型：
b   块设备文件
c   字节设备文件
p   FIFO文件
```

内核支持的设备的主要设备代码和次要设备代码应该查阅官方文档确定


xfs_admin：修改XFS文件系统的参数

```txt
xfs_admin [ -lu ] [ -c 0|1 ] [ -L label ] [ -U uuid ] device
-l  打印当前文件系统标签
-u  打印当前文件系统的UUID
-L  设置当前文件系统标签（12字符以内）
-U  设置当前文件系统的UUID
    生成UUID可以使用uuidgen命令
```

tune2fs：修改ext4文件系统的参数

```txt
tune2fs [-l] [-L LABEL] [-U UUID] 设备文件名
-l  列举文件系统超级区块的内容，包括可以经过当前命令设置的参数的值
-L  设置当前文件系统的标签
-U  设置当前文件系统的UUID
```

### 设置启动挂载

系统挂载的一些限制：

- 根目录必须挂载，并且优先于其他挂载点被挂载

- 其他挂载点必须为已建立的目录，可任意指定，但是必须遵守必需的系统目录架构原则（FHS）

- 所有挂载点在同一时间内只能挂载一次

- 所有硬盘分区在同一时间内只能挂载一次

- 如若进行卸载，必须将工作目录移到挂载点（及其子目录之外）

`/etc/fstab` 文件：静态文件系统表，用来设置启动时挂载的文件系统

```txt
# DEVICE    MOUNT POINT   FILESYSTEM    PARAMETERS    DUMP    FSCK

DEVICE          磁盘设备文件名或UUID或LABEL
MOUNT POINT     挂载点
FILESYSTEM      文件系统类型
PARAMETERS      文件系统参数（mount命令 -o 后面接的参数）
DUMP            能否被dump备份命令作用，一般填0
FSCK            是否以fsck检验扇区
```

`/etc/fstab`文件是启动时的挂载文件系统的配置文件，`/etc/mstab` 文件和`/proc/mounts`文件则是运行时记录文件系统挂载的文件

如果`/etc/fstab`文件配置出错，导致无法正常启动，则无法修改上述文件，只能使用以下命令：

```sh
mount -n -o remount,rw /
```

挂载镜像文件：

```sh
mount -o loop 镜像文件 挂载点

# sample
# 使用dd命令创建一个大文件loopdev
dd if=/dev/zero of=loopdev bs=1M count=16 # count不能过小了，否则无法使用xfs格式化
# 使用命令格式化,-f表示格式化的是一个普通文件
 -f loopdev
# 使用blkid查询设备UUID
blkid loopdev
# 使用mount -o loop挂载
mount -o loop UUID=查询到的loopdev的uuid /mnt
```

### 内存交换分区

使用物理分区创建内存交换分区：

1、分区：使用gdisk命令划分一个分区，其中8200表示linux swap的分区类型；使用partprobe命令更新linux内核分区表信息；使用lsblk验证分区操作成功

2、格式化：使用mkswap命令格式化swap分区

```sh
mkswap 分区
```

3、使用、观察与配置自动启用：使用free命令查看swap空间；使用swapon命令启用swap空间；写入文件系统表`/etc/fstab`，设置挂载点和文件系统类型均为swap;使用swapon -s观察内存的使用量

```sh
swapon 分区
```

使用文件创建内存交换文件

1、创建大文件：使用dd命令在/tmp下创建一个大文件

2、格式化：使用mkswap命令格式化该文件

3、启用与观察：使用swapon命令启动swap空间，使用swapon -s命令观察

4、配置自动启用：使用swapoff命令停用swap空间；使用swapon -s观察；写入文件系统表`/etc/fstab`，设置挂载点和文件系统类型均为swap；使用swapon -a启用`/etc/fstab`文件中记录的未启用的swap文件系统；再使用swapon -s观察

### 文件系统的特殊观察与操作

磁盘空间的浪费问题：

- 使用ls命令查看目录，第一行的total是指该目录下所有数据实际消耗的区块容量

- 使用ls -sl命令查看目录，第一列展示各个文件实际消耗的区块容量

使用parted进行分区操作：

```txt
parted 设备 命令 参数

命令：
    新增分区    mkpart [primary|logical|extended] [ext4|vfat|xfs] 开始 结束
    显示分区    print
    删除分区    rm 分区

分区表信息：
    Number        分区的号码
    Start         分区的起始位置
    End           分区的结束位置
    Size          分区大小
    File System   可能的文件系统
    Name          如同gdisk的System ID

```

## 八、文件与文件系统的压缩

linux常见的压缩文件扩展名：

```txt
*.Z       compress程序压缩的文件
*.zip     zip程序压缩的文件
*.gz      gzip程序压缩的文件
*.bz2     bzip2程序压缩的文件
*.xz      xz程序压缩的文件
*.tar     tar程序打包的文件，并没有压缩过
*.tar.gz  tar程序打包的文件，并且经过gzip的压缩
*.tar.bz2 tar程序打包的文件，并且经过bzip2的压缩
*.tar.xz  tar程序打包的文件，并且经过xz的压缩
```

### linux常见的压缩和打包命令：

1、gzip：压缩

```txt
gzip [-cdtv#] 文件名

-c  输出到标准输出，源文件不变
-d  decompress，解压缩
-f  强制压缩或解压缩
-l  对于每一个文件，列举以下字段：已压缩大小、未压缩大小、压缩比、未压缩文件名
-t  验证压缩文件的完整性
-v  verbose。显示源文件和压缩文件的压缩比等信息
-#  选择一个预设的压缩级别，-1最快，-9最佳，-6默认

使用gzip，源文件直接会被压缩成gz文件。如果想要保留源文件，可以使用：
gzip -c 源文件 > 源文件.gz

gunzip相当于gzip -d

zcat、zmore、zless、zgrep用于查看gz压缩文件的内容，可类比cat、more、less、grep命令
```

2、bzip2：压缩

```txt
bzip2 [-cdkzv#] 文件名

-c  输出到标准输出，源文件不变
-d  decompress，解压缩
-f  强制压缩或解压缩
-k  保留原始文件，而不会删除原始文件
-z  compress，压缩（默认值，可以不加）
-v  verbose，显示压缩或解压缩信息
-#  选择一个预设的压缩级别，-1最快，-9最佳，-6默认

bunzip2相当于bzip2 -d

bzcat、bzmore、bzless、bzgrep用于查看bz2压缩文件的内容，可类比cat、more、less、grep等命令
```

3、xz：压缩

```txt
xz [-dtlkc#] 文件名

-c  输出到标准输出，源文件不变
-d  decompress，解压缩
-f  强制压缩或解压缩
-k  保留原始文件，而不会删除原始文件
-z  compress，压缩（默认值，可以不加）
-v  verbose，显示压缩或解压缩信息
-#  选择一个预设的压缩级别，-1最快，-9最佳，-6默认

unzx相当于xz -d

xzcat、xzmore、xzless、xzgrep用于查看xz压缩文件的内容，可类比cat、more、less、grep等命令
```

4、tar：打包命令，用于将多个文件或目录打包成一个文件

```txt
选项：
-c              打包（压缩）
-t              查询
-x              拆包（解压）
-z              使用gzip压缩或解压
-j              使用bzip2压缩或解压
-J              使用xz压缩或解压
-v              verbose，打印日志
-f filename     压缩或解压（打包或拆包）的文件
-C 目录         拆包（解压）到特定目录
-p              保留备份数据原有的权限和属性
-P              不要剥离文件名前导的/，即文件名保留绝对路径
--exclude=FILE  压缩过程中排除某个文件 


高频用法：
压缩：
tar -jcvf xxx.tar.bz2 源文件或目录
tar -zcvf xxx.tar.gz
tar -Jcvf xxx.tar.xz
查询：
tar -jtvf xxx.tar.bz2
tar -ztvf xxx.tar.gz
tar -Jtvf xxx.tar.xz
解压：
tar -jxvf xxx.tar.bz2
tar -zxvf xxx.tar.gz 
tar -Jxvf xxx.tar.xz

仅解压（拆包）单一文件：
tar -jtvf xxx.tar.bz2 | grep 文件名
tar -jxvf xxx.tar.bz2 待解压文件名

约定俗成的名称：
xxx.tar               tarfile
xxx.tar.(gz|bz2|xz)   tarball
```

time：命令前加time可以打印程序执行时间

### XFS系统备份与还原

文件系统备份的工具—— xfsdump、xfsrestore

xfsdump：可以进行文件系统的完整备份（full backup），也可进行增量备份（incremental backup）

- 使用xfsdump的限制：不支持没有挂载的文件系统备份；必须使用root权限操作；只能备份xfs文件系统；备份的数据只能使用xfsrestore解析；使用文件系统的UUID唯一标识备份文件，不能备份具有相同UUID的文件系统

```txt
xfsdump [-L S_Label] [-M M_Label] [-l #] [-f 备份文件] 待备份数据
xfsdump -I
选项：
-L  为备份会话指定标签，可以填写针对此文件系统的简单说明
-M  为存储媒介指定标签，可以填写此媒介的简单说明
-l  指定备份等级，可选值为0-9（默认是0，即完整备份）
-f  指定备份的文件，可以是一个设备文件、一般文件或者远程磁带驱动，如果指定了标准输出选项，那么这个选项就会被忽略
-I  从/var/lib/xfsdump/inventory列出目前你备份的信息状态``

使用xfsdump备份完整的文件系统：
1、确定备份的目标是完整的文件系统
df -h 备份目标
2、执行备份
xfsdump -l 0 -L boot_all -M boot_all -f /srv/boot.dump 备份目标

使用xfsdump进行增量备份：
1、查看有没有任何文件系统被xfsdump备份过
xfsdump -I
2、开始建立差异备份文件
xfsdump -l 1 -L boot_2 -M boot_2 -f /srv/boot.dump1 备份目标
3、查看备份是否成功
xfsdump -I
```

xfsrestore：xfs文件系统还原

```
# 查看备份文件
xfsrestore -I
# 单一文件全系统恢复
xfsrestore [-f 备份文件] [-L S_Label] [-s] 待恢复目录
# 通过增量备份文件来恢复系统
xfsrestore [-f 备份文件] -r 待恢复目录
# 进入交互模式
xfsrestore [-f 备份目录] -i 待恢复目录

-I  查询备份数据，包括Label名称与备份时间等
-f  指定一个用来恢复文件系统的源文件，可以是一个设备、一般文件或者远程磁带驱动的全路径名
-L  为当前备份恢复会话指定标签，可用-I命令查询
-s  指定一个需要恢复的子树，达到仅恢复一个文件或目录的目的
-r  如果用文件存储备份数据，则无需使用；如果是一个磁带内有多个文件，需要此选项来完成累积修复
-i  进入交互模式，一般不太使用

使用xfsrestore观察xfsdump后的备份数据内容：
xfsrestore -I

简单恢复LEVEL0文件系统：
xfsrestore -f /srv/boot.dump -L boot_all /boot
直接恢复的结果：同名文件被覆盖，其他新增文件会被保留

仅恢复备份文件内的grub2到/tmp/boo2中
xfsrestore -f /srv/boot.dump -L boot_all -s grub2 /tmp/boot2

恢复增量备份数据：
xfsrestore -f /srv/boot.dump1 /tmp/boot
注意，恢复增量备份数据时候要按照增量备份的顺序

仅还原部分文件的xfsrestore交互模式
xfsrestore -f /srv/boot.dump -i /tmp/boot3
使用help查看帮助信息
使用add、delete等交互命令选择文件
使用extract交互命令执行恢复操作
```

### 光盘写入工具

mkisofs：建立镜像文件命令

```
mkisofs [-o 输出的镜像文件名] [-Jrv] [-V vol] [-m file] 待备份文件
-o  指定输出的ISO9660镜像文件
-J  生成Joliet目录记录作为正规iso9660文件名的扩展，用于兼容Windows操作系统
-r  通过Rock Ridge产生支持UNIX/LINUX的文件数据，可记录较多的信息（如UID、GID等）；文件属主和属性被设置为更有用的值，uid和gid被设置为0，所有文件均设为可读，等等，不再赘述
-v  verbose，显示创建镜像文件的过程
-V  指定卷ID（卷名称或卷标签），有点类似于Windows等系统的分区卷标
-m  排除文件，支持通配符，匹配的文件不备份到镜像文件中
-graft-point  有转嫁或移植的意思，使用这个选项后，可用如下的方法定义镜像文件中的目录
                  - 镜像文件中目录等于实际linux文件系统的目录所在
                  - /moives = /srv/movies
                  - /linux/etc = /etc

使用mkisofs命令创建镜像文件：
mkisofs -rvo /tmp/system.img /root /home /etc -m /root/etc -graft-point /root=/root /home=/home /etc=/etc

制作修改可启动光盘镜像文件
1、观察光盘的内容
isoinfo -d -i /home/CentOs-7-x86_64-Minimal-1511.iso
2、挂载光盘到/mnt
mount /home/CentOs-7-x86_64-Minimal-1511.iso /mnt
mkdir /srv/newcd
rsync -a /mnt /srv/newcd
3、制作可启动光盘镜像文件
ll /srv/newcd/isolinux
# boot.cat  启动的安全编录文件
# isoLinux.bin  相当于启动引导程序
# vmlinuz linux内核文件
mkisofs -o /custom.iso -b isolinux/isoLiunux.bin -c isolinux/boot.cat -no-emul-boot -V 'CentOS 7 x86_64' -boot-load-size 4 -boot-info-table -R -J -v -T .
```

光盘刻录工具：wodim

```
查询刻录机的bus位置
wodim --devices dev=/dev/sr0...
抹除重复读写盘
wodim -v dev=/dev/sr0 blank=[fast|all]
格式化DVD+RW
wodim -v dev=/dev/sr0 -format

wodim -v dev=/dev/sr0 [options] file.iso

options:
-data
speed=X
-eject
fs=YM
driveropts=burnfree
-sao
```

### 其他压缩与备份工具

dd：转换和复制文件

```
dd if="input_file" of="output_file" bs="block_size" count="number"

if  输入文件或设备
of  输出文件或设备
bs  设置的一个block的大小，若未指定则默认是512字节
count 多少个bs的意思

cp与dd的区别：
cp是字节拷贝，dd是扇区拷贝
cp命令复制的是文件本身，而dd复制的是文件中的数据，如果是一般文件，差别并不明显，但如果是设备文件，cp只是在复制设备文件，而不是复制设备中的数据
cp命令是在文件系统之上进行的操作，dd命令则无视文件系统

使用dd命令复制文件系统时，会将文件系统的UUID也一并复制，这样会导致文件系统挂载时出现问题，需要先清除文件系统的log
```

cpio：从归档文件中复制或者复制到归档文件，类似于tar

```
常见用法：
1、备份：标准输入或管道输入的文件 => 归档文件
cpio -ovcB > [file|device]
2、还原：
cpio -ivcdu < [file|device]
3、查看
cpio -ivct < [file|device]

-o  将数据复制输出到文件或设备中
-B  让默认的blocks可以增加到5120字节，而不是512字节
-i  将数据从设备或文件复制出来到系统中
-d  自动建立目录，使用cpio命令所备份的内容不一定是在同一层目录，因此必须要让cpio在还原时可以建立新目录，此时就需要-d选项的帮助
-u  自动将较新的文件覆盖较旧的文件
-t  配合-i使用，查看以cpio建立的文件或设备的内容
-v  verbose
-c  以一种较新的portable format方式存储

```

## 九、vi编辑器

三种模式：命令模式、编辑模式、命令行模式

- 按i、I、o、O、a、A、r、R进入编辑模式

- 按 :、/、?进入命令行模式

- 按ESC退回命令模式

简单使用：vi+文件，按iIoOaArR进入编辑模式，ESC，:wq!退出并保存，:q!直接退出

一般命令模式操作：
1、移动光标

```
hjkl或←↓↑→      左下上右
数字+[hjkl←↓↑→] 左下上右
ctrl+fbdu       下一页上一页下半页上半页
+-              非空格符的上一行下一行
n space         光标后移n个字符
0$ HOME END     行首行尾
HML             屏首、屏中、屏尾
G nG gg         文件尾、第n行、文件首
n ENTER         光标下移n行
```

2、查找与替换

```
/word                     向下查询
?word                     向上查询
n                         下一条
N                         上一条
:n1,n2s/word1/word2/g     替换：g可以换成gc，用来让用户确认；n2可以换成$表示最后一行
```

3、删除复制与粘贴

```
xX nx nX                  删除（小写向后删除，大写向前删除，n是删几个字符）
dd ndd d行数 d0 d$        剪切（剪切本行，向下剪切n行，剪切直到某行（1G、nG、G），剪切直到行首、行尾）
yy nyy y行数 y0 y$        复制（描述同上）
pP                        粘贴（向下粘贴，向上粘贴）
J                         合并行
u ctrl+r                  撤销、重做     
```

4、进入编辑模式

```
i   光标所在处插入
I   光标所在行第一个非空格符插入
a   光标所在处下一个字符插入
A   行尾插入
o   下插一行
O   上插一行
r   替换一次
R   一直替换
ESC 退出编辑模式
```

命令行模式：

```
:w :q :w! :q! :wq :wq! ZZ   保存、退出、强制保存、强制退出、强制保存退出、必要时保存退出
:w filename                 保存为
:r filename                 打开文件
:n1,n2 w filename           截取并保存
:! command                  暂时退出vi执行linux命令
:set nu   :setnonu          显示行号，不显示行号
```

vim的缓存、恢复与打开时的警告信息：vim编辑过程中会创建.filename.swp文件，如果中途意外退出，再次打开时会提示警告信息，可按照提示进行恢复

可视区块：

```
v       光标经过的字符会被选中
V       光标经过的行会被选中
Ctrl+V  可以使用矩形选中
y       复制选中的区域
d       将选中的区域删除
p       将刚刚复制的区块粘贴
```

多文件编辑：

```
:n      编辑下一个文件
:N      编辑上一个文件
:files  列出目前vim开启的所有文件
```

多窗口功能（略）

vim关键词补全功能（略）

查看vim环境配置与记录：

```
./.vimrc    vim主动将用户所做的操作记录下来
./.viminfo  用于配置vim参数值
:set all    查看vim当前参数值
```

中文编码问题：

```sh
LANG=zh_CN.gb18030
export LC_ALL=zh_CN.gb18030
```

dos与linux换行符的转换：

```
dos2unix [-kn] file [newfile]
unix2dos [-kn] file [newfile]
-k  保留mtime
-n  保留旧文件
```

语系编码转换：

```
iconv --list
iconv -f 原来编码 -t 新编码 filename [-o newfile]
```

## 十、认识和学习Bash

### 认识BASH

硬件 <=> linux内核 <=> shell、KDE、API

常见的shell：

- sh：Bourne Shell

- csh：C shell

- ksh：K shell

- tcsh

- bash：Bourne Again Shell

可以使用的shell，记录在/etc/shells;/etc/passwd文件中会记录用户登录取得的shell

bash shell的功能：

- history命令：查看命令历史记录，记录在~/.bash_history中

- Tab命令与文件补全

- 命令别名：

```
alias lm='ls -al'
```

- 任务管理、前台、后台控制（job control、foreground、background）

- 程序化脚本（shell scripts）

- 通配符（Wildcard）

type：查询命令是否为bash的内置命令

```
type [-tpa] name

不添加任何参数时，显示name是内置命令还是外部命令
-t  以下面字眼显示含义
      file：外部命令
      alias：命令别名
      builtin：内置命令
-p  外部命令显示全路径名
-a  由PATH变量定义的路径中，将所有包含name的命令列出来，包括alias

eg：
type cd
结果为：cd is a shell builtin
type -t cd
结果为：
buildtin
```

命令的执行：Enter键让命令执行，使用\可以转义Enter，实现命令的折行

命令行快速编辑：

```
ctrl+u  从光标处向前删完
ctrl+k  从光标处向后删完
ctrl+a  光标移动到最前
ctrl+e  光标移动到最后
```

### SHELL变量

1、鸟哥定义变量：变量就是以一组文字或符号等，来替换一些配置或一串保留的数据

2、echo：输出变量和常量的值

```
-e  启用对反斜杠转义的解释
-E  禁用对反斜杠转义的解释（默认）
-n  不输出末尾的换行符
```

3、变量的语法：

- 使用=赋值，=前后不能有空格
- 变量名必须为英文和数字，并且不能以数字开头
- 使用${}引用变量的值，如果{}中只是单个变量名，则可以省略
- 双引号中可以引用变量值；单引号不可引用变量值，解释为纯文本；使用反斜杠转义特殊字符
- 倒引号或$()用来引用命令执行的返回值
- 使用export将变量输出为环境变量
- 系统默认变量一般为大写，用户定义变量尽量使用小写
- set查看变量值，使用unset取消变量

4、环境变量
env：观察环境变量的值（不带参数）；运行一个程序在指定的环境中

常见的环境变量（详情查看man bash）

- HOME：代表用户得到家目录
- SHELL：目前环境使用的SHELL
- HISTSIZE：history命令记录的条数
- MAIL：使用mail命令收信时，系统读取的邮箱文件
- PATH：执行文件查找的路径
- LANG：语系设置
- RANDOM：随机数，无符号2字节
- PS1：命令提示符
- $：本SHELL的PID，使用$引用这个变量的值
- ?：上个执行命令的返回值
- OSTYPE：操作系统类型
- HOSTTYPE：设备类型
- MACHTYPE：完整地描述设备类型

影响显示结果的语系变量：

- locale -a：查询linux支持的语系
- locale：查询语系变量
- LANG：主语言环境
- LC_CTYPE：字符（文字）辨识的编码
- LC_NUMBERIC：数字系统的显示信息
- LC_TIME：时间系统显示数据
- LC_COLLATE：字符的比较与排序
- LC_MONETARY：币值格式的显示
- LC_MESSAGES：信息显示的内容（如功能表、错误信息等）
- LC_ALL：整体语系的环境

一般情况下只需要修改LANG和LC_ALL即可

整体修改语系可以在/etc/locale.conf中进行配置

5、变量的作用域：环境变量可以看成是全局变量，自定义变量可以看成是局部变量，子进程会继承父进程的环境变量

6、从标准输入读取变量值：

```
read [-t sec] [-p message] variable
-p  后接提示信息
-t  设置等待输入的超时秒数
详情查看man bash
```

7、声明变量

```
declare [-aixr] variable
-a  将变量定义为数组类型
-i  将变量定义为整型
-x  将变量定义为环境变量
-r  将变量定义为readonly
-p  列出变量的类型和值
```

变量声明的注意事项：

- 变量默认类型是字符串

- 不支持浮点数的计算

- 将选项中的-变成+可以取消设置的类型

- 使用-p选项可以单独列出变量的类型

- 如果想要访问数组，不能使用$arr[0]，而要写成${arr[0]}

数组的赋值：

```
# 批量赋值
declare -a arr=(1 2 3)

# 单个赋值
arr[0]=1
arr[1]=2
arr[2]=3
```

8、与文件系统及程序的限制关系：ulimit

ulimit：控制shell和shell开启的进程可获得的系统资源。

```
ulimit 选项 配额
选项与参数：
-H  hard limit，严格的设置，必定不能超过设置的数值
-S  soft limit，警告的设置，可以超过设置的值，但是会有警告提示
-a  All current limits are reported.
-c  core file size，单位为block，内核文件大小的最大值。当程序发生错误时，系统可能会将该程序在内存中的信息写成文件，供排查错误使用，这种文件就被称为内核文件（core file）。
-d  data segment size，单位为KB，进程数据段的最大值。程序可能使用的最大段（segment）内存容量
-e  scheduling priority，无单位，进程调度优先级的最大值。系统中运行的每个进程都有一个调度的优先级（亦称为nice值），该选项可以限制进程的nice值，nice值越大越优先被调用。
-f  file size，单位为block，当前SHELL可以建立的最大文件容量（一般可能设置为2GB）
-i  pending signals，无单位，可以被挂起或阻塞的信号数量。
-l  max locked memory，单位为KB。可用于锁定（lock）的内存大小
-m  max memory size，单位为KB，指定可使用内存的上限。
-n  open files，无单位，指定同一时间可以打开的文件数量。
-p  pipe size，单位为512KB，指定管道缓冲区的大小。
-q  POSIX message queues，单位为B。
-r  real-time scheduling priority，实时调度优先级的最大值
-s  maximum stack size，最大堆栈大小
-t  cpu time，单位为秒。可使用的最大CPU的时间。
-u  max user processes，无单位，每个用户同时可以使用的最大进程数
-v  virtual memory，单位为KB，shell可使用的最大虚拟内存。
-x  file locks，无单位。
```

9、变量内容的删除、取代与替换

```
${变量#关键字}                若变量内容前缀符合【关键字】，则将最短匹配前缀删除（关键字可包含*，表示任意字符任意次数的出现）
${变量##关键字}               若变量内容前缀符合【关键字】，则将最长匹配前缀删除
${变量%关键字}                若变量内容后缀符合【关键字】，则将最短匹配后缀删除
${变量%%关键字}               若变量内容后缀符合【关键字】，则将最长匹配后缀删除
${变量/旧字符串/新字符串}      字符串替换，仅替换旧字符串的一次出现
${变量//旧字符串/新字符串}     字符串替换，替换旧字符串的所有出现
```

```
${str-expr}                  不存在时替换
${str:-expr}                 不存在或为空串时替换
${str+expr}                  存在时替换
${str:+expr}                 存在且不为空串时替换
${str=expr}                  不存在时替换并赋值给str
${str:=expr}                 不存在或为空串时替换并赋值给str
${str?expr}                  不替换，不存在时输出expr到stderr
${str:?expr}                 不替换，不存在或为空时输出expr到stderr
```

### 命令别名和历史命令

1、命令别名

```
设置命令别名：
alias 别名='命令'

example：
alias lm='ls -al | more'

查看有哪些命令别名：
alias

取消命令别名
unalias 别名

```

2、历史命令

```
history [n]
history [-c]
history [-raw] histfiles

n   n是数字，表示列出最近的n条命令
-c  清空history
-a  将目前新增的命令附加在histfiles文件中
-r  将目前的histfiles文件中的记录读入history记录中
-w  将目前的history记录内容写入histfiles文件中
```

历史命令的读取：

- 使用bash登录linux主机，从`~/.bash_history`中读取执行过的命令，该文件记录历史命令的上限由HISTFILESIZE变量指定

- 注销时，系统将最近的HISTFILESIZE条命令更新到`~/.bash_history`文件中

- 使用`history -w`命令可以强制更新`~/.bash_history`文件

历史命令的执行：

- 使用上下键切换至历史命令

- 【!!】：执行上一条命令

- 【!n】：执行history中第n条命令

- 【!command】：执行history中上一条开头是command的命令

同一账号登录多个bash，最后注销的bash的history会覆盖之前注销的bash的history

### BASH SHELL操作环境

1、命令查找顺序：

- 以相对路径或绝对路径执行命令

- 由alias找到该命令执行

- 由bash内置的命令执行

- 通过PATH环境变量顺序查找第一个命令来执行

2、bash的登录与欢迎信息：/etc/issue, /etc/motd

```
cat /etc/issue

\S
Kernel \r on \m

issue各代码的意义：
\d  本地端时间的日期
\l  显示第几个终端界面
\m  显示硬件的等级（i386/i486/i586/i686...）
\n  显示主机的网络名称
\O  显示Domain name
\r  操作系统的版本（相当于uname -r）
\t  显示本地端时间的时间
\S  显示操作系统的名称
\v  操作系统的版本
```

如果使用telnet远程登录，则修改/etc/issue.net文件修改登录与欢迎信息

修改/etc/motd文件，可以在用户登录时，打印一些消息

3、bash的环境配置文件

login shell与non-login shell：login shell需要正常输入用户名和密码，non-login shell诸如x-window，bash命令等开启的shell不需要输入用户名和密码。login shell与non-login shell读取的配置文件是不同的。

login shell读取的两个配置文件：

- /etc/profile：系统整体的配置，一般情况下不要修改。每个用户登录取得bash时一定会执行的脚本，一般用来设置所有用户都使用的环境变量，如PATH、MAIL、USER、HOSTNAME、HISTSIZE、umask等

  - /etc/profile.d/*.sh：只要是在/etc/profile.d目录下，扩展名为sh并且用户具有r权限，则该脚本就能被/etc/profile调用

  - /etc/locale.cnf：由/etc/profile.d/lang.sh调用，决定了bash默认使用何种语系，该脚本中设置了LANG/LC_ALL等环境变量

  - /usr/share/bash-completion/completions/*：使用TAB键能够实现命令自动补全，就是从这个目录中找到相应的命令来处理，这个目录下的内容是通过/etc/profile.d/bash_completion.sh这个文件加载的

- ~/.bash_profile或~/.bash_login或~/.profile：属于用户个人设置

  - ~/.bash_profile（login shell才会执行）：login shell登录时会执行的环境配置脚本，如果~/.bash_profile不存在会读取~/.bash_login，如果~/.bash_login不存在会读取~/.profile文件

non-login shell读取的配置文件：

- ~/.bashrc（non-login shell会读）：non-login shell登录时会执行的环境配置脚本。如果误删除了，可以通过复制/etc/skel/.bashrc文件到家目录并进行修改

- /etc/bashrc：在CentOS 7.x中还会主动去调/etc/bashrc这个文件，这个文件时Red Hat中特有的。该文件帮助bash定义以下内容：

  - 根据不同的UID设置umask值

  - 根据不同的UID设置命令提示符（PS1）：

  - 调用/etc/profile.d/*.sh的设置

source：执行环境配置脚本命令，也可以使用小数点

```
source ~/.bashrc
. ~/.bashrc
```

其他比较重要的配置文件：

- /etc/man_db.conf：man命令寻找man page的路径，如果使用tarball方式安装软件，一般man page文件放在/usr/local/softpackage/man中，此时需要手动将其添加进/etc/man_db.conf文件

- ~/.bash_history：历史命令记录文件，记录上限为HISTFILESIZE

- ~/.bash_logout：注销bash后系统帮我执行的操作

4、终端环境设置：

stty：setting tty，设置终端的输入按键的含义

```
stty [-a]
stty 操作 按键

-a  将目前所有的stty参数列出来
```

set：设置一些终端配置

```
set [-uvCHhmBx]

-u  启用【未设置变量时，会显示错误信息】，默认不启用
-v  启用【信息被输出前，会先显示信息的原始内容】，默认不启用
-x  启用【命令被执行前，会显示命令内容】，默认不启用
-h  与历史命令有关，默认启用
-H  与历史命令有关，默认启用
-m  与任务管理有关，默认启用
-B  与中括号[]的作用有关，默认启用
-C  启用【如果使用 > 重定向输出，该文件不会被覆盖】，默认不启用

显示目前所有的set值：
set $-

取消某个配置：将选项中的 - 改成 +
```

其他按键设置功能：/etc/inputrc、/etc/DIR_COLORS*、/usr/share/terminfo/*等

bash默认的组合键：

- ctrl+c：终止目前的命令

- ctrl+d：输入结束（EOF），例如邮件结束的时候

- ctrl+m：回车

- ctrl+s：暂停屏幕的输出

- ctrl+q：恢复屏幕的输出

- ctrl+u：在提示命令之下，将整列命令删除

- ctrl+z：暂停目前的命令执行

5、通配符与特殊符号

通配符：

|符号|含义|
|--|--|
|*|代表任意字符的0或任意次出现|
|?|代表任意字符的1次出现|
|[abc]|代表abc中任意一个字符的一次出现|
|[a-z]|代表a至z区间中任意字符的一次出现|
|[^a-cz]|代表除a至c区间以及z外任意字符的一次出现|

特殊符号：

|符号|含义|
|--|--|
|#|注释|
|\\|转义|
|\||管道|
|;|语句|
|~|家目录|
|$|引用变量|
|&|后台执行|
|!|逻辑非|
|/|路径分割|
|> >>|输出重定向【替换】【附加】|
|< <<|输入重定向|
|''|纯文本（不可引用变量）|
|""|文本（可引用变量）|
|``|执行命令，引用返回值，可用$()替换|
|()|子shell的起始与结束|
|{}|命令区块|

### 数据流重定向

命令的执行流程：

```
1、从文件或标准输入中读取输入数据，默认为标准输入STDIN： < <<
2、执行命令
3.1、命令正常执行，输出结果至屏幕、文件或设备，默认为标准输出STDOUT：> >>
3.2、命令执行出错，输出错误信息至屏幕、文件或设备，默认为标准错误输出STDERR：2> 2>>
```

标准输入输出：

- STDIN：标准输入，代码为0，使用 < 或者 <<

- STDOUT：标准输出，代码为1，使用 > 或者 >> （1> 或者 1>>）

- STDERR：标准错误输出，代码为2，使用 2> 或者 2>>

对于输出，>表示覆盖写入，>>表示附加写入；对于输入，<表示从文件或设备读取输入，<<表示输入指定字符就结束输入

```sh
cat << "eof"  # 表示键入eof后立即结束输入，其中eof不包括在输入的字符中
```

垃圾桶黑洞设备：/dev/null，用于丢弃输出的数据

如果要将执行命令的正常输出与错误输出重定向至同一个文件：

```sh
find /home -name .bashrc > list 2> list # 错误写法
find /home -name .bashrc > list 2>&1  # 正确写法，将错误输出的结果转到输出
find /home -name .bashrc &> list # 正确写法
```

命令执行的判断依据：

- `;`：分隔不同语句

- `&&`：左边命令正常退出才执行右边命令

- `||`：左边命令异常退出才执行右边命令

- 命令的返回值：命令正常退出，$?赋值为0；命令异常退出，返回$?赋值为非0值，`&&`和`||`依据此决定是否执行后面的命令

- 区别于逻辑与、逻辑或，命令之间的`;`、`&&`和`||`优先级相同，从左到右进行判断

### 管道命令

管道：

- 仅会处理标准输出，忽略标准错误

- 能够接受前一个命令的数据作为标准输入

```sh
ls -al /etc | less
```
1、选取命令

cut：remove sections from each line of files，针对文件每一行进行切割

```
-b  选取指定字节的字符
-n  和-b联用，不拆分多字节字符
-d  指定分割字符，默认为TAB
-f  选取分割后的字段
-c  选取指定位置的字符
-s  过滤掉不含分隔符的行


-f和-c选项支持多种选取方式：
3       选取第3列（字符、字节）
3,5     选取第3和第5列（字符、字节）
3-5     选取第3至第5列（字符、字节）
3-5,7   选取第3至5列（字符、字节）以及第7列（字符、字节）
3-      选取第3列（字符、字节）和之后的列（字符、字节）
-3      选取第3列（字符、字节）和之前的列（字符、字节）
```

grep：search the named input FILEs (or standard input if no files are named, or if a single hyphen-minus is given as file name) for lines containing a match to the given PATTERN. By default, grep prints the matching lines. 在指定的输入文件（如果没有指定输入文件或者指定文件名为【-】则从标准输入）中搜索包含匹配指定模式的行。

```
grep [OPTIONS] PATTERN [FILE ...]
grep [OPTIONS] [-e PATTERN | -f FILE] [FILE ...]

Matcher Selection：
-E  将模式（PATTERN）解释为一个扩展正则表达式（ERE，extended regular expression）
-F  将模式解释为一个被换行符分割的固定字符串的列表，其中每一个字符串都被匹配（该选项已废弃）
-G  将模式解释为一个基本正则表达式（BRE，basic regular expression）
-P  将模式解释为一个perl正则表达式（实验特性）

Matching Control：
-e PATTERN  使用指定的PATTERN作为模式
-f FILE     从文件中获得模式，每行一个，用于指定多个模式时使用
-i          忽略大小写的不同
-v          反向选择，查找不匹配的行

General Output Control：
-c  目标字符串（模式）的命中次数
-m  查找到指定个结果后停止查询
-n  顺便输出行号

Output Line Prefix Control：
-b  打印每一个匹配行在文件中的字节偏移量
-H  打印每一个匹配行所在文件的名称（如果有多个输入文件则是默认值）
-h  与-H相反

File and Directory Selection：
-a  将二进制文件当做文本文件进行搜索

Context Line Control：
-A n  输出匹配行的上下文（仅下文n行）
-B n  输出匹配行的上下文（仅上文n行）
-C n  输出匹配行上下文（上下各n行）
```

2、排序命令

sort：sort lines of text files

```
sort [OPTIONS]... [FILE]...
-b  忽略前导空白
-d  字典顺序，只考虑空白和字母数字字符（alphanumeric characters）
-f  忽略大小写
-M  月份排序（JAN、FEB、……）
-h  人类易读数值排序（1K，2G，……）
-n  数值大小排序
-R  随机排序
-r  反向排序
-c  检查是否排序的，但本身并不排序
-u  与-c同时使用时，检查是否严格有序；单独使用时，去重
-t  指定字段分割符，默认是用TAB
-k  以哪个区间进行排序的意思，后接指示排序区间的KEYDEF

KEYDEF := F[.C][OPTS][,F[.C][OPTS]]
F表示field number，字段的序数（从1开始）
C表示字段内字符的位置（从1开始），如果没有指定-t和-b会从前导的空白数起
OPTS就是指示排序方式的选项[bdfgiMhnRrV]，会覆盖全局的排序选项
```

last：展示登录列表

uniq：报告或忽略重复的行

```
-c      统计每一行出现的次数并显示在第一列
-i      忽略大小写
-d      duplicate，只输出重复的行
-f n    跳过每一行前n个字符
-u      只输出不重复的行
```

wc：打印每一个文件的行数、单词数和字节数

```
wc [OPTIONS]... FILE...

不带任何选项时，打印如下内容：
行数  单词数  字节数  文件名

-l  仅列出行数
-w  仅列出单词数
-m  仅列出字符数
-c  仅列出字节数
```

3、双向重定向

tee：从标准输入读取并写入标准输出和文件

```
tee [OPTION]... [FILE]...
-a  附加写入给定的文件，不覆盖
-i  忽略中断信号
```

4、字符转换命令

tr：替换或者删除字符，替换是指从字符集SET1映射为字符集SET2

```
tr [OPTION]... SET1 [SET2]
-d  直接删除SET1中的字符，不进行替换
-s  squeeze repeats，把SET1中列举的字符如果连续出现只保留一个（如果有替换，针对替换后的部分进行squeeze repeats）

SET1与SET2是以下表达式的组合：
CHAR          单个字符（包括反斜杠转义字符）
CHAR1-CHAR2   指定字符区间
[CHAR*]       SET2中使用，CHAR的REPEAT次出现，REPEAT为SET1的长度
[CHAR*REPEAT] CHAR的REPEAT次出现，REPEAT如果以0开头则认为是八进制
[:XXX:]       根据XXX的内容确定语义，XXX可以是alnum、alpha、blank、digit等
[=CHAR=]      所有等于CHAR的字符

eg：
大小写转换：
tr a-z A-Z
tr A-Z a-z

凯撒密码：
tr a-zA-Z x-za-wX-ZA-W
```

col：从输入中过滤逆向换行符（RLF）

```
-x  将TAB换成对等的空格键
-h  将空格键换成对等的TAB
-b  过滤BS
-f  过滤逆向换行符，但允许将半逆向换行符呈现出来（man col原文：Forward half line feeds are permitted fine mode. Normally characters printed on a half-line boundary are printed on the following line.）
-l number 至少number个字节才缓存
```

col能够理解的控制字符：

- 逆向换行符（reverse line feed）：ESC-7，使光标回到上一行的开头

- 半逆向换行符（half-reverse line feed）：ESC-8，使光标回到上一行但并不回到开头

- 换行符（forward line feed, newline）：使光标移动到下一行的开头

- 半前向换行符（half-forward line feed）：ESC-9，使光标移动到下一行但并不回到开头

- 退格（backspace）

- 回车（carriage return）

- shift in：shift to normal character set

- shift out：shift to alternative character set

- tab

- vertical tab

join：通过某个字段关联两个文件（类比数据库的join操作）

```
join [OPTION]... FILE1 FILE2

-1 FIELD    第一个文件使用哪个字段
-2 FIELD    第二个文件使用哪个字段
-i          忽略大小写差异
-t CHAR     使用CHAR作为输入和输出字段分隔符
-a FILENUM  类比MYSQL的左连接和右连接，把没有join上的行也输出
-e EMPTY    如果输入文件中找不到指定的列，则使用EMPTY替换
-j FIELD    相当于 -1 FIELD -2 FIELD
-o FORMAT   构建输出行时遵循FORMAT规则
-v FILENUM  类似于-a，但是排除已经连接上的行

FOMRAT：一个或更多逗号或者空白分割的规则，每一个规则为【FILENUM.FIELD】或者【0】。默认情况下的规则为【关联字段, 文件1剩余字段, 文件2剩余字段】.如果FORMAT被指定为【auto】，那么每一个文件的第一行决定了被输出的每一行字段的序号

【注意】
文件1和文件2中的数据记录必须按照关联字段排序，比较操作遵循LC_COLLATE指定的规则。如果输入文件没有按照关联字段排序，会有warning信息

使用-a左连接、右连接或全连接时，配合-t -e参数使用，可以保证连接后结果不会错行
```

paste：将多个文件的相同行号的行直接合并

```
paste [OPTION]... [FILE]...

如果FILE写成-，表示从标准输入读取
-d  分割字符，默认为TAB
```

expand：将tab转换为空格

```
expand [OPTION]... [FILE]...
-t  设定制表符宽度，默认为8
```

5、划分命令

split：将一个文件划分为多个小文件

```
split [-bl] file PREFIX
-b  想要划分成的文件大小，可加数量级单位k、m、g
-l  以行数进行划分

eg:
ls -al / | split -l 10 - lsroot # 一般情况下，需要文件的地方用-可以指代标准输入和输出
```

6、参数代换

xargs：参数代换，产生某个命令的参数。xargs可以读入stdin的数据，并且以空格符或者换行符作为识别符，将stdin的数据分割成为参数。

```
xargs [-0epn] command

-0  能够将输入项中的特殊字符（引号、反斜杠、EOF等）还原为一般字符进行处理
-e  设置EOF字符，当xargs遇到这个字符时就会停止接收输入
-p  执行每个命令时都进行询问
-n  每次command命令执行时要使用几个参数

example：
ps -ef|grep java|grep -v grep|tr -s ' '|cut -d ' ' -f 2|xargs kill -9

注意，选项中的参数不能通过这种方式处理
```

## 十一、正则表达式与文件格式化处理

正则表达式的分类：

- BRE：basic regular expression，基础正则表达式

- ERE：extended regular expression，扩展正则表达式

### 正则表达式



1、正则表达式

正则表达式：描述字符串集合的模式，类似于算术表达式的构建，使用不同的操作符组合更小的表达式

grep命令理解的正则表达式：基础正则表达式（BRE，basic regular expression）、扩展正则表达式（ERE，extended regular expression）、PCRE（perl compatible regular expression）

正则表达式（regular expression）与通配符（wildcard）是区别的两个概念，不应混淆

语系会对正则表达式有影响，LANG=C与LANG=zh_CN针对字母数字字符（alphanumeric characters）的编码顺序不一样，要特别注意。

字符类（Character Classes）：

|语义符号|代表含义|
|--|--|
|[:alnum:]|字母数字字符|
|[:alpha:]|字母|
|[:blank:]|空格、TAB|
|[:cntrl:]|控制字符，包括但不限于CR、LF、Tab、Del|
|[:digit:]|数字|
|[:graph:]|除空格与Tab外其他按键|
|[:lower:]|代表小写字符|
|[:print:]|代表任何可以被打印出来的字符|
|[:punct:]|代表标点符号|
|[:upper:]|代表大写字符|
|[:space:]|代表空白字符（空格、TAB、CR）|
|[:xdigit:]|代表十六进制的数字类型|

dmesg：列出内核信息

基础正则表达式：

|正则表达式|含义|
|--|--|
|^word|以word开头|
|word$|以word结束|
|.|匹配任意字符|
|\|转义字符|
|c*|c的0至无穷次出现|
|[abc]|abc中任意字符|
|[a-z]|a至z区间中任意字符|
|[^abc]|除abc外任意字符|
|{n,m}|n和m都是数字，表示前面字符的n至m次出现。n和m其中之一可以缺省，表示无穷区间。{n,n}可以简化为{n}|

扩展正则表达式：

|正则表达式|含义|
|--|--|
|c+|c的1至无穷多次出现|
|c?|c的0至1次出现|
|exp1\|exp2|exp1或exp2|
|()|括号内的字符当成一个整体参与运算|

3、sed工具

sed：stream editor for filtering and transforming text. 

sed：用来过滤和转换文本的流编辑程序。

Sed is a stream editor. A stream editor is used to perform basic text transformations on an input stream (a file or input from a pipeline). While in some ways similar to an editor which permits scripted edits (such as ed), sed works by making only one pass over the input(s), and is consequently more efficient. But it is sed's ability to filer text in a pipeline which is particularly distinguishes it from other types of editors.

Sed是一个流编辑程序。流编辑程序是用来在一个输入流（文件或来自管道的输入）上处理基本文本转换的。尽管在很多方面类似于支持脚本化编辑的编辑程序（例如ed），但是sed运转时针对所有输入仅遍历一次因而显得更加高效。但在一个流水线中处理文本是sed命令的本领，这显著区别于其他类型的文本编辑程序。

```
sed [OPTION]... {script-only-if-no-other-script} [input-file]...

1 选项：
-n                压制模式空间（pattern space）的所有自动输出
-e script         向命令添加执行的脚本
-f script-file    向命令添加执行的脚本文件
-i[SUFFIX]        在恰当的地方编辑文件（如果提供了后缀再进行备份，否则写回原位置）
-c
-l N              为l命令指定想要的折行长度
-r                在脚本中使用扩展正则表达式（ERE）
-s                将文件当做相互独立的流而不是一个单独、连续、长的流
-u                从输入文件中加载最小量的数据并且更频繁地刷新缓存
-z                以NULL字符分离不同行

2 命令：

1）零地址命令：
:label            为b和t命令设置标签
#comment          行注释
}                 块结束符号

2）零地址或一地址命令：
=                 打印当前行号
a \
text              附加文本text，文本中每一个内嵌的换行符前都要加一个反斜杠
i \
text              插入文本text，文本中每一个内嵌的换行符前都要加一个反斜杠
q [exit-code]     立即退出sed脚本，不再处理更多的输入（在自动打印没被禁用时当前的模式空间将被打印除外），退出码是一个GNU扩展。
Q [exit-code]     立即退出sed脚本。这是一个GNU扩展
r filename        附加从filename文件中读取的文本
R filename        附加写入一行从filename文件中读取的文本。每次调用都会从文件中读取一行。这是一个GNU扩展

3）接受地址区间的命令
{                 块开始符号
b label           跳转至label所在位置，如果label被省略则跳转至脚本结束
c \
text              使用文本text替换选中的行，文本中每一个内嵌的换行符前都要加一个反斜杠
d                 删除模式空间，开启新的循环
D                 如果模式空间没有换行符，开启一个正常的循环就像发出一个d命令一样；否则，删除模式空间内从开始到第一个换行符的文本，并使用处理后的模式空间重新开始本次循环，而不是读取输入文件新的一行
h H               复制/附加模式空间到保持空间
g G               复制/附加保持空间到模式空间
l                 使用【visually unambiguous】格式列出当前行
l width           同l，但是在第width个字符出中止
n N               读取/附加下一行到模式空间
p                 打印当前的模式空间
P                 打印当前模式空间直到第一个嵌入的换行符
s/regexp/replacement/
                  使用模式空间尝试匹配正则表达式。如果成功。使用replacement替换匹配的部分。replacement中可以使用&引用被匹配的部分，使用\1至\9引用正则表达式中的捕获组。
t label           自从上一次读取输入行和执行命令t或T以来，如果s///命令成功替换，那么跳转至标签label；如果标签被省略，则跳转至结束
T label           自从上一次读取输入行和执行命令t或T以来，如果s///命令未能替换，那么跳转至标签label；如果标签被省略，则跳转至脚本结束。这是一个GNU扩展。
w filename        将当前的模式空间写入文件
W filename        将当前的模式空间的第一行写入文件。这是一个GNU扩展
x                 交换模式空间与保持空间的内容
y/source/dest/    按照source到dest的映射规则直译模式空间的字符

3 地址：

- 零地址：针对所有输入行执行命令
- 一地址：匹配该地址的行执行命令
- 区间地址：匹配该地址区间的行执行命令

注意：
- 语法：addr1,addr2
- addr1如果匹配了就立即接受，即使addr2比addr1地址小
- addr1如果匹配了就立即接受，即使addr2是正则也不再匹配

地址之后命令之前可以插入一个!，表示对地址的反向选择

支持的地址类型：

number        指定行号为number的一行
first~step    指定行号=first+n*step的行，n为自然数
$             最后一行
/regexp/      匹配正则表达式的行
\cregexpc     匹配正则表达式的行，c可以是任意字符

GNU支持的特殊2地址格式：
0,addr2       类似于1,addr2，但是1,addr2必然匹配第一行，而0,addr2不一定匹配第一行
addr1,+N      匹配addr1和之后的N行
addr1，~N     从addr1开始匹配直到行号是N的倍数



如果没有指定-e、--expression、-f或--file选项，第一个非选项的参数被当做sed脚本去解释。留下的非选项参数是输入文件名；如果没有输入文件，将从标准输入中读取。



模式空间（pattern space）：模式空间用于sed执行的正常流程中。该空间是sed内置的一个缓冲区，用来存放、修改从输入文件读取的内容。模式空间的数据在执行完sed命令之后就会被删除，然后再从保持空间取得下一条数据。

保持空间（hold space）：保持空间是sed另一个缓冲区，用来存放临时数据。sed可以在保持空间和模式空间交换数据，但是不能在保持空间上执行普通的sed命令。保持空间的数据不变。
```

### 文件格式化与相关处理

1、格式化打印 printf

```
printf FORMAT [ARGUMENT]...
printf OPTION\
基本用法与C语言printf函数类似
```

```
C语言printf介绍：

转义字符
\"  \\  \a  \b  \c  \e  \f  \n  \r  \t  \v  \NNN  \xHH  \uHHHH  \UHHHHHHHH  %%

（1）格式控制符（flag characters，%后接以下字符）
#     格式转换：对于o转换符，添加前缀0；对于x和X转换符，添加前缀0x；对于a、A、e、E、f、F、g和G转换符，结果中将始终保留小数点，而不会省略；对于g和G转换符，不再移除末尾的0
0     值应当使用0填充，对于diouxXaAeEfFgG转换符，左填充0而不是空格。0和-转换符都出现时，0被忽略；数值转换符被指定精确度时，0被忽略
-     左对齐，右侧补空格
空格  数值前没有数符时显示空格
+     强制显示数符（包括正数，0显示为+0）  

SUSv2扩展格式控制符
'     千分位显示

（2）域宽（field width）：可选的十进制数字字符串（第一个数字不是0）指定最小域宽。如果转换后的值包含比域宽更少的字符，则在左侧或右侧填补空格（左侧还可以是0，如果指定了0填充）；如果转换后的值包含比域宽更多的字符，也始终不会截断。可以使用*替换域宽数字，表示从下一个参数中获得域宽；可以使用*m$替换域宽数字，表示从第m个参数中获得域宽。

（3）精确度（precision）：可选的精确度，以句点（period，【.】）的形式表示，紧跟在后的是一个可选的十进制数字字符串。精确度同样可以使用*或者*m$替代，含义与上面是类似的。如果精确度仅仅以一个句点表示或者为一个负数，则精确度置为0。对于整型（diouxX），精确度规定了数位个数的最小值；对于基数表示法（aAeEfF），精确度规定了基点字符（radix character）之后的数位个数；对于g和G转换符，规定了有效数字（significant digit）的最大位数；对于s和S转换符，精确度规定了字符串截取的位数

（4）长度修饰符（length modifier）
hh  后接整型转换符表示char或unsigned char；后接n转换符，表示char *
h   后接整型转换符表示short或unsigned short；后接n转换符表示short *
l   后接整型转换符表示long或者unsigned long；后接n转换符表示long *；后接c转换符表示wint_t；后接s转换符表示wchar_t
ll  后接整型转换符表示long long int或者unsigned long long int；后接n转换符表示long long int *
L   后接浮点转换符（aAeEfFgG）表示long double
j   后接整型转换符表示intmax_t或者uintmax_t
z   后接整型转换符表示size_t或ssize_t
t   后接整型转换符表示ptrdiff_t

（5）转换符（conversion specifier）
d,i           十进制整数
o,u,x,X       八进制、无符号十进制、十六进制、大写字母十六进制整数
e,E           double型浮点数
f,F           float型浮点数
g,G           double型浮点数，根据情况展示小数或指数
a,A           十六进制double型浮点数
c             字符型
l             字符指针型
p             void指针型
n             指针型
%             %本身
```

2、awk工具的简单使用

```
awk [OPTIONS] -f program-file [--] file ...
awk [OPTIONS] [--] 'program' file ...

-F fs           设置FS变量，该变量用来指定列分割符
-f source-file  读取awk程序文件，而不是把第一个非选项参数作为awk程序
-v var=val      设置变量var的值为val，这样的变量可以在BEGIN规则中访问；一个-v设置一个变量，设置多个变量使用多个-v
```
awk程序的基本结构：

```
pattern1 { action1 }
pattern2 { action2 }
...
```
awk的简单使用：

```awk
# hello world程序
BEGIN { print "hello world" }

# 打印第一列
{ print $1 }

# 查找匹配正则表达式的行，并将其打印出来
/li/ {print $0}

# pattern和action可以省略，但不能同时省略
# 如果pattern被省略了，则匹配所有行
# 如果action被省略了，则默认打印匹配的行

# print every line that is longer then 80 characters
awk 'length($0) > 80' data

# print the length of the longest input line
awk '{ if (length($0) > max) max = length($0)} END {print max}' data

# print the length of the longest line in data
expand data | awk '{ if (x < length($0)) x = length($0)} END {print "maximum line length is " x}'

# print every line that has at least one field
awk 'NF > 0' data

# print seven random numbers from 0 to 100, inclusive
awk 'BEGIN { for (i = 1; i <= 7; i++) print int(101 * rand())}' data

# print total number of bytes used by files
ls -l files|awk '{ x += $5 } END { print "total bytes: " x }'

# print total number of kilobytes used by files
ls -l files|awk '{ x += $5 } END { print "total kilobytes：" x / 1024}'

# print a sorted list of the login names of all users:
awk -F: '{ print $1 }' /etc/passwd | sort

# count the lines in a file
awk 'END { print NR }' data

# print the even-numbered lines in the data file
awk 'NR % 2 == 0' data

```

常见的内置变量：

```
$0    当前行
$n    当前行第n个字段，n>0
NF    当前行字段总数
NR    当前行行号（从1开始）
FS    当前的字段分隔符
```

常见的逻辑运算符：

```
> < >= <= == !=
```

格式化输出：
```
printf FORMAT,ARGV...
```


3、diff：compare files line by line

```
diff [OPTION]... FILES
-b  忽略空白带来的差异
-B  忽略空白行的差异
-i  忽略大小写的不同
```

4、cmp：compare two files byte by byte

```
-l  将不同点的字节处都列举出来
```

5、patch：与diff命令密切相关，用于将旧的文件升级为新的文件

6、pr：文件打印



## 十二、SHELL脚本

```sh
#! /bin/bash
# Program:
#   This program shows hello world in your screen
# History:
# 2020/1/1  VBird   First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "hello world! \a \n"
exit 0
```

- 第一行是shebang行，确定脚本使用什么shell执行

- 第二部分是程序内容的说明

- 主要环境变量的声明

- 主要程序部分

- 定义返回值

1、简单的shell脚本

交互式脚本，变量内容由用户决定

```sh
read -p "Please input your first name: " firstname  # 提示用户输入名
read -p "Please input your last name: " lastname    # 提示用户输入姓
echo -e "\nYour full name is $firstname $lastname"  # 结果由屏幕输出
```

建立随日期变化的文件：

```sh
read -p "Please input a filename: " filename

date1=$(date -d '2 days ago' +%Y%m%d) # 前天的日期
date2=$(date -d '1 days ago' +%Y%m%d) # 昨天的日期
date3=$(date +%Y%m%d) # 今天的日期

# 创建文件
touch $filename$date1 $filename$date2 $filename$date3
```

数值运算，简单的加减乘除

```sh
# 基本运算
echo -e "Please input two numbers: "
read -p "1) " first
read -p "2) " second
echo "first * second = $((first * second))"

# 直接输入一个表达式求值（不支持浮点数）
read -p "Please input an expression: " expression
echo $(($expression))

# 支持浮点数的计算
read -p "Please input an expression: " expression
echo $expression|bc

# 计算圆周率
read -p "Please input the scale: " scale
echo "scale=$scale;4*a(1)" | bc -lq # 这里的a其实是arctan，4arctan(1)=π
```

2、脚本执行方式的差异：

- 直接执行的方式来执行脚本：在子进程中执行脚本

```sh
sh demo.sh
bash demo.sh
csh demo.sh
tcsh demo.sh
```

- 使用source或小数点执行脚本：在父进程中执行脚本

```sh
source demo.sh
. demo.sh
```

3、逻辑判断：

test命令的测试功能：check file types and compare values

```
test EXPRESSION
test
[ EXPRESSION ]

如果使用[]，注意[]内前后各要一个空格。其实把[当成一个命令，把]当成一个参数就好理解了

如果没有EXPRESSION，默认值是false；如果有，则EXPRESSION的值为true或false并据此设置退出状态
```

|运算符|含义|
|--|--|
|1、文件类型类||
|-e|文件是否存在（exists）|
|-f|是否存在且为文件（regular file）|
|-d|是否存在且为目录（directory）|
|-b|是否存在且为块设备文件（block special file）|
|-c|是否存在且为字符设备文件（character special file）|
|-S|是否存在且为Socket文件（socket file）|
|-p|是否存在且为一个FIFO管道文件（pipe file）|
|-L|是否存在且为一个链接文件（link file）|
|2、文件权限类||
|-r|文件是否存在且具有【读】权限（read）|
|-w|文件是否存在且具有【写】权限（write）|
|-x|文件是否存在且具有【运行】权限（execute）|
|-u|文件是否存在且具有【SUID】属性（suid）|
|-g|文件是否存在且具有【GUID】属性（guid）|
|-k|文件是否存在且具有【SBIT】属性（sticky bit）|
|-s|文件是否存在且为非空文件（size）|
|3、文件比较类||
|-nt|文件mtime更新（newer than）|
|-ot|文件mtime更新（older than）|
|-ef|比较文件inode是否相同（equal file）|
|4、整数比较类||
|-eq|两数值相等（equal）|
|-ne|两数值不等（not equal）|
|-gt|n1大于n2（greater than）|
|-lt|n1小于n2（less than）|
|-ge|n1大于等于n2（greater than or equal）|
|-le|n1小于等于n2（less than or equal）|
|5、字符串比较类||
|-z|判断字符串是否为空或长度为0（zero）|
|-n|判断字符串是否不为空且长度大于0（not zero）|
|==|判断字符串是否相等|
|!=|判断字符串是否不相等|
|6、逻辑运算类||
|-a|与（and）|
|-o|或（or）|
|!|非（not）|

4、shell脚本的默认变量

```
$0  对应一条命令的command部分
$n  对应一条命令的非选项参数（nonoption parameters），n为正整数
$#  非选项参数列表（如果加双引号，表示【"$1""$2""$3"...】）
$@  非选项参数列表（如果加双引号，表示【"$1c$2c$3c$4"】,其中c是分隔符，默认为空格）
```

演示$和@$*的差异：
```sh
#! /bin/bash
# $@的演示
for i in "$@"; do
  echo $i
done
# $*的演示
for i in "$*"; do
  echo $i
done

# 执行shell脚本，传递参数 1 2 3
# 对于$@得到结果为
# 1
# 2
# 3
# 对于$*得到结果为
# 1 2 3
```

shift：造成命令行参数的偏移

```sh
#! /bin/bash
while [ $# -gt 0 ]; do
  echo $1
  shift
done

# 执行shell脚本传递参数1 2 3
# 得到结果如下
```

5、分支与循环语句

```sh
# 1. if语句
if condition; then
  do_sth
fi

if condition; then
  do_sth_1
else
  do_sth_2
fi

if condition; then
  do_sth_1
elif condition; then
  do_sth_2
else
  do_sth_3
fi

# 2. case语句
case $variable in
"VALUE1")
  do_sth_1
  ;;
"VALUE2")
  do_sth_2
  ;;
"VALUE3")
  do_sth_3
  ;;
*)  # 相当于通配符，表示0至无穷多个字符
  do_sth_4
  ;;
esac

# 3. while循环
while condition; do # 当条件成立时继续循环
  do_sth
done

# 4. until循环
until condition; do # 当条件不成立时继续循环
  do_sth
done

# 5. for-in
# 遍历枚举值
for item in a b c; do
  echo $item # 引用迭代过程中的每一项
  do_sth
done
# 遍历数组
declare -a arr=(1 2 3 4)
for item in ${arr[*]}; do
  echo $item
  do_sth
done
# 遍历文本文件
echo ${arr[*]} > data.txt
for item in $(cat data.txt); do
  echo $item
  do_sth
done
# 遍历区间
for item in $(seq 1 100); do
  echo $item
  do_sth
done
for item in {1..100}; do
  echo $item
  do_sth
done

# 6. for循环
declare -a arr=(甲 乙 丙 丁 戊 己 庚 辛 壬 癸)
for((i=0;i<100;i++)); do
  echo ${arr[i]} # 访问数组成员
  do_sth
done
```

6、函数

```sh
function sayHello(){
  echo "函数的参数如下："
  # 函数具有类似于脚本的变量
  echo "\$0 = $0"
  echo "\$1 = $1"
  echo "\$2 = $2"
  echo "\$3 = $3"
  echo "\$# = $#"
  echo "\$@ = $@"
  echo "\$* = $*"
  return 0 # return语句只能返回一个整数，其中0表示成功，非0表示失败
}

sayHello "apple" "banana" "coconut"

# 执行结果
# $0 = demo.sh
# $1 = apple
# $2 = banana
# $3 = coconut
# $# = 3
# $@ = apple banana coconut
# $* = apple banana coconut
```

7、脚本的跟踪与调试

```
sh [-nvx] scripts.sh
-n  不执行脚本，仅检查语法
-v  在执行脚本之前将脚本的内容打印出来
-x  在执行脚本过程中将每一步执行内容打印出来
```

## 十三、Linux账号管理与ACL权限设置

### Linux账号与用户组

UID：用户ID，用户信息保存在/etc/passwd中，登录口令保存在/etc/shadow

GID：用户组ID，用户组信息保存在/etc/group中

登录系统的过程：

- 用户输入用户名和口令

- 在/etc/passwd中查询用户

- 在/etc/shadow中查询用户口令，进行比对

- 进入shell

/etc/passwd文件结构：使用【:】作为字段分割符，使用man 5 passwd查看帮助信息
- 账号名称（name）：用户的登录名，不应包含大写字母
- 口令（password）：UNIX早期版本存放加密口令，现在已经改为放在/etc/shadow中，此处用x代替
- UID：用来唯一标识用户的整数，其中0标识系统管理员，1-999一般用来标识系统账号，1000-一般用来标识可登录用户
- GID：用来唯一标识用户组的整数，其中用户组信息存放在/etc/group中
- 用户信息说明（GECOS，有时称为comment字段）
- 家目录（directory）
- shell

```
eg:
root:x:0:0:root:/root:/bin/bash
```

/etc/shadow文件结构：使用【:】作为字段分割符，使用man 5 shadow查看帮助信息
- 登录名（login name）
- 加密口令（encrypted password）：如果包含!和*等特殊字符，则无法使用该口令登录
- 上一次修改口令的日期（date of last password change）：表示为UNIX纪元以来的天数。【0】表示用户下次登录时修改口令
- 最小口令期限（minimum password age）：口令期限指从上一次修改口令的日期以来的天数，超过最小口令期限才能修改口令，【空】或【0】表示没有最小口令期限
- 最大口令期限（maximum password age）：超过最大口令期限则口令失效（expire）。【空】表示没有最大口令期限、口令警告期限和口令失效期限，口令永远不会过期和失效。
- 口令警告期限（password warning period）：设置口令过期多少天前提示警告信息。【空】或【0】表示不进行这样的提示
- 口令失效期限（password inactivity period）：口令过期后多少天内仍然可以登录，登录时会强制要求用户修改口令；超过失效期限口令失效不能登录。【0】表示口令过期当即失效，【空】表示永不失效
- 账号过期日期（account expired date）：表示为UNIX纪元以来的天数，过期后的账号不可登录，【空】表示账号永不过期。区别口令过期（password expiration）与账号过期（account expiration），口令过期只是不能使用这个口令登录，但是还能用其他方法登录；账号过期则无论通过什么方式都不能登录
- 保留（reserved field）

口令相关时间线：

![口令相关时间线](./LINUX学习笔记.assets/shadow.png)

查询口令加密（严格来讲叫摘要）方式：

```sh
authconfig --test|grep hashing
```

/etc/group文件结构：使用【:】作为字段分割符，使用man 5 group查看帮助信息
- 组名（group name）
- 用户组口令（password）
- GID
- 此用户组支持的账号名称（user list）：属于此用户组的用户名称列表，以【,】分割

初始用户组与有效用户组：

- 初始用户组：/etc/passwd文件中指定的是初始用户组
- 支持用户组：/etc/group文件中指定的其他用户组是支持用户组
- 有效用户组：当前用户优先使用的用户组是有效用户组，有效用户组只能从支持用户组中选择一个，创建文件和目录时默认的用户组是有效用户组
- groups命令：查看支持用户组，排在第一位的是有效用户组
- newgrp命令：登录到一个新的用户组上，实现了有效用户组的切换。此命令实现有效用户组的切换是通过新开一个shell实现的

/etc/gshadow文件结构：
- 组名（group name）
- 加密口令（encrpted password）：用户组口令用户非该组成员用户以该组登录；空值表示仅组员可登录
- 用户组管理员账号（administrators）：逗号分割的用户名称列表，用户组管理员有权修改用户组口令
- 用户组成员（members）：逗号分割的用户名称列表，组员可以不输入口令登录


上述四类文件的关系（伪代码）：

```
/etc/passwd join /etc/shadow using UID # 一对一
/etc/passwd join /etc/group on `/etc/passwd`.UID in `/etc/group`.`user list` # 支持用户组，一对多
/etc/passwd join /etc/group using GID # 初始用户组，一对一
/etc/group join /etc/gshadow using GID # 一对一
```

### 账号管理

1、用户管理

useradd：新增用户

useradd操作过程：

- /etc/passwd中新增一条记录，设置UID、GID、家目录等
- /etc/shadow中新增一条记录，设置口令密文为!!,此时尚没有口令
- /etc/group中新增一条记录（默认设置组名与用户名一致）
- 创建家目录，设置访问权限为700（默认在/home下建立）

useradd执行完之后还应该使用passwd命令修改口令

```
useradd [OPTIONS] LOGIN
useradd [-u UID] [-g 初始用户组] [-G 次要用户组] [-mM] [-c 说明栏][-d 家目录绝对路径][-s shell] 账号名称

-u  指定用户UID
-g  指定初始用户组名称
-G  指定次要用户组名称列表，逗号分割
-M  强制不创建家目录（系统用户默认不创建家目录）
-m  强制创建家目录（一般用户默认创建家目录）
-c  设置说明内容，即/etc/passwd第5列的内容
-d  指定某个目录为家目录，务必使用绝对路径
-r  建立系统账号，这个账号的UID有限制，见/etc/login.defs
-s  指定shell，默认是/bin/bash
-e  指定账号过期日期，格式为【YYYY-MM-DD】，对应shadow文件的第8列
-f  指定口令过期后的宽限天数，对应shadow文件的第7列。如果指定0表示口令过期当即失效，如果指定-1则口令永远不会失效
-D  查看useradd创建用户时参数的默认值


简单创建一个用户（不建议修改家目录，设置次要用户组，UID等参数）
useradd -g 用户组名称 账号名称

查看创建用户的默认值：
useradd -D
```

useradd的参考文件：

- /etc/default/useradd：其中useradd -D的结果也是来自这个文件的
  ```
  GROUP=100                 <== 默认的用户组，CENTOS等发行版使用私有用户组机制，默认创建与用户名同名的用户组，不会参考该配置；但是SUSE等发行版使用公有用户组机制，参考这个配置
  HOME=/home                <== 默认的家目录
  INACTIVE=-1               <== 默认口令过期后多少天失效，0表示过期后立即失效，-1表示永远不会失效
  EXPIRE=                   <== 账号失效日期默认值
  SHELL=/bin/bash           <== 默认SHELL
  SKEL=/etc/skel            <== 使用者家目录默认参考目录
  CREATE_MAIL_SPOOL=yes     <== 是否主动帮助使用者建立邮箱
  ```
- /etc/login.defs:
  ```
  MAIL_DIR            /var/spool/mail   <== 默认邮箱目录
  PASS_MAX_DAYS       99999             <== 最大口令期限（maximum password age）
  PASS_MIN_DAYS       0                 <== 最小口令期限（minimum password age）
  PASS_MIN_LEN        5                 <== 口令最短的字符长度（已弃用，被pam模块代替）
  PASS_WARN_AGE       7                 <== 口令警告期限（password warning period）
  UID_MIN             1000              <== 一般用户最小UID
  UID_MAX             60000             <== 一般用户最大UID
  SYS_UID_MIN         201               <== 系统用户最小UID
  SYS_UID_MAX         999               <== 系统用户最大UID
  GID_MIN             1000              <== 一般用户组最小GID
  GID_MAX             60000             <== 一般用户组最大GID
  SYS_GID_MIN         201               <== 系统用户组最小GID
  SYS_GID_MAX         999               <== 系统用户组最大GID
  CREATE_HOME         yes               <== 默认是否创建家目录
  UMASK               077               <== 默认家目录的权限UMASK
  USERGROUPS_ENAB     yes               <== 使用userdel删除用户时，如果用户组成员为空，是否删除用户组
  ENCRPTED_METHOD     SHA512            <== 口令加密（散列）机制
  ```
- /etc/skel/*

passwd：修改口令，更新用户的身份认证令牌（token）。

```
passwd [--stdin] [账号名称]   <== 所有人均可以使用来修改口令
passwd [-k] [-l] [-u [-f]] [-d] [-e] [-n mindays] [-x maxdays] [-w warndays] [-i inactivedays] [-S] [--stdin] [username]  <== root用户修改口令

-k  更新操作应当仅仅针对过期的身份认证令牌（口令）；用户希望保持非过期令牌如初
-l  锁定指定账户的口令，只锁定口令用户还能通过其他方式登录。锁定功能是通过在/etc/shadow文件口令字段（第3列）添加!!前缀来实现的
-u  -l的反操作，解锁指定账号的口令。解锁功能是通过在/etc/shadow文件口令字段（第3列）移除!!前缀实现的。默认无法创建无口令账号（/etc/shadow文件口令字段仅有!!）,配合-f可以强制操作
-d  删除口令（直接删除口令密文和!!前缀），变成无口令保护状态（passwordless）
-e  强行让口令过期
-f  强制操作
-n  设置最小口令期限（minimum password age）
-x  设置最大口令期限（maximum password age）
-w  设置口令警告期限（password warning period）
-i  设置口令失效期限（password inactivity period）
-S  打印口令相关信息，共7个字段，除锁定状态外其他字段与/etc/shadow文件对应字段含义一致
        用户名
        锁定状态：LK - 锁定  NP - 无口令  PS - 口令可用
        最近修改日期
        最小口令期限
        最大口令期限
        口令警告期限
        口令失效期限
```

chage：改变用户口令的过期信息

```
chage [OPTIONS] LOGIN

-d  设置最近修改口令日期，UNIX天数或格式化日期，设成0则表示下次登录必须修改口令
-E  设置账号过期日期，UNIX天数或格式化日期
-I  设置口令失效期限，天数
-m  设置最小口令期限，天数
-M  设置最大口令期限，天数
-W  设置口令警告期限，天数
-R  在指定的目录上应用改变并且使用来自这个目录的配置文件 ？？？
-l  列出以上信息

如果没有设置任何选项，那么会通过交互方式设置以上配置
```

usermod：修改用户信息

```
usermod [OPTIONS] username

与useradd含义相同的选项：
-c -d -e -f -g -G -s -u

新增的选项：
-a  与-G联用，增加（append）而非覆盖（set）次要用户组
-l  修改账号名称
-L  锁定口令，类似于passwd -l
-U  解锁口令，类似于passwd -u
```

userdel：删除用户数据，包括passwd、shadow、group、gshadow、家目录、邮箱目录等

```
userdel [-r] username

-r  连同家目录一起删除
```

id：打印指定用户的（缺省时为当前用户）的用户信息和用户组信息

```
id [OPTION]... [USER]

-Z  只打印当前用户的安全上下文（security context）信息（只在SELinux内核上运行）
-u  只打印有效用户ID
-g  只打印有效用户组ID
-G  打印所有用户组ID
-n  不打印ID，打印名称，针对-ugG选项
-r  打印实际的ID，而不是有效ID
-z  使用空字符给输入定界而不是空格
```

实际UID（RUID，real uid）、有效UID（EUID，effective uid）、保存的设置UID（SSUID）、设置UID（set uid）：

- RUID：real uid，实际用户ID，进程特有的概念，登录时唯一确定用户的ID，站在用户的角度
- EUID：effective uid，有效用户ID，进程特有的概念，系统判断某个进程是否拥有操作某个文件的权限，站在操作系统的角度
- SSUID：saved suid，保存的设置用户ID，进程特有的概念，EUID的备份，由linux中的exec函数保存，用来在执行脚本前后暂存原来的EUID
- SUID：set uid，设置用户ID，文件特有的概念，站在文件的角度上，文件权限有r-w-x-s-t类型。如果一个文件的权限中没有s，则表示SUID是关闭状态。
- 以上结论可以推广至组ID

finger：用户信息查询（这个命令比较危险，默认不安装）

```
finger [-s] 用户名

列出的字段：
  Login：用户账号（/etc/passwd第1列）
  Name：用户全名（/etc/passwd第5列）
  Directory：家目录
  Shell：用户使用的Shell
  用户登录主机情况
  邮箱数据
  PLAN文件


-s  仅列出使用者的账号，全名，终端代号与登录时间等
-m  仅列出账号名称相等的，而不是部分匹配的
```

chfn：改变用户finger信息，这些信息存放在/etc/passwd文件中，并且被finger程序显示出来

- 显示的四类信息：实际姓名（real name）, 办公室房间号（work room number）, 办公电话（work phone number）, 家庭电话（home phone number）

```
chfn [-f full-name] [-o office] ,RB [-p office-phone] [-h home-phone] [-u] [-v] [username]

-f  指定姓名
-o  指定办公室房间号
-p  指定办公电话
-h  指定家庭电话
-u  帮助信息
```

chsh：改变用户的shell

```
chsh [-s shell] [-luv] [username]

-s  shell应用绝对路径
-l  列举可用shell
```

2、用户组管理

groupadd：创建用户组

```
groupadd [options] group

-g  设置GID（不建议）
-r  创建系统用户组
-f  如果用户组已经被指定，那就简单退出即可；与-g联用时，如果GID已存在，会另外指定新的GID（不建议）
```
groupmod：修改用户组信息

```
groupmod [options] GROUP

-g  设置GID（不建议）
-n  修改组名
```

groupdel：删除用户组。但是必须保证成员中没有人以该用户组作为初始用户组（即出现在/etc/passwd中）

```
groupdel 用户组
```

gpasswd：用户组管理命令，管理/etc/group和/etc/gshadow

```
gpasswd [option] group

没有任何参数时表示设置用户组密码
-a  添加用户
-d  删除用户
-r  移除组口令，这样就只能组成员登录了
-R  锁定组口令
-A  设置组管理员，后接逗号分割的用户列表
-M  设置组成员，后接逗号分割的用户列表
```

认证方式配置：authconfig-tui

### ACL

ACL = Access Control List，提供传统权限控制系统之外的详细权限设置。

检查文件系统是否支持ACL：dmesg | grep -i acl

setfacl：设置文件或目录的ACL设置选项

```
setfacl [-bkRd] [{-m|-x} acl参数] 目标文件名

-m  修改ACL条目，后接逗号分割的ACL条目列表
-M  修改ACL条目，后接ACL条目文件
-x  移除ACL条目，后接逗号分割的ACL条目列表
-X  移除ACL条目，后接ACL条目文件
-b  移除所有扩展ACL条目（extended ACL entries），保留所有的基本ACL条目（base ACL entries）
-k  移除所有默认ACL条目（default ACL entries）
-d  表示针对ACL条目的修改、移除等操作都作用在默认ACL条目（default ACL entries）上
-R  递归设置ACL，即子目录也会被设置ACL
-n  不重新计算有效权限掩码（effective permission mask）。默认情况下，setfacl命令会重新计算有效权限掩码，除非已经指定过了。掩码被设置为全部所属用户组和全部被指名的用户和用户组ACL条目的并集。
--mask
    与-n正好相反

ACL条目格式：
[d[efault]:] [u[ser]:]uid [:perms]    指名用户的权限；uid为空时为文件属主（file owner）
[d[efault]:] [g[roup]:]gid [:perms]   指名用户组的权限；gid为空时为所属用户组（owning group）
[d[efault]:] m[ask][:] [:perms]       有效权限掩码（effective permission mask）
[d[efault]:] o[ther][:] [:perms]      其他用户的权限

其中uid和gid可以替换为用户名和用户组名
```

getfacl：查看ACL参数，选项和参数基本与setfacl相同

```
输出格式：
 1: # file: somedir/ 
 2: # owner: lisa
 3: # group: staff
 4: # flags: -s-
 5: user::rwx
 6: user:joe:rwx          # effective:r-x
 7: group::rwx            # effective:r-x
 8: group:cool:r-x
 9: mask::r-x
10: other::r-x
11: default:user::rwx
12: default:user:joe:rwx  # effective:r-x
13: default:group::r-x
14: default:mask::r-x
15: default:other::---

第1-3行表示文件名、所有者、所有组
第4行表明了是否启用SUID、SGID、SBIT
第5、7、10行对应于文件模式（file mode）权限位(permission bits)的用户、用户组、其他用户。这三部分被称为基本ACL条目（base ACL entries）。
第6和8行是指定用户和用户组的ACL条目
第9行是有效权限的掩码（effective rights mask），这个条目限制了被赋予所有用户组和指定用户的有效权限（文件属主和其他权限不受有效权限掩码的影响，其他条目受影响）
第11-15行显示了与当前目录相关的默认ACL配置，目录可以具有默认ACL配置，普通文件（regular file）不具有默认ACL配置
```

几个概念：

- 有效权限掩码：有效权限 = 配置权限 & 有效权限掩码

- 扩展ACL条目：传统ugo权限不能覆盖的ACL权限条目

- 默认ACL条目：针对目录设置，目录下文件继承这些ACL条目

### 用户身份切换

su：身份的切换

```
切换至某个用户：su 用户名
切换至root：su -

【su】与【su -】的区别：是否加上这个【-】的区别很大，加上【-】表示使用login-shell的变量读取方式来登录系统，一些常见的系统变量会替换为root用户的；但是如果不加【-】，则使用nonlogin-shell变量读取方式来登录，一些常见的系统变量可能不会改变

su [options...] [-] [user [args...]]
-   单纯使用时代表使用login-shell方式登录root账号
-l  与【-】的用法类似，后接用户账号，同样是以login-shell方式登录
-c  仅执行一次命令，后接命令
-m  等同于-p，表示【使用当前的环境配置，不使用新用户的环境配置】
```

sudo：以其他用户的名义执行一个命令

/etc/sudoers：并非所有用户都可以使用sudo，只有在/etc/sudoers文件中进行配置过的用户才能使用sudo。系统初始默认仅root用户可以执行sudo

sudo的执行流程：
- 在/etc/sudoers文件中查找该用户是否具有执行sudo的权限
- 验证当前用户的口令（root用户和使用本用户时不用验证）

```
sudo [-b] [-u 新使用者账号] 执行的命令和参数

-b  将后接的命令放到后台执行，并不对当前shell产生任何影响
-u  可以接欲切换的用户
```

visudo：编辑/etc/sudoers文件

```
常见的简单使用方法：
1、单一用户使用root全部命令
root    ALL=(ALL) ALL     # 找到这一行，在这一行下添加新的一行
vbird1  ALL=(ALL) ALL     # 这是要新增的一行内容
# 上面一行四个组件的含义分别为：用户账号、登录主机名、可切换的身份、可用该身份执行的命令（绝对路径）
# 其中ALL表示全部

2、利用wheel用户以及免密码的功能处理visudo
%wheel  ALL=(ALL) ALL     # 找到这一行，把这一行的注释放开即可，其中%表示后面接的是一个用户组
配置免密登录：
%wheel  ALL=(ALL) NOPASSWD:ALL  # 将上述这行如此修改

3、有限制的命令操作
user1   ALL=(root)  /usr/bin/passwd   # 限制用户只能使用root用户执行passwd命令
user1   ALL=(root)  !/user/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
                                      # 限制用户只能使用root用户执行passwd命令且不能修改root用户命令。这里的【!】表示不可执行

4、在visudo中使用别名
User_Alias ADMPW=p1,p2,p3,u1,u2,u3
Cmnd_Alias ADMPWCOM=!/user/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
ADMPW   ALL=(root)  ADMPWCOM

5、sudo搭配su使用
User_Alias ADMINS=p1,p2,p3,u1
ADMINS ALL=(root)   /bin/su -
```
### 用户的特殊SHELL与PAM模块

1、/sbin/nologin

无法使用bash或者其他shell登录系统，但是可以使用其他的系统资源

2、PAM模块

PAM = 插入式验证模块，提供了一套验证机制，只要用户将验证阶段的需求告知PAM，PAM就能返回验证的结果（成功或失败）

模块（Modules）：PAM用来验证的数据称为模块

执行passwd命令后，程序调用PAM的流程：
- 用户输入口令，开始执行passwd程序，passwd程序调用PAM模块进行验证
- PAM模块寻找配置文件/etc/pam.d/passwd，并据此引导相关的PAM模块逐步进行验证分析
- PAM模块返回验证分析的结果，passwd程序根据验证结果进行下一步处理

/etc/pam.d/passwd:

```
#%PAM-1.0
auth      include   system-auth
account   include   system-auth
password  substack  system-auth
-password optional  pam_gnome_keyring.so  use_authtok
password  substack  postlogin

# 各个字段的含义：
# 验证类别  控制标准  PAM模块与该模块的参数
# 
# #表示注释
# 首行显示的是PAM的版本信息
```
- 服务（service）：配置文件名就是service，本例中service就是passwd

- 验证类别（type）
  - auth：认证（authentication）管理。提供两个方面的用户身份验证，一是通过用户名密码或其他方式验证用户的身份；二是通过凭据授予属性（credential granting properties）授予用户组成员特权或其他特权（priviledge）
  - account：账户管理。处理基于非鉴权（non-authentication based）账号管理。典型用法是限制/允许访问基于“一天”时间的服务，当前可获得的系统资源，或请求用户可能的位置（root用户只在控制台登录）
  - session：会话管理。与用户取得服务之前或之后必须要做的事相关，例如执行开启或关闭同一个用户进行的数据交换、挂载目录等操作时日志的记录
  - password：口令管理。更新用户关联的身份认证令牌（authenticaition token），例如口令的修改等
  - 如果验证类别的前面有一个【-】，那么如果由于系统中缺少该模块导致无法加载时PAM库将不会在系统日志中记录日志信息。对于哪些在认证和授权中非必需的模块，这个用法很有用。

- 验证的控制标志（control）：表示如果模块的认证任务失败，PAM-API应该执行的行为
  - required：一个required模块的失败会导致最终PAM-API返回失败，但是并不会中止后续模块的执行
  - requisite：一个requisite模块的失败会导致立即中止后续模块的执行并返回失败
  - sufficient：一个sufficient模块成功并且在这个模块之前没有required模块失败，则立即结束调用并返回成功；一个sufficient模块的失败会则会被忽略并继续执行后面的模块
  - optional：除非是关联到这个service和type上唯一的模块，否则这个模块的成功或失败就不重要
  - include：将其他配置文件中每一行加载进来
  - substack：子栈（substack）。引用其他配置文件，与include区别在于在一个子栈中done和die行为的计算不会导致整个模块栈剩余模块被跳过，而是仅限于子栈中的模块被跳过；子栈中的跳转也不会导致求值跳转跳出子栈，并且整个子栈在父栈中被计算为一个模块；reset操作会导致模块栈的状态重置为子栈开始计算时的状态

3、常用模块的介绍：

模块详细信息的查询：
- /etc/pam.d/*：每个程序的PAM配置文件
- /lib64/security/*：PAM模块实际存放位置
- /etc/security/*：其他PAM环境的配置文件
- /usr/share/doc/pam-*/：详细的PAM说明文件

常见的PAM模块：
- pam_securetty.so：限制系统管理员只能从安全的（secure）终端登录。默认情况下root用户可以通过tty1-7登录，但是不能通过telnet登录
- pam_nologin.so：限制一般用户是否能够登录主机。当/etc/nologin（注意不是nologin.txt）文件存在时，一般用户不能登录系统
- pam_selinux.so：针对程序进行详细管理权限的功能
- pam_console.so：系统出现问题或某些时刻需要使用特殊的终端接口登录主机时，此模块帮助处理一些文件权限的问题
- pam_loginuid.so：验证用户的UID与系统账号、一般账号合法区间相符
- pam_env.so：设置环境变量的模块，如果要进行修改可以参考/etc/security/pam_env.conf这个文件
- pam_unix.so：用在验证阶段的认证功能、授权阶段的账号凭证管理、会话阶段的日志文件记录等，甚至可以用在密码更新阶段的检验
- pam_pwquality.so：用来检验口令的强度，替代pam_cracklib.so；同时提供了/etc/security/pwquality.conf文件可以额外指定默认值
- pam_limits.so：ulimit命令的功能就是通过这个命令实现的

4、login的验证机制流程：

（1）验证阶段（auth）：

- pam_securetty.so：如果是root，参考/etc/securetty的配置
- pam_env.so：设置环境变量
- pam_unix.so：验证口令，如果口令验证成功返回login程序；否则继续执行
- pam_succeed_if.so：UID是否大于等于1000（默认一般用户UID大于等于1000），如果小于1000则返回失败
- pam_deny.so：拒绝连接

（2）授权阶段（account）：

- pam_nologin.so：/etc/nologin存在时一般用户不允许登录’
- pam_unix.so 和 pam_localuser.so：账号管理
- pam_succeed_if.so：UID是否小于1000，如果小于1000则不记录登录信息
- pam_permit.so：允许该账号登录

（3）密码阶段（password）：

- pam_pwquality：设置口令仅能尝试错误3次
- pam_unix：通过sha512或shadow等功能验证口令，若通过则返回login程序，若不通过则通过pam_deny.so拒绝登录
- pam_deny.so：拒绝登录

（4）会话阶段（session）：

- pam_selinux.so：暂时关闭selinux
- pam_limits.so：设置用户能够操作的系统资源等
- 登录成功后开始记录相关信息在日志文件中
- pam_loginuid.so：规范不同的UID权限
- pam_selinux.so：开启selinux

5、其他相关文件：

limits.conf：修改ulimit除了可以通过修改~/.bashrc文件，也可以在limits.conf中统一进行配置，这个文件是PAM模块使用的文件

/var/secure/log、/var/log/message文件：发生无法登录或无法预见的错误时，可以查询这两个文件

### Linux主机上的用户信息传递

1、查询用户

w：打印当前登录的用户信息以及他们当前正在做的事

- 起始部分：展示当前时间、系统运行时长、当前登录用户、过去1分钟、5分钟、15分钟内的系统平均负载（system load averages）

- 后续条目：当前登录用户的列表，显示的列包括：登录名（USER）、tty终端名称（TTY）、远程主机（FROM）、登录时间（LOGIN@）、空闲时间（IDLE）、JCPU、PCPU、当前进程的命令行信息（WHAT）

- JCPU：所有关联在此tty的进程消耗的时间，不包括过去的后台作业但是包括现在正在运行的后台作业

- PCPU：当前进程消耗的时间，WHAT字段指出该进程的名称、

```
w [OPTIONS] user [...]

-h  不打印起始部分
-u  列出当前进程和CPU时间时忽略用户名。为了验证这一点，执行su之后再执行一次"w"和"w -u"
-s  使用简短格式输出，不打印登录时间、JCPU和PCPU
-f  显示FORM（remote hostname）字段（默认开启）
-i  显示IP地址替代主机名
-o  旧风格输出
```

who：显示谁登录在线

```
who [OPTION] ... [ FILE | ARG1 ARG2]

-a  等价于-bdprtTu --login
-b  上一次子系统启动的时间
-d  打印死亡进程
-H  打印列标
-l  打印系统登录进程
--lookup  试图通过DNS规范化主机名
-m  只有与stdin相关的主机和用户
-p  打印活跃进程
-q  所有登录名和登录的用户数
-r  打印运行级别
-s  只打印名称、行和时间（默认）
-t  打印上一次系统时钟改变
-T，-w  使用+-?添加用户消息
-u  列举登录的用户
```

last：显示上一次登录用户列表。

- last命令反向搜索/var/log/wtmp文件（或者使用-f选项自行指定）并且显示一个从该文件创建以来用户登录的列表。用户的名称和tty的名称可以被指定，last命令会只展示匹配所给参数的条目。tty的名称可以简写，如last tty0可以简写为last 0

- 当last命令捕获一个SIGINT信号（如ctrl-c）或者一个SIGQUIT信号（如ctrl-\）,last命令会显示搜索文件到什么程度；在出现SIGINT信号的情况下，last命令之后会终止执行

- 伪用户reboot会在系统每次重启之后登录，因此last reboot可以查看自日志文件创建以来的重启记录

- lastb命令的用法与last一样，只是默认显示的是/var/log/btmp文件，其中包含了失败的登录企图

```
last [-R] [-num] [-n num] [-adFiowx] [ -f file] [-t YYYYMMDDHHMMSS] [name...] [tty...]
lastb [-R] [-num] [-n num] [-f file] [-adFiowx] [name...] [tty...]

-f file 指定last命令读取的文件
-num    指定last命令显示的行数
-n num  与-num相同
-t YYYYMMDDHHMMSS
        显示某个时间用户的登录状态
-R      阻止主机名字段的显示
-a      在最后一列显示主机名字段
-d      对于非本地（non-local）登录，linux不仅会存储远程主机的主机名还会存储它的IP地址，这个选项把IP地址翻译回一个主机名
-F      打印完整的登录和注销时间及日期
-i      类似于-d选项，但是使用点分十进制显示IP地址
-o      读取一个旧风格的wtmp文件
-w      在输出中显示完整的用户和域（domain）名称
-x      显示系统关机条目和运行级别变化
```

lastlog：报告最近所有用户登录或指定某个用户的登录。lastlog会格式化输出上一次登录的日志文件/var/log/lastlog。打印的字段包括登录名（login-name）、端口（port）、上一次登录时间（last login time）默认情况下按照/etc/passwd中的顺序进行排序

```
lastlog [OPTIONS]

-b  DAYS    只打印指定天数之内的lastlog记录
-c          清除某个用户的lastlog，这个选项只能与-u选项联用
-R CHROOT_DIR   应用改变到指定的目录并且使用来自指定目录的配置文件
-S          设置某个用户的lastlog的时间为当前时间，此选项只能与-u联用
-t  DAYS    只打印早于指定天数的lastlog记录
-u  LOGIN|RANGE
            打印指定用户的lastlog，后接用户名、UID或者UID区间（a-b -b a-）
```

2、用户对谈

write：发送消息

mesg [y|n]：接受或拒绝消息、

wall：广播消息

3、用户邮箱

```
发送邮件：
mail -s 邮箱标题 收件人
邮件正文（以.结束）

接收邮件：
mail
?   帮助信息
```

### 大量创建账号的方法：

检查账号的命令：

- pwck：检查/etc/passwd文件，判断家目录是否存在，与/etc/shadow文件是否保持一致

- pwconv：将/etc/passwd文件中口令转移到/etc/shadow文件中

- pwunconv：pwconv的反操作

- chpasswd：读入口令明文，按照/etc/login.defs中规定的加密机制得到口令密文，最后写入/etc/shadow文件

批量创建账号：

- 使用groupadd创建用户组

- 使用useradd创建用户

- 使用chage将【上一次修改口令时间】字段置为0，强制用户第一次登录时修改口令

- 为用户家目录赋予权限

## 十四、磁盘配额（Quota）的应用与实践

### 磁盘配额

磁盘配额的作用：
- 限制某一用户组所能使用的最大磁盘配额
- 限制某一用户的最大磁盘配额
- 限制某一目录的最大磁盘配额

磁盘配额的使用限制：
- ext文件系统仅支持针对文件系统的磁盘配额，不支持针对某个目录的磁盘配额
- Linux内核必须提供磁盘配额的支持，CentOS 7.x默认提供磁盘配额的支持
- 只有一般用户才能设置磁盘配额
- 如果启用了SELinux，那么并非所有目录都可以设置磁盘配额。CentOS新版本默认开启了SELinux，因此磁盘配额似乎只能对/home目录生效
- 不同的文件系统处理磁盘配额的实现方式不同，因此在进行磁盘配额之前必须确认文件系统的类型

xfs文件系统的磁盘配额的规范设置选项：
- 针对用户、用户组、个别目录的磁盘使用率的限制
- 容量限制或文件数量限制（block或inode）
  - 限制inode的使用量，即用户只能建立有限数量的文件
  - 限制block的使用量，即用户只能使用有限的磁盘空间
  - 软限制与硬限制（soft/hard）：硬限制表示用户只能使用限定值的磁盘空间；软限制表示用户可以在宽限时间（grace time）内使用超过限定值的磁盘空间；软限制和硬限制可以同时使用
  - 宽限时间（grace time）：宽限时间只会在磁盘使用量介于软限制与硬限制之间时才会出现，宽限时间会倒数计时

1、xfs文件系统磁盘配额实践

（1）文件系统的支持与查看：
- 尽量避免在根目录下进行磁盘配额设置，这样会导致文件系统变得过于复杂
- 编辑/etc/fstab磁盘挂载信息，在要进行磁盘配额的行的第四列（PARAMETERS）添加参数：
  - uquota/usrquota/quota：针对用户账号的限制
  - gquota/grpquota：针对用户组的限制
  - pquota/prjquota：针对单一目录的限制，但是不可与grpquota同时存在
  ```
  # 示例
  /dev/mapper/centos-home /home xfs defaults,usrquota,grpquota 0 0
  ```
（2）查看磁盘配额报告数据：

```
xfs_quota -x -c "命令" [挂载点]

-x  专家模式，后续才能够使用-c的命令参数
-c  后接命令

数据报告相关命令：
print   单独地列出目前主机内的文件系统参数等数据
df      与原本的df具有相同的功能，可以加上-b（block）、-i（ignore）、-h（human-readable）等
report  列出目前的磁盘配额选项，有-ugr（user/group/project）及-bi等选项
state   说明目前支持磁盘配额的文件系统的信息，有没有使用相关选项等

使用示例：
1、列出目前系统的各个文件系统，以及文件系统的磁盘配额挂载参数支持
xfs_quota -x -c "print"

2、列出目前/home这个支持磁盘配额的挂载点文件系统的使用情况
xfs_quota -x -c "df -h" /data

3、列出目前/home的所有用户的磁盘配额限制值
xfs_quota -x -c "report -ubih" /data

显示的列包括：
（1）User ID：用户名
（2）blocks：存储空间的使用和限制情况
- Used：已经使用的存储空间大小
- Soft：存储空间的软限制，0表示无限制
- Hard：存储空间的硬限制，0表示无限制
- Warn/Grace：宽限时间与警告进度条
（3）inodes：inode节点的使用和限制情况
- Used：已经使用的inode节点数量
- Soft：inode节点数量的软限制
- Hard：inode节点数量的硬限制
- Warn/Grace：宽限时间与警告进度条

4、列出目前支持的磁盘配额文件系统是否启用了磁盘配额功能
xfsquota -x -c "state"

显示的内容为：
User quota state on /data (/dev/sdc1)         # 第一部分：针对用户的限制功能
  Accounting: ON                              # 有启用计算功能
  Enforcement: ON                             # 有实际磁盘配额管理的功能
  Inode: #67 (1 blocks, 1 extents)
Group quota state on /data (/dev/sdc1)        # 第二部分：针对用户组的限制功能
  Accounting: ON
  Enforcement: ON
  Inode: #68 (1 blocks, 1 extents)
Project quota state on /data (/data/sdc1)     # 第三部分：针对Project（也就是所谓目录）的限制功能
  Accounting: ON
  Enforcement: ON
  Inode: #69 (1 blocks, 1 extents)
                                              # 第四部分：宽限时间的限制
Blocks grace time: [7 days]                   # 存储空间超限宽限时间       
Inodes grace time: [7 days]                   # Inode节点超限宽限时间
Realtime Blocks grace time: [7 days]          # Realtime block（参考xfs的实时运行区的概念）超限宽限时间
```
（3）限制值的设置方式：
```
xfs_quota -x -c "limit [-ug] b[soft|hard]=N i[soft|hard]=N name" 目录/分区
xfs_quota -x -c "timer [-ug] [-bir] Ndays" 目录/分区

选项与参数：
limit：实际限制的选项
- 限制的对象：-u表示针对user，-g表示针对group；name是实际用户或用户组的名称
- 限制的内容：b表示block，i表示inode
- 限制的方式：soft表示软限制，hard表示硬限制，后接的数值可以带上K、M、G等单位
timer：设置宽限时间等
- 限制的对象：-u表示针对user，-g表示针对group
- 限制的内容：-b表示限制block的宽限时间，-i表示限制inode的宽限时间，-r表示限制realtime block的宽限时间；Ndays表示设置宽限时间为N天
```
（4）project的限制

project的限制与针对project的限制不能同时存在：
- 先修改/etc/fstab文件，去掉grpquota，换成prjquota；
- 然后再使用mount -a重新挂载，或者直接重新启动。
- 使用xfs_quota -x -c 'state' /home命令查看是否切换成功

设置针对project的限制：

```
- 在/etc/projects文件中配置项目标识符与目录的映射关系
  11:/data/dir1

- 在/etc/projid文件中配置项目名称与项目标识符的映射关系
  project1:11

- 初始化项目
  xfs_quota -x -c "project -s project1"

- 查看xfs_quota配置
  xfs_quota -x -c "print" /data
  xfs_quota -x -c "report -pbih" /data

- 设置基于project的限制
  xfs_quota -x -c "limit -p bsoft=10M bhard=15M project1" /data
  xfs_quota -x -c "report -pbih" /data
```

（5）其他相关命令

```
1、disable：设置enforcement为OFF，不限制磁盘空间或inode的使用，只进行计算
xfs_quota -x -c "disable -up" /data     # -u表示针对user，-p表示针对project，-g表示针对group

2、enable：与disable相反
xfs_quota -x -c "enable -up" /data     # -u表示针对user，-p表示针对project，-g表示针对group

3、off：完全关闭，设置accounting与enforcement均为OFF，只有重新挂载时才会启用磁盘配额（建议使用disable，不建议使用off）
xfs_quota -x -c "off -up" /data

4、remove：必须要在OFF状态下才能执行，删除磁盘配额的限制设置
xfs_quota -x -c "remove -p" /data
```

2、ext磁盘配额实践

```
1、/etc/fstab配置文件修改：
/dev/sdc1    /data    ext3    defaults,usrquota,grpquota    0 0

2、建立磁盘配额配置文件：
quotacheck  [-avugfM] [挂载点]
-a  扫描所有在/etc/mtab中支持quota的文件系统，此时可以不用显式给出挂载点
-u  建立针对user的磁盘配额配置文件aquota.user
-g  建立针对group的磁盘配额配置文件aquota.group
-v  verbose
-f  force
-M  强制以读写的方式扫描文件系统，只有在特殊情况下才会使用
一般来说只需要执行：
quotacheck -avug

3、启动关闭磁盘配额的限制
quotaon [-avug] [mount_point]
quotaoff [-aug] [mount_point]

4、编辑用户或用户组的磁盘配额限制与宽限时间
edquota [-u username] [-g groupname]
edquota -t
edquota -p 源用户名或用户组名 -u 新配置的用户名或用户组名

修改磁盘空间限额时，应该只去修改block的soft和hard值（单位为KB）和inodes的soft和hard值（单位为1），设置为0时表示没有限额

修改宽限时间时，应该只去修改block grace period和inode grace period，单位可以是days, hours, minutes或seconds

5、查看quota报告

用户或用户组视角查看quota报告：
quota [-uvs] [username]
quota [-gvs] [groupname]
-u  后接username，缺省为执行者username
-g  后接groupname
-v  显示每个用户在文件系统中的quota值
-s  使用human-readable方式显示

文件系统视角查看quota报告：
repquota -a [-vugs]
-a  从/etc/mtab中检索文件系统
-v  显示文件系统的详情
-u  显示出user的quota限值
-g  显示出group的quota限值
-s  使用human-readable方式显示

6、警告
warnquota：对超过限额的使用者发出警告mail

7、直接在指令中设置quota限额
setquota [-u|-g] 用户名或用户组名 BLOCK_SOFT BLOCK_HARD INODE_SOFT INODE_HARD FILESYSTEM

```

### 软件磁盘阵列（Software RAID）

1、基本概念

RAID = Redundant Arrays of Inexpensive Disks，独立冗余磁盘阵列。通过软硬件技术将多个较小的磁盘整合称为一个较大的磁盘设备。这个较大的磁盘设备不仅具有数据存储功能，还具有数据保护功能。

RAID（Redundant Arrays of Independent Disks）：独立冗余磁盘阵列，多个物理磁盘构成逻辑上一个完整的磁盘，借此达到提升性能的目的

三个关键技术：镜像（Mirroring）、数据条带（Data Stripping）、数据校验（Data parity）

- 镜像：将数据复制到多个磁盘

- 数据条带：将同一份数据分片存放，每一个磁盘只存储同一份数据的一部分，多个数据分片共同组成一份完整的数据

- 数据校验：利用冗余数据进行数据错误检测和修复

RAID LEVEL：

- RAID0（冗余无校验的磁盘阵列）：将数据拆分为不同的数据块，然后依次存放在不同的物理磁盘上（第1至n块存放在磁盘1至n，然后第n+1至2n块存放在磁盘1至n，……）

- RAID1（镜像磁盘阵列）：每个物理磁盘存放一个完整的数据副本，不同物理磁盘存放数据相同，互为冗余

- RAID0+1（先分块后镜像）和RAID1+0（先镜像后分块）：RAID0、RAID1的混合使用，RAID0+1就是先RAID0，再RAID1，RAID1+0就是先RAID1再RAID0

- RAID2（并行汉明纠错阵列）：按存储单元（字节或位元）拆分，依次存放在不同的物理磁盘上；设置额外的校验盘，存放汉明码。
  - 汉明码：
  ```python
  # 2^检测位数 >= 编码最小距离 = 检测错误的位数 + 纠正错误的位数 + 1
  # 汉明码：根据编码纠错理论设计的具有一位纠错能力的编码。
  # 设数据位共有n位，校验位共有k位，则 2^k >= n+k+1。
  # 例如当n=16，则min(k)=5，将数据位记作D[0]至D[15]，检测位记作H[0]至H[4]
  # 将检测位与数据位混合，其中检测位放在第1、2、4、8、16位，则混合后的bit位为
  [a,b]=[0,0] # H和D的下标
  DH=[-1]     # H和D混合后数据，0下标不使用
  P=[]        # 校验方程结果
  for i in range(1,len(D)+len(H)+1):
    if 1<<a==i:
      DH.append(H[a])
      a=a+1
    else:
      DH.append(D[b])
      b=b+1
  
  for i in range(len(H)):
    cur=0
    for j in range(1,len(DH)):
      if j&(1<<i)>0:
        cur=cur^DH[j]
    P[i]=cur
  # 计算H的方法：先假定H为全0，计算得到P，令H=P即可
  # 校验D的方法：计算P，P组成的二进制数就是出错的位置编号，即DH下标
  ```
  - 在RAID2中使用汉明码：如果有16个数据盘，那么就要设置5个校验盘。将这21个物理磁盘相同位置的比特位拿出来构成一个校验序列DH，计算得到校验结果P，根据P确定这些比特位是否正确，如果出现错误错误的是哪一个磁盘
- RAID3（奇偶校验并行交错阵列）：在RAID0的基础上添加专用的校验盘
  - 奇偶校验码：
  ```python
  # D是一个存储单元的数据
  # C是奇偶校验位
  P = reduce(lambda pre,item:pre^item,D+[C])
  # 计算C的方法：假定C是0，计算P，令C=P即可
  # 校验D的方法：计算P，P为0时校验成功，P为1时校验不通过，但无法确定D中哪一位数据有问题
  ```
  - 在RAID2中使用奇偶校验码：设置独立的校验盘存放奇偶校验码，数据盘与校验盘相同位置的比特位构成一个校验序列
- RAID4（奇偶校验扇区交错阵列）：按块的粒度水平拆分，各个物理磁盘依次存放块；设置独立的校验盘，依次存放各个块的校验数据
  
- RAID4与RAID3的区别：RAID4读取一个块要读取所有的物理磁盘。RAID3读取一个块只需要读取其中一个数据盘和校验盘
  
- RAID5（循环奇偶校验阵列）：RAID4的基础上，不设置额外的校验盘，校验块分散在各个物理磁盘上

- RAID6（二维奇偶校验阵列）：RAID5的基础上支持两种算法的校验

- RAID7：最优化的异步高IO速率和高数据传输率，本质上具有独立存储的计算机，自带专用操作系统和管理工具，可以独立运行

热备份磁盘：正常情况下不使用，一旦某个使用中地磁盘损坏，启用该磁盘主动重建（rebuild），这样地磁盘就是热备份磁盘

硬件RAID和软件RAID：在不同的层级上实现RAID，硬件RAID通过在硬件设备层级就实现了RAID。软件RAID则在操作系统层级实现RAID

2、软件磁盘阵列的设置

```
mdadm --detail /dev/md0
mdadm --create /dev/md[0-9] --auto=yes --level=[015] --chunk=NK --raid-devices=N --spare-devices=N /dev/sdx /dev/hdx ...

选项与参数：
--create        表示执行的操作是建立RAID
--auto=yes      后接软件磁盘阵列设备，即/dev/md0、/dev/md1等
--chunk=NK      决定这个设备的chunk大小，也可以当成stripe大小，一般是64K或者512K
--raid-devices  使用几个磁盘分区作为磁盘阵列的设备
--spare-devices 使用几个磁盘分区作为备用（spare）设备
--level=[015]   设置这组磁盘阵列的级别，支持很多，建议只使用RAID0、RAID1、RAID5
--detail        后面所接磁盘阵列的详细信息


磁盘阵列detail输出内容：
/dev/md0                                          # RAID的设备文件名
           Version : 1.2
     Creation Time : Sun Jan 16 12:45:36 2022     # RAID创建时间
        Raid Level : raid5                        # RAID级别
        Array Size : 58368 (57.00 MiB 59.77MB)    # 磁盘阵列可用容量
     Used Dev Size : 19456 (19.00 MiB 19.92MB)    # 组成磁盘阵列的每块磁盘容量
      Raid Devices : 4                            # 组成RAID的磁盘数量
     Total Devices : 5                            # 包括spare在内的总磁盘数量
       Persistence : Superlock is persistent

       Update Time : Sun Jan 16 13:13:04 2022
             State : clean                        # 目前这个磁盘的使用状态
    Active Devices : 4                            # 启用的设备数量
   Working Devices : 5                            # 目前使用于此阵列的设备数
    Failed Devices : 0                            # 损坏的设备数
     Spare Devices : 1                            # 热备份的磁盘数量

            Layout : left-symmetric
        Chunk Size : 256K                         # chunk数据块的大小

Consistency Policy : resync
              Name : localhost.localdomain:0 (local to host locahost.localdomain)
              UUID : xxxxxxxx:xxxxxxxx:xxxxxxxx:xxxxxxxx
            Events : 18

Number  Majar   Minor   RaidDevice State
   0      8       33       0       active sync    /dev/sdc1
   1      8       34       1       active sync    /dev/sdc2
   2      8       35       2       active sync    /dev/sdc3
   5      8       37       3       active sync    /dev/sdc5

   4      8       38       -       spare    /dev/sdc6
# 最后五行显示的就是这五个设备目前的情况，包括4个active sync一个spare
# RaidDevice指的则是此RAID内的磁盘顺序


除了使用--detail选项查看磁盘阵列详情，还可以通过查看文件来查看软件磁盘阵列的详情
cat /proc/mdstat
```

raid磁盘阵列的格式化与挂载：
```sh
 -f -d su=256k,sw=3 -r extsize=768k /dev/md0
# stripe(trunk)的容量为256KB，所以su=256k
# 共有4块磁盘组成磁盘阵列，校验数据块占据一个磁盘的容量，所以sw=3
# 条带宽度就是su*sw=256K*3=768K

mount /dev/md0 挂载点
```

3、模拟RAID错误的恢复模式

mdadm磁盘管理：
```
mdadm --manage /dev/md[0-9] [--add 设备] [--remove 设备] [--fail 设备]

--add     将后面的设备加入到这个md中
--remove  将后面的设备从这个md中删除
--fail    将后面的设备设置成为出错的状态
```

更换磁盘的操作：
- 将损坏的磁盘从磁盘阵列中移除
- 关机、更换磁盘、开机
- 将新的设备添加到磁盘阵列中

4、配置开机自动启动

```
# 查询磁盘阵列的UUID
blkid /dev/md0

# 编辑/etc/mdadm.conf文件
ARRAY /dev/md0 UUID=xxxxxxxx:xxxx:xxxx:xxxx:xxxxxxxxxxxx

# 编辑/etc/fstab
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  挂载点  xfs  defaults 0 0

# 验证自动挂载是否成功
umount 挂载点
mount -a
```

5、关闭RAID

- 卸载文件系统
- 删除/etc/fstab和/etc/mdadm.conf相关条目
- 覆盖掉/dev/md[0-9]（bs和count根据设定值定）：`dd if=/dev/zero of=/dev/md0 bs=1M count=50`
- 关闭RAID：`mdadm --stop /dev/md0`
- 覆盖掉组成RAID的磁盘/dev/xxx（bs和count根据设定值定）：`dd if=/dev/zero of=/dev/xxx bs=1M count=10`
- 查看mdstat：`cat /proc/mdstat`

### 逻辑卷管理器（Logical Volume Manager）

LVM重点在于【可以弹性地调整文件系统的容量】，LVM可以整合多个物理分区，让这些物理分区看起来就像是一个磁盘一样

1、基本概念

物理卷（Physical Volume，PV）：实际的分区或磁盘需要调整系统标识符（system ID）为8e（LVM的标识符），然后再经过pvcreate命令转换为PV

卷组（Volume Group，VG）：PV被LVM整合成的逻辑上的磁盘设备就是VG，在32位机上LV仅能支持65534个PE，在64位机上几乎没有限制

物理扩展块（Physical Extent，PE）：PE是LVM存储数据的最小单位，类似于文件系统中的block。LVM实现弹性修改文件系统的容量就是通过交换LV的PE实现的

逻辑卷（Logical Volume，LV）：VG最终会被切分成LV使用，VG之于LV类似于磁盘之于分区。LV的文件名一般定义为【/dev/VG名称/LV名称】

2、LVM实践流程

LVM实践流程：

- 使用gdisk将分区的system ID修改为8e（LVM）
- 使用pvcreate、pvscan创建PV并查看
- 使用vgcreate、vgdisplay将PV整合为VG并查看
- 使用lvcreate、lvdisplay将VG拆分为若干LV
- 使用mkfs和mount将LV格式化并挂载

PV阶段：

```
pvcreate  在物理分区上建立PV
pvscan    查询具有PV的磁盘或分区
pvdisplay 显示系统目前的PV状态
pvremove  在物理分区上移除PV特性


sample：
pvscan
pvcreate /dev/sdc{1,2,3,4}
pvdisplay /dev/sdc1


pvdisplay展示的内容：
PV Name       实际的分区设备名称
VG Name       VG名称
PV Size       PV的容量
Allocatable   是否已被分配
PE Size       PE的大小
Total PE      供划分出的PE数量
Free PE       没有被LV使用的PE数量
Allocated PE  尚可分配出去的PE数量
PV UUID
```

VG阶段：

```
vgcreate  主要建立VG的命令
vgscan    查找系统上是否有VG存在
vgdisplay 显示目前系统上的VG状态
vgextend  在VG内增加额外的PV
vgreduce  在VG内删除PV
vgchange  设置VG是否启动
vgremove  删除一个VG

vgcreate [-s N[mgt]] VG名称 PV名称
-s  后接PE的大小，单位可以是mgt
```

LV阶段：

```
lvcreate  建立LV
lvscan    查询系统上面的LV
lvdisplay 显示系统上的LV状态
lvs       显示系统上的LV状态
lvextend  在LV里面增加容量
lvreduce  在LV中减少容量
lvremove  删除一个LV
lvresize  对LV进行容量大小的调整

lvcreate [-L N[mgt]] [-n LV名称] VG名称
lvcreate [-l N] [-n LV名称] VG名称
-L  后面接容量，容量可以带单位，但是容量必须是PE的整数倍，若不相符，系统会计算相近的容量
-l  后面可以接PE的个数
-n  后接LV的名称

lvresize [-L N[mgt]] [-n LV名称] VG名称
lvresize [-l N] [-n LV名称] VG名称
```

文件系统阶段：
```
 /dev/vgfirst/lvfirst
mkdir -p /data/lvfirst
mount /dev/vgfirst/lvfirst /data/lvfirst
```

3、放大LV容量

放大LV容量的流程：
- 确认VG阶段还有剩余的容量，如果VG容量不够，可以利用pvcreate创建PV，再使用vgextend添加到VG中
- LV阶段产生更多的可用容量，利用lvresize命令将VG剩余容量加入到LV中
- 【重要】文件系统阶段的放大，xfs文件系统仅支持放大，使用xfs_growfs命令即可，不支持缩小

4、使用LVM thin Volume动态自动调整磁盘使用率

什么是LVM thin Volume：简单说就是LV按需获取容量，而不是立即分配。设置容量存储池（thin pool），由容量存储池根据LV实际使用率决定容量的分配

设置LVM thin Volume的流程：
- 创建thin pool
  ```sh
  lvcreate -L 1G -T vgfirst/tpoolfirst # 最重要的命令
  lvdisplay /dev/vgfirst/tpoolfirst    # 打印的结果中由再分配的选项
  lvs vgfirst                          # lvs命令查看的结果更加明了
  ```
- 建立thin pool LV
  ```sh
  lvcreate -V 10G -T vgfirst/tpoolfirst -n thinfirst  # -V指定的容量可以大于thin pool拥有的容量
  ```
- 建立文件系统和挂载使用

5、LV磁盘快照

快照的功能：记录系统信息，未来如果有数据修改，原始数据会被搬移到快照区，未被修改的数据则由快照区与文件系统共享，修改后的新数据则不被共享

创建快照的流程：
- 使用vgdisplay查看VG还剩下多少容量
- 创建快照区
  ```sh
  lvcreate -s -l 26 -n snapfirst /dev/vgfirst/lvfirst # 创建快照区
  lvdisplay /dev/vgfirst/snapfirst # 查看快照区
  ```
- 快照区挂载
  ```sh
  mkdir -p /data/snapfirst  # 创建目录
  mount -o nouuid /dev/vgfirst/snapfirst /data/snapfirst # 因为xfs不允许相同UUID文件系统的挂载，所以需要加上nouuid参数
  df -hT  # 查看结果发现快照区与源LV内容一样
  ```

利用快照恢复系统：
- 利用快照区将原文件系统备份，使用xfsdump处理
  ```sh
  xfsdump -l 0 -L lvm2 -f /root/lvm2.dump /data/snapfirst
  ```
- 将快照区卸载并删除
  ```sh
  umount /data/snapfirst
  lvremove /dev/vgfirst/snapfirst
  umount /data/lvfirst
   -f /dev/vgfirst/lvfirst
  mount /dev/vgfirst/lvfirst /data/lvfirst
  xfsrestore -f /root/lvm2.dump -L lvm2 /data/lvfirst
  ```

6、LVM的关闭

关闭LVM的步骤：
- 卸载LVM文件系统（包括快照和所有LV）
- lvremove所有LV
- 让VG不再激活：`vgchange -a n VG名称`
- vgremove所有VG
- pvremove所有PV
- gdisk修改system ID


总结：
|任务|PV阶段|VG阶段|LV阶段|文件系统（XFS/ext4）|
|--|--|--|--|--|--|
|查找（scan）|pvscan|vgscan|lvscan|lsblk、blkid|
|建立（create）|pvcreate|vgcreate|lvcreate|、mkfs.ext4|
|列出（display）|pvdisplay|vgdisplay|lvdisplay|df、mount|
|增加（extend）||vgextend|lvextend（lvresize）|xfs_growfs、resize2fs|
|减少（reduce）||vgreduce|lvreduce（lvresize）|xfs不支持、resize2fs|
|删除（remove）|pvremove|vgremove|lvremove|umount、重新格式化|
|修改容量（resize）|||lvresize|xfs_growfs、resize2fs|
|修改属性（attribute）|pvchange|vgchange|lvchange|/etc/fstab、remount|

## 十五、计划任务（crontab）

at与cron：
- at处理仅执行一次就结束的命令，执行at的前提是要有atd服务
- crontab：按照设定的时间周期性执行

CentOS上常见的定时任务：
- 日志文件的轮询（logrotate）
- 日志文件分析（logwatch）
- locate数据库的建立
- manpage数据库的建立
- rpm软件日志文件的建立
- 缓存的删除
- 与web服务有关的分析操作

### 仅执行一次的计划任务

atd服务的启动：
```sh
systemctl restart atd   # 启动
systemctl enable atd    # 开机自启
systemctl status atd    # 查看状态
```

at命令的原理：at命令将任务以文本形式加入/var/spool/at目录中，atd读取该目录并执行任务

at命令的权限控制：

- /etc/at.allow：at命令用户白名单
- /etc/at.deny：at命令用户黑名单
- 如果两个文件都没有，则仅root能执行at

at命令：从标准输入读取任务并定时执行

- at的执行与终端环境无关，所有的标准输出和标准错误输出都会发送到执行者的mailbox中。建议at执行的命令都使用绝对路径。
- 利用at可以实现命令的后台执行

```
at [-mlv] TIME
at -cd 任务编号

-m  当完成任务之后发送邮件给用户，即使没有任何输出（默认没有输出就不发送邮件）
-l  相当于atq，列出目前系统上所有该使用者的at计划
-d  相当于atrm，取消一个在at计划中的任务
-v  可以使用较明显的时间格式列出at计划中的任务列表
-c  可以列出后面接的该项任务的实际命令内容

TIME时间格式，定义了执行定时任务的时间（详细的时间格式查看manpage）
  HH:MM  今天或明天（如果今天该时刻已过）该时刻执行
  HH:MM YYYY-MM-DD
  HH:MM[am|pm] [月份英文名] [DateOfMonth]
  HH:MM[am|pm] + number [minutes|days|weeks]
  now + number [minutes|days|weeks]


使用at定时关机：
at 23:00 2022-01-17
at> /bin/sync
at> /bin/sync
at> /sbin/shutdown -h now
at> <EOT>
```

batch命令：在CPU的任务负载小于0.8的时候的整分钟时刻才执行任务。

- 任务负载：CPU在单位时间内负载的任务数量

- uptime：查看CPU的负载任务量

### 循环执行的计划任务

cron定时任务的权限控制：
- /etc/cron.allow：白名单
- /etc/cron.deny：黑名单
- 白名单优先于黑名单

用户使用crontab建立定时任务，该项任务就会被记录到`/var/spool/cron/用户名`中

crontab：
```
crontab [-u username] [-l|-e|-r]
-u  指定一个用户建立定时任务，这个选项只有root能使用
-e  编辑crontab的任务内容
-l  查看crontab的任务内容
-r  清空crontab的任务内容
-i  与-r类似，只是删除前会有y/n选项
-n  只有cron -c执行时才有意义，用来启用集群支持：设置用来运行/var/spool/cron下crontab文件指定任务的集群主机
-c  只有cron -c执行时才有意义，用来启用集群支持：查询集群中哪一台主机被设置为运行/var/spool/cron/下crontab文件指定的任务
```

cron表达式与任务

|代表意义|分钟|小时|日期|月份|周|命令|
|--|--|--|--|--|--|--|
|取值范围|0-59|0-23|1-31|1-12|0-7|要执行的命令|

```
0 0 1 * * sh start.sh   # 表示每天1点执行
```

cron表达式特殊字符

|特殊字符|代表意义|
|--|--|
|*|匹配任意数字|
|,|表示并列关系|
|-|表示闭区间|
|/n|表示能被n整除|

/etc/crontab：cron针对系统的配置文件，cron会从配置该文件中读取定时任务

```
SHELL=/bin/bash                           # 使用那种SHELL
PATH=/sbin:/bin:/usr/sbin:/usr/bin        # PATH路径，用于查找可执行文件
MAILTO=root                               # 错误发送邮箱用户

# cron表达式 用户 命令
0 0 1 * * root sh update.sh               # 定时任务（需指定以哪个用户的名义）
```

/etc/cron.d: cron针对系统的配置文件目录，cron也会从该目录下的配置文件中读取定时任务
- /etc/cron.d/0hourly：该文件中配置了一个定时任务，执行的命令是`run-parts /etc/cron.hourly`。run-parts命令的作用是执行目录中所有可执行文件（赋权）。（man 4 crontabs查看run-parts的文档）
  - 与/etc/cron.hourly类似的目录还有/etc/cron.daily、/etc/cron.weekly、/etc/cron.monthly，但这些目录都是与anacron有关的，只有cron.hourly与cron有关的

总结：cron读取定时任务
- /var/spool/cron/用户：个人化的定时任务，使用crontab -e编辑
- /etc/crontab：系统运维使用
- /etc/cron.d/newfile：定制软件使用
- /etc/cron.hourly、/etc/cron.daily、/etc/cron.weekly、/etc/cron.monthly

注意事项：
- 配置定时任务时尽量把时间错开
- 如果有数据，考虑放日志文件，或者重定向到/dev/null
- 检查/var/log/cron判断有无木马风险
- cron表达式周与日月不可共存

### 可唤醒停机期间的工作任务

anacron命令的原理：

- anacron每小时被调用，执行那些超期未执行的定时任务
- anacron通过当前的时间和时间记录文件中记载的上一次调用anacron的时间判断定时任务是否执行
- anacron本质上是一个程序，该程序的配置文件0anacron被放在/etc/cron.hourly下，每小时被调用一次，调用的命令是`/usr/sbin/anacron -s`

> 0anacron最前面的0是为了让anacron最先执行，以免出现定时任务是否执行误判的情况

anacron：

```
anacron [-sfn] [job]...
anacron -u [job]
-s  开始连续地执行各项任务（Job），根据时间记录文件的数据判断是否执行
-f  强制执行，而不去判断时间记录文件的时间戳
-n  立即执行未执行的任务，而不延迟（delay）等待时间
-u  仅更新时间记录文件的时间戳，不执行任何任务
job 由/etc/anacrontab定义的各项任务名称
```

/etc/anacrontab配置文件：
```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# this maximal random delay added to the base delay of the jobs，单位是分钟
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

# period in days  delay in minutes  job-identifier  command
1                 5                 cron.daily      nice run-parts /etc/cron.daily
7                 25                cron.weekly     nice run-parts /etc/cron.weekly
@monthly          45                cron.monthly    nice run-parts /etc/cron.monthly

# period in days: anacron执行当前与时间戳（/var/spool/anacron内的时间记录文件）相差的天数，若超过此天数，就准备执行
# delay in minutes：如果要执行未能如期执行的任务，需要延迟多少分钟执行，以避免资源的冲突
# job-identifier：/var/log/cron中记录日志时任务的唯一标识符
# command：实际要执行的命令，run-parts后接目录，执行该目录中所有可执行文件

# 说明：
# nice命令：Run COMMAND with an adjusted niceness, which affects process scheduling. With no COMMAND, print the current niceness. Niceness values range from -20 (most favorable to the process) to 19 (least favorable to the process)
```

/var/spool/anacron/*：记录了各定时任务的执行时间

anacron的执行流程（cron.daily为例）：

- cron.daily任务执行天数为1
- 从/var/spool/anacron/cron.daily取出最近一次执行anacron的时间戳
- 上一步骤取出的时间与当前时间进行比较，如果差异的天数为1天以上（含1天），就准备执行命令
- 根据/etc/anacrontab配置文件中的RANDOM_DELAY、START_HOURS_RANGE、delay in minutes配置项决定任务的执行时间：开始执行的时间必须在START_HOURS_RANGE中，延迟的时间 = 固定延迟得到时间（delay in minutes）+ 随机延迟时间（RANDOM_DELAY）
- 开始执行配置的命令
- 程序结束

## 十六、进程管理与SELinux初探

### 进程

进程的定义：

- 进程是程序的动态执行

- 进程是程序和数据在处理机上顺序执行时所发生的的活动

- 进程是具有独立功能的程序在一个数据集合上运行的过程，是系统进行资源分配和调度的基本单位

- 进程是进程实体的运行过程

- 在Linux系统中，触发任何一个事件时，系统都会将它定义为一个进程，并且给予这个进程一个唯一标识符称为PID，同时根据触发这个进程的用户与相关属性关系，给予这个进程一组有效的权限设置

进程的特征：

- 动态性

- 并发性

- 独立性

- 异步性

进程的创建：

- init进程：系统启动时，内核只会建立init进程，剩下所有进程都是init进程通过fork机制创建的

- 进程PID：操作系统用来唯一标识不同进程的标识符，称为PID

- 父进程与子进程：父进程在执行命令或程序时，触发的新的进程是子进程吗，子进程会继承父进程的一些特性。子进程中会维护父进程的PID，称为PPID。使用ps -l命令可以看到某个进程的PPID

- fork and exec机制：
  - fork函数：创建一个子进程，该进程是父进程的一个副本，继承了父进程大部分特性
  - exec函数：提供了在一个进程中启动另一个程序执行的方法，根据指定的文件名或目录名找到可执行文件，将其加载入内存，并取代该进程的数据段、代码段和堆栈段，该进程除PID外的所有内容都被替换了
  - fork and exec机制：父进程执行某个命令或程序时，使用fork方式创建子进程，然后通过exec方式将该子进程更改为最终的子进程

进程的终止：

- 进程终止的方式：正常退出、异常退出、操作系统干预、父进程请求
- 孤儿进程：父进程先于子进程结束时，子进程会被过继给init进程
- 僵尸进程：子进程先于父进程结束，但父进程没有回收子进程资源，此时子进程就成为僵尸进程

守护进程：常驻内存持续不断执行的进程称为守护进程（daemon），服务就是通过守护进程实现的。常见的守护进程crond、atd、rsyslogd、apache、named、postfix、vsftpd、mysqld（一般守护进程都会以d结尾）

Linux多人多任务环境：

- 多人多任务：可以同时多个用户登录，CPU可以同时并发地处理多个任务

- 7个基本终端：6个命令行登录界面/dev/tty[1-6]，1个图形操作界面/dev/tty7

- `&`：置于命令最后，表示后台执行

### 任务管理

任务管理：登录系统并获取bash shell之后在单一终端下同时执行多个任务的操作管理

- 执行任务管理的操作中，每个任务都是目前bash的子进程，即彼此之间是有相关性的，无法使用任务管理的方式由tty1的环境去管理tty2的bash

- 前台与后台的区别：
  - 前台进程：能够与用户交互，独占终端并且受终端的控制，这样的进程称为前台进程。前台进程可以使用ctrl-c终止
  - 后台进程：不与用户交互，不占据终端并且不受终端控制的进程称为后台进程。可以使用bg、fg命令调用后台进程

`&`：直接将命令放在后台执行

```
COMMAND &

输出内容：
[1]+   Done    COMMAND
# 1表示的是任务编号
# +表示是最近被切换到后台执行
# Done表示任务已经完成,Stopped表示被挂起，Killed表示被终止，Running表示正在执行
# COMMAND则是具体的命令
```

注意，如果将命令放在后台执行，命令中的输出和错误输出内容依旧会被输出到屏幕上。因此需要将输出重定向至日志文件

```
COMMAND > LOG_FILE 2>&1 &
```

`ctrl-z`：将前台进程切换至后台执行并挂起

jobs：查看目前的后台任务状态

```
jobs [-lrs]
-l  除了job-number和command，还将pid列出来
-r  running only，仅列出后台正在运行的任务
-s  stopped only，仅列出后台挂起的任务

输出中的 + 表示最近被放到后台的任务，- 表示最近第二个被放到后台的任务
fg命令不指定任务时，默认选择带有 + 的任务
```

fg：将任务切换到前台执行（如果任务被挂起，继续执行）

```
fg              将最近的任务切换到前台执行，即带有 + 的任务
fg -            将最近第二个任务切换到前台执行，即带有 - 的任务
fg %jobnumber   将指定jobnumber的任务切换到前台执行
```

bg：将任务（前台或后台）切换到后台执行（如果任务被挂起，继续执行）

```
bg              将最近的任务切换到后台执行，即带有 + 的任务
bg -            将最近第二个任务切换到后台执行，即带有 - 的任务
bg %jobnumber   将指定jobnumber的任务切换到后台执行
```

kill：杀死进程或向进程发送信号（注意kill的manpage应该查man bash而不是man kill）

```
kill [-s sigspec|-n signum|-sigspec] [pid|jobspec]
kill -l [sigspec|exit_status]

sigspec 信号名称（SIG前缀可以省略）或signum
signum  信号值
jobspec 任务标识符，格式：%jobnum  %+ %-  （其他格式参考man bash，搜索jobspec）

-s  指定信号，后接sigspec
-n  指定信号，后接signum
-sigspec  指定信号，- 后直接跟sigspec
```

nohup命令：退出当前shell之后希望进程依旧执行下去，可以使用nohup命令

- 为什么要使用nohup：当用户注销或者网络断开时，终端会收到SIGHUP信号从而关闭其所有子进程。即用户准备退出session时，系统向该session发出SIGHUP信号，session将SIGHUP信号发送给所有子进程，子进程收到SIGHUP信号之后自动退出。nohup能够屏蔽SIGHUP信号，从而避免这一问题（引用自https://www.thisfaner.com/p/cron-nohup）

- nohup命令的特性：如果标准输入是一个终端，重定向至/dev/null；如果标准输出是一个终端，以附加方式重定向至~/nohup.out文件，如果想要重定向至其他文件请显式使用重定向符号

### 进程管理

1、查看进程

ps：process snapshot，报告当前进程的一个快照

```
ps aux  查看系统所有的进程
ps -lA  查看系统所有的进程
ps axjf 查看系统进程连同部分进程树
ps -l   仅查看自己的bash相关进程

选项与参数：
-A  所有的进程均显示出来，与-e具有相同的效果
-a  不显示与终端有关的所有进程
-u  有效使用者相关的进程
x   通常与a这个参数一起使用
l   较长、较详细地将该PID的信息列出
j   任务job的格式
-f  做一个更完整的输出
```

ps -l输出内容详解：
- F：与进程关联的标志位
  - 1：此子进程仅通过fork创建起来，但还没有执行exec
  - 4：此进程的权限为root
- S：此进程的状态（STAT）：
  - R：Running，进程正在执行或处于就绪队列中
  - S：Interruptible Sleep，可中断睡眠状态，可以被唤醒（signal）
  - D：Uninterruptible Sleep，不可中断睡眠状态，通常是在等待IO操作
  - T：Stopped by Job Control，被任务控制停止状态
  - t：Stopped by Debugger during Tracing，在追踪轨迹时被调试器停止执行
  - Z：Zombie Process，僵尸进程
- UID/PID/PPID：进程所属用户ID、进程ID和父进程ID
- C：CPU的使用率，单位为百分比
- PRI/NI:Rriority/Nice，进程优先级（不可调控，越小优先级越高）和NICE值（可调控，-20~19，越小优先级越高）
- ADDR/SZ/WCHAN：ADDR是内核函数（kernel function），指出该进程在内存的哪个部分，如果是正在运行的进程，一般显示【-】；SZ表示此进程用掉多少内存；WCHAN表示目前进程是否正在运行，若为【-】表示正在运行
- TTY：登录者的终端位置，如果是远程登录则使用动态终端接口名称（pts/n）
- TIME：使用的CPU时间，注意，是实际花费的CPU的运行时间，而不是系统时间
- CMD：就是command的缩写，表示造成此进程的触发进程的命令是什么

ps aux输出内容详解：

- USER：进程所属用户名称
- PID：进程ID
- %CPU：该进程使用的CPU资源的百分比
- %MEM：该进程所占用的物理内存的百分比
- VSZ：该进程使用的虚拟内存的量（KB）
- RSS：该进程所占用的固定内存量（KB）
- TTY：该进程在哪个终端上运行，若与终端无关则显示？
- STAT：进程目前的状态
- START：该进程被触发启动的时间
- TIME：该进程实际使用的CPU运行的时间
- COMMAND：该进程实际命令是什么

top：动态查看进程的变化

```
top [-d 数字]
top [-bnp]

-d  后接整个页面刷新的秒数。默认是5秒
-b  以批量的方式执行top，还有更多的参数可以使用，通常会搭配数据流重定向将批量的结果输出为文件
-n  与-b搭配，意义是，需要执行几次top的输出结果
-p  指定某些PID，查看这些进程的监测信息

在top执行过程中可以使用的按键命令：
?   显示在top中可以输入的按键命令
P   以CPU的使用排序
M   以MEMORY的使用排序
N   以PID进行排序
T   由进程使用的CPU累计时间排序（TIME+）
k   向某个PID对应的进程发送一个信号（signal）
r   某个PID对应进程重新指定NICE值
q   quit，退出top
```

显示的内容：

```
top - 19:07:27 up 312 days, 12:06,  1 user,  load average: 0.00, 0.02, 0.05
        (1)        (2)              (3)               (4)
Tasks: 2414 total,   1 running, 2413 sleeping,   0 stopped,   0 zombie              (5)
%Cpu(s):  0.2 us,  0.5 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st     (6)
KiB Mem : 12004088 total,  5225848 free,  3681732 used,  3096508 buff/cache         (7)
KiB Swap:  8388604 total,  6759904 free,  1628700 used.  7501376 avail Mem          (8)
PID    USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND        (9)
 37876 liangjh3  20   0  164484   4692   1584 R   1.6  0.0   0:02.48 top
199238 root      20   0  544436  40280   2476 S   1.0  0.3 442:05.30 titanagent
 11568 root      20   0 4670020 188236   7528 S   0.3  1.6  67:14.69 java
199240 root       9 -11   87708   1392    564 S   0.3  0.0  68:30.38 titan_monitor
     1 root      20   0  192996   4668   1504 S   0.0  0.0  46:39.00 systemd
     2 root      20   0       0      0      0 S   0.0  0.0   0:04.19 kthreadd
     4 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
     6 root      20   0       0      0      0 S   0.0  0.0   0:49.16 ksoftirqd/0

（1）当前的时间
（2）开机累计时间
（3）在线人数
（4）1、5、15分钟平均任务负载
（5）总进程数、正在运行进程数、睡眠进程数、暂停进程数、僵尸进程数
（6）CPU的整体负载：
      - us：未调整nice值的用户进程的CPU时间百分比
      - sy：运行内核进程（kernel process）的CPU时间百分比
      - ni：调整过nice值的用户进程的CPU时间百分比
      - id：CPU空闲时间百分比
      - wa：CPU等待IO操作完成的时间百分比
      - hi：CPU处理硬中断花费时间百分比
      - si：CPU处理软中断花费时间百分比
      - st：被强制等待虚拟CPU的时间百分比，此时Hypervisor在为另一个虚拟处理器服务
（7）物理内存的总量、空余量、使用量、缓冲/缓存（buffer/cache），默认的单位是KB
（8）交换空间（虚拟内存）的总量、空余量、使用量、，默认的单位是KB
（9）每个进程使用系统资源的情况
PID   每个进程的ID
USER  该进程所属的用户
PR    进程优先级（Priority），越小越先执行
NI    进程的NICE值，越小表示越优先
VIRT  虚拟内存的使用率
RES   常驻内存的大小，默认单位为KB
SHR   共享内存的大小，默认单位为KB
S     进程状态，D=不可唤醒睡眠，R=运行中，S=可唤醒睡眠，T=任务管理暂停执行，t=debugger调试暂停执行，Z=僵尸进程
%CPU  CPU的使用率百分比
%MEM  内存的使用率百分比
TIME+ CPU累计使用时间
```

> https://zhuanlan.zhihu.com/p/376734504

中断：CPU正常执行程序时，接收到来自CPU内部或外部的中断信号，转而执行中断处理程序，然后再继续执行当前程序，这一过程称为中断。
- 软中断与硬中断：按照中断实现的方式进行区分。硬中断是真正的中断，是CPU立即停止当前程序的执行，转而指向中断处理程序。中断处理程序必须小而短，以便减小对被中断执行程序的影响。软中断是在硬中断的基础上用来处理硬中断未完成的任务，以内核线程的方式执行，不是真正的中断。
- 内中断与外中断：按照中断源位于CPU内部和外部分为内中断与外中断。
- 可屏蔽中断和不可屏蔽中断：按照中断是否能够屏蔽分为可屏蔽中断和不可屏蔽中断。

操作命令：
```
方向键        移动光标，向左可以刷新页面
PGUP、PGDOWN  向上翻页向下翻页
HOME、END     行首行尾
Z   进入调整颜色页面
B   加粗
E   调整总结（summary）部分内存的单位（KB、MB、GB、TB、PB、EB）
e   调整任务（task）部分的内存单位（KB、MB、GB、TB、PB、EB）
l   切换load average这一行显示状态（显示、不显示）
t   切换Task和%Cpu这一行的显示状态（数据形式、进度条形式1、进度条形式2、不显示）
m   切换Mem和Swap这两行的显示状态（数据形式、进度条形式1、进度条形式2、不显示）
0   切换0值是否显示
1   切换%Cpu的显示模式为分CPU核心显示
2   切换%CPU的显示模式为分NUMA Node显示。所谓的NUMA Node就是把CPU核心分成若干组，每组放在一个Node中，每个Node具有独立的CPU核心、总线和内存。
3   切换%CPU的显示模式
I   是否启用Irix模式（至于Irix模式则不太清楚）
f/F 进入字段页面（可以进行添加删除字段、字段显示位置排序、字段值排序）
X   调整列宽
L   字符串查询
&   跳转到L查询的下一个结果
<   切换排序字段为上一个字段
>   切换排序字段为下一个字段
R   切换排序方向（增序、降序）
H   切换显示进程或线程（轻权进程）
V   切换为树形视图
J   数值对齐方式
c   切换显示完整的命令
i   切换显示空闲命令
S   切换累计时间模式。在累计时间模式下，进程TIME字段的计算会包括进程自身和已经死去的子进程
j   字符串对齐方式
x   切换排序列高亮显示
y   切换状态为运行的进程所在行高亮显示
z   切换x和y命令高亮显示的颜色
b   切换x和y命令高亮显示的字体粗细
u   按照有效用户（effective user）筛选
U   按照所有用户筛选
o   按照其他字段进行筛选（忽略大小写）
O   按照其他字段进行筛选（大小写敏感）
n或#调整分页大小
=   取消当前页的筛选条件
+   取消所有页的筛选条件
C   显示光标的位置
k   杀死进程或发送信号（如果反悔可按ESC，或者在选择PID时输入不存在的PID，或者在选择信号时输入0）
r   重新指定nice值（如果反悔可按ESC，或者在选择PID时输入不存在的PID，或者在选择nice值的时候不输入直接回车）
d/s 设置更新频率
q   退出

g   切换窗口 1-Def 2-Job 3-Mem 4-Usr
A   切换alternatice-display-mode，可以同时看四个窗口，该模式下按键的含义有变化
        -|_ 显示或隐藏窗口
        =|+ 清空当前页和全部页面的筛选条件
        g|G 切换当前窗口，g命令根据编号，G命令根据名称
        a|w 切换当前窗口为上一个和下一个
B   加粗的全局控制
W   写入配置
M   按内存排序
N   按PID排序
P   按%CPU排序
T   按TIME+排序
```

pstree：显示进程树
```
-A  ACSII字符绘制树干
-U  UNICODE字符绘制树干
-p  同时列出进程PID
-u  同时列出所属用户
```

信号：进程管理是通过信号进行的，信号是进程之间通信的一种方式。

```
常见的信号：
1  SIGHUP  启动被终止的进程
2  SIGINT  向进程发送中断信号
9  SIGKILL 强制中断一个进程的执行
15 SIGTERM 以正常的方式终止进程的执行
19 SIGSTOP 停止进程的执行
其他信号可以查看man 7 signal
```

2、杀死进程的命令：
```
kill    杀死给定PID的进程
killall 根据给定的名称查询进程，然后再决定是否杀死进程
```

3、进程的执行顺序：

进程优先级（PRI，priority）：越小优先级越高，内核动态调整，用户无法直接修改

NICE值：-20至19，用户可调整，PRI会受到NICE值的影响，负值表示增加优先级，正值表示减少优先级。NICE还会在父子进程之间传递

nice：指定NICE值

```
nice [-n NICE值] COMMAND
```

renice：重新指定进程的NICE值

```
renice [NICE值] PID
```

4、查看系统资源信息

free：查看内存使用情况

```
free [-b|-k|-m|-g|-h] [-t] [-s N -c N]
-b -k -m -g		指定显示的单位是B、KB、MB、GB
-h			    采用human-readable方式显示内存的单位
-t			    在输出的最终结果中显示物理内存和swap的总量
-s			    后接秒数，表示每隔若干秒列出一次内存信息
-c			    后接次数，与-s同时处理，限制刷新的次数
```
uname：查看系统与内核相关信息

```
uname [options]
-a	所有与系统相关信息，包括但不限于下述的信息
-s	系统内核名称
-r	内核的版本（kernel-release）
-v	内核的版本（kernel-version）
-m	本系统的硬件架构
-p	CPU的类型
-i	硬件的平台
-n	节点名称
-o	操作系统
```

uptime：查看系统启动时间与CPU平均负载（1、5、15分钟）

netstat：追踪网络或socket文件

```
netstat [-atunlp]
-a	将目前系统上所有的连接、监听、socket信息都列出来
-t	列出TCP网络封包的信息
-u	列出UDP网络封包的信息
-n	使用进程的端口号替代进程的服务名称
-l	列出目前正在网络监听的服务
-p	列出目前网络服务的进程PID
-o	包括与网络定时器有关的信息
```

net-tools逐渐被ipoute代替

|用途|net-tools|ipoute|
|--|--|--|
|地址和路由链路|ifconcfg|ip addr、iplink|
|路由表|route|ip route|
|邻居|arp|ip neigh|
|VLAN|vconfig|ip link|
|隧道|iptunnel|ip tunnel|
|组播|ipmaddr|ip maddr|
|统计|netstat|ss|

ip：显示/操纵路由（routing）、设备（devices）、路由策略（poliicy routing）和隧道（tunnels）
```
ip [options] object {command|help}
ip [-force] -batch filename
object := link|address|addrlabel|route|rule|...
options:
-V	打印ip工具的版本
-h	human-readable方式打印
-b	从文件或标准输入中读取命令并执行
-force	在batch模式下，镇压所有异常，强制执行后面的命令
-s	输出更多信息。如果选项出现多次，那么会输出更详细的信息
-d	输出更多细节信息
-l	指定‘ip address flush’命令将会尝试的循环次数
-f	后接使用的协议家族，包括inet、inet6、bridge、ipx、dnet、mpls、link
-4	相当于-f inet
-6	相当于-f inet6
-o	一行输出一个记录
-r	使用系统的名称代替主机地址打印DNS名称
-n	后接网络命名空间。
		使用该选项可以将
			ip netns exec NETNS ip [OPTIONS] OBJECT {COMMAND|help}
		简化为
			ip -n NETNS [OPTIONS] OBJECT {COMMAND|help}
-a	在所有的对象（objects）上执行指定的命令
-t	显示当前的时间
-ts	类似于-t，但是使用更短的格式
-rc	设置网络连接socket接收的buffer大小

OBJECT：
address		某台设备上的协议地址
addrlabel	选中的协议地址标签配置
l2tp		在IP协议上运行的隧道以太网
link		网络设备
maddress	组播地址
monitor		监控网络连接信息
mroute		组播路由缓存条目
mrule		组播路由策略数据库中的规则
neighbour	管理ARP或者NDISC缓存条目
netns		管理网络命名空间
ntable		管理邻居缓存操作
route		路由表条目
rule		路由策略数据库中的规则
tcp_metrics/tcpmetrics
			管理TCP条目
token		管理令牌化的接口标识符
tunnel		运行在IP协议上的隧道
tuntap		管理TUN/TAP设备
xfrm		管理IP规程策略

COMMAND：
add			添加一个条目
delete		删除一个条目
show/list	显示条目内容

常见的用法：
1、检查网卡信息
ip address show
= ip a s

2、启用/禁用网卡
ip link set 网卡 up
ip link set 网卡 down

3、为网卡分配IP地址或其他网络信息
ip addr add ip地址/子网掩码 dev 网卡
ip addr add broadcast 广播地址 dev 网卡
ip addr add ip地址/网络前缀位数 brd + dev 网卡

4、删除网卡中配置的IP地址
ip addr del ip地址/网络前缀位数 dev 网卡

5、为网卡添加别名
ip addr add ip地址/网络前缀位数 dev 网卡 label 别名

6、查看路由信息
ip route show
ip route get 目标IP

7、添加静态路由
ip route add default via ip地址/网络前缀位数	# 修改默认路由
ip route add 目标ip via ip地址/网络前缀位数 dev 网卡 # 修改某个网卡的路由

8、删除默认路由
ip route del ip地址/网络前缀位数

9、检查所有的ARP记录
ip neigh

10、修改ARP记录
ip neigh del ip地址 dev 网卡
ip neigh add ip地址 lladdr MAC地址 dev 网卡 nud perm

11、查看网络统计信息
ip -s link
```

ss：socket statistics，查看socket的另外一个命令

```
ss [options] [FILTER]
-H	不要header部分
-n	不尝试去解析服务名称
-r	尝试去解析服务名称
-a	所有监听和非监听（listening & non-listening）socket
-o	查看定时器（timer）信息
-e	展示详细的socket信息
-m	展示socket使用的内存大小
-p	展示使用socket的进程
-i	展示内部的（internal）TCP信息
-K	试图强制关闭socket连接
-s	打印概要信息
-N	切换到指定的网络命名空间
-b	显示socket BPF过滤器
-4	显示IPv4
-6	显示IPv6
-0	显示PACKET sockets
-t	显示tcp sockets
-u	显示udp sockets
-d	显示dccp sockets
-w	显示原生的socket
-x	显示unix domain socket
-s	显示sctp sockets
-f	后接协议族名称，包括unix、inet、inet6、link、netlink、vsock
-A	后接查询条件，包括all、inet、tcp、udp、raw、unix、packet、netlink、unix_dgram、unix_stream等等
-D	不显示任何信息，输出至文件
-F	从文件中读取过滤信息

FILTER：
established syn-sent syn-recv fin-wait-1 fin-wait-2 time-wait closed close-wait listen closing
all: 匹配所有状态
connected：除listen和closed外的其他状态
synchronized：除syn-sent外所有connected状态
bucket：被当作minisocket维护的状态，例如time-wait和sync-recv
big：bucket的反义词

常用选项：
1、列出已建立的连接
ss

2、显示正在监听的套接字
ss -lt
-l列出了正在监听的套接字，-t则表示只列出tcp连接

3、显示进程信息
ss -lp
-p选项列出了拥有套接字的进程或pid号

4、不解析服务名称
默认情况下ss会解析端口号，并替换为服务名称，如果想要看到端口号，就需要使用-n选项
ss -ln

5、解析IP地址和端口号
使用-r选项可以将ip地址和端口号解析为主机名和服务名
ss -r

6、只显示ipv4、ipv6、tcp、udp相关的连接
使用-4、-6、-t、-u选项
ss -4
ss -6
ss -t
ss -u

7、显示unix域套接字
ss -x

8、显示所有的套接字
包括监听和非监听套接字

查看所有的udp套接字
ss -ua
查看所有的tcp套接字
ss -ta

9、显示套接字内存使用情况
使用-m选项
ss -ltm

10、显示TCP内部信息
可以使用-i选项请求额外的内部TCP信息
ss -lti
一般情况下应该配合-t选项使用，如果指定-u选项则不会打印出多余的信息

11、显示统计信息
ss -s

12、基于状态的过滤器

常见的套接字的状态：established、syn-sent、syn-recv、fin-wait-1、fin-wait-2、time-wait、closed、closed-wait、last-ack

ss -t state established

tcp三次握手：
1、客户端：CLOSED —> SYN-SENT -> ESTABLISHED
2、服务端：LISTEN -> SYN-RECV -> ESTABLISHED
3、三次握手：
（1）客户端向服务端发送请求，SYN=1，seq=x
（2）服务端确认请求，SYN=1，ACK=1，seq=y，ack=x+1
（3）客户端确认，ACK=1，seq=x+1，ack=y+1

tcp四次挥手：
1、客户端：ESTABLISHED —> FIN-WAIT-1 -> FIN-WAIT-2 -> TIME-WAIT -> CLOSED
2、服务端：ESTABLISHED -> CLOSE-WAIT -> LAST-ACK -> CLOSED
3、四次挥手：
（1）客户端主动关闭，进入FIN-WAIT-1状态，发送释放连接请求，FIN=1，seq=u
（2）服务端收到请求，进入关闭等待状态，发送确认，ACK=1，seq=v，ack=u+1，服务端在关闭等待状态时仍然可以发送数据给客户端；客户端收到确认后进入FIN-WAIT-2状态
（3）数据发送完毕之后服务端被动关闭，进入LAST-ACK状态，发送确认，FIN=1，ACK=1，seq=w，ack=u+1
（4）客户端收到最终的确认之后，发送确认，ACK=1，seq=u+1,ack=w+1,进入TIME-WAIT状态，2MSL之后进入关闭状态;服务端接收到确认之后立即进入关闭状态。

为什么是三次握手：让双方都明白各自的收发是正常的，防止失效的连接请求到达服务器

13、根据端口号进行过滤

根据端口号过滤端口号小于等于500的端口
ss -ltn sport le 500

其他的关系运算符：lt、gt、eq、ne、le、ge

还可以根据源或者目标端口进行筛选，搜索具有ssh源端口运行的tcp套接字
ssh -t '( sport = :ssh)'

14、显示套接字的SELinux上下文
使用-Z或-z选项

15、其他
显示版本号 -v
显示帮助信息 -h
显示扩展信息 -e
显示计时器信息 -o
```

- socket：网络上不同主机之间进程通信端点的抽象

dmesg：分析内核产生的信息

```sh
dmesg|less
dmesg|grep -i vda
```

vmstat：检测系统资源变化

```
reports information about processes, memory, paging, block IO, traps, disks and cpu activity.
The first report produced gives averges since the last reboot.
Additional reports give information on a sampling period of length delay

CPU、内存等信息
vmstat -a [延迟 [总计检测次数]]
内存相关信息
vmstat -fs
设置显示数据的单位
vmstat -S
与磁盘和分区有关的信息
vmstat -d
vmstat -p 分区

选项与参数
-a	使用inactive/active替换buffer/cache的内存输出信息
-f	开机到目前为止，系统fork的进程数

统计目前主机CPU状态，每秒一次，共计3次
vmstat 1 3

统计目前主机CPU状态，每秒1次，次数无限制，直至按下ctrl-c
vmstat 1

统计表格的字段：
Procs: 
	r表示等待运行中的进程数量
	b表示不可被唤醒的进程数量
	这里两个项目越多表示系统越忙碌
Memory：
	swpd表示虚拟内存使用量（KB）
	free表示未被使用的内存容量（KB）
	buffer表示用于缓存的内存容量（KB）
	cache表示用于高速缓存的容量（KB）
	inact表示非活动状态的内存容量（KB）
	active表示活动状态的内存容量（KB）
Swap：
	si表示从磁盘被交换进内存的容量（/s)
	so表示从内存交换至磁盘的 容量（/s)
IO：
	bi表示接收到来自磁盘设备的区块数量（blocks/s）
	bo表示发送到磁盘设备的区块数量（blocks/s）
System：
	in表示每秒钟每中断的进程数，包括clock
	cs表示每秒执行的事件切换次数
	这两个参数表明了系统与外设沟通的频繁程度
Cpu：
	us（user time）：非内核进程消耗CPU时间的百分比
	sy（system time）：内核进程消耗CPU时间的百分比
	id（idle time）：CPU空闲时间的百分比
	wa（wait time）：等待IO消耗的CPU时间百分比
	st（stolen time)：被虚拟机盗用的CPU时间百分比
	
```

### 特殊文件与进程

SUID权限的特点：

- 仅对二进制程序有效
- 执行者需要具有x权限
- SUID权限只在执行该程序的过程中有效
- 执行者将具有该程序拥有者的权限

/proc目录表示的意义：

- /proc/buddyinfo 每个内存区中的每个order有多少块可用，和内存碎片问题有关

- /proc/cmdline 启动时传递给kernel的参数信息

- /proc/cpuinfo cpu的信息

- /proc/crypto 内核使用的所有已安装的加密密码及细节

- /proc/devices 已经加载的设备并分类
- /proc/dma 已注册使用的ISA DMA频道列表

- /proc/execdomains Linux内核当前支持的execution domains

- /proc/fb 帧缓冲设备列表，包括数量和控制它的驱动

- /proc/filesystems 内核当前支持的文件系统类型

- /proc/interrupts x86架构中的每个IRQ中断数

- /proc/iomem 每个物理设备当前在系统内存中的映射

- /proc/ioports 一个设备的输入输出所使用的注册端口范围

- /proc/kcore 代表系统的物理内存，存储为核心文件格式，里边显示的是字节数，等于RAM大小加上4kb

- /proc/kmsg 记录内核生成的信息，可以通过/sbin/klogd或/bin/dmesg来处理

- /proc/loadavg 根据过去一段时间内CPU和IO的状态得出的负载状态，与uptime命令有关

- /proc/locks 内核锁住的文件列表

- /proc/mdstat 多硬盘，RAID配置信息(md=multiple disks)

- /proc/meminfo RAM使用的相关信息

- /proc/misc 其他的主要设备(设备号为10)上注册的驱动

- /proc/modules 所有加载到内核的模块列表

- /proc/mounts 系统中使用的所有挂载

- /proc/mtrr 系统使用的Memory Type Range Registers (MTRRs)

- /proc/partitions 分区中的块分配信息

- /proc/pci 系统中的PCI设备列表

- /proc/slabinfo 系统中所有活动的 slab 缓存信息

- /proc/stat 所有的CPU活动信息

- /proc/sysrq-trigger 使用echo命令来写这个文件的时候，远程root用户可以执行大多数的系统请求关键命令，就好像在本地终端执行一样。要写入这个文件，需要把/proc/sys/kernel/sysrq不能设置为0。这个文件对root也是不可读的

- /proc/uptime 系统已经运行了多久

- /proc/swaps 交换空间的使用情况

- /proc/version Linux内核版本和gcc版本

- /proc/bus 系统总线(Bus)信息，例如pci/usb等

- /proc/driver 驱动信息

- /proc/fs 文件系统信息

- /proc/ide ide设备信息

- /proc/irq 中断请求设备信息

- /proc/net 网卡设备信息

- /proc/scsi scsi设备信息

- /proc/tty tty设备信息

- /proc/net/dev 显示网络适配器及统计信息

- /proc/vmstat 虚拟内存统计信息

- /proc/vmcore 内核panic时的内存映像

- /proc/diskstats 取得磁盘信息

- /proc/schedstat kernel调度器的统计信息

- /proc/zoneinfo 显示内存空间的统计信息，对分析虚拟内存行为很有用

以下是/proc目录中进程N的信息

- /proc/N pid为N的进程信息

- /proc/N/cmdline 进程启动命令

- /proc/N/cwd 链接到进程当前工作目录

- /proc/N/environ 进程环境变量列表

- /proc/N/exe 链接到进程的执行命令文件

- /proc/N/fd 包含进程相关的所有的文件描述符

- /proc/N/maps 与进程相关的内存映射信息

- /proc/N/mem 指代进程持有的内存，不可读

- /proc/N/root 链接到进程的根目录

- /proc/N/stat 进程的状态

- /proc/N/statm 进程使用的内存的状态

- /proc/N/status 进程状态信息，比stat/statm更具可读性

- /proc/self 链接到当前正在运行的进程

查询已占用文件或进程使用的文件：

fuser：找出占用文件的进程

```
fuser [-umv] [-k [i] [-signal]] file/dir

-u	列出进程的属主
-m	后接已挂载文件系统或设备的挂载点，查询所有访问该文件系统或设备的进程
-v	使用类似ps命令输出格式展示占用文件的所有进程
-k	向查询出来的进程发送SIGKILL(-9)信号
-i	与-k选项配合使用，在发送SIGKILL信号前询问
-signal 与-k配合使用，指定发送的信号

ACCESS项目：
c	此进程在当前的目录下（非子目录）
e	可被触发为执行状态
f	是一个被开启的文件
r	代表顶层目录
F	是一个开启的文件，等待写入
m	mmap’ed文件或共享的库

找到所有使用文件系统的进程
fuser -uv /proc
```

lsof：列出被进程使用的所有文件名称

```
lsof [-aUu] [+d]

-a	结果进行“与”运算而不是“或”
-I	在输出显示用户ID而不是用户名
-U	仅列出Unix-like系统的socket文件类型
-u	后面接用户名，列出指定用户相关进程所使用的文件
+d	后面接目录，表示找出某个目录下面已经被使用的文件

列出系统上所有被打开的文件或设备
lsof

仅列出root用户所有进程使用的socket文件
lsof -u root -a -U

列出目前系统上所有被使用的socket文件
lsof +d /dev

显示属于root用户bash程序开启的文件
lsof -u root|grep bash
```

pidof：找出某个正在执行的进程的PID

```
pidof [-sx] 程序名

-s	仅列出一个pid而不是所有的pid
-x	同时列出该程序名
```

### SELinux

SELinux = Security Enhanced Linux，指安全强化的Linux之意

SELinux是在进行进程、文件等详细权限配置时依据的一个内核模块，因为启动网络服务的也是进程，因此也成为控制网络服务能否读取系统资源的一道关卡。

1、自主访问控制（Discretionary Access Control）

DAC：依据进程的拥有者与文件资源的读、写、执行权限来决定有无读写的权限

DAC的缺陷：

- root具有最高的权限，不受控制
- 用户可以获取进程来修改文件资源的访问权限

2、强制访问控制（MAC，Mandatory Access Control）

MAC：针对特定的进程和特定的文件资源来管理权限，即便是root用户针对特定进程和文件资源的权限也可能不同

策略：进程针对不同的用户和不同的文件资源设置的权限规则（Rule）的集合就是策略（Policy）

DAC与MAC的区别就是同一用户启动的不同进程对同一文件资源的访问权限不同



SELinux的运行模式：

- 主体（Subject）：进程
- 目标（Object）

## 附录一：awk程序设计

1、Getting Started with awk

1.1 运行一个AWK程序

awk概念：

- search files for lines that contain certain patterns.
- perform specified actions on lines.
- process input lines until it reaches the end of input files

规则：一个awk程序包含很多规则（rules，也可能包含函数定义（function definition））

- 规则（rule） = 模式（pattern）+ 行为（action）

```awk
pattern { action }
```

运行一个AWK程序：

（1）命令行方式执行

```sh
awk 'program' input-file1 input-file2 ...
awk -f program-file input-file1 input-file2 ...
```

awk的helloworld：

```sh
awk 'BEGIN { print "hello world!" }'
# awk在读取输入之前先执行BEGIN关联的语句
# 如果程序中没有其他语句，awk将会停止读取输入，直接退出程序

# 与单纯执行cat无带参数有同样效果
awk '{ print }'

```

（2）awk脚本

```awk
#! /bin/awk -f
BEGIN { print "hello world" }
```

在linux脚本中，第一行的#!用来设置脚本的解释器及命令行参数，awk命令所在位置因系统而异

注释：#

引号：

- 使用命令行执行awk程序时，最好使用单引号将程序包围起来

- 注意命令行中单引号和双引号的使用，以及转义字符的使用

一些简单的示例：

```sh
# 查找匹配正则表达式的行，并将其打印出来
/li/ {print $0}

# pattern和action可以省略，但不能同时省略
# 如果pattern被省略了，则匹配所有行
# 如果action被省略了，则默认打印匹配的行

# print every line that is longer then 80 characters
awk 'length($0) > 80' data

# print the length of the longest input line
awk '{ if (length($0) > max) max = length($0)} END {print max}' data

# print the length of the longest line in data
expand data | awk '{ if (x < length($0)) x = length($0)} END {print "maximum line length is " x}'

# print every line that has at least one field
awk 'NF > 0' data

# print seven random numbers from 0 to 100, inclusive
awk 'BEGIN { for (i = 1; i <= 7; i++) print int(101 * rand())}' data

# print total number of bytes used by files
ls -l files|awk '{ x += $5 } END { print "total bytes: " x }'

# print total number of kilobytes used by files
ls -l files|awk '{ x += $5 } END { print "total kilobytes：" x / 1024}'

# print a sorted list of the login names of all users:
awk -F: '{ print $1 }' /etc/passwd | sort

# count the lines in a file
awk 'END { print NR }' data

# print the even-numbered lines in the data file
awk 'NR % 2 == 0' data
```

包含两条规则（rules）的示例：

```awk
# 这两条规则顺序执行
/12/ { print $0 }
/21/ { print $0 }
```

更复杂的示例：

```sh
ls -l| awk '$6 == "NOV" { sum += $5 }
            END { print sum }'
```

换行符：
- awk程序一般一行一条语句或一个规则，除以下情况换行符被认为是一条语句的结束
- `, { ? : || && do else` 之后换行符会被忽略
- 如果一条语句跨越了多行可以使用 `\` 进行拆分
- 如果一行要写多条语句，使用 `;`

其他特性：
- 预定义、内置变量
- 内置函数

2、运行awk和gawk

```
awk [OPTIONS] -f program-file [--] file ...
awk [OPTIONS] [--] 'program' file ...

-F fs           设置FS变量，该变量用来指定列分割符
-f source-file  读取awk程序文件，而不是把第一个非选项参数作为awk程序
-v var=val      设置变量var的值为val，这样的变量可以在BEGIN规则中访问；一个-v设置一个变量，设置多个变量使用多个-v
-W gawk-opt     gawk专用选项
--              指示选项的边界，如果文件名是以-开头的，这个标志就很有用

gawk专用选项（略）：

-b  把输入输出的数据都按照单字节字符对待
-c  指定兼容模式，在这种模式下GNU针对awk的扩展将被禁用
-C  打印简短的GPL版本信息并退出
-d[file]
    打印排好序的全局变量的列表、它们的类型以及最终的值。如果file参数没有被提供，那么打印列表到名称为awkvars.out的文件中。注意，-d和file之间没有间隔.
-D[file]
    启用awk程序的debug。默认情况下。调试器（debugger）从键盘交互式地读取命令。可选的file参数用于重定向标准输入，以便非交互式地执行
-e  program-text
    提供程序源代码，这个选项允许你混合文件中的源代码和你在命令行输入的源代码。这个选项尤其适用于你有想要从命令行程序中使用的库函数
-E file
    类似于-f选项，从文件中读取awk程序，有两点区别于-f选项：1）这个选项会中止选项的处理，命令行之上的任何其他东西都会被直接传递给awk程序；2）命令行变量赋值【var=value】不被允许
-g  分析源代码程序，生成GNU gettext可移植模板文件到标准输出，针对所有被标记翻译的字符串常量
-h  帮助信息
-i  source-file
    从source-file文件中读取awk源代码库。等价于在代码中使用@include。类似于-f选项，但是有两个区别：1）使用-i不会发生重复加载的问题，如果已经加载过就不再加载了，但是-f始终会加载；2）因为这个选项是用来加载库文件的，所以这些文件不会被gawk当作组成主程序来输入。-i选项指定的文件会被当作在他们的开头好像存在这`@namespace "awk"`一样
-I  打印内部的字节码
-l ext
    加载一个称为ext的动态扩展
-L[value]
    提供关于有隐患或不可移植到其他awk实现的构建方面的警告信息
-M  选择在数值上使用高精度算术运算
-n  启用输入数据中八进制和十六进制数的自动解释
-N  使用本土化的小数点
-o[file]
    启用awk程序的美化打印（pretty-printing），默认情况下创建文件awkprof.out
-O  开启gawk在程序内部表达上的默认优化
-p[file]
    开启awk程序的profiling
-P  在严格的POSIX模式下操作
-r  允许interval expressions
-s  禁用gawk在程序内部表达上的默认优化
-S  沙盒模式
-t  不兼容旧版本awk时警告
-V  版本信息
--  标记最后参数
```

在兼容模式下，只要程序文本（program text）被提供，其他任何选项都将被标记为无效，并且伴随警告信息；在兼容模式下，作为一种特殊情况，-F的选项的参数是t，则FS变量赋值为\t

-f选项可以多次使用，然后所有的输入将被整合在一起。这对于创建库函数非常有用。

如果没有指定-f选项，那么第一个非选项参数就是程序源代码，其余非选项参数作为ARGV数组的元素

如果POSIXLY_CORRECT环境变量存在，gawk将会在严格的POSIX模式下运行


































































