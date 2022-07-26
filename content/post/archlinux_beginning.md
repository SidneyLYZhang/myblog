---
title: "Arch Linux 安装笔记"
date: 2020-01-16T17:16:21+08:00
lastmod: 2020-02-05T17:35:21+08:00
draft: false
keywords: ["arch", "linux", "Learning", "install"]
description: "安装archlinux..."
tags: ["学习", "笔记"]
categories: ["笔记","学习","archlinux"]
author: "Sidney Zhang"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: true
reward: false
mathjax: true
mathjaxEnableSingleDollar: true
---

Update: 2020-09-24

### 废话在前

真的是人生苦短……但我用不了Manjaro……

Manjaro，公司这台老爷机真的是装不上……只好退而求其次，安装Arch Linux吧……

最早以前还在上学的时候开始接触Ubuntu，感觉到那种拖拉机感的沉重后，开始用Fedora，用到自己觉得没意思了，虽然很方便，可还是想更有适合我的使用一些。所以，开始用Manjaro。

最厉害的时候还全系统制霸过……（win、mac、linux、[学校的]unix）

现在，公司电脑就是用不了Manjaro，打死也安装不上，只好一切从源头开始，用Arch Linux吧……

我也是头一遭安装Arch Linux。没想到，还挺简单的。真的是没想到……

安装的基本流程是：

1. 准备
2. 分区
3. 安装
4. 配置
5. 扩展

这五步，就是后续的全部内容的总结。有什么问题还请 <a href="mailto:zly@lyzhang.me">Email</a> 我改正，如果是希望我帮忙装机的请绕路。

### 1 . 准备

准备从心理建设开始。我确实一开始被大家说的，Arch有多难以安装所吓到。但是，这个复杂程度也并没有超过写脚本代码，所以如果你觉得自己动手能力和处理问题的能力都还可以，那么请放心食用Arch Linux吧。过于担心安装的难度，反而会造成很多无谓的忧心，也不用害怕安装错误，错误在所难免，平静对待、仔细处理就好了。

下载Arch Linux，我使用的 [USTC](https://mirrors.ustc.edu.cn/archlinux/iso/2020.01.01/) 的镜像进行下载，当然，你也可以使用自己喜欢的镜像源，Arch厉害的地方就是它的[下载页面](https://www.archlinux.org/download/)好全，好方便！

选择形式 `archlinux-yyyy.mm.dd-x86_64.iso` 如此的那个文件下载，当然，使用 `archlinux-yyyy.mm.dd-x86_64.iso.torrent` 下载也完全可以。总之要得到iso镜像文件。当然，还需要准备一个U盘，最好大于4G。

还有一个必须的准备就是制作安装盘的工具了。我这次在windows上制作的安装盘，当然也可以在其他系统上制作。

__Windows__ ：

一开始使用[Rufus](https://rufus.ie/)，但是失败，启动时BIOS无法找到有效的启动项，也是因为我用的U盘是32G的，不想太浪费，就像只用一部空间用作Arch Linux的安装，结果就出错了。之后重新用 [USBWriter](https://sourceforge.net/p/usbwriter/wiki/Documentation/) 傻瓜式制作了一下安装盘就成功了。所以，还是越简单的制作约不容易出现问题。

__Linux__ :

使用 `dd` 命令制作就可以，基本形式如下：

```bash
$ dd bs=4M if=path/to/archlinux.iso of=/dev/sdx status=progress oflag=sync
```

基本不会不成功，只要iso镜像位置和U盘磁盘正确就可以。

其它系统制作我不曾试过，所以请参考 [官方启动盘制作的说明](https://wiki.archlinux.org/index.php/USB_flash_installation_media) 。

准备的最后一个环节，其实就是对安装盘的准备。如果是在全新硬盘上安装，那么不需要什么特殊的后续处理了，去安装吧。如果你和我一样，是电脑上已有系统了，需要安装双系统，则还需要一些磁盘空间的准备：

公司电脑使用的是已不再被官方支持的Windows7，不过基本操作流程是一样。

- 最简单的一种处理：腾空一个Windows磁盘，在[磁盘管理工具](https://docs.microsoft.com/zh-cn/windows-server/storage/disk-management/overview-of-disk-management)中，“删除卷”就可以了。
- 复杂一些的就是需要无损分割一部分磁盘用来安装，这是一般就需要用到压缩卷的功能，或者更专业的磁盘工具，比如： [DiskGenius](http://www.diskgenius.cn/) 、 [diskpart](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart) 等等。

最终，都会准备出一块已经从windows删除的磁盘空间，这个空间就是未来Arch Linux的存储空间了。我的感觉是，这个空间最好大于40G，才不至于使用时捉襟见肘。

### 2 . 分区

这个分区不是从WIndows里面划出一块空间给Linux的意思。是Linux本身的磁盘管理问题。即，需要熟悉Linux的磁盘管理模式。这里不科普，就是记录一下基本的操作和对应笔记，所以对于Linux的磁盘管理可以参阅如下文档：

- [Linux文件系统剖析](https://www.ibm.com/developerworks/cn/linux/l-linux-filesystem/index.html)
- [Linux磁盘管理](https://www.runoob.com/linux/linux-filesystem.html)
- [分区](https://wiki.archlinux.org/index.php/Partitioning)
- [★磁盘系统](https://wiki.archlinux.org/index.php/Device_file#Block_devices)
- [★文件系统（ArchLinux）](https://wiki.archlinux.org/index.php/File_systems)
- [★GRUB](https://wiki.archlinux.org/index.php/GRUB)

上一节中已经准备好了安装空间。对于怎么安装，其实就是把准备的空间格式化为Linux可使用的磁盘。这一过程分为两个步骤：

1. 划分磁盘
2. 创建文件系统

与Windows不一样的地方是，Linux可以把划分与文件系统分开处理。但Windows一般需要一次性确定好。Arch的操作更细致，到可以反过来帮助我们立即额分区与文件系统的关系。

#### 划分磁盘

一般根据Linux的系统目录结构，很多人会推荐如下划分方式：

    - `/` ：根目录，存放系统大部分文件的地方，一般建议20G以上的空间
    - `/boot` ：启动文件保存的地方，一般建议都是300M左右的大小
    - `/home` ：存放个人物件的地方，重装系统可以更快的转换到自己的系统配置上，也可以更安全的保存个人文件，一般建议20G以上
    - `swap` ：交换分区，一般建议和内存一样大即可

不过，如果你的硬盘没有很多分区的话，可以这么详细的划分分区倒是很有帮助。一般就可以简单一些，我觉得是使用 `/` 和 `/home` 两个分区就可以了。就算只有一个根目录分区也完全可以，在操作上并没有什么不可以。

特别的一点是交换空间（Swap）。Linux支持交换分区和交换文件，两种类型的交换空间。交换分区是专门用于交换的一个磁盘空间，交换文件是特殊的文件，介于系统文件与数据文件之间（[more](https://www.linux.com/news/all-about-linux-swap-space/)）。从我的感受来看，对磁盘使用效率来说，交换文件要远好于交换分区。

分区的基本工具要基于 `fdisk` 命令行工具。

由于公司电脑实在垃圾，前任所有者堆砌了很多逻辑分区而非主分区，虽然我已经整合了一些分区，消减了一些逻辑磁盘，但是因为磁盘内容众多，无法完全消除逻辑磁盘（分区号占用的也很诡异），这也是导致基于Manjaro安装程序无法安装的主要原因。我在安装Arch Linux的时候，就简单一些，只使用一个分区，避免分区表过复杂。

可以先，简单查检一下当前的磁盘状态（下示分好分区的样子）：

```bash
# fdisk -l

Disk /dev/sda: 931.53 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: ST31000524AS
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x05ce400c

Device     Boot      Start        End   Sectors   Size Id Type
/dev/sda1  *          2048     206847    204800   100M ef EFI (FAT-12/16/32)
/dev/sda2           206848  490706943 490500096 233.9G  7 HPFS/NTFS/exFAT
/dev/sda3       1104619457 1953521071 848901615 404.8G  f W95 Ext'd (LBA)
/dev/sda4        490706944 1104619400 613912457 292.8G 83 Linux
/dev/sda5       1104619520 1526505471 421885952 201.2G  7 HPFS/NTFS/exFAT
/dev/sda6       1526507520 1953521071 427013552 203.6G  7 HPFS/NTFS/exFAT

Partition table entries are not in disk order.

```

查看实际磁盘情况，主要关注这样一个问题，就是各个分区之间的差异，即关注每个磁盘的“End”与其后分区的“Start”之间的差，如果差的很大，那就是现在需要分区的区域了。

进入分区程序：

```bash
# fdisk /dev/sda
```

进入分区程序后，按照提示，进行分区操作。需要注意的是，选择分区范围的时候，要仔细选好预计分区的"start-end"的范围，尤其是预留的磁盘空间在某几个分区中间的时候，end的位置没选好，就会出现覆盖既有分区的情况，即误删分区。分区的详细指导，可以参考 [Viseator](https://www.viseator.com/2017/05/17/arch_install/) 的介绍。

fdisk的 [基本帮助](https://wiki.archlinux.org/index.php/Fdisk#Create_a_partition_table_and_partitions) 情况（已安装好Arch后的情况）：

```bash
# fdisk /dev/sda

Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table

```

所以一般的安装流程就是：“fdisk \dev\sdX => n => p => start位置 => End位置 => y => w”，需要创建多个分区就是多次新键分区“n”。

#### 创建文件系统

完成分区之后，就需要创建文件系统了。我在公司这里选择的是常用的 ext4 文件系统，因为公司硬盘是普通的磁盘式硬盘，如果是SSD硬盘，我更推荐使用针对性的 [F2FS](https://wiki.archlinux.org/index.php/F2FS) 文件系统。

对刚刚获得的新分区“sda4”（每个人的sda4不同，请根据自己的实际情况，参考前述磁盘系统的文章）进行文件系统初始化：

```bash
# mkfs.ext4 /dev/sda4
```

使用mkfs的中间需要人工确认一下，这个操作也并不复杂（mkfs的详细说明，请参看[mkfs官方文档](https://jlk.fjfi.cvut.cz/arch/manpages/man/mkfs.8)）。使用 `lsblk -f` 查验分区的文件系统。

```bash
$ lsblk -f
NAME   FSTYPE LABEL  UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda
├─sda1 ntfs   SYSTEM F2B83324B832E6AF
├─sda2 ntfs          44F8C695F8C68520
├─sda3
├─sda4 ext4          5aa92175-db0a-4144-a747-3011df03ebce    260G     4% /boot
├─sda5 ntfs   新加卷 2412E73312E7091E
└─sda6 ntfs   新加卷 866ACC646ACC531B
sr0
```

总之，分区就是划分区域与文件系统的选择，两个部分。任何一个部分也都并不复杂，仔细就可以处理的很好了。

### 3 . 安装

#### 一些基本准备工作

由于Arch Linux需要在线安装，所以安装的第一步就是连接上网络。

**通过有线网络连接**

```bash
$ dhcpcd
```

**通过Wifi连接**

```bash
$ wifi-menu
```

连接之后可以使用 `ping www.baidu.com` 来检验网络连接情况（使用 `CTRL+C` 结束检测）。

连接好网络，然后挂载需要安装系统的磁盘，否则无法针对这个磁盘进行操作。

首先挂载根目录，我的根目录在“/dev/sda4”，所以：

```bash
$ mount /dev/sda4 /mnt
```

这台电脑是MBR启动引导，分区类型是DOS，所以无需挂载引导分区。如果使用EFI/GPT的方式还需要挂载一下引导分区（一般引导分区在“/dev/sda1”）：

```bash
$ mkdir /mnt/boot
$ mount /dev/sda1 /mnt/boot
```

如果还有其他分区也需要这时候挂载（按顺序先挂载/mnt，然后是boot，最后是其他）。这里有一点需要注意，如果你选择使用交换分区的话，那么还有一个步骤需要你操作，即格式化并挂载交换分区(X为硬盘号，y为分区号)：

```bash
$ mkswap /dev/sdXy
$ swapon /dev/sdXy
```

不要忘记，联网之后，更新一下时间：

```bash
$ timedatectl set-ntp true
```

#### 安装上Arch linux

做完上面那些，基本上就可以正式开始安装了！可以稍微兴奋一下了！因为下面要面对第一个挑战，VIM。很多人觉得vim很难操作，一方面是因为vim的操作不是直观的，而是通过不同的代码进行。虽然便于键盘上进行操作，但记住这一套编码也需要一些时日，不过这个挑战并不复杂，还算简单，只有四个主要命令需要记住（更详细的教程可参见[简明 VIM 练级攻略](https://coolshell.cn/articles/5426.html)）：

- `dd` ：剪切一行文本
- `gg` ：回到文本开头
- `P` ：粘贴行到文件最开始的位置
- `:wq` ：退出并保存

使用 `vim` 打开“/etc/pacman.d/mirrorlist”，找到属于China的源，移至文件开始的位置。推荐清华和浙大的源(浙大源大约在64%的位置；清华源在最后)：

```
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
```

保存之后，就是安装了：

```bash
$ pacstrap /mnt base base-devel linux linux-firmware dhcpcd netctl dialog wpa_supplicant ppp
```

命令会自动下载并安装基本内容到 `/mnt` 。如果不更改源的顺序，也是完全可以的，只是下载速度不一定那么快（我的电脑大概60.89s user 18.62s system的耗时）。如果你需要使用wifi连接网络，那么netctl、dialog与wpa_supplicant三个包都要安装上，免得在命令行界面上失去wifi连接的支持。

理论上，到这里最核心的Linux已经安装好了。我们就可以生成自动挂载的 [fstab文件](https://wiki.archlinux.org/index.php/Fstab) ，保证每次启动可以自动运行系统：

```bash
$ genfstab -U /mnt >> /mnt/etc/fstab
```

Viseator提供了一个很棒的检查方法（[出处：以官方Wiki的方式安装ArchLinux](https://www.viseator.com/2017/05/17/arch_install/#%E9%85%8D%E7%BD%AEFstab)），用于确定挂载和加载是否正确：

```bash
$ cat /mnt/etc/fstab
```

如果挂载有问题，重新处理后，可以刷新一次genfstab命令，然后用vim删除重复、错误的内容。之后就可以把操作转移到新安装的系统上了：

```bash
$ arch-chroot /mnt
```

### 4 . 配置

通过chroot命令进入新系统，也就说明安装已经完成绝大多数了任务了。现在我们要做的就是设置系统的信息并完成基本的配置工作。

#### 基本设置

先从时区设置开始：

```bash
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
$ hwclock --systohc
```

这里设置的就是上海所在时区，咱们中国一般选这。如果你真的不知道自己的时区怎么选择，可以使用 `curl https://ipapi.co/timezone` 命令查看。

```bash
$ curl https://ipapi.co/timezone
Asia/Shanghai
```

如果按照官方说明，现在应该设置语言，但是，由于新系统没有vim，所以我们索性先安装几个必要的软件(pacman的使用请移步[官方介绍](https://wiki.archlinux.org/index.php/Pacman#Usage))：

```bash
$ pacman -S vim vi sudo ntfs-3g networkmanager intel-ucode os-prober grub openssh
```

openssh可以按需安装，因为我需要远程连接自己的电脑进行工作，所以安装了这个。然后就可以修改语言信息了：

```bash
vim /etc/locale.gen
```

需要去掉“zh_CN.UTF-8 UTF-8、zh_HK.UTF-8 UTF-8、zh_TW.UTF-8 UTF-8、en_US.UTF-8 UTF-8”这四行前面的注释符号“#”。使用 `x` 去除特定字符，然后":wq"保存退出。如果你需要其他语言支持，就去掉对应语言的注释即可，然后，就可以初始化语言环境了：

```bash
$ locale-gen
Generating locales...
  en_US.UTF-8... done
  zh_CN.UTF-8... done
  zh_HK.UTF-8... done
  zh_TW.UTF-8... done
Generation complete.
```

然后使用vim，编辑一个本地化配置文件“/etc/locale.conf”，在首行用 `i` 启动插入模式后，录入 `LANG=en_US.UTF-8` ，然后使用“:wq”保存退出。到这里，时区、基本的语言属性就搞定了。

继续使用vim设置主机名，新键文件“/etc/hostname”，直接写入主机名保存就可以。比如我的公司电脑的主机名就是： `DMAComputer` 。

对于键盘的布局设置，可以参考官方的 [配置文档](https://jlk.fjfi.cvut.cz/arch/manpages/man/vconsole.conf.5) 进行设置，一般只需要有德语键盘的时候才需要吧？总之，需要特殊键盘布局才需要更改。

还需要设置一下hosts文件，已明确本机的一些情况：

```bash
$ vim /etc/hosts
```

在文件末尾使用 `o` 命令添加新行，插入如下内容后保存（“DMAComputer”这里记得按照积极的情况换成自己的主机名）：

```
127.0.0.1 localhost
::1 localhost
127.0.1.1 DMAComputer.localdomain DMAComputer
```

这些基本配置完成，就可以开始设置root用户密码了，主要也是为了保护自身电脑安全性，减少安全隐患。

```bash
$ passwd
New password:
Retype new password:
passwd: password updated successfully
```

按提示输入两边，密码就设置好了。为了便于日常使用，还需要建立一个新的用户。

```bash
$ useradd -m -G wheel sidney
```

“sidney”就是我的用户名，输入时换成自己的用户名即可。同样自己的账号也要设置密码，增强安全性：

```bash
$ passwd sidney
New password:
Retype new password:
passwd: password updated successfully
```

然后我们需要做一件很重要的事情，就是建立交换文件。因为我没使用交换分区，如果使用交换分区，那么在前面就已经通过挂载设置清晰了。

对于交换文件，首先要分配交换文件使用的磁盘空间，然后修改必要权限，格式化再启用交换文件：

```bash
$ fallocate -l 8G /swapfile
$ chmod 600 /swapfile
$ mkswap /swapfile
$ swapon /swapfile
```

这里如果你fstab初始化时，使用的是 `-U` 参数，就是基于UUID进行记录的，那么，你需要记录一下这个交换文件的UUID，以便于下一步设置——设置启动自动应用，就是编辑 `fstab` 文件：

```bash
$ vim /etc/fstab
```

添加代码 `/swapfile none  swap  defaults  0 0` 。无论`fstab`是否基于标签存储，对于swapfile，必须如此，这也是官方说明的，所以虽然有点迥异，但并不妨碍。如果需要更全面的交换文件设置或者说更厉害的应用模式，就需要安装 `systemd-swap` ，按照[官方说明](https://github.com/Nefelim4ag/systemd-swap)进行设置，这里就不再多言了。

进一步设置我们之前安装的sudo，使用 `visudo` 进行设定。一样是vim的操作，找到 “# %wheel ALL=(ALL)ALL
” 这一行，删掉注释，保存退出就可以了。

#### 系统启动设置

最后做启动设置，如果你使用EFI的登陆引导方式，那么就需要安装 `efibootmgr` 。否则前面我们已经安装了所需必要软件包。启动设置，只是在部署上略有不同：

- BIOS/MBR（分区类型一般为DOS）： `grub-install --target=i386-pc /dev/sda`
- EFI/GPT（分区类型为GPT）: `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`

然后生成配置文件：

```bash
$ grub-mkconfig -o /boot/grub/grub.cfg
```

使用U盘安装，在生成启动项时一般会出现一些只想U盘的错误，这些均可忽略。另外，如果像我一样，双系统启动的另一个操作系统是Windows7，那么，大概率安装“os-prober”和“ntfs-3g”也不能解决自动添加windows启动的问题。

那么就需要手动解决一下，使用vim打开“/boot/grub/grub.cfg”，在后面添加：

```
menuentry 'Windows 7'{
        insmod part_msdos
        insmod ntfs
        insmod ntldr
        set root=(hd0,1)
        chainloader +1
        boot
}
```

可以选择在 `40_custom` 处添加上述内容。其中 `(hd0,1)`是磁盘与windows7的安装盘位置。

上面这一套完成，那么基本就可以退出当前位置，解除挂载后重启进入新系统了。基本的操作过程就是：

```bash
$ exit
$ umount /mnt
$ reboot
```

如果你使用了更多分区，例如引导分区，则要先取消挂载：`umount /mnt/root` 。再取消根目录/mnt的挂载。

重启，取下U盘，就可以进入新系统了。不过新系统还是命令行的。我们在前面没有下载安装图形界面。重启后，我们再来设置图形界面，一般没有特殊需求，我就不安装图形界面了。

登陆后，需要先输入要登陆的用户，建议使用新用户登录，不要使用root用户。没有特殊情况不要使用root账户登陆操作比较安全。

```bash
Arch Linux 5.4.15-arch1-1 (tty1)

DMAComputer login: sidney
Password: 
[sidney@DMAComputer]$ 
```

上面显示的是，我完成在公司电脑上登陆的样子。不要忘记先连接网络（dhcpcd/wifi-menu），然后，我们开始安装图形界面了。

#### 图形界面

公司电脑较简单，因为是Intel集显的，所以只需要安装 唯一的开源驱动就好： `sudo pacman -S xf86-video-intel` 。如果你不知道自己的显卡情况，可以如下确定：

```bash
$ lspci | grep -e VGA -e 3D
01:00.0 VGA complatible controller : NVIDIA Corporation GT216M [GeForce GT 240M] (rev a2)
        Subsystem: Lenovo GT216M [GeForce GT 240M]
        Kernel driver is use: nouveau
```

上面是我家里的电脑输出的情况。如果你的显卡很老的话，比如我家里的这台Lenovo笔记本，可能需要安装AUR源的驱动包，而安装AUR源的包，可以使用 `yay` 工具，我们先安装这个工具，这个工具也是AUR的，所以，如果你不习惯使用yay，也可以按照后续方法安装别的AUR源的包：

```bash
$ mkdir ~/workplace
$ mkdir ~/workplace/tools
$ cd ~/workplace/tools
$ git clone https://aur.archlinux.org/yay.git
$ cd yay
$ makepkg -si
```

根据 [Arch Linux官方介绍](https://wiki.archlinux.org/index.php/Xorg#Driver_installation) 谨慎选择驱动，是十分必要的。否则，就要重新由安装U盘进入命令行系统进行救援卸载。

显卡驱动选择并安装好，就可以开始安装基础桌面部件Xorg了：

```bash
$ sudo pacman -S xorg
```

距离桌面还有两步之遥了。公司桌面毫不犹豫的选择xfce，因为资源消耗很少，适合公司这台姥爷机的内存……

```bash
$ sudo pacman -S xfce4 xfce4-goodies sddm
```

桌面管理你可以用这里使用的sddm，也可以使用其他的桌面管理器，请参考 [官方说明](https://wiki.archlinux.org/index.php/Display_manager#List_of_display_mana) 选择。

现在需要做的，就是添加sddm到启动服务中：

```bash
$ sudo systemctl enable sddm
```

系统服务的管理与基本使用方式，可参考[官方说明](https://wiki.archlinux.org/index.php/Systemd#Using_units)进一步学习。

到这里，事实上已经完成了Arch安装。只是有的使用可能并没有很符合自己的习惯。比如，网络还需要打开终端进行连接。这些属于系统的便利化扩展，我放到下一部分说明。

#### 使用EFI System引导

最后，我再补充一点，关于初始建立EFI分区时需要注意的事情。

新磁盘安装其实要简单很多。经衡量，我选择使用EFI/GPT进行分区和引导系统。基本上还是使用 `fdisk` 进行分区（基本操作顺序：“g => n => p => 1 => enter => +512M => t => 1”）。

更改分区类型需要使用所支持的分区类型编号，否则不能有效更改，很多教程上并没有说清这一点。在fdisk中，输入 `t` 命令后，可以使用 `L` 查看所有支持的分区类型编码。

分区之后，需要格式化对应的EFI分区：

```bash
$ mkfs.fat -F32 /dev/sda1
```

至此引导分区就建好了。

### 5 . 扩展

为了便于使用，先把AUR的源换成国内（[清华源](https://mirror.tuna.tsinghua.edu.cn/help/AUR/)）的，方便使用yay进行安装：

```bash
$ yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```

#### 便利化扩展

首先对联网方式进行便利化。如果你使用有线网，那么只需要把有线网络的管理器做成服务就好：

```bash
$ sudo systemctl enable dhcpcd
```

无线网略微复杂：

```bash
$ sudo systemctl disable netctl
$ sudo systemctl enable NetworkManager
```

如果不想使用命令行查看网络情况，还需要安装一个小工具，一般桌面环境已经包含，但是Xfce4要使用NetworkManager进行自动网络管理的话，还是需要这个软件的：

```bash
$ sudo pacman -S network-manager-applet
```

然后，就是在字体部分，我偏向于安装“adobe source han fonts”思源字体，当然“noto fonts”也很不错。可以下载所需的字体文件，很容易通过pacman进行安装。当然，AUR中也有很多很不错的字体可供选择，可以从 [官方字体中查找](https://wiki.archlinux.org/index.php/Fonts#Chinese,_Japanese,_Korean,_Vietnamese)。

还有一些便利化操作，比如添加ssh服务、自动跨越长城之类的，其实也并不复杂，都是使用systemctl启动为服务就好。这一类便利化并不大众，我也就不在这里专门说明了。
