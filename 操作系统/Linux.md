# Linux

### Linux 简介

* 支撑互联网的开源技术

![image-20200710214606468](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200710214606468.png)

**Linux 应用范围**

* 企业服务器（软件丰富、系统安全性高、开源->内核免费）
* 嵌入式系统（安卓/IOS/智能家电）
* 电影特效

**命令行**

* 图形界面耗费资源较多
* 在操作系统的设计与图形的关联性会变大

### Linux 系统启动

* 正常的物理机器大概使用的都是/dev/sd\[a-p]的磁盘文件名，至于在虚拟环境中，为了加速，可能就会使用/dev/vd\[a-p]这种设备文件名。
* 设备文件名的命名顺序是**按照检测到的顺序来决定设备文件名**，并非按照实际插槽顺序。

#### MBR和GPT

* MBR（MS-DOS）：依据柱面，GPT（patition table）：依据扇区

**MBR分区表格式与限制**

> 主引导记录（Master Boot Record）：可以安装启动引导程序的地方，有446字节；
>
> 分区表（patition table）：记录整块分区的状态，有64字节。
>
> 由于分区表所在区块仅有64字节容量，因此最多仅能有四组记录区，每组记录区记录了该区段的启始与结束的柱面号码。
>
> 标准MBR结构如下：
>
> 地址 描述 长度（字节） 0 代码区 440（最大446） 440 选用磁盘标志 4 444 一般为空值; 0x0000 2 446 标准MBR分区表规划（四个16 byte的主分区表入口） 64 511 MBR有效标志：0x55AA 2 MBR总大小：446 + 64 + 2 = 512。

**磁盘分区结构信息**(最低也需要下面的字节大小才是一个完整的分区信息)

* 这16个字节中的每个字节都表示了一种定义:

> 偏移 长度（字节） 意义 00H 1 分区状态：00–>非活动分区；80–>活动分区；其它数值没有意义 01H 1 分区起始磁头号（HEAD），用到全部8位 02H 2 分区起始扇区号（SECTOR），占据02H的位0－5；该分区的起始磁柱号（CYLINDER），占据02H的位6－7和03H的全部8位 04H 1 文件系统标志位 05H 1 分区结束磁头号（HEAD），用到全部8位 06H 2 分区结束扇区号（SECTOR），占据06H的位0－5；该分区的结束磁柱号（CYLINDER），占据06H的位6－7和07H的全部8位 08H 4 分区起始相对扇区号 0CH 4 分区总的扇区数

* **这四组划分信息我们称为主要（Primary）或扩展（Extend）分区，最多为4个（硬盘的限制）**
* **最小单位为柱面（Cylinder）**
* 扩展分区只记录他自己的大小，及一些简单索引信息，**在他范围内的逻辑分区的区头各自记录自己的分区信息**
* 扩展分区最多只能有1个（操作系统的限制），若被破坏，所有的逻辑分区将被删除
* **逻辑分区的数量依操作系统而不同，逻辑分区的设备名称号码由5号开始，依据系统有不同的分区上限限制**
* MBR最大只能为2.2TB限制，且只有一个区块，无备份，风险大
* 只能存446字节的启动引导程序

**GPT**

* GPT使用34个LBA区块记录分区信息，磁盘的最后也有34个LBA备份
* 34个LBA区块：一块为512字节

> LBA0(MBR兼容区块）两部分：
>
> 1. 启动引导程序
> 2. 特殊标识符(MBR为分区表)
>
> LBA1(GPT表头记录)：
>
> 1. 记录了分区表本身的大小与位置
> 2. 备份GPT位置
> 3. 校验码，出粗则调用备份
>
> LBA2-33（实际记录分区）
>
> 1. 每个LBA可以记录4组分区记录
> 2. 再为每组分组记录分别提供64位记载开始/结束的扇区号码（一个扇区512字节）：所以一分区的最大容量为2^64×512字节 = 8ZB

#### **固件接口标准**

* 以下两者的功能都是雕刻在硬件中通电自动执行的的，没有存储空间和程序在上面
* BIOS

IBM推出的业界标准的固件接口，存储于主板的ROM/EEPROM/flash中，提供的功能包括：

1. 开机自检
2. 加载引导程序（MBR中的，通常是bootloader的第一级）
3. 向OS提供抽象的硬件接口

PS：CMOS是PC上的另一个重要的存储器，用于保存BIOS的设置结果，CMOS是RAM。

* EFI/UEFI

Unified Extensible Firmware Interface，架设在系统固件之上的软件接口，用于替代BIOS接口，EFI是UEFI的前称。

一般认为，UEFI由以下几个部分组成：

1. Pre-EFI初始化模块
2. EFI驱动程序执行环境（DXE）
3. EFI驱动程序
4. 兼容性支持模块（CSM）
5. EFI高层应用
6. [GUID磁盘分区表](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%85%A8%E5%B1%80%E5%94%AF%E4%B8%80%E6%A8%99%E8%AD%98%E5%88%86%E5%8D%80%E8%A1%A8)（GPT）

通常初始化模块和DXE被集成在一个ROM中；EFI驱动程序一般在设备的ROM中，或者ESP中；EFI高层应用一般在ESP中。CSM用于给不具备UEFI引导能力的操作系统提供类似于传统BIOS的系统服务。

#### **启动方式**

* Legacy modecun

BIOS+（MBR/GPT）进行引导的传统模式，流程如下：

1. BIOS加电自检（Power On Self Test -- POST），检查其他硬件。
2. 读取主引导记录（MBR/GPT）。**BIOS根据CMOS中的设置依次检查启动设备**：将相应启动设备的第一个扇区（也就是MBR扇区）读入内存。
3.
   1. 检查MBR/GPT的结束标志位是否等于55AAH，若不等于则转去尝试其他启动设备，如果没有启动设备满足要求则显示"NO ROM BASIC"然后死机。
   2. 当检测到有启动设备满足要求后，BIOS将控制权交给相应启动设备的MBR/GPT。
4. 根据MBR/GPT的引导代码启动[引导程序](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%BC%95%E5%AF%BC%E7%A8%8B%E5%BA%8F%22%20%5Co%20%22%E5%BC%95%E5%AF%BC%E7%A8%8B%E5%BA%8F)。（\*\*若识别是GPT，有一些引导程序需要多配置一些启动参数。\*\*因为引导程序的不同，若用gpt，则需要额外划分出一个BIOS boot分区用于提供引导程序放置一些启动配置）

* UEFI mode(UEFI+GPT)

UEFI启动不依赖于Boot Sector（比如MBR），大致流程如下：

1. Pre-EFI初始化模块运行，自检
2. 加载DXE（EFI驱动程序执行环境），枚举并加载EFI驱动程序（设备ROM或ESP中）
3. 找到ESP中的引导程序，通过其引导操作系统。

* GPT 可以提供到 64bit 的寻址，然后也能够使用较大的区块来处理开机管理程序。但BIOS不懂GPT，需要GPT提供兼容模式才可以，而且BIOS是16位的，与操作系统的兼容性也有些差，因此有了UEFI（Unified extensible firmware Interface）
* 相较于BIOS，有了安全启动（secure boot），可以防止Cracker在BIOS阶段获取系统控制权，但兼容不好会导致启动不了操作系统
* 另外**为了引导程序能读懂gpt分区表，最好拥有BIOS boot的分区支持**，而且为了启动UEFI的应用程序，需要格式化一个FAT格式的文件系统分区，大约提供512MB到1GB的大小。

![image-20200804105047046](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200804105047046.png)

* CSM mode

UEFI中的兼容性支持模块（Compatible Support Module）提供了引导UEFI固件的PC上的传统磁盘（MBR格式）的方法。

#### 启动流程

1. BIOS：启动主动执行的固件（固件就是写入到硬件上的一个软件程序）
2. MBR/GPT:第一个可启动设备的第一个扇区内的主引导记录块，内含启动引导代码
3. 启动引导程序（boot loader）：一个可读取内核文件来执行的软件（安装操作系统时附带的，会自动识别硬盘的文件系统格式，此时分区表可能为MBR/GPT，若是GPT,启动程序要支持GPT，才能取到正确的操作系统内核）

> boot loader主要任务：
>
> 1. 提供选项：用户可以选择不同的启动选项，这也是多重引导的重要功能
> 2. 加载内核文件：直接指向可使用的程序区段来启动操作系统
> 3. 转交其他启动引导程序：将启动管理功能转交给其他启动引导程序负责（每个区都拥有自己的启动分区boot sector）
> 4. **启动引导程序只会认识自己的系统分区内的可启动的内核文件，以及其他启动引导程序**
> 5. **双系统要先安装windows再安装linux**，linux有选择功能，而window安装时，会主动覆盖掉MBR以及分区的启动扇区
> 6. 内核文件：操作系统启动
>
> Grub
>
> GNU的开源引导程序，可以用于引导Linux等操作系统，或者用于链式引导其它引导程序（比如Windows Boot Manager），分为三个部分，分别称为步骤1、1.5、2，看名字就可以知道，步骤1.5是可有可没有的，这三个步骤对应的文件分别是：
>
> 1. Boot.img：步骤1对应的文件，446个字节大小，步骤1可以引导步骤1.5也可以引导步骤2。MBR分区格式的磁盘中，放在MBR里（446也是为了符合MBR的启动代码区大小）； GPT分区格式的磁盘中，放在Protective MBR中。
> 2. Core.img：步骤1.5对应的文件，32256字节大小。MBR分区格式的磁盘中，放在紧邻MBR的若干扇区中；GPT分区格式的磁盘中，则放在34号扇区开始的位置（第一个分区所处的位置），而对应的GPT分区表中的第一个分区的entry被置空。通常其中包含文件系统驱动以便load步骤2的文件。
> 3. /boot/grub：步骤2对应的文件目录，放在系统分区或者单独的Boot分区中
>
> > 启动管理程序GRUB的目录，里面存放的都是GRUB在启动时所需要的画面、配置及各阶段（stage1, stage1.5, stage 2）的文件。见下图。
> >
> > **grub.conf文件**
> >
> > 这个文件其实是启动管理程序GRUB的配置文件。在同一层目录下面（/boot/grub/）还有一个它的镜像文件menu.lst。而在SUSE中menu.lst是GRUB实际用到的文件。
> >
> > 下面这个文件是我系统上摘录的。
> >
> > ​ # grub.conf generated by anaconda
> >
> > ​ #
> >
> > ​ # Note that you do not have to rerun grub after making changes to this file
> >
> > ​ # NOTICE: You have a /boot partition. This means that
> >
> > ​ # all kernel and initrd paths are relative to /boot/, eg.
> >
> > ​ # root (hd0,0) # kernel /vmlinuz-version ro root=/dev/sda2
> >
> > ​ # initrd /initrd-version.img
> >
> > ​ # boot=/dev/sda default=0 timeout=10 splashimage=(hd0,0)/grub/splash.xpm.gz title Red Hat Linux (2.4.20-8) **root (hd0,0) A** kernel /vmlinuz-2.4.20-8 ro root=LABEL=/ **B** initrd /initrd-2.4.20-8.img **C**
> >
> > 说明：
> >
> > ​ A： root（hd0,0)表示/boot/的路径。我的/boot/位于/dev/sda1，也就是BIOS检测到的第0号硬盘的0号扇区。
> >
> > ​ B： 告诉GRUB到哪里去找vmlinuz-2.4.20-8这个kernel，这里的“绝对路径”其实是/boot/vmlinuz-2.4.20-8，而文件的物理位置 在/dev/sda1上。后面的ro表示以只读的方式读取该文件，而“root=LABEL=/”表示以标签名称为“/”的文件系统为根文件系统。这个 根文件系统与/boot/的位置是两个概念，这里的root是加载Kernel时的一个参数，目的是告诉Kernel，根文件系统在哪里。
> >
> > ​ 实际上/boot/可以挂载到其他的硬盘上。只要在A的位置说明准确就可以了，如root(hd1,0)表示/boot/在第2块硬盘上。
> >
> > ​ C： 告诉GRUB到哪里去取文件initrd，它的“绝对路径”也是/boot/initrd-2.4.20-8.img。
> >
> > **2.2 其他文件**
> >
> > ​ 毫无疑问，grub.conf文件最重要。但在/boot/grub/中还有其他一些文件，我们也可以看看它们的作用。
> >
> > **2.2.1 stages文件**
> >
> > ![img](http://img.blog.csdn.net/20131013192442937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ1RPXzUx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
> >
> > \* stage1：它是硬件引导扇区（MBR）的备份文件。
> >
> > \* xxx\_stage1\_5：这些文件的作用是连接stage1到stage2的一个通道，里面唯一存放的是该系统文件的格式，所以只要被支持的文件，就会预先存放一个格式文件在其中。（与core.img协同识别以何种方式启动stage2）
> >
> > \* stage2：该文件是GRUB的核心程序，它的主要功能是：
> >
> > * 提供菜单
> > * 读取配置文件
> > * 连接下一个boot sector（MBR）/下一个驱动模块（GPT）
> >
> > **2.2.2 device.map**
> >
> > 该文件直接侦测目前的硬件来假设BIOS所记录的实体磁盘有哪些，默认值是**安装系统时**就记录好的。在之后加入的磁盘，在该文件中没有显示。
> >
> > **2.2.3 splash.xpm.gz**
> >
> > 启动时的背景图片。
>
> ![img](https://picb.zhimg.com/80/v2-cccf2befeb5363c48ca4911c88e00329\_720w.jpg)
>
> * Windows Boot Manager
>
> 是从[Windows Vista](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/Windows\_Vista)开始引进的新一代开机管理程序，用以取代[NTLDR](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/NTLDR)。
>
> 当电脑运行完开机自检后，传统型[BIOS](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/BIOS)会根据[引导扇区](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%95%9F%E5%8B%95%E7%A3%81%E5%8D%80)查找开机硬盘中标记"引导"分区下的BOOTMGR文件；若是[UEFI](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/UEFI)则是Bootmgfw.efi文件和Bootmgr.efi文件，接着管理程序会读取开机配置数据库（BCD, Boot Configuration Database）下的引导数据，接着根据其中的数据加载与默认或用户所选择的[操作系统](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1)。
>
> * NTLDR
>
> 是[微软](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%BE%AE%E8%BD%AF)的[Windows NT](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/Windows\_NT)系列[操作系统](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F)（包括[Windows XP](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/Windows\_XP)和[Windows Server 2003](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/Windows\_Server\_2003)）的引导程序。NTLDR可以从[硬盘](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E7%A1%AC%E7%9B%98)以及[CD-ROM](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/CD-ROM)、[U盘](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/U%E7%9B%98)等移动存储器运行并引导Windows NT系统的启动。如果要用NTLDR启动其他操作系统，则需要将该操作系统所使用的[启动扇区](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/w/index.php%3Ftitle%3D%E5%90%AF%E5%8A%A8%E6%89%87%E5%8C%BA%26action%3Dedit%26redlink%3D1)代码保存为一个文件，NTLDR可以从这个文件加载其它[引导程序](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%BC%95%E5%AF%BC%E7%A8%8B%E5%BA%8F)。
>
> NTLDR主要由两个文件组成，这两个文件必须放在系统分区（大多数情况下都是C盘）：
>
> 1. NTLDR，引导程序本身
> 2. boot.ini，引导程序的配置文件

#### 目录树与挂载

**目录树（directory tree）**

以根目录为主，向下呈现分支状的目录结构的一种文件架构，整个目录树架构最重要的就是那个根目录（root directory），这个根目录的表示方法为一条斜线“/”。

如何结合目录树的架构和磁盘内的数据呢？

**挂载**（安装Linux分区的时候会同时选择挂载的目录）

所谓“挂载”就是利用一个目录当成进入点，将磁盘分区的数据放置在该目录下，**进入该目录就可以读取该分区的意思**。这个操作我们称为“挂载”，进入点的目录称为“挂载点”，根目录一定要挂载到某个分区。

**分区后的磁盘需要格式化为相应的文件系统，目录树挂载在分区磁盘分区上，目录树依靠文件系统的inode记录信息**

#### 主服务规划

需要考虑硬件的常用服务：

**NAT(达成 IP分享器的功能）：**

通常小型企业或者是学校单位大多仅会有一条对外的联机，然后全公司/学校内的计算机全部透过这条联机连到因特网上。

此时我们就得要使用 IP 分享器来让这一条对外联机分享给所有的公司内部员工使用。 那么 Linux 能不能达到此一 IP 分享的功能呢？当然可以，就是透过 NAT 服务即可达成这项任务了！

在这种环境中，由于 Linux 作为一个内/外分离的实体，因此网络流量会比较大一点。 此时 Linux 主机的**网络卡**就需要比较好些的配备。其他的 CPU、RAM、硬盘等等的影响就小很多。 事实上，单利用 Linux 作为 NAT 主机来分享 IP 是很不智的～因为 PC 的耗电能力比 IP 分享器要大的多～

那么为什么你还要使用**Linux 作为 NAT 呢？因为 Linux NAT 还可以额外的加装很多分析软件， 可以用来分析客户端的联机，或者是用来控制带宽与流量，达到更公平的带宽使用呢！**

**SAMBA(加入 Windows 的网络邻居）**

分享数据给客户端，一般学校环境的文件服务器(file server)的角色

这种服务器由于分享的数据量较大，对于系统的网络卡与硬盘的大小及速度就比较重要， 如果你还针对不同的用户提供文件服务器功能，那么/home 这个目录可以考虑独立出来，并且加大容量。这种服务器由于分享的数据量较大，对于系统的网络卡与硬盘的大小及速度就比较重要， 如果你还对不同的用户提供文件服务器功能，那么/home 这个目录可以考虑独立出来，并且加大容量。

**Mail(邮件服务器)**

如果你是一间私人单位的公司，你的公司内传送的 email 是具有商业机密或隐私性的，那你还想要交给免费信箱去管理吗？ 此时才有需要架设 mail server 啰。在 mail server 上面，重要的也是硬盘容量与网络卡速度，在此情境中，也可以将/var 目录独立出来，并加大容量。

**Web(WWW** **服务器**)

WWW 服务器几乎是所有的网络主机都会安装的一个功能，因为他除了可以提供 Internet 的 WWW 联机之外， 很多在网络主机上面的软件功能(例如某些分析软件所提供的最终分析结果的画面)也都使用 WWW 作为显示的接口， 所以这家伙真是重要到不行的。

CentOS 使用的是 Apache 这套软件来达成 WWW 网站的功能，在 WWW 服务器上面，如果你还有提供数据库系统的话， 那么 CPU 的等级就不能太低，而最重要的则是 RAM 了！要增加 WWW 服务器的效能，通常提升 RAM 是一个不错的考虑。

**DHCP(提供客户端自动取得 IP的功能)：**

局域网管理员提供给别人自动获得IP的功能

**FTP**

文件共享

#### 硬件规划

硬盘的规划对于 Linux 新鲜人而言，那将是造成你『头疼』的主要凶手之一！ 因为硬盘的分区技巧需要对于 Linux 文件结构有相当程度的认知之后才能够做比较完善的规划的。

* 最简单的分区方法：

这个在上面第二节已经谈过了，就是仅分区出根目录与内存置换空间( / & swap )即可。 然后再预留一些剩余的磁盘以供后续的练习之用。不过，这当然是不保险的分区方法(所以鸟哥常常说这是『懒人分区法』)！因为如果任何一个小细节坏掉(例如坏轨的产生)，你的根目录将可能整个的损毁～挽救方面较困难！

* 稍微麻烦一点的方式：

较麻烦一点的分区方式就是先分析这部主机的未来用途，然后根据用途去分析需要较大容量的目录， 以及读写较为频繁的目录，将这些重要的目录分别独立出来而不与根目录放在一起， 那当这些读写较频繁的磁盘分区槽有问题时，至少不会影响到根目录的系统数据，那挽救方面就比较容易啊！ 在默认的 CentOS 环境中，底下的目录是比较符合容量大且(或)读写频繁的目录啰：

* /boot （1）系统Kernel的配置文件；（2）启动管理程序GRUB的目录，里面存放的都是GRUB在启动时所需要的画面、配置及各阶段（stage1, stage1.5, stage 2）的文件。
* / ：根目录
* /home ：如果建立一个用户，用户名是"xx"，那么在/home目录下就有一个对应的/home/xx路径，用来存放用户的主目录。
* /var：目录主要针对常态性变动的文件，包括缓存(cache)、登录档(log file)以及某些软件运作所产生的文件
* Swap：当物理内存容量不够时，可以拿着来存放内存中较少被使用的数据，使得较快的物理内存可以让给需要频繁调用的资源，可以看成是一个虚拟内存。该区使用频繁，一般也意味着攒钱买内存条了。
* 绝对路径：由根目录(/)开始写起的文件名或目录名称， 例如 /home/dmtsai/.bashrc；
*   相对路径：相对于目前路径的文件名写法。 例如 ./home/dmtsai 或 ../../home/dmtsai/ 等等。反正开头不是 /

    就属于相对路径的写法
* 详细p166

![image-20200804143334766](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200804143334766.png)

![image-20200711175133209](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200711175133209.png)

![image-20200711194043727](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200711194043727.png)

![image-20200711202121200](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200711202121200.png)

#### 注意事项

* Linux 中严格区分大小写
* Linux 中所有内容以文件形式保存，包括硬学
  * 硬盘文件时/dev/sd\[a-p]
  * 光盘文件时/dev/sr0等
  * 所有需要设置的参数（网关），只有写到文件中才能保存
*   Linux不靠扩展名区分文件类型（可以加以下的扩展名，但他的作用仅限于给管理员更好的观察，linxu本身是能不靠扩展名识别文件类型的）

    ![image-20200712095129339](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712095129339.png)
* windows下的程序不能直接Linux中安装和运行（除非借用虚拟机）
* ![image-20200712133541790](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712133541790.png)
* ![image-20200712140433734](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712140433734.png)

![image-20200712141426655](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712141426655.png)

![image-20200712141608233](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712141608233.png)

### Linux常用命令

#### linux命令连接符

& 表示任务在后台执行，如要在后台运行redis-server,则有 redis-server &

&& 表示前一条命令执行成功时，才执行后一条命令 ，如 echo '1‘ && echo '2'

\| 表示管道，上一条命令的输出，作为下一条命令参数，如 echo 'yes' | wc -l

|| 表示上一条命令执行失败后，才执行下一条命令，如 cat nofile || echo "fail" 注转自：https://blog.csdn.net/chinabestchina/article/details/72686002

;分号表示命令依次执行。

管道符'|'：

命令格式：**命令A|命令B**，即命令1的正确输出作为命令B的操作对象

\1. 例如：　ps aux | grep "test" 在 ps aux中的結果中查找test。

\2. 例如： find . -name "\*.txt" | xargs grep "good" -n --color=auto 把find的结果当成参数传入到grep中，即在那些文件内部查找good关键字。

注：本例中xargs将[find](https://baike.baidu.com/item/find)产生的长串文件列表[拆散](https://baike.baidu.com/item/%E6%8B%86%E6%95%A3)成多个子串，

​ 如“”find /path -type f -print0 | xargs -0 rm

​ xargs将[find](https://baike.baidu.com/item/find)产生的长串文件列表[拆散](https://baike.baidu.com/item/%E6%8B%86%E6%95%A3)成多个子串，然后对每个子串调用[rm](https://baike.baidu.com/item/rm)

　 xargs 可能就会误判了,如果需要处理特殊字符，需要使用-0参数进行处理。

**选项解释** 　　　　-0 ：当sdtin含有特殊字元时候，将其当成一般字符，想/'空格等

![image-20200712160519465](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712160519465.png)

#### 文件处理命令

**ls**

![image-20200712160750472](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712160750472.png)

> \-lh人性化显示
>
> \-ld显示当前目录详细信息

![image-20200712162047867](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712162047867.png)

> ![image-20200712162827636](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712162827636.png)
>
> ​ 被引用的次数 所有者 所有组 大小 最后修改时间 文件名

**tee**

Linux tee命令用于读取标准输入的数据，并将其内容输出成文件。

tee指令会从标准输入设备读取数据，将其内容输出到标准输出设备，同时保存成文件。

#### 语法

```bash
tee [-ai][--help][--version][文件...]
```

**参数**：

* \-a或--append 　附加到既有文件的后面，而非覆盖它．
* \-i或--ignore-interrupts 　忽略中断信号。
* \--help 　在线帮助。
* \--version 　显示版本信息。

#### 目录处理命令

**mkdir**

![image-20200712163210851](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712163210851.png)

> 可以同时创建多个目录
>
> ![image-20200712163538422](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712163538422.png)

![image-20200712163600323](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712163600323.png)

**pwd**

![image-20200712163650292](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712163650292.png)

**cp**

![image-20200712164109412](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712164109412.png)

> 默认 -r

**远程拷贝文件scp/rsync**

scp：在网络上的两个主机之间复制文件，它使用 ssh 做文件传输，并使用相同的认证方式，具有相同的安全性。 rsync：是一个既快速又出众的多功能文件复制工具。它能本地复制、通过远程 [shell](https://www.linuxcool.com) 在其它主机之间复制，或者与远程的 rsync 守护进程daemon 之间复制。 pscp：是一个并行复制文件到多个主机上的程序。它提供了诸多特性，例如为 scp 配置免密传输，保存输出到文件，以及超时控制。 prsync：也是一个并行复制文件到多个主机上的程序。它也提供了诸多特性，例如为 ssh 配置免密传输，保存输出到 文件，以及超时控制。

> scp是相当于复制，黏贴，如果有的话是覆盖，比较耗时间，不智能。
>
> rsync是复制，如果有重复的文件，会直接跳过，而且他自己的算法优化。
>
> scp是把文件全部复制过去，当文件修改后还是把所有文件复制过去，rsync 第一次是把所有文件同步过去，当文件修改后，只把修改的文件同步过去。

**方式 1：如何在 Linux 上使用 scp 命令从本地系统向远程系统复制文件/文件夹？**

`scp` 命令可以让我们从本地系统复制文件/文件夹到远程系统上。

我会把 output.txt 文件从本地系统复制到 2g.[CentOS](https://www.linuxprobe.com).com 远程系统的 /opt/backup 文件夹下。

```
# scp output.txt root@2g.CentOS.com:/opt/backup

output.txt                                                                                              100% 2468    2.4KB/s  00:00
```

**从本地系统复制两个文件 output.txt 和 passwd-up.sh 到远程系统 2g.CentOs.com 的 /opt/backup 文件夹下。**

```
# scp output.txt passwd-up.sh root@2g.CentOS.com:/opt/backup

output.txt 100% 2468 2.4KB/s 00:00
passwd-up.sh 100% 877 0.9KB/s 00:00
```

**从本地系统复制 shell-script 文件夹到远程系统 2g.CentOs.com 的 /opt/back 文件夹下。**

这会连同shell-script 文件夹下所有的文件一同复制到/opt/back 下。

```
# scp -r /home/daygeek/2g/shell-script/ root@:/opt/backup/

output.txt 100% 2468 2.4KB/s 00:00
ovh.sh      100% 76 0.1KB/s 00:00
passwd-up.sh 100% 877 0.9KB/s 00:00
passwd-up1.sh 100% 7 0.0KB/s 00:00
server-list.txt 100% 23 0.0KB/s 00:00
```

**方式 2：如何在 Linux 上使用 scp 命令和 Shell** [**脚本**](https://www.linuxcool.com)**复制文件/文件夹到多个远程系统上？**

如果你想复制同一个文件到多个远程服务器上，那就需要创建一个如下面那样的小 shell 脚本。

并且，需要将服务器添加进 server-list.txt 文件。确保添加成功后，每个服务器应当单独一行。

**最终，你想要的脚本就像下面这样：**

```
# file-copy.sh

#!/bin/sh
for server in `more server-list.txt`
do
  scp /home/daygeek/2g/shell-script/output.txt root@$server:/opt/backup
done
```

**完成之后，给 file-copy.sh 文件设置可执行权限。**

```
# chmod +x file-copy.sh
```

**最后运行脚本完成复制。**

```
# ./file-copy.sh

output.txt 100% 2468 2.4KB/s 00:00
output.txt 100% 2468 2.4KB/s 00:00
```

**使用下面的脚本可以复制多个文件到多个远程服务器上。**

```
# file-copy.sh

#!/bin/sh
for server in `more server-list.txt`
do
  scp /home/daygeek/2g/shell-script/output.txt passwd-up.sh root@$server:/opt/backup
done
```

**下面结果显示所有的两个文件都复制到两个服务器上。**

```
# ./file-cp.sh

output.txt 100% 2468 2.4KB/s 00:00
passwd-up.sh 100% 877 0.9KB/s 00:00
output.txt 100% 2468 2.4KB/s 00:00
passwd-up.sh 100% 877 0.9KB/s 00:00
```

**使用下面的脚本递归地复制文件夹到多个远程服务器上。**

```
# file-copy.sh

#!/bin/sh
for server in `more server-list.txt`
do
  scp -r /home/daygeek/2g/shell-script/ root@$server:/opt/backup
done
```

**上述脚本的输出。**

```
# ./file-cp.sh

output.txt 100% 2468 2.4KB/s 00:00
ovh.sh      100% 76 0.1KB/s 00:00
passwd-up.sh 100% 877 0.9KB/s 00:00
passwd-up1.sh 100% 7 0.0KB/s 00:00
server-list.txt 100% 23 0.0KB/s 00:00

output.txt 100% 2468 2.4KB/s 00:00
ovh.sh      100% 76 0.1KB/s 00:00
passwd-up.sh 100% 877 0.9KB/s 00:00
passwd-up1.sh 100% 7 0.0KB/s 00:00
server-list.txt 100% 23 0.0KB/s 00:00
```

**方式 3：如何在 Linux 上使用 pscp 命令复制文件/文件夹到多个远程系统上？**

`pscp`命令可以直接让我们复制文件到多个远程服务器上。

**使用下面的 pscp 命令复制单个文件到远程服务器。**

```
# pscp.pssh -H 2g.CentOS.com /home/daygeek/2g/shell-script/output.txt /opt/backup

[1] 18:46:11 [SUCCESS] 2g.CentOS.com
```

**使用下面的 pscp 命令复制多个文件到远程服务器。**

```
# pscp.pssh -H 2g.CentOS.com /home/daygeek/2g/shell-script/output.txt ovh.sh /opt/backup

[1] 18:47:48 [SUCCESS] 2g.CentOS.com
```

**使用下面的 pscp 命令递归地复制整个文件夹到远程服务器。**

```
# pscp.pssh -H 2g.CentOS.com -r /home/daygeek/2g/shell-script/ /opt/backup

[1] 18:48:46 [SUCCESS] 2g.CentOS.com
```

**使用下面的 pscp 命令使用下面的命令复制单个文件到多个远程服务器。**

```
# pscp.pssh -h server-list.txt /home/daygeek/2g/shell-script/output.txt /opt/backup

[1] 18:49:48 [SUCCESS] 2g.CentOS.com
[2] 18:49:48 [SUCCESS] 2g.Debian.com
```

**使用下面的 pscp 命令复制多个文件到多个远程服务器。**

```
# pscp.pssh -h server-list.txt /home/daygeek/2g/shell-script/output.txt passwd-up.sh /opt/backup

[1] 18:50:30 [SUCCESS] 2g.Debian.com
[2] 18:50:30 [SUCCESS] 2g.CentOS.com
```

**使用下面的命令递归地复制文件夹到多个远程服务器。**

```
# pscp.pssh -h server-list.txt -r /home/daygeek/2g/shell-script/ /opt/backup

[1] 18:51:31 [SUCCESS] 2g.Debian.com
[2] 18:51:31 [SUCCESS] 2g.CentOS.com
```

**mv**

![image-20200712164859602](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712164859602.png)

**rm**

![image-20200712170429966](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712170429966.png)

#### 文件处理命令

**touch**

![image-20200712171624510](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712171624510.png)

> 命名有空格需要用到“”，不能用/

**cat**

![image-20200712175542509](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712175542509.png)

**more**

![image-20200712173252225](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712173252225.png)

**less**

![image-20200713000155502](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713000155502.png)

**head**

![image-20200712174640837](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712174640837.png)

> 默认10行

**tail**

![image-20200712174904371](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712174904371.png)

**硬链接与软链接： ln**

**Hard Link**

有没有可能有多个文件名对应到同一个 inode 号码呢？有的！那就是 hard link 的由来。

所以简单的说：**hard link 只是在某个目录下新增一个文件名链接到某 inode 号码的关连记录而已。**

![image-20200810163708514](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810163708514.png)

* **硬链接和原文件的索引一致，删除一个并不影响另一个**
* **hard link 只是在某个已经存在的inode指向的 block 存储的目录中多写入一个关联数据而已，不会增加 inode**
* **不能跨 Filesystem；**
* **不能 link 目录。**（**链接的数据需要连同被链接目录底下的所有数据都建立链接**）

**Symbolic Link**

* Symbolic link 就是在建立一个独立的文件，这个文件会让数据的读取指向他 link 的那个文件的文件名，即相当于多创建了一个目录Block！新创建的目录block的重要内容就是他**会写上目标文件的『文件名』**
* **Symbolic Link 与 Windows 的快捷方式可以给他划上等号，由 Symbolic link 所建立的文件为一个独立的新的文件，所以会占用掉 inode 与 block**（相当于多了一级目录）
*   软链接：

    　　a.可以对目录创建软链接，遍历操作会忽略目录的软链接。

    　　b:可以跨文件系统

    　　c:可以对不存在的文件创建软链接，因为放的只是一个字符串，至于这个字符串是不是对于一个实际的文件，就是另外一回事了

![image-20200810164252656](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810164252656.png)

```bash
[root@study ~]# ln [-sf] 来源文件 目标文件
选项与参数： -s ：如果不加任何参数就进行连结，那就是 hard link，至于 -s 就是 symbolic link
-f ：如果 目标文件 存在时，就主动的将目标文件直接移除后再建立！
```

**关于目录的 link 数量**：

当我们以 hard link 进行『文件的连结』时，可以发现，在 ls -l 所显示的第二字段会增加一才对，那么请教，如果建立目录时，他默认的 link 数量会是多少？ 让我们来想一想，**一个『空目录』里面至少会存在些什么？呵呵！就是存在 . 与 .. 这两个目录啊！** 那么，当我们建立一个新目录名称为 /tmp/testing 时，基本上会有三个东西，那就是：

 /tmp/testing

 /tmp/testing/.

 /tmp/testing/..

**而其中 /tmp/testing 与 /tmp/testing/. 其实是一样的！都代表该目录啊～而 /tmp/testing/.. 则代表 /tmp 这个目录，所以说，当我们建立一个新的目录时， 『新的目录的 link 数为 2 ，而上层目录的 link数则会增加 1 』**

#### 权限管理命令

**权限管理命令**

**chmod**

![image-20200712200330982](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712200330982.png)

![image-20200712202001951](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712202001951.png)

> Linux 对文件和目录的权限等级赋予的内容不一致，一个是针对文件，一个是针对目录以及目录下的所有文件（一般文件的权限跟目录的是一样的）
>
> eg: file 有 w 是可以更改文件，但只有所属directory 有w权限才可以删除他。

**其他权限管理命令**

**chown**

![image-20200712203743860](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712203743860.png)

> 只有管理员root可以这样操作

**chgrp**

![image-20200712204448551](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712204448551.png)

**umask**

![image-20200712204827886](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712204827886.png)

> 这是针对目录是这样的，新建一个文件一般不会匹配x权限，避免病毒木马的可执行性，提高安全性
>
> 用户更改缺省创建一个目录和文件的权限（不建议）
>
> ![image-20200712210730451](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712210730451.png)

#### 文件搜索命令

**find**

![image-20200712212122647](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712212122647.png)

![image-20200712214847559](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712214847559.png)

> \-name 搜索匹配条件的文件（区分大小写）
>
> \-iname 不区分大小写的搜索
>
> Linux find 只精确搜索，若要模糊搜索需要用到el表达式：\*表示匹配全部的（前或后） ？匹配单个

![image-20200712214913040](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712214913040.png)

![image-20200712215140266](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712215140266.png)

![image-20200712215235835](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712215235835.png)x

**其他**

**locate**

![image-20200712230343213](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712230343213.png)

> /tmp并不在资料库的收录范围内
>
> \-i 不区分大小写

**which**

![image-20200712231200446](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712231200446.png)

**whereis**

![image-20200712232342778](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712232342778.png)

> whereis 会搜索命令所在目录和帮助文档位置

**grep**

![image-20200712232416213](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712232416213.png)

> 查找注释 用v排除# 要用^#来表示是行首的#避免排除掉中间的

#### 帮助命令

**man**

![image-20200712235557678](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200712235557678.png)

> 1命令的帮助 5配置文件的帮助
>
> 如果想知道命令是做什么用的，可以用whatis 命令 配置文件 apropos 配置文件
>
> 查询常用选项 命令 --help

**help**

![image-20200713081016826](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713081016826.png)

#### 用户管理命令

**useradd**

![image-20200713085156754](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713085156754.png)

**passwd**

![image-20200713085222070](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713085222070.png)

**who**

![image-20200713091614432](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713091614432.png)

**w**

* 查看登陆用户**详细**信息

**uptime**

* 查看登陆用户**缩略**信息

#### 压缩

**gzip**

![image-20200713092147206](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713092147206.png)

> 压缩后源文件会被删除

**gunzip**

![image-20200713092212680](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713092212680.png)

> 只能压缩文件

**tar**

![image-20200713095226283](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713095226283.png)

![image-20200713095855948](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713095855948.png)

**zip**

![image-20200713100957299](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713100957299.png)

> 会保留原文件

**unzip**

![image-20200713100508807](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713100508807.png)

**bzip2**

![image-20200713100531419](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713100531419.png)

**bunzip2**

![image-20200713100648212](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713100648212.png)

#### 网络命令

**write**

![image-20200713101433410](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713101433410.png)

**wall**

![image-20200713101826900](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713101826900.png)

**ping**

![image-20200713162820886](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713162820886.png)

**ifconfig**

![image-20200713163033219](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713163033219.png)

**mail**

![image-20200713163518417](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713163518417.png)

**last**

![image-20200713163802840](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713163802840.png)

**lastlog**

![image-20200713163841117](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713163841117.png)

**traceroute**

![image-20200713163933099](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713163933099.png)

**netstat**

![image-20200713171015428](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713171015428.png)

![image-20200713170941382](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713170941382.png)

**mount**

![image-20200713201415059](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713201415059.png)

#### 关机重启命令

**shutdown**

![image-20200713202525278](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713202525278.png)

![image-20200713202957680](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713202957680.png)

![image-20200713203838397](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713203838397.png)

### Vim

![image-20200713204415482](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713204415482.png)

**插入**

![image-20200713210657766](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713210657766.png)

**定位**

![image-20200713210836814](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713210836814.png)

**删除**

![image-20200713211305560](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713211305560.png)

**复制和剪切**

![image-20200713212144512](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713212144512.png)

**区块选择(Visual Block)**

![image-20200810093413385](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810093413385.png)

**替换和取消**

![image-20200713212350349](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713212350349.png)

**搜索和搜索替换**

![image-20200713212746988](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713212746988.png)

**保存和退出**

![image-20200713212959247](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200713212959247.png)

**其他**

![image-20200714000400498](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200714000400498.png)

> r 为导入， ！命令为执行命令 两者可以分开
>
> 可将定义快捷键和替换vi入相应用户的.vimrc文件中持久化保持

**多文件编辑**

1. 透过『 vim hosts /etc/hosts 』指令来使用一个 vim 开启两个文件；
2. ![image-20200810093506052](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810093506052.png)

**多窗口功能**

**如果想要在新窗口启动另一个文件，就加入档名，否则仅输入 :sp 时， 出现的则是同一个文件在两个窗口间！**

![image-20200810093615289](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810093615289.png)

![image-20200810093627915](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810093627915.png)

**vim 的挑字补全功能**

![image-20200810093710090](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200810093710090.png)

* **vim 会主动的将你曾经做过的行为登录下来，好让你下次可以从上次编辑的地方开始！ 那个记录动作的文件就是： \~/.viminfo ！**

### 软件包

#### 分类

**源码包**

* 为各种语言的包，如C、JAVA
*   安装时间比二进制包慢较多，因为要编译，但是会更契合系统环境，使得软件执行效率较高

    ![image-20200717172853194](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717172853194.png)

    ![image-20200717173208628](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717173208628.png)

    **脚本安装包**

**二进制包 （RPM包，系统默认包）**

* windows是exe
* java的class文件也是二进制文件，但是其为十六进制的由一序列 op 代码/数据对 组成的中间代码，需要直译器转义后才能称为机器码为操作系统调用

![image-20200717174625949](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717174625949.png)

![image-20200717174634769](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717174634769.png)

#### RPM命令管理

类似Ubuntu中的dpkg

> #### dpkg命令：
>
> dpkg是Debian系统的后台包管理器,类似RPM。也是Debian包管理系统的中流砥柱,负责安全卸载软件包,配置,以及维护已安装的软件包。由于ubuntu和Debian乃一脉相承，所以很多命令是不分彼此的。
>
> Ubuntu中所有packages的信息都在/var/lib/dpkg/目录下，其中子目录”/var/lib/dpkg/info”用于保存各个软件包的配置文件列表.不同后缀名代表不同类型的文件，如:
>
> .conffiles 记录了软件包的配置文件列表。
>
> .list 保存软件包中的文件列表,用户可以从.list的信息中找到软件包中文件的具体安装位置。
>
> .md5sums 记录了软件包的md5信息，这个信息是用来进行包验证的。
>
> .prerm 脚本在Debian报解包之前运行，主要作用是停止作用于即将升级的软件包的服务,直到软件包安装或升级完成。
>
> .postinst脚本是完成Debian包解开之后的配置工作，通常用于执行所安装软件包相关命令和服务重新启动。
>
> /var/lib/dpkg/available文件的内容是软件包的描述信息，该软件包括当前系统所使用的Debian安装源中的所有软件包,其中包括当前系统中已安装的和未安装的软件包。
>
> 命令:
>
> * dpkg –l | grep package 查询deb包的详细信息，没有指定包则显示全部已安装包
> * dpkg -s package 查看已经安装的指定软件包的详细信息
> * dpkg -L package 列出一个包安装的所有文件清单
> * dpkg -S file 查看系统中的某个文件属于哪个软件包,搜索已安装的软件包
> * dpkg -i 安装指定deb包
> * dpkg -R 后面加上目录名，用于安装该目录下的所有deb安装包
> * dpkg -r remove，移除某个已安装的软件包
> * dpkg -P 彻底的卸载，包括软件的配置文件
> * dpkg -c 查询deb包文件中所包含的文件
> * dpkg -L 查看系统中安装包的的详细清单，同时执行 -c

**安装、更新**

![image-20200717203654114](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717203654114.png)

![image-20200717203938055](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717203938055.png)

![image-20200717205721788](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717205721788.png)

![image-20200717211120938](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717211120938.png)

![image-20200717211819230](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717211819230.png)

**查询、卸载**

![image-20200717212018916](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717212018916.png)

![image-20200717212334586](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717212334586.png)

> \-i 查询软件信息
>
> \-p 查询未安装包信息
>
> \-l 列表
>
> \-f 查询系统文件属于哪个软件包 eg：-qf系统文件名 （需要路径）
>
> \-R 查询软件包的依赖性（ requires）
>
> * 删查改，以安装的不需要进入相应目录（只需要用包名），他会在操作系统的数据库中查询，未安装的需要到相应文件夹（光盘目录），在进行查询（用包全名）

**校验和文件提取**

![image-20200717220257036](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200717220257036.png)

![image-20200718140722400](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718140722400.png)

![image-20200718140604894](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718140604894.png)

![image-20200718140708790](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718140708790.png)

![image-20200718140855727](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718140855727.png)

#### yum命令管理

**ubuntu系统的网络配置文件**

Ubuntu 的网络配置文件主要有以下几个

IP地址配置文件： /etc/network/interfaces 打开后里面可设置DHCP或手动设置静态ip。前面auto eth0，让网卡开机自动挂载.

1. **以DHCP方式配置网卡**

编辑文件/etc/network/interfaces: sudo vi /etc/network/interfaces

并用下面的行来替换有关eth0的行: # The primary network interface - use DHCP to find our address

```
auto eth0
iface eth0 inet dhcp
```

用下面的命令使网络设置生效: sudo /etc/init.d/networking restart 也可以在命令行下直接输入下面的命令来获取地址

sudo dhclient eth0

1. **为网卡配置静态IP地址**

编辑文件/etc/network/interfaces:

sudo vi /etc/network/interfaces

并用下面的行来替换有关eth0的行:# The primary network interface

```
auto eth0
iface eth0 inet static
address 192.168.3.90
gateway 192.168.3.1
netmask 255.255.255.0
```

将上面的ip地址等信息换成你自己就可以了.用下面的命令使网络设置生效: sudo /etc/init.d/networking restart

1. **设定第二个IP地址(虚拟IP地址)**

编辑文件/etc/network/interfaces:

sudo vi /etc/network/interfaces

在该文件中添加如下的行:

```
auto eth0:1
iface eth0:1 inet static
address 192.168.1.60
netmask 255.255.255.0
network x.x.x.x
broadcast x.x.x.x
gateway x.x.x.x
```

根据你的情况填上所有诸如address,netmask,network,broadcast和gateways等信息. 用下面的命令使网络设置生效: sudo /etc/init.d/networking restart

主机名称配置文件(/bin/hostname)

使用下面的命令来查看当前主机的主机名称:

sudo /bin/hostname

使用下面的命令来设置当前主机的主机名称:

sudo /bin/hostname newname

系统启动时,它会从/etc/hostname来读取主机的名称.

DNS配置文件

首先,你可以在/etc/hosts中加入一些主机名称和这些主机名称对应的IP地址,这是简单使用本机的静态查询.

要访问DNS 服务器来进行查询,需要设置/etc/resolv.conf文件.

```
sudo vi /etc/resolv.conf

nameserver 202.96.128.68

nameserver 61.144.56.101

nameserver 192.168.8.220
```

/重新设置网络，以启用新设置

sudo /etc/init.d/networking restart

**网络yum源**

![image-20200718142959522](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718142959522.png)

**yum/apt命令**

> 更改配置文件要注意他的格式，如果格式出错也有可能报错

**源码包**

![image-20200718164306927](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718164306927.png)

![image-20200718165204188](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718165204188.png)

![image-20200718165609684](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718165609684.png)

* 对于不认识的源码包，需要调用INSTALL安装说明文件查看安装步骤

![image-20200718184811919](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718184811919.png)

![image-20200718184727358](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718184727358.png)

**脚本安装包**

> 主要是针对硬件驱动，比较难以自己独立安装

![image-20200718185208798](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200718185208798.png)

> 1. 安装相应的脚本安装包
> 2. 解压
> 3. 运行相应的.sh文件

### 用户和用户组管理

#### 用户配置文件

**用户信息文件/etc/passwd**

* 用户管理简介：
* 越是对服务器安全性要求高的服务器，越需要建立合理的用户权限登记制度和服务器操作规范
* **在Linux中主要是通过用户配置文件来查看和修改用户信息** **/etc/passwd** 通过命令：man 5 passwd 来查看passwd配置文件帮助信息 5代表配置文件
* passwd配置文件分为七个字段：
  * 第一个字段：用户名称
  * 第二个字段：**密码标志 x代表用户有密码，真正的密码放在/etc/shadow里**，它里面进行了SHA512加密，**如果没有x，代表没有密码，只能进行本地登陆，远程登陆会被禁止，SHA协议禁止**
  * 第三个字段：UID（用户ID）
    * 0：超级用户
    * 1-499：系统用户（伪用户）
    * 500-65535：普通用户
    * **windows修改用户类型一般是加入root用户组，Linux直接修改UID**
  * 第四个字段：GID（**用户初始组ID**）
    * 初始组：就是指用户一登陆就立刻拥有这个用户组的相关权限，**每个用户的初始组只能有一个**，一般就是和这个用户的用户名相同的组名作为这个用户的初始组，**创建用户时自动加入的组**
    * **附加组：指用户可以加入多个其他的用户组，并拥有这些组的权限，附加组可以有多个**（将**用户**添加到相应的**gourp**后面）
  * **第五个字段：用户说明**
  * **第六个字段：家目录**
    * **普通用户：/home/用户名/**
    * **超级用户：/root/**
  * **第七个字段：登陆之后的Shell** **Shell就是Linux的命令解释器 在/etc/passwd当中，除了标准Shell是/bin/bash（为可执行程序，不为文件）之外。还可以写如/sbin/nologin，普通用户全为/sbin/nologin /bin/bash为可以登陆，/sbin/nologin为禁止登陆，修改之后便可以禁止普通用户登陆**

**影子文件/etc/shadow**

**Shadow配置文件分为个字段：**

* 第一个字段：用户名
* 第二个字段：**加密算法** 加密算法升级为SHA512散列加密算法 **如果密码是“!!”或者“0”代表没有密码，不能登陆** 可以用感叹号禁用用户密码
* 第三个字段：**密码最后一次修改日期** 使用1970年1月1日作为标准时间，每过一天时间戳加1 时间戳换算： 时间戳换算日期： date -d “1970-01-01 \[时间戳] days” 日期换算时间戳： echo((((((date --date=“年/月/日” +%s)/\[时间戳]+1))
* 第四个字段：**两次密码的修改时间间隔时间（和第三个字段相比）**
* 第五个字段：**密码有效期（和第三个字段相比）**
* 第六个字段：密码修改到期前的警告天数（和第五个字段相比）
* 第七个字段：密码过期后的宽限天数（和第五个字段相比） 0：代表密码过期后立即失效 -1：代表密码永远不会失效
* 第八个字段：**账号失效时间** **要用时间戳表示，优先级比第五字段高**
* 第九个字段：保留字段，未设置

**组文件信息/etc/group**

组文件信息配置文件分为个字段：

* 第一个字段：组名
* 第二个字段：组密码标志
* 第三个字段：GID
* 第四个字段：组中附加用户（**作为某用户的初始组时不会将用户名写入该字段，若是创建用户为默认创建，则第一字段与用户名相同，不然在/etc/passwd 第四个字段中标识自己的初始组，初始组与用户的绑定信息仅在/etc/passwd 第四字段，不在该文件）**

**组密码文件/etc/gshadow**

组密码文件配置文件分为个字段：（一般不用配置到该文件）

* 第一个字段：组名
* 第二个字段：组密码
* 第三个字段：组管理员用户名
* 第四个字段：组中附加用户

#### 用户相关服务文件

* 用户的家目录
  * 普通用户：/home/用户名/，所有者和所属组都是此用户，权限是700
  * 超级用户：/root/，所有者和所属组都是root用户，权限是550(权限对他来说没有意义)

**普通用户变为超级用户需要修改/etc/passwd文件的UID，但家目录不会变，仍为/home/username**

* 用户的邮箱 /var/spool/mail/用户名/
* 用户模板目录 /etc/skel 在/etc/skel里创建了文件的话，在创建新用户时会自动出现在新用户家目录的默认隐藏信息里（用于写警告信息）

#### 用户管理命令

**用户添加命令useradd**

**useradd命令格式**

* useradd\[选项] 用户名
  * \-u UID：手工指定用户的UID号
  * \-d 家目录：手工指定用户的家目录，指定后它会自动创建并分配权限给用户，自己创建会将所有者设置为root，很麻烦
  * \-c 用户说明：手工指定用户的说明
  * \-g 组名：手工指定用户的初始组，如果自己设定导致和用户名不同，容易导致管理时不明白他的作用导致混乱
  * \-G 组名：手工指定用户的附加组，GID默认与UID一样
  * \-s shell：手工指定用户的登陆shell。默认是/bin/bash

\*\*例子： useradd yangyang（大致会进行以下几个文件的创建） grep yangyang /etc/passwd grep yangyang /etc/shadow grep yangyang /etc/group grep yangyang /etc/gshadow ll -d /home/yangyang ll /var/spool/mail/yangyang \*\*

* **添加用户时默认读取的配置文件，里面定义了建立用户的默认信息**
* /etc/default/useradd
  * **GROUP=100 用户默认组**
  * **HOME=/home 用户家目录**
  * **INACTIVE=-1 密码过期宽限天数（shadow文件第七个字段）**
  * **EXPIRE= 密码失效时间（shadow文件第八个字段）**
  * **SHELL=/bin/bash 默认shell**
  * **SKEL=/etc/skel 模板目录**
  * **CREATE\_MALL\_SPOOL=yes 是否建立邮箱**
* /etc/login.defs
  * **PASS\_MAX\_DAYS 99999 密码有效期shadow文件第五个字段）**
  * **PASS\_MIN\_DAYS 0 密码修改间隔shadow文件第四个字段）**
  * **PASS\_MIN\_LEN 5 密码最小5位（PAM）** **现在为PAM生效，5位密码不生效**
  * **PASS\_WARN\_AGE 7 密码到期警告shadow文件第六个字段）**
  * **UID\_MIN 500**
  * **UID\_MAX 60000 最小和最大UID范围**
  * **ENCRYPT\_METHOD SHA512 加密模式**

**修改用户密码passwd**

**passwd \[选项] 用户名**

* \-S 查询用户密码的密码状态。仅root用户可以
* \-l 暂时锁定用户/仅root用户可用 **查看shadow可以看出加密密码前加了！！，使得输入的密码加密后不匹配而达到锁定的效果**
* \-u 解锁用户。仅root用户可用
* –stdin 可以通过管道符(|)输出的数据作为用户的密码（shell编程用） 例子：echo“123” | passwd – stdin \[用户名 ]

**修改用户信息usermod**

**usermod \[选项] 用户名**

* \-u UID：**修改用户的UID号**
* \-c 用户说明：**修改用户的说明信息**
* \-G 组名：修改用户的附加组
* \-L：临时锁定用户（Lock）**只加一个！**
* \-U：解锁用户锁定（Unlock）

**修改用户密码状态chage**

**chage \[选项] 用户名** **（除了-d 其他的一般用vim /etc/shadow）**

* \-l：列出用户的详细密码状态
* **-d \[日期] ：修改密码最后一次更新时间（shadow文件第三个字段）** **例子重要用法：** **chage -d 0 \[用户名]** **这个命令其实是把密码修改日期归0了，这样用户一登陆就要修改密码（学校，公司身份系统修改初始密码）**
* \-m \[天数]：两次修改密码间隔（shadow文件第四个字段）
* \-M \[天数]：密码有效期（shadow文件第五个字段）
* \-W \[天数]：密码过期前警告天数（shadow文件第六个字段）
* \-I \[天数]：密码过期后宽限天数（shadow文件第七个字段）
* \-E \[日期]：账号失效时间（shadow文件第八 个字段）

**删除用户userdel**

**userdel \[-r] 用户名**

* **-r：删除用户的同时删除用户家目录**
* **Id 用户名 查看用户ID，UID，GID**

**用户切换命令su**

**su \[选项] 用户**(会在shell中生成一个新的shell，用完最好exit)

* **- :选项只使用“-”代表连带用户的环境变量一起切换**
  * **env：查看用户环境变量**
* **-c：仅执行一次命令，而不切换用户身份** **例子： su - root -c “useradd yangyang” 不切换成root，但是执行useradd命令添加yangyang用户**

#### 用户组管理命令

groupadd \[选项] 组名

* \-g GID：指定组ID

groupmod \[选项] 组名 修改用户组

* \-g GID：修改组ID
* \-n 新组名：修改组名

groupdel 组名：删除用户组 **gpasswd 选项 组名 ：把用户添加入组或者从组中删除**（acl）

* **-a 用户名 ：把用户加入组**
* **-d 用户名 ：把用户从组中删除**

### 权限管理

**ACL权限简介与开启**

ACL权限是为了解决所有者，所属组，其他人三个权限**用户身份分配不足的问题**（直接添加用户到权限组中，是前面三个文件系统没有的补充）

* dumpe2fs -h \[分区] dumpe2fs命令是查询指定分区详细文件系统信息的命令
* \-h ：仅显示超级块中信息，而不显示磁盘块组的详细信息

**临时开启分区ACL权限** **mount -o remount,acl / 重新挂载根分区，并挂载加入acl权限** **永久开启分区ACL权限** **vim /etc/fstab** **显示：UUID=59d9ca7b-4f39-4c0c-9334-c56c182076b5 / ext4 defaults 1 1** **在ext4后面的 defaults加,acl 成为** **UUID=59d9ca7b-4f39-4c0c-9334-c56c182076b5 / ext4 defaults,acl 1 1** **然后输入：mount -o remount /** **重新挂载文件系统或重启系统，使修改生效** **Linux现在一般所有分区全部默认开启ACL，不用修改配置**

**ACL权限查看与设定**

* **getfacl 文件名 查看ACL命令 查看ACL权限**
* **setfacl \[选项] 文件名**
  * **-m：设定ACL权限** **例子：**
    * **setfacl -m u:st:rx /tmp/project 给用户设定ACL权限读和操作**
    * **setfacl -m g:tg1:rwx /tmp/project 给用户组设定ACL权限读，写和操作**
  * **-x：删除指定的ACL权限**
  * **-b：删除所有的ACL权限**
  * **-d：设定默认的ACL权限**
  * **-k：删除默认ACL权限**
  * **-R：递归设定ACL权限**

**ACL权限最大有效权限与删除**

**最大有效权限mask** mask是用来指导最大有效权限的。如果给用户赋予了ASL权限，是需要和**mask 的权限“按位与”才能得到用户的真正权限**

* setfscl -m m:rx 文件名 设定mask权限为r-x。使用“m:权限”格式 **为了防止用户或者用户组给的权限过高，提前设定，对于ACL和文件本身的group生效，对于文件本身的user无效**

**权限的删除**

* setfacl -x u:用户名 文件名 删除指定用户的ACL权限
* setfacl -x g:组名 文件名 删除指定用户组的ACL权限
* **setfacl -b 文件名 会删除文件的所有ACL权限，mask也会删除**

**ACL权限默认与递归**

**递归ACL权限（针对现有的子文件/子目录）** 递归是父目录在设定ACL权限时，所有的子文件和子目录也会拥有相同的ACL权限 命令：

* **setfacl -m u:用户名:权限 -R 目录名 -R必须在这个位置**

**默认ACL权限（针对以后新建的子文件/子目录）** 默认ACL权限的作用是如果给父目录设定默认ACL权限，那么父目录中所有新建的子文件和子目录都会继承父目录的ACL权限 命令：

* **Setfacl -m d:u:用户名:权限 目录名 可以在权限后面加-R来进行递归**

**SetUID**

* **只有可以执行的（二进制程序）才能设定SUID权限，普通文件或者目录没有意义**
* **命令执行者要对该程序拥有x(执行)权限**
* **命令执行者在执行该程序时获得该程序文件属主的身份(在执行程序的过程中灵魂附体为文件的属主)**
* **SetUID权限只在该程序执行过程中有效，也就是说身份改变只在程序执行过程中有效**

**例子：** **passwd命令拥有SetUID权限，所以普通用户可以修改自己的密码**

* 设定SetUID的方法
  * **chmod 4755 文件名 4代表SUID权限**
  * **chmod u+s 文件名**
* 取消SetUID的方法
  * **chmod 755 文件名**
  * **chmod u-s 文件名**

**如果其他用户对文件没有执行权限，就会报错，此时给他加特殊权限，就会显示大S**（Ubuntu全都是s，没有权限也不会变成S） **危险的SetUID** **关键目录应当严格控制写权限。比如：“/”，“/usr”，vim命令等 用户的密码设置要严格遵守密码三原则 对系统中默认应该具有SetUID权限的文件作一列表，定时检查有没有这之外的文件被设置了SetUID权限**

**SetGID**

**SetGID针对文件**

* **只有可以执行的（二进制程序）才能设定SGID权限**
* **命令执行者要对该程序拥有x(执行)权限**
* **命令执行在执行程序的时候，组身份升级为该程序文件的所属组SetGID权限同只在该程序执行过程中有效，也就是说组身份改变只在程序执行过程中有效**

**例子：** **locate 命令拥有SetGID权限，所以普通用户可以使用locate来查询**

* 设定SetUID的方法
  * **chmod 2755 文件名 2代表SGID权限**
  * **chmod g+s 文件名**
* 取消SetUID的方法
  * **chmod 755 文件名**
  * **chmod g-s 文件名**

**SetGID针对目录**（是为了下面的文件新建时就只能让文件夹的组读取，而不用用户自己默认的组再自己往自己组里添加成员） **普通用户必须对此目录拥有r和x权限，才能进入此目录** **普通用户在此目录中的有效组会变成此目录的所属组** **若普通用户对此目录拥有w权限时，新建的文件的默认所属组是这个目录的所属组**

**Sticky BIT**

**SBIT粘着位作用**

* **粘着位目前只对目录有效**
* **普通用户对该目录拥有w和x权限，即普通用户可以在此目录拥有写入权限**（若是没有w不像前面的s会变成S，而是仍未t）
* **如果没有粘着位，因为普通用户拥有w权限，使用可以删除此目录下所有文件，包括其他用户建立的文件。一旦赋予了粘着位，除了root可以删除所有文件，普通用户就算拥有了w权限，也只能删除自己建立的文件，但是不能删除其他用户建立的文件**
* **设定粘着位的方法**
  * **chmod 1755 目录名 1代表粘着位**
  * **chmod o+t 目录名**
* **取消粘着位的方法**
  * **chmod 755 目录名**
  * **chmod o-t 目录名**

**文件系统属性chattr权限**

**chattr命令格式**（为了防止包括root在内的用户误删目录下的文件/修改文件）（文件系统新建的一个权限管理文件管理）

* chattr \[+ - =] \[选项] 文件名或者目录名
  * **+：增加权限**
  * **-：删除权限**
  * **=：等于某权限**
  * **i：如果对文件设置i属性，那么不允许对文件进行删除，改名，也不能添加和修改数据；如果对目录设置i属性,那么只能修改目录下文件的数据，但是不允许建立和删除文件**
  * **a：如果对文件设置a属性，那么只能在文件中增加数据，但是不能删除或者修改数据；如果对目录设置a属性,那么只允许在目录中建立和修改文件，但是不允许删除文件**

**查看文件系统属性**

* lsattr \[选项] 文件名
  * **-a：显示所有文件和目录**
  * **-d：若目标是目录，仅列出目录本身的属性，而不是子文件的**

**系统命令sudo权限**

**（上面的针对的是文件和目录的权限，这个是对命令的）**

**sudo权限**

* **root把本来只能超级用户执行的命令赋予普通用户执行**
* **sudo的操作对象是系统命令**

**sudo使用**

* visudo 实际修改的是/etc/sudoers文件
  * **root ALL=(ALL) ALL** **用户名 被管理主机的地址=(可使用身份) 授权命令(绝对路径)**
  * **%wheel ALL=(ALL) ALL** **%组名 被管理主机的地址=(可使用的身份) 授权命令(绝对路径)** **被管理主机的地址：本机IP或者ALL，限制的不是来源IP，而是访问IP**
* **授权命令需要全部一样才行，若有路径，路径也需要写**

**例子：** **授权用户可以可以重启服务器** **visudo** **yangyang ALL= /sbin/shutdown –r now**

**sudo -l 查看可用的sudo命令** **sudo \[授权命令的绝对路径] 普通用户执行sudo赋予的命令** **例子： sudo /sbin/shutdown -r now**

### Linux 文件系统

**文件系统的特性**

* 不同操作系统常用的文件系统

> window：FAT/NTFS Linux:ext2（ext2fs）/ext3/ext4 U盘: FAT
>
> 传统磁盘与文件系统一般为一个文件系统一个硬盘分区，但LVM和软件磁盘阵列可以将一个分区格式化为多个文件系统（LVM），也可以将多个分区合成一个文件系统。
>
> 文件系统一开始就将 inode 与 block 规划好了，除非重新格式化(或者利用resize2fs 等指令变更文件系统大小)，否则 inode 与 block 固定后就不再变动。

* 超级区块（super block）、inode、数据区块

> * superblock：记录此 filesystem 的整体信息，包括 inode/block 的总量、使用量、剩余量， 以及文件系统的
>
> 格式与相关信息等；
>
> * inode：记录文件的属性，一个文件占用一个 inode，同时记录此文件的数据所在的 block 号码；（索引式文件系统(indexed allocation)）
> * block：实际记录文件的内容，若文件太大时，会占用多个 block 。
>
> 因为inode 与 block 的数量太庞大，不容易管理，所以出现了：区块群组 (block group)
>
> 文件系统最前面有一个启动扇区(boot sector)，这个启动扇区可以安装开机管理程序
>
> ![image-20200805173924152](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200805173924152.png)

* FAT

> FAT 这种格式的文件系统并没有 inode 存在，所以 FAT 没有办法将这个文件的所有 block 在一开始就读取出来。
>
> 每个 block 号码都记录在前一个 block 当中。
>
> 导致果同一个文件数据写入的 block 分散的太过厉害时，则我们的磁盘读取头将无法在磁盘转一圈就读到所有的数据，因此磁盘就会多转好几圈才能完整的读取到这个文件的内容，效率过低，因此需要**碎片整理**
>
> 需要碎片整理的原因就是文件写入的 block 太过于离散了，此时文件读取的效能将会变的很差所致。这个时候可以透过碎片整理将同一个文件所属的 blocks 汇整在一起，这样数据的读取会比较容易啊！

**ext2**

* 数据区块（data block）:是用来放置文件内容数据地方

> 1. 在 Ext2 文件系统中所支持的 block 大小有 1K, 2K 及4K 三种而已。
> 2. 每个 block 都有编号，以方便 inode 的记录
> 3. 原则上，block 的大小与数量在格式化完就不能够再改变了(除非重新格式化)；
> 4. 每个 block 内最多只能够放置一个文件的数据；
>    * 如果文件大于 block 的大小，则一个文件会占用多个 block 数量；
>    * 若文件小于 block ，则该 block 的剩余容量就不能够再被使用了(磁盘空间会浪费)

* inode table(inode 表):记录文件的属性以及该文件实际数据是放置在哪几号 block 内

> 1. inode 记录的文件数据至少有底下这些:
> 2. 该文件的存取模式(read/write/excute)
> 3. 该文件的拥有者与群组(owner/group)
> 4. 该文件的容量；
> 5. 该文件建立或状态改变的时间(ctime)；
> 6. 最近一次的读取时间(atime)；
> 7. 最近修改的时间(mtime)；
> 8. 定义文件特性的旗标(flag)，如 SetUID...；
> 9. 该文件真正内容的指向 (pointer)
> 10. 每个 inode 大小均固定为 128 bytes (新的 ext4 与 xfs 可设定到 256 bytes)；
> 11. 每个文件都仅会占用一个 inode 而已,因此文件系统能够建立的文件数量与 inode 的数量有关；
> 12. 系统读取文件时需要先找到 inode，并分析 inode 所记录的权限与用户是否符合，若符合才能够开始实际
>
>     读取 block 的内容.
>
> inode 的结构:
>
> ![image-20200805174954460](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200805174954460.png)
>
> 12 个直接指向： 12\*1K=12K
>
> 由于是直接指向，所以总共可记录 12 笔记录，因此总额大小为如上所示；
>
>  间接： 256\*1K=256K
>
> 每笔 block 号码的记录会花去 4bytes，因此 1K 的大小能够记录 256 笔记录，因此一个间接可以记录的
>
> 文件大小如上；
>
>  双间接： 256\_256\_1K=2562K
>
> 第一层 block 会指定 256 个第二层，每个第二层可以指定 256 个号码，因此总额大小如上；
>
>  三间接： 256\_256\_256\*1K=2563K
>
> 第一层 block 会指定 256 个第二层，每个第二层可以指定 256 个第三层，每个第三层可以指定 256 个号
>
> 码，因此总额大小如上；
>
>  总额：将直接、间接、双间接、三间接加总，得到 12 + 256 + 256\_256 + 256\_256\*256 (K) = 16GB

* Superblock（超级区块）

> 1. 数据区块与inode的总量
> 2. 未使用与已使用的inode与数据区块 数量
> 3. block 与 inode 的大小 (block 为 1, 2, 4K，inode 为 128bytes 或 256bytes)；
> 4.  filesystem 的挂载时间、最近一次写入数据的时间、最近一次检验磁盘 (fsck) 的时间等文件系统的相关信
>
>     息；
> 5. 一个 valid bit 数值，若此文件系统已被挂载，则 valid bit 为 0 ，若未被挂载，则 valid bit 为 1 。
> 6. 除了第一个 block group 内会含有 superblock 之外，后续的 block group 不一定含有 superblock ， 而若含有 superblock 则该 superblock 主要是做为第一个 block group 内 superblock 的备份

* Filesystem Description（文件系统描述说明）

> 这个区段可以描述每个 block group 的开始与结束的 block 号码，以及说明每个区段 (superblock,
>
> bitmap, inodemap, data block) 分别介于哪一个 block 号码之间。这部份也能够用 dumpe2fs 来观察的。

* block bitmap（区块对照表）

> 记录block的状态，用于增删时效率更高地执行

* inode bitmap（inode 对照表）

> 记录使用与未使用地inode号码

* dumpe2fs：查询ext系列超级区块信息的命令

**与目录树的关系**

* 目录

> 建立一个目录时，文件系统会分配一个inode与至少一块区块给该目录
>
> * inode记录该目录的相关权限与属性，并记录分配到的block号码。
> * block记录该目录下的文件名与该文件名占用的inode号码数据

* 文件

> 一般是一个inode+文件大小的区块数量
>
> 若是用到间接则多用一个区块记录来记录区块号码

* 目录树读取

> * 由于目录树是由根目录开始读起，因此系统透过挂载的信息可以找到挂载点的 inode 号码，此时就能够得到根目录的 inode 内容，并依据该 inode 读取根目录的 block 内的文件名数据，再一层一层往下读到正确的档名。
> * 虽说block的号码由inode记录了，但是若是太分散，如一个在磁盘头一个在磁盘尾，那机械手臂移动幅度也会过大，导致性能下降，因此要规划好

**文件存取与日志**

* 新增文件时，文件系统的操作是：

> * 一般地，称inode table 和 data block 为数据存放区域
> * superblock、block bitmap、 inode bitmap 称为元数据（metadata）

* 数据的不一致（Inconsistent）性

> 若因为断电等原因第四步没完成，若是传统地则是元数据与实际数据对比，效率过低

* 日志式文件系统（Journaling filesystem）

1. 预备：当系统要写入一个文件时，会先在日志记录区块中纪录某个文件准备要写入的信息；
2. 实际写入：开始写入文件的权限与数据；开始更新 metadata 的数据；
3. 结束：完成数据与 metadata 的更新后，在日志记录区块当中完成该文件的纪录。

**内存与磁盘**

​ 如果内存中的文件数据被更改过了(例如你用 nano 去编辑过这个文件)，此时该内存中的数据会被设定为脏的 (Dirty)。此时所有的动作都还在内存中执行，并没有写入到磁盘中！系统会不定时的将内存中设定为『Dirty』的数据写回磁盘，以保持磁盘与内存数据的一致性。

​ 系统会将常用的文件数据放置到主存储器的缓冲区，以加速文件系统的读/写，因此 Linux 的物理内存最后都会被用光！这是正常的情况！可加速系统效能；

​ 你可以手动使用 sync 来强迫内存中设定为 Dirty 的文件回写到磁盘中；

​ 若正常关机时，关机指令会主动呼叫 sync 来将内存的数据回写入磁盘内；

​ 但若不正常关机(如跳电、当机或其他不明原因)，由于数据尚未回写到磁盘内， 因此重新启动后可能会花很多时间在进行磁盘检验（日志式文件系统），甚至可能导致文件系统的损毁(非磁盘损毁)。

​

**挂载点的意义（mount point）**

* 挂载：将文件系统与目录树结合的操作，挂载点一定是目录，该目录为进入该文件系统的入口
* 会用一个inode（如centOS的128）标记挂载的目录在一个block中。

![image-20200807130818086](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200807130818086.png)

* **同一个文件系统的某个inode只会对应到一个文件内容而已（因为一个文件占用一个inode）**，因此我们可以通过判断inode号码来确认不同文件名是否为相同的文件，或者判断目录是不是属于同一个inode

**Linux支持的文件系统**

* 传统文件系统：ext2 / minix / MS-DOS / FAT (用 vfat 模块) / iso9660 (光盘)等等；
* 日志式文件系统： ext3 /ext4 / ReiserFS / Windows' NTFS / IBM's JFS / SGI's XFS / ZFS
* 网络文件系统： NFS / SMBFS

想要知道你的 Linux 支持的文件系统有哪些：

\[root@study \~]# **ls -l /lib/modules/$(uname -r)/kernel/fs**

系统目前已加载到内存中支持的文件系统则有：

\[root@study \~]# **cat /proc/filesystems**

**VFS**

整个 Linux 的系统都是透过一个名为 Virtual Filesystem Switch 的核心功能去读取 filesystem 的。 也就是说，整个 Linux 认识的 filesystem 其实都是 VFS 在进行管理，我们使用者并不需要知道每个 partition 上头的 filesystem 是什么～ VFS 会主动的帮我们做好读取的动作呢

![image-20200807131517972](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200807131517972.png)

**XFS**

* Ext 文件系统家族对于文件格式化的处理方面，采用的是预先规划出所有的 inode/block/meta data 等数据，未来系统可以直接取用， 不需要再进行动态配置的作法。**格式化太慢了**
* xfs 就是一个日志式文件系统，而 CentOS 7.x 拿它当预设的文件系统，几乎所有 Ext4 文件系统有的功能， xfs 都可以具备。
* xfs 文件系统在资料的分布上，主要规划为三个部份，一个资料区 (data section)、一个文件系统活动登录区 (log section)以及一个实时运作区 (realtime section)。 这三个区域的数据内容如下：
* 资料区（data section）

> 这个数据区与 ext 家族的 block group 类似，也是分为多个储存区群组(allocation groups) 来分别放置文件系统所需要的数据。
>
> 每个储存区群组都包含了：
>
> 1. 整个文件系统的 superblock
> 2. 剩余空间的管理机制
> 3. inode 的分配与追踪
> 4. inode 与 block 都是系统需要用到时， 这才动态配置产生，所以格式化动作超级快！
> 5. 与 ext 家族不同的是， xfs 的 block 与 inode 有多种不同的容量可供设定，block 容量可由 512bytes \~ 64K 调配，不过，**Linux 的环境下**， 由于**内存控制**的关系 (页面文件 pagesize 的容量之故)，因此最高可以使用的 block 大小为 4K 而已！否则即使格式化也**无法挂载**
> 6. 至于 inode 容量可由 256bytes 到 2M 这么大！不过，大概还是保留 256bytes 的默认值就很够用了！

* 文件系统活动登录区 (log section)

> 在登录区这个区域主要被用来纪录文件系统的变化，其实有点像是日志区啦！文件的变化会在这里纪录下来，直到该变化完整的写入到数据区后， 该笔纪录才会被终结。如果文件系统因为某些缘故 (例如最常见的停电) 而损毁时，系统会拿这个登录区块来进行检验.
>
> 因为系统所有动作的时候都会在这个区块做个纪录，因此这个区块的磁盘活动是相当频繁的！xfs 设计有点有趣，在这个区域中，
>
> 妳**可以指定外部的磁盘来作为 xfs 文件系统的日志区块**喔！例如，妳可以将 SSD 磁盘作为 xfs 的登录区，这样当系统需要进行任何活动时， 就可以更快速的进行工作！相当有趣！

* 实时运作区 (realtime section)

> 1. 当有文件要被建立时，xfs 会在这个区段里面找一个到数个的 extent 区块，将文件放置在这个区块内
> 2. 等到分配完毕后,再写入到 data section 的 inode 与 block 去！
> 3. 这个 extent 区块的大小得要在格式化的时候就先指定，最小值是 4K 最大可到 1G。一般非磁盘阵列的磁盘默认为 64K 容量，而具有类似磁盘阵列的 stripe 情况下，则建议 extent 设定为与 stripe 一样大较佳。这个extent 最好不要乱动，因为可能会影响到实体磁盘的效能喔。

* **XFS** **文件系统的描述数据观察**

\[root@study \~]# **xfs\_info** **挂载点**\*\*|\*\***装置文件名**

**文件系统的简单操作**

* **磁盘与目录的容量**

 df：列出文件系统的整体磁盘使用量；

 du：评估文件系统的磁盘使用量(常用在推估目录所占容量)

* df

```perl
[root@study ~]# **df [-ahikHTm] [****目录或文件名****]**

选项与参数： 

-a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统； 

-k ：以 KBytes 的容量显示各文件系统； 

-m ：以 MBytes 的容量显示各文件系统； 

-h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；

-H ：以 M=1000K 取代 M=1024K 的进位方式；

-T ：连同该 partition 的 filesystem 名称 (例如 xfs) 也列出；

-i ：不用磁盘容量，而以 inode 的数量来显示
```

* **du**

```perl
[root@study ~]# du [-ahskm] 文件或目录名称
选项与参数： -a ：列出所有的文件与目录容量，因为默认仅统计目录底下的文件量而已。
-h ：以人们较易读的容量格式 (G/M) 显示；
-s ：列出总量而已，而不列出每个各别的目录占用容量；
-S ：不包括子目录下的总计，与 -s 有点差别。 -k ：以 KBytes 列出容量显示；
-m ：以 MBytes 列出容量显示；
```

### 文件系统管理

**df命令、du命令、fsck命令和dump2fs命令**

* df \[选项] \[挂载点]
  * **-a 显示所有的文件系统信息，包括特殊文件系统，如 /proc、/sysfs**
  * **-h 使用习惯单位显示容量，如KB，MB或GB等**
  * **-T 显示文件系统类型**
  * **-m 以MB为单位显示容量**
  * **-k 以KB为单位显示容量。默认就是以KB为单位**

**统计目录或文件大小**

* du \[选项] \[目录或文件名]
  * **-a 显示每个子文件的磁盘占用量。默认只统计 子目录的磁盘占用量**
  * **-h 使用习惯单位显示磁盘占用量，如KB，MB 或GB等**
  * **-s 统计总占用量，而不列出子目录和子文件的 占用量**

**du命令与df命令的区别**

* **df命令是从文件系统考虑的，不光要考虑 文件占用的空间，还要统计被命令或程序占用的空间(最常见的就是文件已经删除 ，但是程序并没有释放空间)**
* **du命令是面向文件的，只会计算文件或目录占用的空间**

**文件系统修复命令fsck**

* fsck \[选项] 分区设备文件名
  * **-a: 不用显示用户提示，自动修复文件系统**
  * **-y:自动修复。和-a作用一致，不过有些文件系统只支持-y**

**注意：知道就行，不必操作，有可能弄崩溃系统**

**显示磁盘状态命令dumpe2fs**

* **dumpe2fs 分区设备文件名**

**挂载命令**

**查询与自动挂载**

* **mount 查询系统中已经挂载的设备**
* **mount -l 查询系统中已经挂载的设备并会显示卷标名称**
* **mount –a 依据配置文件/etc/fstab的内容，自动挂载**

**挂载命令格式** **mount \[-t 文件系统] \[-L 卷标名] \[-o 特殊选项] 设备文件名 挂载点**

* **-t 文件系统:加入文件系统类型来指定挂载的类型，如果文件系统是硬盘，分区就写ext3、ext4 ，如果是光盘，就写iso9660**
* **-L 卷标名: 挂载指定卷标的分区，而不是安装设备文件名挂载**
* **-o 特殊选项:可以指定挂载的额外选项**

| 参数            | 说明                                                |
| ------------- | ------------------------------------------------- |
| atime/noatime | 更新访问时间/不更新访问时间。访问分区文件时，是否更新文件 的访问时间，默认为更新         |
| async/sync    | 异步/同步，默认为异步                                       |
| auto/noauto   | 自动/手动，mount –a命令执行时，是否会自动安装/etc/fstab文件内容挂载，默认为自动 |
| defaults      | 定义默认值，相当于rw,suid,dev,exec,auto,nouser,async这七个选项  |
| exec/noexec   | 执行/不执行，设定是否允许在文件系统中执行可执行文件，默认 是exec允许             |
| remount       | 重新挂载已经挂载的文件系统，一般用于指定修改特殊权限                        |
| rw/ro         | 读写/只读，文件系统挂载时，是否具有读写权限，默认是rw                      |
| suid/nosuid   | 具有/不具有SUID权限，设定文件系统是否具有SUID和SGID的权限，默认是具有         |
| user/nouser   | 允许/不允许普通用户挂载，设定文件系统是否允许普通用户挂载 默认是不允许，只有root可以挂载分区 |
| usrquota      | 写入代表文件系统支持用户磁盘配额，默认不支持                            |
| grpquota      | 写入代表文件系统支持组磁盘配额，默认不支持                             |

**注意：针对的都是分区**

**挂载光盘与U盘**

\*\*当你插入硬件的时候，操作系统会自动识别设备到/dev 文件下（内核有相应的驱动），并自动加上设备文件名，但是他并没有加入文件系统，只是将他整个视为一个文件，你分不清楚里面文件和文件夹的界限和联系（例如ext4，你分不清楚哪个是inode哪个是datablock），只能通过read/write命令对他进行读写。想要他里面的文件或者文件夹分开，只有加载了文件系统，才可以。所以你用mount命令的时候要加-t指定文件系统，例如：mount -t vfat /dev/hda1 /mnt，挂载/dev/hda1设备到/mnt，文件系统是vfat。 \*\*

**只要是一个空目录都可以挂载，mnt那些只是习惯，他只是一个进入设备的入口，并依据文件系统格式使得操作系统可以正确的分清楚其中的区块而已，因此若没有格式化的硬件需要先格式化**

**挂载光盘** 例子： mkdir /mnt/cdrom/ 建立挂载点 mount -t iso9660 /dev/cdrom /mnt/cdrom/

* **挂载光盘** **mount /dev/sr0 /mnt/cdrom/**
* **卸载命令** **umount 设备文件名或挂载点**

**挂载U盘**

* **fdisk –l 查看U盘设备文件名**

例子：

​ mount -t vfat /dev/sdb1 /mnt/usb/

* **卸载命令** **umount 设备文件名或挂载点**

**注意:**

* **Linux默认是不支持NTFS文件系统的**
* **格式化就是重新写入文件系统**
* **fat16分区识别为fat fat32分区识别为vfat**

**支持NTFS文件系统**

* 没有相应的驱动，可以重新编写内核后编译或者用第三方插件
* **下载NTFS-3G插件 tar** **wget https://tuxera.com/opensource/ntfs-3g\_ntfsprogs-2017.3.23.tgz**
* **解压** **tar -zxvf ntfs-3g\_ntfsprogs-2013.1.13.tgz** **cd ntfs-3g\_ntfsprogs-2013.1.13 进入解压目录**
* **安装NTFS-3G** **./configure 编译器准备。没有指定安装目录，安装到默认位置中** **make 编译** **make install 编译安装**
* **使用** **mount -t ntfs-3g 分区设备文件名 挂载点**

**fdisk分区**

* **通过虚拟机加入新硬盘**
  * **查看新硬盘** **fdisk -l**
* **使用fdisk命令分区** **fdisk /dev/sdb 后面不能加数字 因为此时硬盘还没有分区** **通过交互进行分区，交互指令如下：**

| 命令 | 说明                                     |
| -- | -------------------------------------- |
| a  | 设置可引导标记                                |
| b  | 编辑bsd磁盘标签                              |
| c  | 设置DOS操作系统兼容标记                          |
| d  | 删除一个分区                                 |
| l  | 显示已知的文件系统类型。82为Linux swap分区，83为Linux分区 |
| m  | 显示帮助菜单                                 |
| n  | 新建分区                                   |
| o  | 建立空白DOS分区表                             |
| p  | 显示分区列表                                 |
| q  | 不保存退出                                  |
| s  | 新建空白SUN磁盘标签                            |
| t  | 改变一个分区的系统ID                            |
| u  | 改变显示记录单位                               |
| v  | 验证分区表                                  |
| w  | 保存退出                                   |
| x  | 附加功能(仅专家)                              |

* **重新读取分区表信息**
  * **partprobe**
* **格式化分区**
  * **mkfs -t ext4 /dev/sdb1**
* **建立挂载点并挂载**
  * **mkdir /disk1**
  * **mkdir /disk5**
  * **mount /dev/sdb1 /disk1/**
  * **mount /dev/sdb5 /disk5/**

**分区自动挂载与fstab文件修复**

上一节说的挂载操作在重启之后便会消失，每次重启都得重新挂载，**使用我们需要把它写入系统挂载命令文件中**，每次开机都会自动扫描挂载，使用\*\*/etc/fstab文件，将挂载信息写入文件\*\*

* /etc/fstab文件
  * **第一字段：分区设备文件名或UUID(硬盘通用唯一识别码)**
  * **第二字段：挂载点**
  * **第三字段:文件系统名称**
  * **第四字段:挂载参数**
  * **第五字段:指定分区是否被dump备份，0代表不备份，1 代表每天备份，2代表不定期备份**
  * **第六字段:指定分区是否被fsck检测，0代表不检测，其 他数字代表检测的优先级，那么当然1的优先级比2高**

**注意：在写入文件之后先不要着急重启，我们可以先用mount -a命令来实现系统自动重新挂载，如果出现错误会提示，不至于系统崩溃**

**/etc/fstab文件修复**

* 如果一旦写错了，出现了报错，可以在开机显示之后出现一个让你输入root用户密码的界面，再输入密码之后，可以使用vim /etc/fstab进入fstab文件修改错误
* **如果因为/etc/fstab文件出错出现文件只有只读权限，不能修改，强制保存也不行，退出文件输入命令：mount -o remount,rw / ，重新把根分区挂载读写权限，就可以保存了，而且只能在根分区没有错误，在本机登陆，不能使用服务器或者远程连接的情况下才能修复。**

**swap分区**

swap 分区通常被称为交换分区，这是一块特殊的硬盘空间，即当实际内存不够用的时候，操作系统会从内存中取出一部分暂时不用的数据，放在交换分区中，从而为当前运行的程序腾出足够的内存空间。

也就是说，当内存不够用时，我们使用 swap 分区来临时顶替。这种“拆东墙，补西墙”的方式应用于几乎所有的操作系统中。

*   **free命令** **free 查看内存与swap分区使用状况**

    * **-m：按MB字节显示**

    **cached(缓存):是指把读取出来的数据保存在内存当中，当再次读取时，不用读取硬盘而直接从内存当中读取，加速了数据的读取过程** **buffer(缓冲):是指在写入数据时，先把分散的写入操作保存到内存当中，当达到一定程度再集中写入硬盘， 减少了磁盘碎片和硬盘的反复寻道，加速了数据的写入过程**

**新建swap分区**

* **fdisk /dev/sdb** **分区操作** **别忘记把分区ID改为82 ，记得保存，然后重启 格式化**
* **分区完之后需要格式化** **mkswap /dev/sdb1 这里不能使用mkfs进行格式化**
* **加入swap分区** **swapon /dev/sdb1 加入swap分区**
* **如果不想用了使用以下命令取消：** **swapoff /dev/sdb1 取消swap分区**
* **swap分区开机自动挂载** **vi /etc/fstab** **/dev/sdb1 swap swap defaults 0 0** **注意：swap前面没有/，他不是根分区下的 修改之后使用mount -a 命令来检测错误**

### shell

**Shell概述**

* **Shell是什么**（每个之前使用的命令都是一个独立的应用程序，由shell解释调用，shell只是提供用户操作系统一个界面）
  * **Shell是一个命令行解释器，它为用户提供 了一个向Linux内核发送请求以便运行程 序的界面系统级程序，用户可以用Shell来 启动、挂起、停止甚至是编写一些程序。**
  * **Shell还是一个功能相当强大的编程语言， 易编写，易调试，灵活性较强。Shell是解 释执行的脚本语言，在Shell中可以直接调 用Linux系统命令。**
* **Shell的分类**
  * **Bourne Shell:从1979起Unix就开始使用 Bourne Shell，Bourne Shell的主文件名为 sh。**
  *   **C Shell: C Shell主要在BSD版的Unix系 统中使用，其语法和C语言相类似而得名**

      **Shell的两种主要语法类型有Bourne和C， 这两种语法彼此不兼容。Bourne家族主要 包括sh、ksh、Bash、psh、zsh;C家族主 要包括:csh、tcsh Bash: Bash与sh兼容，现在使用的Linux 就是使用Bash作为用户的基本Shell。**
* **Linux支持的Shell /etc/shells 会显示： /bin/sh (已经被 /bin/bash 所取代) /bin/bash (就是 Linux 预设的 shell) /sbin/nologin /bin/tcsh (整合 C Shell ，提供更多的功能) /bin/csh (已经被 /bin/tcsh 所取代) 都是Linux支持的Shell**

为什么我们系统上合法的 shell 要写入 /etc/shells 这个文件啊？ 这是因为系统某些服务在运作过程中，会去检查使用者能够使用的 shells ，而这些 shell 的查询就是藉由/etc/shells 这个文件啰！

举例来说，我们的 CentOS 7.x 的 /etc/shells 里头就有个 /sbin/nologin 文件的存在，这个就是我们说的怪怪的 shell 啰～

**Shell脚本的执行方式**

**echo输出命令 echo \[选项] \[输出内容] -e: 支持反斜线控制的字符转换** **下表为控制符的作用：**

| 控制字符  | 作用                                  |
| ----- | ----------------------------------- |
| \\    | 输出\本身                               |
| \a    | 输出警告音                               |
| \b    | 退格键，也就是向左删除键                        |
| \c    | 取消输出行末的换行符。和“-n”选项一致                |
| \e    | ESCAPE键                             |
| \f    | 换页符                                 |
|       | 换行符                                 |
|       | 回车键                                 |
|       | 制表符，也就是Tab键                         |
| \v    | 垂直制表符                               |
| \0nnn | 按照八进制ASCII码表输出字符。其中0为数字零，nnn是三位八进制数 |
| \xhh  | 按照十六进制ASCII码表输出字符。其中hh是两位十六进制数      |

**例子：**

* **echo -e “ab\bc” 输出：ac 删除左侧字符**
* **echo -e "a\tb\tc\nd\te\tf"** **输出： a b c d e f** **制表符与换行符**
* **echo -e “\x61\t\x62\t\x63\n\x64\t\x65\t\x66” 输出： a b c d e f 按照十六进制ASCII码也同样可以输出**
* **echo -e "\e\[1;31m abcd \e\[0m"** **输出： 红色的abcd** **因为\e\[1 表示开启颜色区别 \e\[0m 表示结束颜色区别 31m表示红色 还有其他： 30m=黑色，31m=红色，32m=绿色，33m=黄色，34m=蓝色，35m=洋红，36m=青色，37m=白色**

**脚本** **vi hello.sh** **内容： #!/bin/Bash #The first program #Author: yangyang (E-mail: 1771566679@qq.com) echo -e ‘Hello World！’**

**注意：在这一段脚本中，#!/bin/Bash这一句是个例外，他并不是注释，是标识，说明以下语句是Shell脚本，‘Hello World！’如果要加感叹号就得是单引号，如果没有感叹号才可以是双引号，这感叹号有意义。**（有空格要用双引号）

**脚本执行**

* **赋予执行权限，直接运行** **chmod 755 hello.sh ./hello.sh**
*   **通过Bash调用执行脚本 bash hello.sh** **不需要执行权限就可以执行** **所有程序必须用绝对路径或者相对路径执行**

    **有一个操作： 如果从Windows里面拷贝一个脚本到Linux 虽然有的时候格式一样但是还是会报错，这便是因为两个系统中脚本的格式不同，比如Windows中的回车在脚本中用^M$表示，而Linux中为$，（可以用cat -A \[文件名] 来查询）所以需要转变，此时用到一个命令：dos2unix \[文件名] 转换后，Linux就可以执行啦，通过没有这个命令可以使用yum安装**

**Bash的基本功能**

**历史命令history**

**history \[选项] \[历史命令保存文件]**

* **-c: 清空历史命令**
*   **-w: 把缓存中的历史命令写入历史命令保存文件 \~/.bash\_history**

    历史命令默认会保存1000条,可以在环境 变量配置文件\*\*/etc/profile\*\*中进行修改 找到HISTSIZE=1000进行修改，随意修改到100000条都可以，修改之后重启使配置文件生效

**历史命令的调用**

* **使用上、下箭头调用以前的历史命令**
* **使用“!n”重复执行第n条历史命令**
* **使用“!!”重复执行上一条命令**
* **使用“!字串”重复执行最后一条以该字 串开头的命令**

**命令与文件补全** **在Bash中，命令与文件补全是非常方便与常用的功能，我们只要在输入命令或文件时，按“Tab”键就会自动进行补全，一下tab没有补全则是有多个或者没有，若是有多个第二次按Tab会输出有相同前缀的命令/文件以供参考**

**命令别名与常用快捷键**

**命令别名**

* **alias 别名=‘原命令’ 设定命令别名**
* **alias 查询命令别名**

**命令执行时顺序**

* **1 第一顺位执行用绝对路径或相对路径执行 的命令。**
* **2 第二顺位执行别名。**
* **3 第三顺位执行Bash的内部命令。**
* **4 第四顺位执行按照$PATH环境变量定义的 目录查找顺序找到的第一个命令。**

\==**让别名永久生效**== ==**vi /root/.bashrc**==

**删除别名** **unalias 别名**

**Bash常用快捷键** 下表：

| 快捷键        | 作用                                                      |
| ---------- | ------------------------------------------------------- |
| ctrl+a     | 把光标移动到命令行开头。如果我们输入的命令过长，想要把光标移 动到命令行开头时使用。              |
| ctrl+e     | 把光标移动到命令行结尾。                                            |
| **ctrl+c** | 强制终止当前的命令。                                              |
| **ctrl+l** | 清屏，相当于clear命令。                                          |
| **ctrl+u** | 删除或剪切光标之前的命令。我输入了一行很长的命令，不用使用退 格键一个一个字符的删除，使用这个快捷键会更加方便 |
| **ctrl+k** | 删除或剪切光标之后的内容。                                           |
| **ctrl+y** | 粘贴ctrl+U或ctrl+K剪切的内容。                                   |
| **ctrl+r** | 在历史命令中搜索，按下ctrl+R之后，就会出现搜索界面，只要输入 搜索内容，就会从历史命令中搜索。      |
| **ctrl+d** | 退出当前终端。                                                 |
| ctrl+z     | 暂停，并放入后台。这个快捷键牵扯工作管理的内容，我们在系统管 理章节详细介绍。                 |
| ctrl+s     | 暂停屏幕输出。                                                 |
| ctrl+q     | 恢复屏幕输出。                                                 |

**其中标记的为重点快捷键，需要熟练使用 注意：ctrl+z 快捷键一定要谨慎使用，如果使用的多了，系统会占用大量存储空间来存放暂停的数据，用多了系统会变卡！！！**

**输入输出重定向**

**标准输入输出**

| 设备  | 设备文件名       | 文件描述符 | 类型     |
| --- | ----------- | ----- | ------ |
| 键盘  | /dev/stdin  | 0     | 标准输入   |
| 显示器 | /dev/sdtout | 1     | 标准输出   |
| 显示器 | /dev/sdterr | 2     | 标准错误输出 |

**输出重定向** **就是改变输出方向，比如由屏幕输出到文件，非常有用**

**在输入报错文件中 2和>>必选连着写**

| 类型        | 符号         | 作用                              |
| --------- | ---------- | ------------------------------- |
| 标准输出重定向   | 命令 > 文件    | 以覆盖的方式，把命令的正确输出输 出到指定的文件或设备当中。  |
| 标准输出重定向   | 命令 >> 文件   | 以追加的方式，把命令的 正确输出输出到指定的文 件或设备当中。 |
| 标准错误输出重定向 | 错误命令 2>文件  | 以覆盖的方式，把命令的 错误输出输出到指定的文 件或设备当中。 |
| 标准错误输出重定向 | 错误命令 2>>文件 | 以追加的方式，把命令的错误输出输出到指定的文件或设备当中。   |

**正确输出和错误输出同时保存**

| 类型            | 符号               | 作用                              |
| ------------- | ---------------- | ------------------------------- |
| 正确输出和错误输出同时保存 | 命令 > 文件 2>&1     | 以覆盖的方式，把正确输 出和错误输出都保存到同 一个文件当中。 |
| 正确输出和错误输出同时保存 | 命令 >> 文件 2>&1    | 以追加的方式，把正确输 出和错误输出都保存到同 一个文件当中。 |
| 正确输出和错误输出同时保存 | 命令 &>文件          | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中。   |
| 正确输出和错误输出同时保存 | 命令 &>>文件         | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中。   |
| 正确输出和错误输出同时保存 | 命令 >> 文件1 2>>文件2 | 把正确的输出追加到文件1中，把错误的输出追加到文件2中。    |

**命令 >> 文件 2>&1 ，命令 &>>文件 两种保存都一样，只不过是格式不同** ==**有一个用法： 命令 &>/dev/null 不管命令是否正确，直接丢掉这个文件夹（/dev/null为空），不保存任何数据，在写shell脚本时有用**==

**输入重定向** **不通过键盘输入，通过文件输入，在实际中用的不多，用在给源码包打补丁**

**wc \[选项] \[文件名]**

* **-c 统计字节数**
* **-w 统计单词数**
* **-l 统计行数**

**用法： 命令 < 文件 把文件作为命令的输入 命令 << 标识符 一直输入，直到输入标识停止输入把标识符之间内容作为命令的输入**

**多命令顺序执行与管道符**

| 多命令执行符 | 格式           | 作用                                       |
| ------ | ------------ | ---------------------------------------- |
| ;      | 命令1 ;命令2     | 多个命令顺序执行，命令之间没有任何逻辑联系，就算第一条报错，第二条也会执行    |
| &&     | 命令1 && 命令2   | 逻辑与当命令1正确执行，则命令2才会执行 当命令1执行不正确，则命令2不会执行  |
| \|\|   | 命令1 \|\| 命令2 | 逻辑或当命令1 执行不正确，则命令2才会执行 当命令1正确执行，则命令2不会执行 |

**磁盘文件复制：** **dd if=输入文件 of=输出文件 bs=字节数 count=个数**

* **if=输入文件 指定源文件或源设备**
* **of=输出文件 指定目标文件或目标设备**
* **bs=字节数 指定一次输入/输出多少字节，即把这些字节看做 一个数据块**
* **count=个数 指定输入/输出多少个数据块** **这条命令可以把系统文件，磁盘都复制了，非常强大** **例子： date ; dd if=/dev/zero of=/root/testfile bs=1k count=100000 ; date**

**管道符**

**命令1 | 命令2** **注意：命令1的==正确输出==作为命令2的操作对象 颜色显示**

**grep**

**grep \[选项] “搜索内容” 文件名**

* **-i: 忽略大小写**
* **-n: 输出行号**
* **-v: 反向查找**
* **–color=auto 搜索出的关键字用颜色显示**

**通配符与其他特殊符号**

**通配符**

| 通配符  | 作用                                                            |
| ---- | ------------------------------------------------------------- |
| ?    | 匹配一个任意字符                                                      |
| \*   | 匹配0个或任意多个任意字符，也就是可以匹配任何内容                                     |
| ^    | 行首                                                            |
| $    | 行尾                                                            |
| \[]  | 匹配中括号中任意\*\*==一个字符==\*\*。例如:\[abc]代表一定匹配 一个字符，或者是a，或者是b，或者是c。 |
| \[-] | 匹配中括号中任意一个字符，-代表一个范围。例如:\[a-z] 代表匹配一个小写字母。                    |
| \[^] | 逻辑非，表示匹配不是中括号内的一个字符。例如:\[^0- 9]代表匹配一个不是数字的字符。                 |

**Bash中其他特殊符号**

| 符号   | 作用                                                                               |
| ---- | -------------------------------------------------------------------------------- |
| ‘ ’  | 单引号。在单引号中**所有的特殊符号**，如“$”和“\`”(反引号)**都没有特殊含义**。（上面的几个命令，一些命令如printf的%特殊符号则有特殊含义） |
| “ ”  | 双引号。在双引号中特殊符号都没有特殊含义，但是“**$”、“\`” 和“\”是例外，拥有“调用变量的值”、“引用命令”和“转义符”的特殊含义。**        |
| \`\` | 反引号。反引号括起来的内容是系统命令，在Bash中会先执行它。 和$()作用一样，不过推荐使用$()，因为反引号非常容易看错。                  |
| $()  | 和反引号作用一样，用来引用系统命令。 **$( )中放的是命令，${ }中放的是变量**                                     |
| #    | 在Shell脚本中，#开头的行代表注释。                                                             |
| $    | **用于调用变量的值**，如需要调用变量name的值时，需要用$name 的方式得到变量的值。                                  |
| \\   | 转义符，跟在\之后的特殊符号将失去特殊含义，变为普通字符。 如$将输出“$”符号，而不当做是变量引用。                              |

**egrep**

egrep = grep -E 可以使用基本的正则表达外, 还可以用扩展表达式. 注意区别. 扩展表达式:

> \+ 匹配一个或者多个先前的字符, 至少一个先前字符. ? 匹配0个或者多个先前字符. a|b|c 匹配a或b或c () 字符组, 如: love(able|ers) 匹配loveable或lovers. (..)(..)\1\2 模板匹配. \1代表前面第一个模板, \2代第二个括弧里面的模板. x{m,n} =x{m,n} x的字符数量在m到n个之间.

egrep '^+' file 以一个或者多个空格开头的行. grep '^\*' file 同上 egrep '(TOM|DAN) SAVAGE' file 包含 TOM SAVAGE 和DAN SAVAGE的行. egrep '(ab)+' file 包含至少一个ab的行. egrep 'x\[0-9]?' file 包含x或者x后面跟着0个或者多个数字的行. egrep 'fun.$' \* 所有文件里面以fun.结尾的行. egrep '\[A-Z]+' file 至少包含一个大写字母的行. egrep '\[0-9]' file 至少一个数字的行. egrep '\[A-Z]...\[0-9]' file 有五个字符, 第一个式大写, 最后一个是数字的行. egrep '\[tT]est' file 包含单词test或Test的行. egrep 'ken sun' file 包含ken sun的行. egrep -v 'marry' file 不包含marry的行. egrep -i 'sam' file 不考虑sam的大小写,含有sam的行. egrep -l "dear ken" \* 包含dear ken的所有文件的清单. egrep -n tom file 包含tom的行, 每行前面追加行号. egrep -s "$name" file 找到变量名$name的, 不打印而是显示退出状态. 0表示找到. 1表示表达式没找到符合要求的, 2表示文件没找到.

**用户自定义变量**

**用户自定义变量**

**什么是变量：** **变量是计算机内存的单元，其中存放的值可以改变。当Shell脚本需要保存一些信息 时，如一个文件名或是一个数字，就把它 存放在一个变量中。每个变量有一个名字 ，所以很容易引用它。使用变量可以保存 有用信息，使系统获知用户相关设置，变量也可以用于保存暂时信息。**

**变量设置规则：**

* **变量名称可以由字母、数字和下划线组成 ，但是不能以数字开头。如果变量名是 “2name”则是错误的。**
* **在Bash中，变量的默认类型都是字符串型 ，如果要进行数值运算，则必需指定变量类型为数值型。**
* \==**默认变量类型全都是字符串型，和其他语言不太一样 变量用等号连接值，等号左右两侧不能有空格。**==
* **变量的值如果有空格，需要使用单引号或双引号包括。**
* **在变量的值中，可以使用“\”转义符。**
* **如果需要增加变量的值，那么可以进行变量值的叠加。不过变量需要用双引号包含 “$变量名”或用${变量名}包含。**
* **如果是把命令的结果作为变量值赋予变量 ，则需要使用反引号或$()包含命令。**
* **环境变量名建议大写，便于区分。**

**变量的分类：**

* **用户自定义变量**
* **环境变量:这种变量中主要保存的是和系统操作环境相关的数据。**
* **位置参数变量:这种变量主要是用来向脚本当 中传递参数或数据的，变量名不能自定义，变量作用是固定的。**（也是预定义）
* **预定义变量:是Bash中已经定义好的变量，变量名不能自定义，变量作用也是固定的。**

**本地变量(用户自定义变量)** **变量定义** **例子：** **name=“yang yang”**

* **变量叠加** **aa=123 aa="$aa"456（“”中用$读取变量时后面加的若是变量命名的规则的会一起读取，导致出错，后面需要加非命名的字符才能叠加，因此最好叠加的放到外面） aa=${aa}789**
* **变量调用 echo $变量名**
* **变量查看 set**
* **变量删除 unset 变量名**

**环境变量**

**环境变量： 用户自定义变量只在当前的Shell中生效（shell变量，本地变量）， 而环境变量（用户变量）会在当前Shell和这个Shell的所 有子Shell当中生效。如果把环境变量写入相应的配置文件，那么这个环境变量就会在所有的Shell中生效**

**设置环境变量：**

* **export 变量名=变量值 申明变量**
*   **env 查询变量**

    > set命令显示当前shell的变量，包括当前用户的变量;
    >
    > env命令显示当前用户的变量(环境变量)；
    >
    > export命令显示当前导出成用户变量的shell变量。
    >
    > 每个shell有自己特有的变量（set）显示的变量，这个和用户变量是不同的，当前用户变量和你用什么shell无关，不管你用什么shell都在，比如HOME,SHELL等这些变量，但shell自己的变量不同shell是不同的，比如BASH\_ARGC， BASH等，这些变量只有set才会显示，是bash特有的，**export不加参数的时候，显示哪些变量被导出成了用户变量，因为一个shell自己的变量可以通过export “导出”变成一个用户变量**
* **echo $变量名 变量调用**
* **unset 变量名 删除变量**
* **pstree 树形显示进程数** **没有这条命令可以执行以下命令下载： yum -y install psmisc** **yum provides /命令 查看没有的命令的安装包 配合yum -y install使用**

**系统常见环境变量**

* **PATH:系统查找命令的路径** **这便是输入命令之前不用输入绝对路径的根本原因，系统会提前在PATH环境变量里的所有路径中查询一遍有没有你输入的命令，找到之后直接执行** **如果你想直接执行shell脚本，不加绝对路径，直接写入PATH环境变量，使用叠加** **例子： echo $PATH PATH="$PATH":/root/sh PATH变量叠加** **此后，/root/sh路径里面的执行文件都可以在任意目录下直接执行，不过是临时生效**
* **PS1：定义系统提示符的变量 用来改\[root@localhost \~]# 这个显示**
* **\d:显示日期，格式为“星期 月 日”**
* **\h:显示简写主机名。如默认主机名“localhost”**
* **\t:显示24小时制时间，格式为“HH:MM:SS”**
* **\T:显示12小时制时间，格式为“HH:MM:SS”**
* **\A:显示24小时制时间，格式为“HH:MM”**
* **\u:显示当前用户名**
* **\w:显示当前所在目录的完整名称**
* **\W:显示当前所在目录的最后一个目录**
* **#:执行的第几个命令**
* **$:提示符。如果是root用户会显示提示符为“#”，如果是普通用户 会显示提示符为“$”**

**位置参数变量**

**位置参数变量**

| 位置参数变量 | 作用                                                                                                           |
| ------ | ------------------------------------------------------------------------------------------------------------ |
| $n     | n为数字，$0代表命令本身，$1-9代表第一到第九个参数，十以上的参数需要用大括号包含，如9代表第一 到第九个参数，十以上的参数需要用大括号 包含，如9代表第一到第九个参数，十以上的参数需要用大括号包含，如{10}. |
| $\*    | 这个变量代表命令行中所有的参数，$\*把所 有的参数看成一个整体                                                                             |
| $@     | 这个变量也代表命令行中所有的参数，不过 $@把每个参数区分对待                                                                              |
| $#     | 这个变量代表命令行中所有参数的个数                                                                                            |

**例子脚本：**

* $n的例子： #!/bin/bash num1=$1 num2=$2 sum=$(( $num1 + $num2)) #变量sum的和是num1加num2 echo $sum #打印变量sum的值
* $\*，$@，$#的例子:（也都是位置参数，须在执行文件语句后面加 输入） #!/bin/bash echo “A total of $# parameters” #使用$#代表所有参数的个数 echo “The parameters is: $\*” #使用$\*代表所有的参数 echo “The parameters is: $@” #使用$@也代表所有参数
* $\*与$@的区别例子： #!/bin/bash for i in “$\*” \*_#$\*中的所有参数看成是一个整体，所以这个for循环只会循环一次\*_ do echo “The parameters is: $i” done x=1 for y in “$@” \*_#$@中的每个参数都看成是独立的，所以“$@”中有几个参数，就会循环几次\*_ do echo “The parameter$x is: $y” x=$(( $x +1 )) done

**预定义变量**

**预定义变量**

| 预定义变量 | 作用                                                                                     |
| ----- | -------------------------------------------------------------------------------------- |
| $?    | 最后一次执行的命令的返回状态。如果这个变 量的值为0，证明上一个命令正确执行;如果 这个变量的值为非0(具体是哪个数，由命令 自己来决定)，则证明上一个命令执行不正确 了。 |
| \$$   | 当前进程的进程号(PID)                                                                          |
| $!    | 后台运行的最后一个进程的进程号(PID)                                                                   |

**例子：** #!/bin/bash #Author: yangyang (E-mail: 1771566679@qq.com) echo “The current process is \$$” #输出当前进程的PID。 #这个PID就是variable.sh这个脚本执行时，生成的进程的PID find /root -name hello.sh & #使用find命令在root目录下查找hello.sh文件 #符号&的意思是把命令放入后台执行，工作管理在系统管理章节会详细介绍 echo "The last one Daemon process is $!"

**接受键盘输入** ==**read \[选项] \[变量名]**==

* **-p “提示信息”:在等待read输入时，输出提示信息**
* **-t 秒数: read命令会一直等待用户输入，使用 此选项可以指定等待时间**
* **-n 字符数:read命令只接受指定的字符数，就会执行**
* **-s: 隐藏输入的数据，适用于机密信息的输入**

**数值运算与运算符**

**declare声明变量类型** **declare \[+/-]\[选项] 变量名**

* **-: 给变量设定类型属性**
* **+: 取消变量的类型属性**
* \*\*-i: 将变量声明为整数型(integer) \*\*
* **-x: 将变量声明为环境变量**
* **-p: 显示指定变量的被声明的类型**

**数值运算**

* **数值运算—方法1** aa=11 bb=22 给变量aa和bb赋值 declare -i cc=$aa+$bb
* **expr或let数值运算工具—方法2** aa=11 bb=22 给变量aa和bb赋值 dd=$(expr $aa + $bb) dd的值是aa和bb的和。注意“+”号左右两 侧必须有空格 let与expr一样
* **“$((运算式))”或“$\[运算式]” —方法3**（$单括号系统命令，双括号运算表达式） aa=11 bb=22 给变量aa和bb赋值 ff=$(( $aa+$bb )) gg=$\[ $aa+$bb ]

**运算符** **运算符优先级表：**（与C的优先值数值相反，但是这边优先级越大越高，所以是一样的效果）

| 优先级 | 运算符                                             | 说明                |
| --- | ----------------------------------------------- | ----------------- |
| 13  | -, +                                            | 单目负、单目正           |
| 12  | !, \~                                           | 逻辑非、按位取反或补码       |
| 11  | \*,/, %                                         | 乘、除、取模            |
| 10  | +, -                                            | 加、减               |
| 9   | << , >>                                         | 按位左移、按位右移         |
| 8   | < =, > =, < , >                                 | 小于或等于、大于或等于、小于、大于 |
| 7   | == , !=                                         | 等于、不等于            |
| 6   | &                                               | 按位与               |
| 5   | ^                                               | 按位异或              |
| 4   | \|                                              | 按位或               |
| 3   | &&                                              | 逻辑与               |
| 2   | \|\|                                            | 逻辑或               |
| 1   | =,+=,-=,\*=,/=,%=,&=, ^=,赋值、运算且赋值 \|=, <<=, >>= |                   |

例子：

* aa=$(( (11+3)\*3/2 )) 虽然乘和除的优先级高于加，但是通过小括号可以调整运算优先级
* bb=$(( 14%3 )) 14不能被3整除，余数是2
* cc=$(( 1 && 0 )) 逻辑与运算只有想与的两边都是1，与的结果才是1，否则与的结果是0
* dd=$(( 1 || 0 )) 逻辑或运算只要有一边是1，或的结果就是1，两边都为0，或的结果才是0

**变量测试与内容替换**

**用来测试一个变量到底有没有设置，测试表：**

| 变变量置换方式    | 变量y没有设置           | 变量y为空值     | 变量y设置值    |
| ---------- | ----------------- | ---------- | --------- |
| x=${y-新值}  | x=新值              | x为空        | x=$y      |
| x=${y:-新值} | x=新值              | x=新值       | x=$y      |
| x=${y+新值}  | x为空               | x=新值       | x=新值      |
| x=${y:+新值} | x为空               | x为空        | x=新值      |
| x=${y=新值}  | x=新值 y=新值         | x为空 y值不变   | x=$y y值不变 |
| x=${y:=新值} | x=新值 y=新值         | x=新值 y=新值  | x=$y y值不变 |
| x=${y?新值}  | 新值输出到标准错误输出(就是屏幕) | x为空        | x=$y      |
| x=${y:?新值} | 新值输出到标准错误输出       | 新值输出到标准错误输 | x=$y      |

**例子：** **测试x=${y-新值} 测试y变量存不存在**

* **unset y 删除变量y x=${y-new} 进行测试 echo $x 显示new，y变量不存在 因为变量y不存在，所以x=new**
* **y="" 给变量y赋值为空 x=${y-new} 进行测试 echo $x 显示空，y为空值**
* **y=old 给变量y赋值 x=${y-new} 进行测试 echo $x 显示old ，y变量存在且有值**

\==**在用到的时候查询就好，不需要死记硬背。这个表是在写脚本的时候给电脑程序看的，人不参与其中**==

**环境变量配置文件简介**

**source命令**

* **source 配置文件 强制使配置文件在修改之后生效，不需要重启**
* **. 配置文件 和source 配置文件的作用是一样的**

**环境变量配置文件简介** **环境变量配置文件中主要是定义对系统的操作环境生效的系统默认环境变量，比如 PATH、HISTSIZE、PS1、HOSTNAME等 默认环境变量。**

**配置文件保存位置**

* **/etc/profile**（针对操作系统改变，是对所有用户生效）
* **/etc/profile.d/\*.sh 指/etc/profile.d/下所有的.sh结尾的文件**（针对操作系统改变，是对所有用户生效）
* **\~/.bash\_profile**（\~表示某个用户，只在该用户登陆时读取该配置文件，‘.’表示隐藏文件夹）
* **\~/.bashrc**（\~表示某个用户，只在该用户登陆时读取该配置文件，‘.’表示隐藏文件夹）
* **/etc/bashrc**（针对操作系统改变，是对所有用户生效）

**环境变量配置文件调用顺序流程图**

![流程图](https://img-blog.csdnimg.cn/20200525190315737.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70#pic\_center)

* **登陆输入密码之后，第一步是读取/etc/profile文件**==（ubuntu /etc下的配置文件很少代码，调用/etc/bashrc来实现complition调用，其会调用用户自己的bash\_profile，用户自己的环境变量基本都写在了用户自己的/.profile和bashrc上，最后再回来调用自己的/etc/bashrc的参数功能）== **/etc/profile的作用：** **USER变量 LOGNAME变量 MAIL变量 PATH变量 HOSTNAME变量 HISTSIZE变量 umask 里面有以上环境变量的配置**
* **接下来便调用/etc/profile.d/\*.sh文件** **然后就是下面的文件，语言包文件，识别系统自带的语言 \~/.bash\_profile的作用**
* **调用了\~/.bashrc文件。 在PATH变量后面加入了“:$HOME/bin” 这个目录** **\~/.bashrc的作用 定义默认别名**
* **调用/etc/bashrc /etc/bashrc的作用 PS1变量 umask PATH变量**
* **调用/etc/profile.d/\*.sh文件** **这一块就是进入界面以内，切换shell登陆方式，这种不需要密码，所以和前面的/etc/profile的作用不冲突**

> ①/etc/profile: 该文件登录操作系统时，为**每个用户**设置环境信息，当用户第一次登录时,该文件被执行。也就是说这个文件对每个shell都有效，用于获取系统的环境信息。可以通过**命令source /etc/profile立即生效**
>
> > $ vi /etc/profile 在里面加入: export PATH="$PATH:/my\_new\_path"
>
> > \# /etc/profile # System wide environment and startup programs, for login setup # Functions and aliases go in /etc/bashrc # It's NOT a good idea to change this file unless you know what you # are doing. It's much better to create a custom.sh shell script in # /etc/profile.d/ to make custom changes to your environment, as this # will prevent the need for merging in future updates.
>
> ②\~/.bash\_profile或～/.profile :
>
> 每个用户都可使用该文件输入专用于当前用户使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件. 此文件类似于/etc/profile，也是需要需要重启才会生效，/etc/profile对所有用户生效，\~/.bash\_profile只对当前用户生效。
>
> ③/etc/bashrc 或 /etc/bash.bashrc： 为每一个运行bash shell的用户执行此文件，当bash shell被打开时,该文件被读取。也就是说，当用户shell执行了bash时，运行这个文件。**修改这个文件不用重启，重新打开一个bash即可生效**。
>
> > \# /etc/bashrc # System wide functions and aliases # Environment stuff goes in /etc/profile # It's NOT a good idea to change this file unless you know what you # are doing. It's much better to create a custom.sh shell script in # /etc/profile.d/ to make custom changes to your environment, as this # will prevent the need for merging in future updates.
>
> ④\~/.bashrc 该文件存储的是专属于个人bash shell的信息，当登录时以及每次打开一个新的shell时,执行这个文件。在这个文件里可以自定义用户专属的个人信息。（**每个用户都有一个.bashrc文件，在用户目录下**）可以通过**命令source \~/.bashrc立即生效.**
>
> 那么在用户登录系统时候，相关的**文件执行顺序**是什么呢。 在刚登录Linux时，首先启动 /etc/profile 文件，然后再启动用户目录下的 \~/.bash\_profile、 \~/.bash\_login或 ~~/.profile文件中的其中一个，执行的顺序为：~~/.bash\_profile、 \~/.bash\_login、 \~/.profile。如果 \~/.bash\_profile文件存在的话，一般还会执行 \~/.bashrc文件。

**交互式的**：顾名思义。这样的shell中的命令时由用户从键盘交互式地输入的，执行的结果也可以输出到终端显示给用户看。

**非交互式的**：这样的shell可能由某些自己主动化过程启动，不能直接从请求用户的输入。也不能直接输出结果给终端用户看。输出最好写到文件。

**login的**：意思是这样的是在某用户由/bin/login登陆进系统后启动的shell，跟这个用户绑定。这个shell是用户登陆后启动的第一个进程。login进程在启动shell时传递第0个參数指明shell的名字，该參数第一个字符为"-"，指明这是一个login shell。比方对bash而言，启动參数为"-bash"。当bash以login shell启动时，它会运行/etc/profile中的命令，然后/etc/profile调用/etc/profile.d文件夹下的全部脚本。然后运行\~/.bash\_profile。~~/.bash\_profile调用~~/.bashrc，最后\~/.bashrc又调用/etc/bashrc。

要识别一个shell是否为login shell。仅仅需在该shell下运行echo $0:

\# echo $0

假设输出为该shell名字，加上一个'-'前缀。则说明该shell为login shell。比如-bash，-su等等。实验一下。在本人的Ubuntu系统下。打开Terminal，输入echo $0，得到的是"bash"，说明这不是一个login shell。而由SSH登陆到server上，运行相同命令。得到了"-bash"的结果，说明由SSH登陆的为login shell。

**非login的**：不需login而由某些程序启动的shell。传递给shell的參数，是没有'-'前缀的。还以Bash为例。当以非login方式启动时，它会调用\~/.bashrc。随后\~/.bashrc中调用/etc/bashrc。最后/etc/bashrc调用全部/etc/profile.d文件夹下的脚本。这个有兴趣的能够打开这些文件看一看。非login的shell主要包含以"#su","#su USERNAME"启动的shell。和图形终端（比如Ubuntu的Terminal），运行的脚本等等。识别非login的shell方法还是运行#echo $0命令，得到的结果假设没有'-'前缀。即为非login的。

**其他配置文件和登录信息**

**注销时生效的环境变量配置文件**

* **\~/.bash\_logout 注销登陆时写入**

**其他配置文件**

* **\~/bash\_history 历史命令文件**

**Shell登录信息**

* **本地终端欢迎信息: /etc/issue**

| 转义符 | 作用                   |
| --- | -------------------- |
| \d  | 显示当前系统日期             |
| \s  | 显示操作系统名称             |
| \l  | 显示登录的终端号，这个比较常用。     |
| \m  | 显示硬件体系结构，如i386、i686等 |
|     | 显示主机名                |
| \o  | 显示域名                 |
|     | 显示内核版本               |
|     | 显示当前系统时间             |
| \u  | 显示当前登录用户的序列号         |

**==远程终端==欢迎信息: /etc/issue.net**

* \==**转义符在/etc/issue.net文件中不能使用**==
* **是否显示此欢迎信息，由ssh的配置文件 /etc/ssh/sshd\_config决定，加入“Banner /etc/issue.net”行才能显示(记得重启SSH服务)**

**==登陆后==欢迎信息:/etc/motd 不管是本地登录，还是远程登录，都可以显示此欢迎信息**

### 正则表达式

**基础正则表达式**

**正则表达式与通配符**

* **正则表达式用来在文件中匹配符合条件的 字符串，==正则是包含匹配==（命令不加特殊字符的默认情况）。grep、awk、 sed等命令可以支持正则表达式。**
* **通配符用来匹配符合条件的文件名，==通配符是完全匹配==（命令不加特殊字符的默认情况）。ls、find、cp这些命令不支持正则表达式，所以只能使用shell自己的通配符来进行匹配了。**

**基础正则表达式**

| 元字符        | 作用                                                                                               |
| ---------- | ------------------------------------------------------------------------------------------------ |
| \*         | 前一个字符匹配0次或任意多次。                                                                                  |
| .          | 匹配除了换行符外任意一个字符。                                                                                  |
| ^          | 匹配行首。例如:^hello会匹配以hello开头的行。                                                                     |
| $          | 匹配行尾。例如:hello&会匹配以hello结尾的行。                                                                     |
| \[]        | 匹配中括号中指定的任意一个字符，只匹配一个字符。 例如:\[aoeiu] 匹配任意一个元音字母，\[0-9] 匹配任意一位 数字， \[a-z]\[0-9]匹配小写字和一位数字构成的两位字符。 |
| \[^]       | 匹配除中括号的字符以外的任意一个字符。例如:\[^0-9] 匹配 任意一位非数字字符，\[^a-z] 表示任意一位非小写字母。                                  |
| \\         | 转义符。用于将特殊符号的含义取消。                                                                                |
| \\{n\\}    | 表示其前面的字符恰好出现n次。例如:\[0-9]{4} 匹配4位数 字，\[1]\[3-8]\[0-9]{9} 匹配手机号码。                                  |
| \\{n,\\\}} | 表示其前面的字符出现不小于n次。例如: \[0-9]{2,} 表示两 位及以上的数字。                                                      |
| \\{n,m\\}  | 表示其前面的字符至少出现n次，最多出现m次。例如: \[a- z]{6,8} 匹配6到8位的小写字母。                                              |

* “\*”前一个字符匹配0次，或任意多次
  * grep “a\*” test\_rule.txt 匹配所有内容，包括空白行
  * grep “aa\*” test\_rule.txt 匹配至少包含有一个a的行
  * grep “aaa\*” test\_rule.txt 匹配最少包含两个连续a的字符串
  * grep “aaaaa\*” test\_rule.txt 则会匹配最少包含四个个连续a的字符串
* “.” 匹配除了换行符外任意一个字符
  * grep “s…d” test\_rule.txt “s…d”会匹配在s和d这两个字母之间一定有两个字符的单词
  * grep “s.\*d” test\_rule.txt 匹配在s和d字母之间有任意字符
  * grep “.\*” test\_rule.txt 匹配所有内容
  * “^”匹配行首，“$”匹配行尾
    * grep “^M” test\_rule.txt 匹配以大写“M”开头的行
    * grep “n$” test\_rule.txt 匹配以小写“n”结尾的行
    * grep -n “^$” test\_rule.txt 会匹配空白行
* “\[]” 匹配中括号中指定的任意一个 字符，只匹配一个字符
  * grep “s\[ao]id” test\_rule.txt 匹配s和i字母中，要不是a、要不是o
  * grep “\[0-9]” test\_rule.txt 匹配任意一个数字
  * grep “^\[a-z]” test\_rule.txt 匹配用小写字母开头的行
  * “\[^]” 匹配除中括号的字符以外的 任意一个字符
    * grep “^\[^a-z]” test\_rule.txt 匹配不用小写字母开头的行
    * grep “^\[^a-z A-Z]” test\_rule.txt 匹配不用字母开头的行
* “\” 转义符
  * grep “.$” test\_rule.txt 匹配使用“.”结尾的行
  * “\\{n\\}”表示其前面的字符恰好出现n次
  * grep “a\\{3\\}” test\_rule.txt 匹配a字母连续出现三次的字符串
  * grep “\[0-9]\\{3\\}” test\_rule.txt 匹配包含连续的三个数字的字符串
  * “\\{n,\\}”表示其前面的字符出现不小于n次
  * grep “^\[0-9]\\{3,\\}\[a-z]” test\_rule.txt 匹配最少用连续三个数字开头的行
  * “\\{n,m\\}”匹配其前面的字符至少出现n次， 最多出现m次
  * grep “sa\\{1,3\\}i” test\_rule.txt 匹配在字母s和字母i之间有最少一个a，最多三个a

**字符截取命令**

**cut字段提取命令**

**cut \[选项] 文件名**

* **-f 列号: 提取第几列**
* **-d 分隔符: 按照指定分隔符分割列**

**grep为提取行，cut提取列，而且cut提取的表格中，只能用制表符，一般用空格难以数清有几个空格而用空格做分隔符时不用这个命令，比如：**

| ID | Name | gender | Mark |
| -- | ---- | ------ | ---- |
| 1  | Li   | M      | 86   |
| 2  | Shen | M      | 90   |
| 3  | Gao  | M      | 83   |

**他们之间所有的都是拿Tab键隔开的，不是空格**

* **提取多列时，用“，”隔开就可以** **cut -f 2 student.txt** **cut -f 2,3 student.txt**
* **有具体分割符时，也可以没有Tab键**（如果是空格则“”中有几个空格代表过滤几个，之后若是有剩空格则一个算一列，容易犯错） **cut -d “:” -f 1,3 /etc/passwd 以：为分隔符取1，3列**
* **一般在使用cut命令的时候和管道符“|”连着使用**

**printf命令**

\==**printf ‘输出类型输出格式’ 输出内容**==（‘’或者“”都可以）

* 输出类型:
  * **%ns: 输出字符串。n是数字指代输出几个字符**
  * **%ni: 输出整数。n是数字指代输出几个数字**
  * **%m.nf: 输出浮点数。m和n是数字，指代输出的整数 位数和小数位数。如%8.2f代表共输出8位数， 其中2位是小数，6位是整数。**
* 输出格式:
  * **\a: 输出警告声音**
  * **\b: 输出退格键，也就是Backspace键**
  * **\f: 清除屏幕**
  * **\n: 换行**
  * **\r: 回车，也就是Enter键**
  * **\t: 水平输出退格键，也就是Tab键 \v: 垂直输出退格键，也就是Tab键**

例子： printf %s 1 2 3 4 5 6 printf %s %s %s 1 2 3 4 5 6 printf ‘%s %s %s’ 1 2 3 4 5 6 printf ‘%s %s %s\n’ 1 2 3 4 5 6 只有最后一个会输出： 1 2 3 4 5 6 **其实现了格式化与参数自动匹配，格式化一次需要几个参数他就提取几个** **他在与cat命令结合使用的时候，需要用$()把cat命令扩起来，使用这种命令赋予变量的方式，才能正确输出文件内容，但是具体格式还得用%s\t 或者%s\n控制**

* **printf主要在awk命令编程中使用** 例子： vi student.txt ID Name PHP Linux MySQL Average 1 Liming 82 95 86 87.66 2 Sc 74 96 87 85.66 3 Gao 99 83 93 91.66\*\*
* **printf ‘%s’ $(cat student.txt)** **不调整输出格式**
* **printf ‘%s\t %s\t %s\t %s\t %s\t %s\t \n’ $(cat student.txt) 调整格式输出**

**在awk命令的输出中支持print和printf命令**

* **print:print会在每个输出之后自动加入一 个换行符(Linux默认没有print命令)**
* **printf:printf是标准格式输出命令，并不会自动加入换行符，如果需要换行，需要手工加入换行符**

**awk命令**

**awk命令也叫awk编程，可以识别非制表符的空格，用来解决cut命令解决不了的提取列工作，他是把需要提取的原文件一行一行扫描，扫描每一行中所需的列，然后把它记录下来，在全部扫描完之后全部打印出来。**

\==整个 awk 的处理流程是：==

1. 读入第一行，并将第一行的资料填入 $0, $1, $2.... 等变数当中；
2. 依据 "条件类型" 的限制，判断是否需要进行后面的 "动作"；
3. 做完所有的动作与条件类型；
4. 若还有后续的『行』的数据，则重复上面 1\~3 的步骤，直到所有的数据都读完为止。

\==awk 的内建变量：==

![image-20200812222506800](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200812222506800.png)

\==**awk** 的逻辑运算字符==

![image-20200812222529801](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20200812222529801.png)

* \==awk ‘条件1{动作1} 条件2{动作2}…’ 文件名==
  * **条件(Pattern):** **一般使用关系表达式作为条件**
    * **x > 10 判断变量 x是否大于10**
    * **x>=10 大于等于**
    * **x<=10 小于等于**
  * **动作(Action):**
  * 格式化输出流程控制语句 例子： vi student.txt ID Name PHP Linux MySQL Average 1 Liming 82 95 86 87.66 2 Sc 74 96 87 85.66 3 Gao 99 83 93 91.66
  * awk ‘{printf $2 “\t” $6 “\n”}’ student.txt 其中$2代表第2列，$6代表第6列，他可以识别非制表符的空格，单引号里面直接大括号代表没有条件，只要是输入有内容全部符合 取第2列和第6列 df -h | awk '{print $1 “\t” $3}' 提取 df -h命令显示之后的内容中第一列和第三列

**需要注意：** **printf 不可以自动换行，print 可以在末尾自动换行，但是在Linux系统中没有print命令，只有printf命令，但是在wak命令中两个都有，使用print可以少一个换行符。**

* **BEGIN** **BEGIN必须是大写，他是一个条件。** **awk ‘BEGIN{printf “This is a transcript \n” } {printf $2 “\t” $6 “\n”}’ student.txt** **他会在打印出2，6行之前先输出一句话This is a transcript 它的作用是强者命令第一个执行他后面的语句，也可以指定分割符**
* **FS内置变量** **FS是用来指定分隔符的** **FS=“:”就是指定：为分隔符**

例子： cat /etc/passwd | grep “/bin/bash” | awk 'BEGIN {FS=":"} {printf $1 “\t” $3 “\n”}'

**这是打印用户信息地一和第三列，为什么需要在{FS=":"} 前加BEGIN呢？** **因为如果你不加BEGIN你会发现除了第一行，其他都已经按格式打印出来了，但是只有第一行会照原样输出，因为awk默认是空格为分隔符，他在执行这条命令的时候，第一行数据已经被扫描了，所以来不及修改格式，但是加了BEGIN，他会第一步强制先把默认分隔符修改了。**

* **关系运算符** **cat student.txt | grep -v Name | awk '$6 >= 87 {printf $2 “\n” }'**

**重要事项应该要先说明的：**

** awk 的指令间隔：所有 awk 的动作，亦即在 {} 内的动作，如果有需要多个指令辅助时，可利用分号『;』**

**间隔， 或者直接以 \[Enter] 按键来隔开每个指令，例如上面的范例中，鸟哥共按了三次 \[enter] 喔！**

** 逻辑运算当中，如果是『等于』的情况，则务必使用两个等号『==』！**

** 格式化输出时，在 printf 的格式设定当中，务必加上 \n ，才能进行分行！**

** 与 bash shell 的变量不同，在 awk 当中，变量可以直接使用，不需加上 $ 符号。**

**sed命令**

**sed命令** **sed 是一种几乎包括在所有 UNIX 平台(包括 Linux)的轻量级流编辑器。**

**sed主要是用来==配合管道符==进行选取、替换、删除、新增的命令。** **它不仅可以修改文件内容，还可以修改命令结果，支持管道符操作，这就是与vim最大的区别**

**sed \[选项] ‘\[动作]’ 文件名** **选项：**

* **-n: 一般sed命令会把所有数据都输出到屏幕 ， 如果加入此选择，则只会把经过sed命令处理的行输出到屏幕。**
* **-e: 允许对输入数据应用多条sed命令编辑**
* **-i: 用sed的修改结果直接修改读取数据的文件， 而不是由屏幕输出**

**动作:**

* **a : 追加，在当前行后添加一行或多行。添加多行时，除最后 一行外，每行末尾需要用“\”代表数据未完结。**
* **c : 行替换，用c后面的字符串替换原数据行，替换多行时，除最后一行外，每行末尾需用“\”代表数据未完结。**
* **i : 插入，在当期行前插入一行或多行。插入多行时，除最后 一行外，每行末尾需要用“\”代表数据未完结。**
* **d: 删除，删除指定的行。**
* **p:打印，输出指定的行。**
* **s:字串替换，用一个字符串替换另外一个字符串。格式为“行范 围s/旧字串/新字串/g”(和vim中的替换格式类似)。**

**例子：** **sed ‘2p’ student.txt** **查看文件的第二行会显示：**

| ID | Name   | PHP | Linux | MySQL | Average |
| -- | ------ | --- | ----- | ----- | ------- |
| 1  | Liming | 82  | 95    | 86    | 87.66   |
| 1  | Liming | 82  | 95    | 86    | 87.66   |
| 2  | Sc     | 74  | 96    | 87    | 85.66   |
| 3  | Gao    | 99  | 83    | 93    | 91.66   |

**会发现多了一行，因为一般sed命令会把所有数据都输出到屏幕 ，只不过会先输出你想要的，这时候就需要-n配合**

* sed -n ‘2p’ student.txt 输入-n后就没有多余的了
* sed ‘2,4d’ student.txt 删除第二行到第四行的数据，但不修改文件本身
* sed ‘2a hello’ student.txt 在第二行后追加hello
* sed ‘2i hello \ world’ student.txt 在第二行前插入两行数据
* sed '2c No such person‘ student.txt

**数据替换** **字符串替换** **sed ‘s/旧字串/新字串/g’ 文件名**

* sed ‘3s/74/99/g’ student.txt 在第三行中，把74换成99
* sed -i ‘3s/74/99/g’ student.txt sed操作的数据直接写入文件
* sed -e ‘s/Liming//g ; s/Gao//g’ student.txt 同时把“Liming”和“Gao”替换为空

**字符处理命令**

**排序命令sort**

**sort \[选项] 文件名**

* **-f:忽略大小写**
* **-n:以数值型进行排序，默认使用字符串型排序**
* **-r:反向排序**
* **-t:指定分隔符，默认是分隔符是制表符**
* **-k n\[,m]: 按照指定的字段范围排序。从第n字段开始， m字段结束(默认到行尾)**

例子：

* sort /etc/passwd 排序用户信息文件
* sort -r /etc/passwd 反向排序
* sort -t “:” -k 3,3 /etc/passwd 指定分隔符是“:”，用第三字段开头，第三字段结尾排序，就是只用第三字段排序,但是他不认识数字，会把数字当成字符串，认为3比11大 ，所以我需要加-n，进行数值排序
* sort -n -t “:” -k 3,3 /etc/passwd

**统计命令wc** **wc \[选项] 文件名**

* **-l: 只统计行数**
* **-w: 只统计单词数**
* **-m: 只统计字符数**

**条件判断**

* **按照文件类型进行判断**

| 测试选项  | 作用                                |
| ----- | --------------------------------- |
| -b 文件 | 判断该文件是否存在，并且是否为 块设备文件(是块设备文件 为真)  |
| -c文件  | 判断该文件是否存在，并且是否为字符设备文件(是字符设备 文件为真) |
| -d 文件 | 判断该文件是否存在，并且是否为目录文件(是目录为真)        |
| -e 文件 | 判断该文件是否存在(存在为真)                   |
| -f 文件 | 判断该文件是否存在，并且是否为普通文件(是普通文件为真)      |
| -L 文件 | 判断该文件是否存在，并且是否为管道文件(是管道文件为真)      |
| -p 文件 | 判断该文件是否存在，并且是否为符号链接文件(是符号链接 文件为真) |
| -s 文件 | 判断该文件是否存在，并且是否为非空(非空为真)           |
| -S 文件 | 判断该文件是否存在，并且是否为套接字文件(是套接字文件 为真)   |

**两种判断格式** **上面的表结合一下命令来判断**

* **test -e /root/install.log**
* **\[ -e /root/install.log ]** ==**中括号两边必须有空格==，只能为\[ -e /root/install.log ] ，不能是\[-e /root/install.log]**

在判断之后，使用echo $?来观察输出语句是否为真 \[ -d /root ] && echo “yes” || echo "no" 第一个判断命令如果正确执行，则打印“yes”，否则打印“no”

* **按照文件权限进行判断**

| 测试选项  | 作用                                    |
| ----- | ------------------------------------- |
| -r 文件 | 判断该文件是否存在，并且是否该文件拥有读权限(有读 权限为真)       |
| -w文件  | 判断该文件是否存在，并且是否该文件拥有写权限(有写 权限为真)       |
| -x 文件 | 判断该文件是否存在，并且是否该文件拥有执行权限(有 执行权限为真)     |
| -u 文件 | 判断该文件是否存在，并且是否该文件拥有SUID权限(有 SUID权限为真) |
| -g 文件 | 判断该文件是否存在，并且是否该文件拥有SGID权限(有 SGID权限为真) |
| -k 文件 | 判断该文件是否存在，并且是否该文件拥有SBit权限(有 SBit权限为真) |

例子： \[ -w student.txt ] && echo “yes” || echo "no" 判断文件是拥有写权限的 不过系统不会区分，比如-w，只要所有者，所属组，其他人其中有一个有写权限，他就会返回yes，所以这个时候就需要我们自己写脚本

* **两个文件之间进行比较**

| 测试选项        | 作用                                                       |
| ----------- | -------------------------------------------------------- |
| 文件1 -nt 文件2 | 判断文件1的修改时间是否比文件2的新(如果新则为真)                               |
| 文件1 -ot 文件2 | 判断文件1的修改时间是否比文件2的旧(如果旧则为真)                               |
| 文件1 -ef 文件2 | 判断文件1是否和文件2的Inode号一致，可以理解为两个文件是否为同一个文件。这个判断用于判断硬链接是很好的方法 |

例子： ln /root/student.txt /tmp/stu.txt 创建一个硬链接 \[ /root/student.txt -ef /tmp/stu.txt ] && echo “yes” || echo “no” yes 用test测试

* **两个整数之间比较**

| 测试选项         | 作用                     |
| ------------ | ---------------------- |
| 整数1 -eq 整数 2 | 判断整数1是否和整数2相等(相等为真)    |
| 整数1 -ne 整数 2 | 判断整数1是否和整数2不相等(不相等位置)  |
| 整数1 -gt 整数2  | 判断整数1是否大于整数2(大于为真)     |
| 整数1 -lt 整数2  | 判断整数1是否小于整数2(小于位置)     |
| 整数1 -ge 整数2  | 判断整数1是否大于等于整数2(大于等于为真) |
| 整数1 -le 整数2  | 判断整数1是否小于等于整数2(小于等于为真) |

例子：

* \[ 23 -ge 22 ] && echo “yes” || echo “no” yes 判断23是否大于等于22
* \[ 23 -le 22 ] && echo “yes” || echo “no” no 判断23是否小于等于22\*\*
* **字符串的判断**

| 测试选项       | 作用                       |
| ---------- | ------------------------ |
| -z 字符串     | 判断字符串是否为空(为空返回真)         |
| -n 字符串     | 判断字符串是否为非空(非空返回真)        |
| 字串1 ==字串2  | 判断字符串1是否和字符串2相等(相等返回真)   |
| 字串1 != 字串2 | 判断字符串1是否和字符串2不相等(不相等返回真) |

例子： name=sc 给name变量赋值 \[ -z “$name” ] && echo “yes” || echo “no” no 判断name变量是否为空，因为不为空，所以返回no aa=11 bb=22 给变量aa和变量bb赋值 \[ “$aa” == “$bb" ] && echo “yes” || echo "no" 判断两个变量的值是否相等，明显不相等 ，所以返回no

* **多重条件判断**

| 测试选项       | 作用                         |
| ---------- | -------------------------- |
| 判断1 -a 判断2 | 逻辑与，判断1和判断2都成立，最终的结果才为真    |
| 判断1 -o 判断2 | 逻辑或，判断1和判断2有一个成立，最终的结果就为 真 |
| !判断        | 逻辑非，使原始的判断式取反              |

例子： aa=11 \[ -n “$aa” -a “$aa” -gt 23 ] && echo “yes” || echo "no" 判断变量aa是否有值，同时判断变量aa的是否大于23 因为变量aa的值不大于23，所以虽然第一个判断值为真， 返回的结果也是假 aa=24 \[ -n “$aa” -a “$aa” -gt 23 ] && echo “yes” || echo “no” yes

**流程控制**

**if语句**

* **单分支if条件语句**

```shell
if [ 条件判断式 ];then
	程序
fi 
123
```

**或者**

```shell
if [ 条件判断式 ]
	then 
		程序 
fi 
1234
```

**单分支条件语句需要注意几个点**

* **if语句使用fi结尾，和一般语言使用大括号结尾不同**
* **\[ 条件判断式 ]就是使用test命令判断，所以中括号和条件判断式之间必须有空格**
* **then后面跟符合条件之后执行的程序，可以放在\[]之后，用“;”分割。也可以换行写入，就不需要“;”了**

**例子：判断分区使用率**

```shell
#!/bin/bash  #统计根分区使用率 
#Author: yangyang (E-mail: 1771566679@qq.com) 
rate=$(df -h | grep "/dev/sda3" | awk '{print $5}' | cut -d "%" - f1) 
#把根分区使用率作为变量值赋予变量rate 
if [ $rate -ge 80 ]
	then
	echo "Warning! /dev/sda3 is full!!"
fi 
12345678
```

* **双分支if条件语句**

```shell
if [ 条件判断式] 
   then 
   		条件成立时，执行的程序 
   else 
   		条件不成立时，执行的另一个程序
fi 
123456
```

**例子1:备份mysql数据库**

```shell
#!/bin/bash 
#备份mysql数据库。
# Author:yangyang (E-mail: 1771566679@qq.com) 
ntpdate asia.pool.ntp.org &>/dev/null #同步系统时间 
date=$(date +%y%m%d) #把当前系统时间按照“年月日”格式赋予变量date 
size=$(du -sh /var/lib/mysql) #统计mysql数据库的大小，并把大小赋予size变量 
if [ -d /tmp/dbbak ]
   then
        echo "Date : $date!" > /tmp/dbbak/dbinfo.txt
        echo "Data size : $size" >> /tmp/dbbak/dbinfo.txt
        cd /tmp/dbbak
tar -zcf mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null 
        rm -rf /tmp/dbbak/dbinfo.txt
   else
        mkdir /tmp/dbbak
        echo "Date : $date!" > /tmp/dbbak/dbinfo.txt
        echo "Data size : $size" >> /tmp/dbbak/dbinfo.txt
        cd /tmp/dbbak
tar -zcf mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null 
        rm -rf /tmp/dbbak/dbinfo.txt
fi
123456789101112131415161718192021
```

**例子2:判断apache是否启动**

```shell
#!/bin/bash 
#Author: yangyang (E-mail:1771566679@qq.com) 
port=$(nmap -sT 47.95.5.171 | grep tcp | grep http | awk '{print $2}') 
#使用nmap命令扫描服务器，并截取apache服务的状态，赋予变量port if [ "$port" == "open" ] 
	then
		echo “$(date) httpd is ok!” >> /tmp/autostart-acc.log 
    else
        /etc/rc.d/init.d/httpd start &>/dev/null
		echo "$(date) restart httpd !!" >> /tmp/autostart-err.log 
fi 
12345678910
```

**nmap 远程扫描，检查服务是否启动 nmap -sT 扫描指定服务器上开启的TCP端口**

* **多分支if条件语句**

```shell
if [ 条件判断式1 ] 
			   then 
					当条件判断式1成立时，执行程序1 
elif [ 条件判断式2 ] 
				then
					当条件判断式2成立时，执行程序2
...省略更多条件... 
else 
					当所有条件都不成立时，最后执行此程序
 fi 
12345678910
```

**例子：**

```shell
#!/bin/bash #判断用户输入的是什么文件 
#Author: yangyang (E-mail:1771566679@qq.com) 
read -p "Please input a filename: " file 
#接收键盘的输入，并赋予变量file if [ -z "$file" ] 
#判断file变量是否为空 
	then 
		echo "Error,please input a filename" 
		exit 1 #定义错误返回值1
elif [ ! -e "$file" ] #判断file的值是否存在 
	then
		echo "Your input is not a file!" 
		exit 2   #定义错误返回值2
elif [ -f "$file" ] #判断file的值是否为普通文件 
   then
        echo "$file is a regulare file!"
elif [ -d "$file" ] #判断file的值是否为目录文件 
   then
        echo "$file is a directory!"
else
        echo "$file is an other file!"
fi
```

**for循环**

* **语法一**

```shell
for 变量 in 值1 值2 值3
do 
	程序
done
1234
```

**例子**

```shell
#!/bin/bash

#Author：yangyang (Email:1771566679@qq.com)
#打印时间

for time in morning noon afternoon evening
	do
		echo “This time is $time!”
	done
123456789
```

**例子1：批量解压缩**

```shell
#!/bin/bash

#Author:yangyang (Email:1771566679@qq.com)
#批量解压缩软件包

cd /lamp
ls *.tar.gz > ls.log  #ls *.tar.gz 输出结果覆盖到ls.log文件
for i in $(cat ls.log)
        do
        tar -zxf $i $>/dev/null
        done
rm -rf /lamp/ls.log
123456789101112
```

**例子2:打印车票**

```shell
#!/bin/bash

#Author:yangyang (Email:1771566679@qq.com)
#计算文件个数，并打印到屏幕

cd /root/sh
ls *.sh > ls.log
for i in $(cat ls.log)
        do
        echo $y
        y=$(($y+1))
        done
rm -rf ls.log
12345678910111213
```

* **语法二**

```shell
for ((初始值;循环控制调节;变量变化))
   do
   	程序
   done
1234
```

**例子：计算1加到100**

```shell
#!/bin/bash

#Author:yangyang (Email:1771566679@qq.com)
#计算1加到100

s=0
for ((i=1;i<=100;i++))
        do
                s=$(($s+$i))
        done
echo "The sum of 1+2+...+99+100 is $s!"
1234567891011
```

**这种情况适用于知道循环次数**

**例子：批量创建用户**

```shell
 #!/bin/bash

#Author:yangyang (Email:1771566679@qq.com)
#批量添加新用户

read -p "Please input user name: " -t 30 name   #输入用户名，等待时间30s
read -p "Please input the number of users: " -t 30 num  #输入创建用户个数，等待时间30s
read -p "Please input the password of users: " -t 30 pass       #输入用户密码，等待时间30s
if [ ! -z "$name" -a ! -z "$num" -a ! -z "$pass" ]      #判断输入信息是否为空 
        then
        y=$(echo $num | sed s/'^[0-9]*$'//g)    #这里是判断输入的用户个数是否为数字，sed后也可以把^[0-9]*$换为's/[0-9]//g'
                if [ -z "$y" ]  #如果上一条语句输出不为空，就是输入的用户个数为数字，继续执行
                        then
                        for ((i=1;i<=$num;i++)) #开始循环
                                do
                                        /usr/sbin/useradd "$name$i" &>/dev/null #建立用户
                                        echo $pass | /usr/bin/passwd --stdin "$name$i" &>/dev/null      #设置用户密码，与用户名相同
                                done
                        echo "Build seccees!"
                fi
fi 
123456789101112131415161718192021
```

**如果输入的时候输错了需要按，ctrl+退格键**

**while循环与until循环**

**while循环** **while循环是不定循环，也称作条件循环 。只要条件判断式成立，循环就会一直继续，直到条件判断式不成立，循环才会停止。这就和for的固定循环不太一样了。**

```shell
while [ 条件判断式 ] 
	do 
		程序 
	done 
1234
```

**例子：从1加到100**

```shell
#!/bin/bash  

#Author: yangyang (E-mail: 1771566679@qq.com) 
#从1加到100

i=1 
s=0 
while [ $i -le 100 ] #如果变量i的值小于等于100，则执行循环 
	do 
                s=$(( $s+$i ))
				i=$(( $i+1 )) 
	done 
echo "The sum is: $s"
12345678910111213
```

**until循环** **until循环，和while循环相反，until循环时只要条件判断式不成立则进行循环，并执行循环程序。一旦循环条件成立，则终止循环。**

```shell
until [ 条件判断式 ] 
	do 
		程序 
	done 
1234
```

例子：从1加到100

```shell
#!/bin/bash 

#Author: yangyang (E-mail: 1771566679@qq.com) 
#从1加到100

i=1 
s=0 
until [ $i -gt 100 ] #循环直到变量i的值大于100，就停止循环 
		do 
                s=$(( $s+$i ))
				i=$(( $i+1 )) 
		done 
echo "The sum is: $s"
```

### Linux服务管理

\_daemon\_也是一段程序(program),不过它运行后是常驻在内存中的,_daemon\_所提供的系统或网络功能就叫\_service_。

> 若是服务的守护进程，命名时一般会在后面加个d，例如 crond 及 atd ，还有 rsyslogd 等等的。还有一些则是负责网络联机的服务，例如Apache, named, postfix, vsftpd... 等等的。

**守护进程及初始化守护进程**

若是从某个父进程中通过fork的方法创建一个子进程，若想其变为守护进程，我们需要将其与运行前的环境隔离开来，这些环境包括未关闭的文件描述符、控制终端、会话和进程组、工作目录及文件权限掩码（通过c语言实现调用操作系统实现）

1. 创建子进程，父进程退出

```
if((pid = fork())>0)
   exit(0);
else if(pid<0)
{
    perror("fail to fork");
    exit(-1);
}
//在Linux中父进程先于子进程退出会造成子进程成为孤儿进程，而每当系统发现一个孤儿进程是，就会自动由1号进程（init）收养它，这样，原先的子进程就会变成init进程的子进程。
```

1. 在子进程中创建新会话

> Linux中的进程与控制终端，登录会话和进程组之间的关系：进程属于一个进程组，进程组号（GID）就是进程组长的进程号（PID）。登录会话可以包含多个进程组。这些进程组共享一个控制终端。这个控制终端通常是创建进程的登录终端。
>
> 一个会话期可以有一个单独的控制终端，只有其前台进程才可以拥有控制终端，实现与用户的交互。**从shell中启动的**每一个进程将继承一个与之相结合的终端，以便进程与用户交互，==但是守护进程不需要这些==，子进程继承父进程的会话期和进程组ID，子进程会受到发送给该会话期的信号的影响，所以守护进程应该创建一个新的会话期
>
> 控制终端，登录会话和进程组通常是从父进程继承下来的。我们的目的就是要摆脱它们，使之不受它们的影响。方法是在第1点的基础上，调用setsid()使进程成为会话组长：`setsid()`,从真正意义上的独立开，从而与别的进程彻底隔离开。
>
> setsid函数用于创建一个新的会话，并担任该会话组的组长。调用setsid有下面的3个作用： 让进程摆脱原会话的控制 让进程摆脱原进程组的控制 让进程摆脱原控制终端的控制
>
> ```
>  //现在，进程已经成为无终端的会话组长。但它可以重新申请打开一个控制终端。可以通过使进程不再成为会话组长来禁止进程重新打开控制终端： 
>    if(pid=fork()) 
>       exit(0);//结束第一子进程，第二子进程继续（第二子进程不再是会话组长）
> ```

![image-20210103134718603](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20210103134718603.png)

1. 改变当前目录为根目录

> 使用fork创建的子进程继承了父进程的当前工作目录。守护进程不应当使用父进程的工作目录，应该设置自己的工作目录，通常可以通过chdir()来完成，一般可以将其设置为根目录，不过有些守护进程需要将它设置到自己特定的工作目录，**但此时必须保证所设置的工作目录处于一个不能卸载的文件系统中**，因为守护进程通常在系统引导后是一直存在的。如：改变到/tmp目录中
>
> chdir("/tmp") ;

1. 重设文件权限掩码

> 守护进程从父进程继承来的文件创建方式掩码可能会拒绝设置某些许可权限，文件权限掩码是指屏蔽掉文件权限中的对应位。比如，有个文件权限掩码是050，它就屏蔽了文件组拥有者的可读与可执行权限。由于使用fork函数新建的子进程继承了父进程的文件权限掩码，可能使守护进程的执行出现问题，因此，把文件权限掩码设置为0，可以大大增强该守护进程的灵活性。设置文件权限掩码的函数是umask。在这里，通常的使用方法为umask(0)。

1. 关闭文件描述符

```
//进程从创建它的父进程那里继承了打开的文件描述符。如不关闭，将会浪费系统资源，造成进程所在的文件系统无法卸下以及引起无法预料的错误:
for(i=0;i<=getdtablesize();i++)
 close(i);
```

1. 忽略SIGCHLD信号

> \==这一步只对**需要创建子进程的守护进程**才有必要==，很多服务器守护进程设计成通过派生子进程来处理客户端的请求，因为一般不会关闭身为父进程的本守护进程，如果父进程不对SIGCHLD信号进行处理的话，子进程在终止后变成僵尸进程，通过将信号SIGCHLD的处理方式设置为SIG\_IGN可以避免这种情况发生。
>
> signal(SIGCHLD,SIG\_IGN);

1. 用日志系统记录出错信息

因为守护进程没有控制终端，当进程出现错误时无法写入到标准输出上，可以通过调用syslog将出错信息写入到指定的文件中。

1. 守护进程退出处理

当用户需要外部停止守护进程运行时，往往会使用 kill命令停止该守护进程。所以，守护进程中需要编码来实现kill发出的signal信号处理，达到进程的正常退出。

**服务简介与分类**

**服务的分类**

![服务的分类](https://img-blog.csdnimg.cn/20200529121600849.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70) **启动与自启动**

* **服务启动:就是在当前系统中让服务运行 ，并提供功能。**
* **服务自启动:自启动是指让服务在系统开 机或重启动之后，随着系统的启动而自动 启动服务。**

**查询已安装的服务**

* **RPM包安装的服务**（ubuntu ==sysv-rc-conf==）
  * **chkconfig --list 查看服务自启动状态，可以看到所有RPM包安装的服务，查看在进入不同级别的启动中所有服务的启动状态**
  * **ps aux** **查看启动服务的进程**
* **源码包安装的服务** **查看服务安装位置，一般是/usr/local/下**

**RPM安装服务和源码包安装服务的区别** **就是安装位置的不同**

* **service 等用于启动服务的都是读取/etc/sbin目录下的可执行文件，rpm包默认安装的位置，源码包也可在里面设置硬链接，但是尽量不要这样，因为这本就是源码包和rpm包的区别**
* **源码包安装在指定位置，一般是/usr/local/**
* **RPM包安装在默认位置中**

**独立服务的管理**

**RPM包安装服务的位置** **RPM安装服务和源码包安装服务的区别就是安装位置的不同**

* **源码包安装在指定位置，一般是/usr/local/**
* **RPM包安装在默认位置中**

**/etc/init.d/:启动脚本位置** **/etc/sysconfig/:初始化环境配置文件位置** **/etc/:配置文件位置** **/etc/xinetd.conf:xinetd配置文件** **/etc/xinetd.d/:基于xinetd服务的启动脚本** **/var/lib/:服务产生的数据放在这里** **/var/log/:日志**

**独立服务的启动**

* **/etc/rc.d/init.d/独立服务名 start|stop|status|restart**（服务文件里有位置参数的case判断 判断start）
* **service 独立服务名 start|stop|restart|status** **service --status-all 查询服务器全部已经安装的RPM包的服务的运行状态** ==**CentOS7为systemctl list-unit-files**== ==**不过server是红帽版本专有的**==

**独立服务的自启动**

* **chkconfig \[–level 运行级别] \[独立服务名] \[on|off]**
  * **chkconfig --level 2345 htttpd on 下次开机自启动apache**
  * **chkconfig --level 2345 htttpd off 下次开机不自启动apache**
* \==**修改/etc/rc.d/rc.local文件**（推荐方式）====（initd管理系统，改用systemd. ，systemd 默认会读取 /etc/systemd/system 下的配置文件，该目录下的文件会链接 /lib/systemd/system/ 下的文件。一般系统安装完 /lib/systemd/system/ 下会有 rc-local.service 文件，即我们需要的配置文件。）== **把/etc/rc.d/init.d/ 独立服务名 start 写入文件**
* **使用ntsysv命令管理自启动**
* \==**如果没有ntsysv命令，yum -y install ntsysv下载即可，不过这个也是红帽专有的**==

**基于xinetd服务的管理**

* **xinetd是新一代的网络守护进程服务程序，又叫超级Internet服务器,常用来管理多种轻量级Internet服务。 xinetd提供类似于inetd+tcp\_wrapper的功能，但是更加强大和安全。**
* **Telnet协议是TCP/IP协议族中的一员，是Internet远程登录服务的标准协议和主要方式。不过现在已经被ssh替代。**

**安装xinetd与telnet** **yum -y install xinetd** **yum -y install telnet-server** **telnet 服务器端不安全，实验完之后立马删除！！！**

**xinetd服务的启动**

* **进入配置文件，把disable=yes改为no** **vim /etc/xinetd.d/telnet** **service telnet 服务的名称为telnet** **{** **flags = REUSE #标志为REUSE，设定TCP/IP socket可重用** **socket\_type = stream #使用TCP协议数据包** **wait =no #允许多个连接同时连接** **user =root #启动服务的用户为root** **server =/usr/sbin/in.telnetd #服务的启动程序** **log\_on\_failure += USERID #登陆失败后，记录用户的ID** **disable = no #服务不启动** **}**
* **重启xinetd服务** **service xinetd restart** **重启xinetd服务,基于他的telnet自动重启**

**xinetd服务的自启动**

* **chkconfig telnet on**
* **ntsysv** ==**基于xinetd的服务自启动和启动是相通的，非常不适合做服务器管理，开启自启动，服务自动启动。关闭自启动，服务也关闭 。**==

**源码包安装服务的管理**

* **源码包安装服务的启动** **使用绝对路径，调用启动脚本来启动。不同的源码包的启动脚本不同。可以查看源码包的安装说明，查看启动脚本的方法。** **/usr/local/apache2/bin/apachectl start|stop**
* **源码包服务的自启动** **vi /etc/rc.d/rc.local** **加入** **/usr/local/apache2/bin/apachectl start**
* **让源码包服务被服务管理命令识别** **让源码包的apache服务能被service命令管理启动** **ln -s /usr/local/apache2/bin/apachectl /etc/init.d/apache**
* **让源码包的apache服务能被chkconfig与 ntsysv命令管理自启动**
  * **在创建好软链接后，修改servic可以扫描的Apache文件 vi /etc/init.d/apache** **#chkconfig: 35 86 76** **#指定httpd脚本可以被chkconfig命令管理。格式是: chkconfig: 运行级别 启动顺序 关闭顺序** ==**启动级别在/etc/rc.d/ 里面查看，rc0.d rc1.d rc2.d rc3.d rc4.d rc5.d rc6.d这六个就是0-6运行级别中文件的启动以及关闭顺序，启动顺序以S打头，关闭顺序以K打头，只需要找一个没有被占用的数字即可，不能和现有的顺序重叠。**== **#description: source package apache** **#说明，内容随意** **只需要写两句英文即可，两句都是注释，==都得有#号==，写在文件开始。** ==**不太推荐修改，使用标准的启动方式就比较好**==

**服务管理总结**

![服务管理总结](https://img-blog.csdnimg.cn/20200529123748236.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70)

* 常见服务简介p588

### Linux系统管理

**进程查看**

**进程简介**

程序 (program)：通常为 binary program ，放置在储存媒体中 (如硬盘、光盘、软盘、磁带等)， 为实体文件的型态存在；

进程 (process)：程序被触发后，执行者的权限与属性、程序的程序代码与所需数据等都会被加载内存中，操作系统并给予这个内存内的单元一个标识符 (PID)，可以说，进程就是一个正在运作中的程序，**衍生出来的其他进程在一般状态下，也会沿用这个进程的相关权限的（User/Group）**

**fork and exec**

在 Linux 的进程呼叫通常称为 fork-and-exec 的流程 (注 1)！进程都会藉由父进程以复制 (fork) 的方式产生一个一模一样的子进程， 然后被复制出来的子进程再以 exec 的方式来执行实际要进行的程序，最终就成为一个子进程的存在。

![image-20201004163547854](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20201004163547854.png)

**进程管理的作用**

* **判断服务器健康状态**
* **查看系统中所有进程**
* **杀死进程**

**查看系统中所有进程**

* **ps aux** **查看系统中所有进程，使用BSD操作系统格式。(Unix)**

**ps aux 输出信息**

> * **USER:该进程是由哪个用户产生的;**
> * **PID:进程的ID号;**
> * **%CPU:该进程占用CPU资源的百分比，占用越高，进程 越耗费资源;**
> * **%MEM:该进程占用物理内存的百分比，占用越高，进程 越耗费资源;**
> * **VSZ:该进程占用虚拟内存的大小，单位KB;**
> * **RSS:该进程占用实际物理内存的大小，单位KB;**
> * **TTY:该进程是在哪个终端中运行的。其中tty1-tty7代表 本地控制台终端，tty1-tty6是本地的字符界面终端，tty7是图形终端。pts/0-255代表虚拟终端。**
> * **STAT:进程状态。常见的状态有:R:运行、S:睡眠 、T:停止状态、s:包含子进程、+:位于后台**
> * **START:该进程的启动时间**
> * **TIME:该进程占用CPU的运算时间，注意不是系统时间**
> * **COMMAND:产生此进程的命令名**

* **ps -le** **查看系统中所有进程，使用Linux标准命令格式。**

>  F：代表这个进程旗标 (process flags)，说明这个进程的总结权限，常见号码有：
>
> ​ 若为 4 表示此进程的权限为 root ；
>
> ​ 若为 1 则表示此子进程仅进行复制(fork)而没有实际执行(exec)。
>
>  S：代表这个进程的状态 (STAT)，主要的状态有：
>
> ​ R (Running)：该程序正在运作中；
>
> ​ S (Sleep)：该程序目前正在睡眠状态(idle)，但可以被唤醒(signal)。
>
> ​ D ：不可被唤醒的睡眠状态，通常这支程序可能在等待 I/O 的情况(ex>打印)
>
> ​ T ：停止状态(stop)，可能是在工作控制(背景暂停)或除错 (traced) 状态；
>
> ​ Z (Zombie)：僵尸状态，进程已经终止但却无法被移除至内存外。
>
>  UID/PID/PPID：代表『此进程被该 UID 所拥有/进程的 PID 号码/此进程的父进程 PID 号码』
>
>  C：代表 CPU 使用率，单位为百分比；
>
>  PRI/NI：Priority/Nice 的缩写，代表此进程被 CPU 所执行的优先级，数值越小代表该进程越快被 CPU 执行。
>
> 详细的 PRI 与 NI 将在下一小节说明。
>
>  ADDR/SZ/WCHAN：都与内存有关，ADDR 是 kernel function，指出该进程在内存的哪个部分，如果是个running 的进程，一般就会显示『 - 』 / SZ 代表此进程用掉多少内存 / WCHAN 表示目前进程是否运作中，同样的， 若为 - 表示正在运作中。
>
>  TTY：登入者的终端机位置，若为远程登录则使用动态终端接口 (pts/n)；
>
>  TIME：使用掉的 CPU 时间，注意，是此进程实际花费 CPU 运作的时间，而不是系统时间；
>
>  CMD：就是 command 的缩写，造成此进程的触发程序之指令为何。

**僵尸进程**

**孤儿进程：一个父进程退出，而它的一个或多个子进程还在运行，那么那些子进程将成为孤儿进程。孤儿进程将被init进程(进程号为1)所收养，并由init进程对它们完成状态收集工作。**

**僵尸进程：一个进程使用fork创建子进程，如果子进程退出，而父进程并没有调用wait或waitpid获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中。这种进程称之为僵尸进程。**

> unix提供了一种机制\*\*可以保证只要父进程想知道子进程结束时的状态信息， 就可以得到。\*\*这种机制就是: 在每个进程退出的时候,内核释放该进程所有的资源,包括打开的文件,占用的内存等。 但是仍然为其保留一定的信息(包括进程号the process ID,退出状态the termination status of the process,运行时间the amount of CPU time taken by the process等)。==直到父进程通过wait / waitpid来取时才释放。== 但这样就导致了问题，**如果进程不调用wait / waitpid的话，** **那么保留的那段信息就不会释放，其进程号就会一直被占用，但是系统所能使用的进程号是有限的，如果大量的产生僵死进程，将因为没有可用的进程号而导致系统不能产生新的进程. 此即为僵尸进程的危害，应当避免。**

如果你发现在某个进程的 CMD 后面还接上 时，就代表该进程是僵尸进程啦，例如：

```bash
apache 8683 0.0 0.9 83384 9992 ? Z 14:33 0:00 /usr/sbin/httpd <defunct>
```

当系统不稳定的时候就容易造成所谓的僵尸进程，可能是因为程序写的不好啦，或者是使用者的操作习惯不良等等所造成。

如果你发现系统中很多僵尸进程时，记得啊！要找出该进程的父进程，然后好好的做个追踪，好好的进行主机的环境优化啊！ 看看有什么地方需要改善的，不要只是直接将他 kill 掉而已呢！不然的话，万一他一直产生，那可就麻烦了！

事实上，通常僵尸进程都已经无法控管，而直接是交给 systemd 这支程序来负责了，偏偏 systemd 是系统第一支执行的程序， 他是所有程序的父程序！我们无法杀掉该程序的 (杀掉他，系统就死掉了！)，所以啰，如果产生僵尸进程， 而系统过一阵子还没有办法透过核心非经常性的特殊处理来将该进程删除时，那你只好透过 **reboot 的方式**来将该进程抹去了！

> 程序设计中处理僵尸进程的方式：
>
> wait/waitpid，两次fork，捕捉信号SIGCHLD
>
> 僵尸进程示例：
>
> pid\_t fork(void);
>
> （pid\_t 是一个宏定义，其实质是int 被定义在#include\<sys/types.h>中） 返回值： 若成功调用一次则返回两个值，子进程返回0，父进程返回子进程ID；否则，出错返回-1
>
> 调用fork之后，数据、堆、栈有两份，代码仍然为一份但是这个[代码段](https://baike.baidu.com/item/%E4%BB%A3%E7%A0%81%E6%AE%B5)成为两个进程的共享代码段都从fork函数中返回，箭头表示各自的执行处。当父子进程有一个想要修改数据或者堆栈时，两个进程真正分裂。
>
> ```
> #include<stdio.h>
>
> #include<stdlib.h>
>
> #include<sys/types.h>
>
> #include<sys/wait.h>
>
> int main()
>
> {
> int pid;
>
> pid = fork();
>
> if(pid == 0) //son process
>
> {
> sleep(2);
>
> printf("child process %d  exit\n",getpid());
>
> exit(0);
>
> }
>
> else if(pid > 0) //father process
>
> { 
>
> printf("hello child\n");
>
> while(1)
>
>  {
>    sleep(1);
>
>  }
>
> }
>
> return 0;
>
> }
> ```
>
> 办法1：两次fork()
>
> 虽然仍然需要wait，但是子进程会马上退出，父进程可以继续自己的任务，孙子进程因为子进程的退出变成了孤儿进程，由systemctl进程接管，会由systemctl回收。它产生的僵尸进程就变成了孤儿进 程，这些孤儿进程会被init/systemctl进程接管，init进程会wait()这些孤儿进程，释放它们占用的系统进程表中的资源，这样，这些已经僵尸的孤儿进程就能瞑目而去了。
>
> ```
> #include<stdio.h>
>
> #include<stdlib.h>
>
> #include<sys/types.h>
>
> #include<sys/wait.h>
>
> int main()
>
> {
> int pid;
>
> pid = fork();
>
> if(pid > 0)//father process
>
> {
> wait(0);//waiting for the end of son process
>
> int j = 1;
>
> while(j <= 12)
>
> {
> sleep(1);
>
> j ++;
>
> }
>
> }
>
> else if(pid == 0)//son process
>
> {
> int pid2;
>
> pid2 = fork();
>
> if(pid2 > 0)//son process
>
> {
> exit(0);
>
> }
>
> else if(pid2 == 0)//grandson process
>
> {
> int i = 1;
>
> while(i <= 4)
>
> {
>     sleep(1);
>
>     i ++;
>
> }
>
> exit(0);
>
> }
>
> }
>
> return 0;
>
> }
> ```
>
> 1.  signal()函数
>
>     通过signal（SIGCHLD,SIG\_IGN）通知内核该进程的对其子进程不关心，由内核进行回收。SIGCHLD信号是子进程退出时向父进程发送的.
>
>     ```
>     void（* signal（int sig，void（* func）（int）））（int）;
>     ```
>
>     sig:要捕捉的信号
>
>     SIGABRT （信号中止）异常终止，例如由...发起 退出 功能。 SIGFPE （信号浮点异常）错误的算术运算，例如零分频或导致溢出的运算（不一定是浮点运算）。 SIGILL （信号非法指令）无效的功能图像，例如非法指令。这通常是由于代码中的损坏或尝试执行数据。 SIGINT （信号中断）交互式注意信号。通常由应用程序用户生成。 SIGSEGV （信号分段违规）对存储的无效访问：当程序试图在已分配的内存之外读取或写入时。 SIGTERM （信号终止）发送到程序的终止请求。 SIGCHLD 进程Terminate或Stop的时候，SIGCHLD会发送给它的父进程。缺省情况下该Signal会被忽略
>
>     func:我们要对信号进行的处理方式 参数func指定程序可以处理信号的三种方式之一：
>
>     默认处理（SIG\_DFL）：信号由该特定信号的默认动作处理。 忽略信号（SIG\_IGN）：忽略信号，即使没有意义，代码执行仍将继续。 函数处理程序：定义一个特定的函数来处理信号。 如果是一个函数，它应该遵循以下原型（使用C链接）： void handler\_function (int parameter);
>
> ````
> 返回值：
> 返回类型与参数func的类型相同，如果请求成功，则该函数返回指向特定处理函数的指针
> 		#include<stdio.h>
> #include<stdlib.h>
> #include<sys/types.h>
> #include<sys/wait.h>
> main(){
>     signal(SIGCHLD,SIG_IGN);
>     int pid;
>     if((pid=fork())){
>         sleep(2);
>         printf("child process %d exit\n",getpid());
>         exit();
>     }
>     else{
>         printf("hello child \n");
>         while(1){
>  	           sleep(1);
>         }
>     }
> }
> ```
> ````
>
> 1. wait()
>
> 进程一旦调用了wait，就立即阻塞自己。由wait自动分析某个子进程是否退出，记录消息并彻底销毁。
>
> ```
> pid_t wait(int *status)
>  	参数status用来保存被收集进程退出时的一些状态，若是只想消灭僵尸进程，可以直接设置为NULL
> ```
>
> ```
> #include<stdio.h>
>
> #include<stdlib.h>
>
> #include<sys/types.h>
>
> #include<sys/wait.h>
>
> int main()
>
> {
>     int pid;
>
>     pid = fork();
>
>     if(pid > 0)//father process
>
>      {
>       wait(0);//waiting for the end of son process
>
>       int i = 1;
>
>       while(i <= 10)
>
>       {
>         sleep(1);
>
>         i ++;
>
>       }
>
>      }
>
>     else if(pid == 0)//son process
>
>      {
>       int j = 1;
>
>       while(j <= 5)
>
>       {
>         sleep(1);
>
>         j ++;
>
>       }
>
>      exit(0);
>
>      }
>
>     return 0;
>
> }
> ```
>
> waitpid():
>
> wait函数其实是waitpid()的一个特例，waitpid可以选择调用者是否阻塞，可以控制它等待的特定进程。
>
> ```
> #include<stdlib.h>
>
> #include<sys/types.h>
>
> #include<sys/wait.h>
>
> int main()
>
> {
>     int pid;
>
>     pid = fork();
>
>     if(pid > 0)//father process
>
>      {
>       waitpid(-1, NULL, 0);//waiting for the end of son process
>
>       int i = 1;
>
>       while(i <= 10)
>
>       {
>         sleep(1);
>
>         i ++;
>
>       }
>
>      }
>
>     else if(pid == 0)//son process
>
>      {
>       int j = 1;
>
>       while(j <= 5)
>
>       {
>         sleep(1);
>
>         j ++;
>
>       }
>
>      exit(0);
>
>      }
>
>     return 0;
>
> }
> ```

**查看系统健康状态**

Linux中常用的监控CPU整体性能的工具有：

§ mpstat： mpstat 不但能查看所有CPU的平均信息，还能查看指定CPU的信息。

§ vmstat：只能查看所有CPU的平均信息；查看cpu队列信息；

§ iostat: 只能查看所有CPU的平均信息。

§ sar： 与mpstat 一样，不但能查看CPU的平均信息，还能查看指定CPU的信息。

§ top：显示的信息同ps接近，但是top可以了解到CPU消耗，可以根据用户指定的时间来更新显示。

**top \[选项]**

* **-d 秒数: 指定top命令每隔几秒更新。默认是3秒 在top命令的交互模式当中可以执行的命令**
* **?或h: 显示交互模式的帮助**
* **P: 以CPU使用率排序，默认就是此项**
* **M: 以内存的使用率排序**
* **N: 以PID排序**
* **q: 退出top**

**第一行信息为任务队列信息**

| 内容                             | 说明                                                    |
| ------------------------------ | ----------------------------------------------------- |
| 12:26:46                       | 系统当前时间                                                |
| up 1 day, 13:32                | 系统的运行时间，本机已经运行1天 13小时32分钟                             |
| 2 users                        | 当前登录了两个用户                                             |
| load average: 0.00, 0.00, 0.00 | 系统在之前1分钟，5分钟，15分钟 的平均负载。一般认为小于1时，负载较小。如果大于1，系统已经超出负荷。 |

**第二行为进程信息**

| 内容              | 说明                    |
| --------------- | --------------------- |
| Tasks: 95 total | 系统中的进程总数              |
| 1 running       | 正在运行的进程数              |
| 94 sleeping     | 睡眠的进程                 |
| 0 stopped       | 正在停止的进程               |
| 0 zombie        | 僵尸进程。如果不是0，需要手工检查僵尸进程 |

**第三行为CPU信息**

| 内容             | 说明                                                 |
| -------------- | -------------------------------------------------- |
| Cpu(s): 0.1%us | 用户模式占用的CPU百分比                                      |
| 0.1%sy         | 系统模式占用的CPU百分比                                      |
| 0.0%ni         | 改变过优先级的用户进程占用的CPU百分比                               |
| 99.7%id        | 空闲CPU的CPU百分比                                       |
| 0.1%wa         | 等待输入/输出的进程的占用CPU百分比                                |
| 0.0%hi         | 硬中断请求服务占用的CPU百分比                                   |
| 0.1%si         | 软中断请求服务占用的CPU百分比                                   |
| 0.0%st         | st(Steal time)虚拟时间百分比。就是当有虚拟机时，虚拟CPU等待实际CPU的时间百分比。 |

**第四行为物理内存信息**

| 内容                 | 说明                                                |
| ------------------ | ------------------------------------------------- |
| Mem: 625344k total | 物理内存的总量，单位KB                                      |
| 571504k used       | 已经使用的物理内存数量                                       |
| 53840k free        | 空闲的物理内存数量，我们使用的是虚拟机，总共只分配了628MB内存，所以 只有53MB的空闲内存了 |
| 65800k buffers     | 作为缓冲的内存数量                                         |

**第五行为交换分区(swap)信息**

| 内容                  | 说明             |
| ------------------- | -------------- |
| Swap: 524280k total | 交换分区(虚拟内存)的总大小 |
| 0k used             | 已经使用的交互分区的大小   |
| 524280k free        | 空闲交换分区的大小      |
| 409280k cached      | 作为缓存的交互分区的大小   |

**查看进程树** **pstree \[选项]**

* **-p: 显示进程的PID**
* **-u: 显示进程的所属用户**

**mpstat**

mpstat 是Multiprocessor Statistics的缩写，是实时系统监控工具。其报告与CPU的一些统计信息，这些信息存放在/proc/stat文件中。在多CPUs系统里，其不但能查看所有CPU的平均状况信息，而且能够查看特定CPU的信息。下面只介绍 mpstat与CPU相关的参数，mpstat的语法如下：

```bash
mpstat [-P {|ALL}] [internal [count]]
```

参数的含义如下：

参数 解释

\-P {|ALL} 表示监控哪个CPU， cpu在\[0,cpu个数-1]中取值

internal 相邻的两次采样的间隔时间

count 采样的次数，count只能和delay一起使用

当没有参数时，mpstat则显示系统启动以后所有信息的平均值。有interval时，第一行的信息自系统启动以来的平均信息。从第二行开始，输出为前一个interval时间段的平均信息。

与CPU有关的输出的含义如下：

参数 解释 从/proc/stat获得数据

```bash
#mpstat -P ALL 2 10
Linux 2.6.18-53.el5PAE (localhost.localdomain)  03/28/2009
 
10:07:57 PM  CPU   %user   %nice    %sys %iowait    %irq   %soft  %steal   %idle    intr/s
10:07:59 PM  all   20.75    0.00   10.50    1.50    0.25    0.25    0.00   66.75   1294.50
10:07:59 PM    0   16.00    0.00    9.00    1.50    0.00    0.00    0.00   73.50   1000.50
10:07:59 PM    1   25.76    0.00   12.12    1.52    0.00    0.51    0.00   60.10    294.00

CPU 处理器ID

user 在internal时间段里，用户态的CPU时间（%） ，不包含 nice值为负 进程 dusr/dtotal*100

nice 在internal时间段里，nice值为负进程的CPU时间（%） dnice/dtotal*100

system 在internal时间段里，核心时间（%） dsystem/dtotal*100

iowait 在internal时间段里，硬盘IO等待时间（%） diowait/dtotal*100

irq 在internal时间段里，软中断时间（%） dirq/dtotal*100

soft 在internal时间段里，软中断时间（%） dsoftirq/dtotal*100

idle 在internal时间段里，CPU除去等待磁盘IO操作外的因为任何原因而空闲的时间闲置时间 （%） didle/dtotal*100

intr/s 在internal时间段里，每秒CPU接收的中断的次数 dintr/dtotal*100

CPU总的工作时间=total_cur=user+system+nice+idle+iowait+irq+softirq

total_pre=pre_user+ pre_system+ pre_nice+ pre_idle+ pre_iowait+ pre_irq+ pre_softirq

duser=user_cur – user_pre

dtotal=total_cur-total_pre
```

**vmstat命令监控系统资源** vmstat \[刷新延时 刷新次数] 例子：

```bash
# vmstat 1 3
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 154364 197028 1324988    0    0     0     4    0    1  0  0 99  0  0
 0  0      0 154364 197028 1324988    0    0     0    44  603  875  0  0 100  0  0
 0  0      0 154592 197028 1324988    0    0     0     0  612  840  0  0 100  0  0
PROC(ESSES)
--r:如果在processes中运行的序列(process r)是连续的大于在系统中的CPU的个数表示系统现在运行比较慢,有多数的进程等待CPU.
如果r的输出数大于系统中可用CPU个数的4倍的话,则系统面临着CPU短缺的问题,或者是CPU的速率过低,系统中有多数的进程在等待CPU,造成系统中进程运行过慢.
SYSTEM
--in:每秒产生的中断次数
--cs:每秒产生的上下文切换次数
上面2个值越大，会看到由内核消耗的CPU时间会越大
 
CPU
-us:用户进程消耗的CPU时间百分
us的值比较高时，说明用户进程消耗的CPU时间多，但是如果长期超50%的使用，那么我们就该考虑优化程序算法或者进行加速（比如PHP/PERL）
-sy:内核进程消耗的CPU时间百分比（sy的值高时，说明系统内核消耗的CPU资源多，这并不是良性表现，我们应该检查原因）
-wa:IO等待消耗的CPU时间百分比
wa的值高时，说明IO等待比较严重，这可能由于磁盘大量作随机访问造成，也有可能磁盘出现瓶颈（块操作）。
-id:CPU处于空闲状态时间百分比,如果空闲时间(cpu id)持续为0并且系统时间(cpu sy)是用户时间的两倍(cpu us) 系统则面临着CPU资源的短缺.

 解决办法:
当发生以上问题的时候请先调整应用程序对CPU的占用情况.使得应用程序能够更有效的使用CPU.同时可以考虑增加更多的CPU.  关于CPU的使用情况还可以结合mpstat,  ps aux top  prstat –a等等一些相应的命令来综合考虑关于具体的CPU的使用情况,和那些进程在占用大量的CPU时间.一般情况下，应用程序的问题会比较大一些.比如一些SQL语句不合理等等都会造成这样的现象.
```

**iostat**

```bash
#iostat -c 2 10
Linux 2.6.18-53.el5PAE (localhost.localdomain)  03/28/2009
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
                    30.10    0.00          4.89         5.63    0.00   59.38
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
                    8.46       0.00          1.74         0.25    0.00   89.55
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
                    22.06     0.00          11.28       1.25    0.00   65.41
```

**sar**

```bash
sar [options] [-A] [-o file] t [n]
```

在命令行中，n 和t 两个参数组合起来定义采样间隔和次数，t为采样间隔，是必须有的参数，n为采样次数，是可选的，默认值是1，-o file表示将命令结果以二进制格式存放在文件中，file 在此处不是关键字，是文件名。options 为命令行选项，sar命令的选项很多，下面只列出常用选项：

\-A：所有报告的总和。 -u：CPU利用率 -v：进程、I节点、文件和锁表状态。 -d：硬盘使用报告。 -r：内存和交换空间的使用统计。 -g：串口I/O的情况。 -b：缓冲区使用情况。 -a：文件读写情况。 -c：系统调用情况。 -q：报告队列长度和系统平均负载 -R：进程的活动情况。 -y：终端设备活动情况。 -w：系统交换活动。 -x { pid | SELF | ALL }：报告指定进程ID的统计信息，SELF关键字是sar进程本身的统计，ALL关键字是所有系统进程的统计。

用sar进行CPU利用率的分析

```bash
#sar -u 2 10
Linux 2.6.18-53.el5PAE (localhost.localdomain) 03/28/2009
07:40:17 PM    CPU   %user   %nice  %system  %iowait  %steal   %idle
07:40:19 PM    all     12.44   0.00     6.97     1.74     0.00    78.86
07:40:21 PM    all     26.75   0.00    12.50     16.00    0.00    44.75
07:40:23 PM    all     16.96   0.00     7.98     0.00     0.00    75.06
07:40:25 PM    all     22.50   0.00     7.00     3.25     0.00    67.25
07:40:27 PM    all     7.25    0.00     2.75     2.50     0.00    87.50
07:40:29 PM    all     20.05   0.00     8.56     2.93     0.00    68.46
07:40:31 PM    all     13.97   0.00     6.23     3.49     0.00    76.31
07:40:33 PM    all     8.25    0.00     0.75     3.50     0.00    87.50
07:40:35 PM    all     13.25   0.00     5.75     4.00     0.00    77.00
07:40:37 PM    all     10.03   0.00     0.50     2.51     0.00    86.97
Average:       all     15.15   0.00     5.91     3.99     0.00    74.95

在显示内容包括：

　%user：CPU处在用户模式下的时间百分比。
    %nice：CPU处在带NICE值的用户模式下的时间百分比。
　%system：CPU处在系统模式下的时间百分比。
　%iowait：CPU等待输入输出完成时间的百分比。
    %steal：管理程序维护另一个虚拟处理器时，虚拟CPU的无意识等待时间百分比。
　%idle：CPU空闲时间百分比。
    在所有的显示中，我们应主要注意%iowait和%idle，%iowait的值过高，表示硬盘存在I/O瓶颈，%idle值高，表示CPU较空闲，如果%idle值高但系统响应慢时，有可能是CPU等待分配内存，此时应加大内存容量。%idle值如果持续低于10，那么系统的CPU处理能力相对较低，表明系统中最需要解决的资源是CPU。
```

用sar进行运行进程队列长度分析：

```bash
#sar -q 2 10
Linux 2.6.18-53.el5PAE (localhost.localdomain) 03/28/2009
07:58:14 PM  runq-sz plist-sz  ldavg-1  ldavg-5 ldavg-15
07:58:16 PM     0     493     0.64    0.56    0.49
07:58:18 PM     1     491     0.64    0.56    0.49
07:58:20 PM     1     488     0.59    0.55    0.49
07:58:22 PM     0     487     0.59    0.55    0.49
07:58:24 PM     0     485     0.59    0.55    0.49
07:58:26 PM     1     483     0.78    0.59    0.50
07:58:28 PM     0     481     0.78    0.59    0.50
07:58:30 PM     1     480     0.72    0.58    0.50
07:58:32 PM     0     477     0.72    0.58    0.50
07:58:34 PM     0     474     0.72    0.58    0.50
Average:        0     484     0.68    0.57    0.49

runq-sz 准备运行的进程运行队列。
plist-sz 进程队列里的进程和线程的数量
ldavg-1 前一分钟的系统平均负载(load average)
ldavg-5 前五分钟的系统平均负载(load average)
ldavg-15 前15分钟的系统平均负载(load average)

顺便说一下load avarage的含义
load average可以理解为每秒钟CPU等待运行的进程个数.
在Linux系统中，sar -q、uptime、w、top等命令都会有系统平均负载load average的输出，那么什么是系统平均负载呢？
　　系统平均负载被定义为在特定时间间隔内运行队列中的平均任务数。如果一个进程满足以下条件则其就会位于运行队列中：

  - 它没有在等待I/O操作的结果
  - 它没有主动进入等待状态(也就是没有调用'wait')
  - 没有被停止(例如：等待终止)
    例如：
    \# uptime
    　　20:55:40 up 24 days, 3:06, 1 user, load average: 8.13, 5.90, 4.94
    　　命令输出的最后内容表示在过去的1、5、15分钟内运行队列中的平均进程数量。
    　　一般来说只要每个CPU的当前活动进程数不大于3那么系统的性能就是良好的，如果每个CPU的任务数大于5，那么就表示这台机器的性能有严重问题。对 于上面的例子来说，假设系统有两个CPU，那么其每个CPU的当前任务数为：8.13/2=4.065。这表示该系统的性能是可以接受的。
```

**dmesg开机时内核检测信息** dmesg 例子：

* dmesg | grep CPU

**free命令查看内存使用状态** free \[-b|-k|-m|-g]

* \-b: 以字节为单位显示
* \-k: 以KB为单位显示，默认就是以KB为单位显示
* \-m: 以MB为单位显示
* \-g: 以GB为单位显示
* 缓存和缓冲的区别 简单来说缓存(cache)是用来加速数据 从硬盘中“读取”的，而缓冲(buffer) 是用来加速数据“写入”硬盘的。
* 查看CPU信息 cat /proc/cpuinfo

**uptime命令** uptime 显示系统的启动时间和平均负载，也就是top命令的第一行。w命令也可以看到这个数据。

**查看系统与内核相关信息unmae** uname \[选项]

* \-a: 查看系统所有相关信息;
* \-r: 查看内核版本;
* \-s: 查看内核名称。
* 判断当前系统的位数 没有直接的命令可以查看 只能通过查看系统外部命令的文件类型，顺带写出位数 file /bin/ls
* 查询当前Linux系统的发行版本 lsb\_release -a

**列出进程打开或使用的文件信息lsof**

lsof \[选项] 列出进程调用或打开的文件的信息

* \-c 字符串: 只列出以字符串开头的进程打开的文件
* \-u 用户名: 只列出某个用户的进程打开的文件
* \-p pid: 列出某个PID进程打开的文件

**进程管理**

**kill 命令** **kill \[信号代号] ==进程号==** **kill后加PID号** **kill –l** **查看可用的进程信号**

**常用进程信号表**

| 信号代号 | 信号名称    | 说明                                                                         |
| ---- | ------- | -------------------------------------------------------------------------- |
| 1    | SIGHUP  | 该信号让进程立即关闭，然后重新读取配置文件之后重启。                                                 |
| 2    | SIGINT  | 程序终止信号，用于终止前台进程。相当于输出ctrl+c快捷 键。                                           |
| 8    | SIGFPE  | 在发生致命的算术运算错误时发出. 不仅包括浮点运算错误, 还包括溢出及除数为0等其它所有的算术的错误。                        |
| 9    | SIGKILL | 用来立即结束程序的运行. 本信号不能被阻塞、处理和忽略。 一般用于强制终止进程。                                   |
| 14   | SIGALRM | 时钟定时信号, 计算的是实际的时间或时钟时间. alarm函数 使用该信号。                                     |
| 15   | SIGTERM | 正常结束进程的信号，kill命令的默认信号。有时如果进程已 经发生问题，这个信号是无法正常终止进程的，我们才会尝试SIGKILL信号，也就是信号9。 |
| 18   | SIGCONT | 该信号可以让暂停的进程恢复执行，本信号不能被阻断。                                                  |
| 19   | SIGSTOP | 该信号可以暂停前台进程，相当于输入ctrl+z快捷键。本信号 不能被阻断。                                      |

例子：

* kill -1 22354 重启进程号为22354的进程
* kill -9 22368 强制杀死进程号为22368的进程

**killall 命令** **killall \[选项]\[信号代号] ==进程名==** **按照进程名杀死进程**

* **-i: 交互式，询问是否要杀死某个进程**
* **-I: 忽略进程名的大小写** **和kill不一样，他加进程名**

**pkill 命令** **pkill \[选项] \[信号] ==进程名==** **按照进程名终止进程**

*   \-t 终端号: 按照终端号踢出用户

    按照终端号踢出用户

**w** **使用w命令查询本机已经登录的用户**

* **pkill -t -9 pts/1** **强制杀死从pts/1虚拟终端登录的进程**

**工作管理**

**把进程放入后台**

* \==命令后面加&（会在后台运行）==
  * **例子：** \*\*tar -zcf etc.tar.gz /etc & \*\*==但是像top，vim和用户交互的命令放在后台自动停止，不再运行==
* \==运行界面按按ctrl+z（放入后台并终止）==
  * **例子：** **top** **在top命令执行的过程中，按ctrl+z快捷键放入后台（后台暂停）**

**查看后台的工作** **jobs \[-l]**

* **-l 显示工作的PID** **注意：“+”号代表最近一个放入后台的工作，也是工作恢复时，默认恢复的工作。“-”号代表倒数第二个放入后台的工作**

**将后台暂停的工作恢复到==前台执行 ==**

* **fg (%工作号)/(命令名)** **%工作号：%号可以省略，但是注意工作号和PID到区别**

**把后台暂停的工作恢复到==后台执行==**

* **bg %工作号/(命令名)** **注意：后台恢复执行的命令，是不能和前台有交互的（像top，vim），否则仍会自己stoped**

**脱机管理问题**

1. at
2. nohup

> nohup 并不支持 bash 内建的指令，因此你的指令必须要是外部指令才行。

**系统定时任务**

**at**

at是个可以处理仅执行一次就结束的命令，不过要执行at时，必须要有atd这个服务的支持才行。

```bash
atd服务的开启
[root@study ~]# systemctl restart atd # 重新启动 atd 这个服务
[root@study ~]# systemctl enable atd # 让这个服务开机就自动启动
[root@study ~]# systemctl status atd # 查阅一下 atd 目前的状态
atd.service - Job spooling tools
 Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled) # 是否开机启动
 Active: active (running) since Thu 2015-07-30 19:21:21 CST; 23s ago # 是否正在运作中
Main PID: 26503 (atd)
 CGroup: /system.slice/atd.service
 └─26503 /usr/sbin/atd -f
Jul 30 19:21:21 study.centos.vbird systemd[1]: Starting Job spooling tools...
Jul 30 19:21:21 study.centos.vbird systemd[1]: Started Job spooling tools.
```

我们使用 at 这个指令来产生所要运作的工作，并将这个工作以文本文件的方式写入 /var/spool/at/ 目录内，该工作便能等待 atd 这个服务的取用与执行了。

> at 的权限设置
>
> 1. 先找寻 **/etc/at.allow** 这个文件，写在这个文件中的使用者才能使用 at ，没有在这个文件中的使用者则不能使用 at (即使没有写在 at.deny 当中)；
> 2. 如果 /etc/at.allow 不存在，就寻找 **/etc/at.deny** 这个文件，若写在这个 at.deny 的使用者则不能使用 at ，而没有在这个 at.deny 文件中的使用者，就可以使用 at 咯；
> 3. 如果两个文件都不存在，那么只有 root 可以使用 at 这个指令。

**实际运作单一工作排程**

```bash
[root@study ~]# at [-mldv] TIME
[root@study ~]# at -c 工作号码
选项与参数：
-m ：当 at 的工作完成后，即使没有输出讯息，亦以 email 通知使用者该工作已完成。
-l ：at -l 相当于 atq，列出目前系统上面的所有该用户的 at 排程；
-d ：at -d 相当于 atrm ，可以取消一个在 at 排程中的工作；
-v ：可以使用较明显的时间格式栏出 at 排程中的任务栏表；
-c ：可以列出后面接的该项工作的实际指令内容。
TIME：时间格式，这里可以定义出『什么时候要进行 at 这项工作』的时间，格式有：
 HH:MM ex> 04:00
在今日的 HH:MM 时刻进行，若该时刻已超过，则明天的 HH:MM 进行此工作。
 HH:MM YYYY-MM-DD ex> 04:00 2015-07-30
强制规定在某年某月的某一天的特殊时刻进行该工作！
 HH:MM[am|pm] [Month] [Date] ex> 04pm July 30
也是一样，强制在某年某月某日的某时刻进行！
 HH:MM[am|pm] + number [minutes|hours|days|weeks]
ex> now + 5 minutes ex> 04pm + 3 days
就是说，在某个时间点『再加几个时间后』才进行。
```

> 建议你最好使用绝对路径来下达你的指令,
>
> at 在运作时，会跑到==当时下达 at 指令的那个工作目录==
>
> at 的执行与终端机环境无关，而所有 standard output/standard error output 都会传送到执行者的 mailbox 去,因此若想在终端机显示，需要输出到终端机假如你在 tty1 登入，则可以使用『 echo "Hello" > /dev/tty1 』来取代
>
> 由于 at 工作排程的使用上，==系统会将该项 at 工作独立出你的 bash 环境中， 直接交给系统的 atd 程序来接管==，因此，当你下达了 at 的工作之后就可以立刻脱机了， 剩下的工作就完全交给 Linux 管理即可

**atq和atrm**

```bash
[root@study ~]# atq
[root@study ~]# atrm (jobnumber)
范例一：查询目前主机上面有多少的 at 工作排程？
[root@study ~]# atq
3 Tue Aug 4 23:00:00 2015 a root
# 上面说的是：『在 2015/08/04 的 23:00 有一项工作，该项工作指令下达者为
# root』而且，该项工作的工作号码 (jobnumber) 为 3 号喔！
范例二：将上述的第 3 个工作移除！
[root@study ~]# atrm 3
[root@study ~]# atq
# 没有任何信息，表示该工作被移除了
```

**batch**

系统有空时才进行背景任务,他**会在 CPU 的工作负载小于 0.8 的时候，才进行你所下达的工作任务**,其实 batch 是利用 at 来进行指令的下达啦！只是加入一些控制参数而已。

**crond服务管理与访问控制（Ubuntu 用cron）**

* **service crond restart 启动**
* **chkconfig crond on 自启动**

**用户的crontab设置** **crontab \[选项]**

* **-e: 编辑crontab定时任务**
* **-l: 查询crontab任务**
* **-r: 删除当前用户所有的crontab 任务 ，如果想删一个，-e进去之后删除** **crontab -e 标准格式** **进入crontab编辑界面。会打开vim编辑你的工作。**
*   默认格式 \* \* \* \* \* command

    **其中\*号代表**

| 项目      | 含义         | 范围             |
| ------- | ---------- | -------------- |
| 第一个“\*” | 一小时当中的第几分钟 | 0-59           |
| 第二个“\*” | 一天当中的第几小时  | 0-23           |
| 第三个“\*” | 一个月当中的第几天  | 1-31           |
| 第四个“\*” | 一年当中的第几月   | 1-12           |
| 第五个“\*” | 一周当中的星期几   | 0-7(0和7都代表星期日) |

**这个表可以配合特殊符号使用：**

| 特殊符号 | 含义                                                                  |
| ---- | ------------------------------------------------------------------- |
| \*   | 代表任何时间。比如第一个“\*”就代表一小时中 每分钟都执行一次的意思。                                |
| ，    | 代表不连续的时间。比如“0 8,12,16 \* \* \* 命令”， 就代表在每天的8点0分，12点0分，16点0分都执 行一次命令 |
| -    | 代表连续的时间范围。比如“0 5 \* \* 1-6命令”， 代表在周一到周六的凌晨5点0分执行命令                  |
| \*/n | 代表每隔多久执行一次。比如“\*/10 \* \* \* \* 命 令”，代表每隔10分钟就执行一遍命令                |

**例子：**

| 时间               | 含义                                                                         |
| ---------------- | -------------------------------------------------------------------------- |
| 45 22 \* \* \*   | 命令 在22点45分执行命令                                                             |
| 0 17 \* \* 1     | 命令 每周1 的17点0分执行命令                                                          |
| 0 5 1,15 \* \*   | 命令 每月1号和15号的凌晨5点0分执行命 令                                                    |
| 40 4 \* \* 1-5   | 命令 每周一到周五的凌晨4点40分执行命 令                                                     |
| \*/10 4 \* \* \* | 命令 每天的凌晨4点，每隔10分钟执行一 次命令                                                   |
| 0 0 1,15 \* 1    | 命令 每月1号和15号，每周1的0点0分都会 执行命令。==注意:星期几和几号最好 不要同时出现，因为他们定义的都是 天。非常容易让管理员混乱。== |

\==**注意：在crontab -e 编辑下 %有特殊含义，所以就应该加转义符**==

**测试端口**

**telnet**

telnet命令通常用来远程登录。telnet程序是基于TELNET协议的远程登录客户端程序。Telnet协议是TCP/IP协议族中的一员，是Internet远程登陆服务的标准协议和主要方式。

```bash
1．命令格式：

telnet[参数][主机]

2．命令功能：

执行telnet指令开启终端机阶段作业，并登入远端主机。

3．命令参数：

-8 允许使用8位字符资料，包括输入与输出。

-a 尝试自动登入远端系统。

-b<主机别名> 使用别名指定远端主机名称。

-c 不读取用户专属目录里的.telnetrc文件。

-d 启动排错模式。

-e<脱离字符> 设置脱离字符。

-E 滤除脱离字符。

-f 此参数的效果和指定"-F"参数相同。

-F 使用Kerberos V5认证时，加上此参数可把本地主机的认证数据上传到远端主机。

-k<域名> 使用Kerberos认证时，加上此参数让远端主机采用指定的领域名，而非该主机的域名。

-K 不自动登入远端主机。

-l<用户名称> 指定要登入远端主机的用户名称。

-L 允许输出8位字符资料。

-n<记录文件> 指定文件记录相关信息。

-r 使用类似rlogin指令的用户界面。

-S<服务类型> 设置telnet连线所需的IP TOS信息。

-x 假设主机有支持数据加密的功能，就使用它。

-X<认证形态> 关闭指定的认证形态。
```

> telnet为用户提供了在本地计算机上完成远程主机工作的能力，因此可以通过telnet来测试端口的连通性，具体用法格式：

```bash
telnet ip port
```

**ssh**

SSH(远程连接工具)连接原理：ssh服务是一个守护进程(demon)，系统后台监听客户端的连接，ssh服务端的进程名为sshd,负责实时监听客户端的请求(IP 22端口)，包括公共秘钥等交换等信息。

ssh服务端由2部分组成： openssh(提供ssh服务) openssl(提供加密的程序)

ssh的客户端可以用 XSHELL，Securecrt, Mobaxterm等工具进行连接

**SSH的工作机制**

> 服务器启动的时候自己产生一个密钥(768bit公钥)，本地的ssh客户端发送连接请求到ssh服务器，服务器检查连接点客户端发送的数据和IP地址，确认合法后发送密钥(768bits)给客户端，此时客户端将本地私钥(256bit)和服务器的公钥(768bit)结合成密钥对key(1024bit),发回给服务器端，建立连接通过key-pair数据传输。

**SSH知识小结**

> 1.SSH是安全的加密协议，用于远程连接Linux服务器\
> 2.SSH的默认端口是22，安全协议版本是SSH2\
> 3.SSH服务器端主要包含2个服务功能SSH连接和SFTP服务器\
> 4.SSH客户端包含ssh连接命令和远程拷贝scp命令等

**SSH功能大全**

```bash
1.登录 (测试端口：ssh -v -p port username@ip)                  
       ssh -p22 omd@192.168.25.137               
2.直接执行命令  -->最好全路径                   
       ssh root@192.168.25.137 ls -ltr /backup/data                       
           ==>ssh root@192.168.25.137 /bin/ls -ltr /backup/data               
3.查看已知主机                    
       cat /root/.ssh/known_hosts
4.ssh远程执行sudo命令
       ssh -t omd@192.168.25.137 sudo rsync hosts /etc/
 
5.scp               
       1.功能   -->远程文件的安全(加密)拷贝                   
           scp -P22 -r -p /home/omd/h.txt omd@192.168.25.137:/home/omd/               
       2.scp知识小结                   
           scp是加密远程拷贝，cp为本地拷贝                   
           可以推送过去，也可以拉过来                   
           每次都是全量拷贝(效率不高，适合第一次)，增量拷贝用rsync
6.ssh自带的sftp功能               
             1.Window和Linux的传输工具                   
                  wincp   filezip                   
               sftp  -->基于ssh的安全加密传输                   
               samba   
             2.sftp客户端连接                   
                sftp -oPort=22 root@192.168.25.137                   
                put /etc/hosts /tmp                   
                get /etc/hosts /home/omd   
            3.sftp小结：                   
                1.linux下使用命令： sftp -oPort=22 root@x.x.x.x                   
                2.put加客户端本地路径上传                  
                3.get下载服务器端内容到本地                   
                4.远程连接默认连接用户的家目录
```

**crul**

在Linux中curl是一个利用URL规则在命令行下工作的文件传输工具，可以说是一款很强大的http命令行工具。它支持文件的上传和下载，是综合传输工具，但按传统，习惯称url为下载工具。

https://www.cnblogs.com/duhuo/p/5695256.html

```bash
语法：# curl [option] [url]
-A/--user-agent <string>              设置用户代理发送给服务器
-b/--cookie <name=string/file>    cookie字符串或文件读取位置
-c/--cookie-jar <file>                    操作结束后把cookie写入到这个文件中
-C/--continue-at <offset>            断点续转
-D/--dump-header <file>              把header信息写入到该文件中
-e/--referer                                  来源网址
-f/--fail                                          连接失败时不显示http错误
-o/--output                                  把输出写到该文件中
-O/--remote-name                      把输出写到该文件中，保留远程文件的文件名
-r/--range <range>                      检索来自HTTP/1.1或FTP服务器字节范围
-s/--silent                                    静音模式。不输出任何东西
-T/--upload-file <file>                  上传文件
-u/--user <user[:password]>      设置服务器的用户和密码
-w/--write-out [format]                什么输出完成后
-x/--proxy <host[:port]>              在给定的端口上使用HTTP代理
-#/--progress-bar                        进度条显示当前的传送状态
```

**wget**

Linux系统中的wget是一个下载文件的工具，它用在命令行下。对于Linux用户是必不可少的工具，我们经常要下载一些软件或从远程服务器恢复备份到本地服务器。wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理。

所谓的自动下载是指，**wget可以在用户退出系统的之后在后台执行**。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成，相对于其它大部分浏览器在下载大量数据时需要用户一直的参与，这省去了极大的麻烦。

wget 可以跟踪HTML页面上的链接依次下载来创建远程服务器的本地版本，完全重建原始站点的目录结构。这又常被称作”递归下载”。在递归下载的时候，wget 遵循Robot Exclusion标准(/robots.txt). wget可以在下载的同时，将链接转换成指向本地文件，以方便离线浏览。

wget 非常稳定，它在带宽很窄的情况下和不稳定网络中有很强的适应性.如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。

```bash
1．命令格式：

wget [参数] [URL地址]

端口测试： wget [参数] [URL地址][:port]nb  

2．命令功能：

用于从网络上下载资源，没有指定目录，下载资源回默认为当前目录。wget虽然功能强大，但是使用起来还是比较简单：

1）支持断点下传功能；这一点，也是网络蚂蚁和FlashGet当年最大的卖点，现在，Wget也可以使用此功能，那些网络不是太好的用户可以放心了；

2）同时支持FTP和HTTP下载方式；尽管现在大部分软件可以使用HTTP方式下载，但是，有些时候，仍然需要使用FTP方式下载软件；

3）支持代理服务器；对安全强度很高的系统而言，一般不会将自己的系统直接暴露在互联网上，所以，支持代理是下载软件必须有的功能；

4）设置方便简单；可能，习惯图形界面的用户已经不是太习惯命令行了，但是，命令行在设置上其实有更多的优点，最少，鼠标可以少点很多次，也不要担心是否错点鼠标；

5）程序小，完全免费；程序小可以考虑不计，因为现在的硬盘实在太大了；完全免费就不得不考虑了，即使网络上有很多所谓的免费软件，但是，这些软件的广告却不是我们喜欢的。
```

**SELinux**

其实他是『 Security Enhanced Linux 』的缩写，字面上的意义就是安全强化的Linux

很多企业界发现， 通常系统出现问题的原因大部分都在于『内部员工的资源误用』所导致的，实际由外部发动的攻击反而没有这么严重。 其实 SELinux 是在进行进程、文件等细部权限设定依据的一个核心模块！ 由于启动网络服务的也是进程，因此刚好也能够控制网络服务能否存取系统资源的一道关卡！

**DAC**

传统的文件权限与账号关系：自主式访问控制,

当某个进程想要对文件进行存取时， 系统就会根据该进程的拥有者/群组，并比对文件的权限，若通过权限检查，就可以存取该文件了。基本上，

就是依据进程的拥有者与文件资源的 rwx 权限来决定有无存取的能力。

缺点：

> * root 具有最高的权限：如果不小心某支进程被有心人士取得， 且该进程属于 root 的权限，那么这支进程就可以在系统上进行任何资源的存取！真是要命！
> * 使用者可以取得进程来变更文件资源的访问权限：如果你不小心将某个目录的权限设定为 777 ，由于对任何人的权限会变成 rwx ，因此该目录就会被任何人所任意存取！

**MAC**

委任式访问控制 (Mandatory Access Control, MAC) 他可以针对特定的进程与特定的文件资源来进行权限的控管

![image-20201007212203524](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20201007212203524.png)

**SELinux 的运作模式**

> * **主体** **(Subject）**：
>
> SELinux 主要想要管理的就是进程，因此你可以将『主体』跟本章谈到的 process 划上等号；
>
> * **目标** **(Object)**：
>
> 主体进程能否存取的『目标资源』一般就是文件系统。因此这个目标项目可以等文件系统划上等号；
>
> * **政策** **(Policy)**：
>
> 由于进程与文件数量庞大，因此 SELinux 会依据某些服务来制订基本的存取安全性政策。这些政策内还会有详细的规则 (rule) 来指定不同的服务开放某些资源的存取与否。在目前的 CentOS 7.x 里面仅有提供三个主要的政策，分别是：
>
> o targeted：针对网络服务限制较多，针对本机限制较少，是预设的政策；
>
> o minimum：由 target 修订而来，仅针对选择的进程来保护！
>
> o mls：完整的 SELinux 限制，限制方面较为严格。

**安全上下文（Security context）**

主体与目标的安全性本文必须一致才能够顺利存取,主体安全上下文为进程上下文（ps -eZ），目标安全上下文一般问文件上下文（ll -Zd），两者通过策略的规则相关联。

> 安全上下文是放置到文件的 inode 内的，因此主进程想要读取目标文件资源时，同样需要读取 inode ， 这 inode 内就可以比对安全性本文以及 rwx等权限值是否正确，而给予适当的读取权限依据。

![image-20201007212612631](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20201007212612631.png)

上图的重点在『主体』如何取得『目标』的资源访问权限！ 由上图我们可以发现

(1)主体进程必须要通过 SELinux 策略内的规则放行后，就可以与目标资源进行安全性本文的比对(seseacrch 命令输出是否有相应的allow )

(2)若比对失败则无法存取目标，若比对成功则可以开始存取目标。（即进程的Type与文件/目录的Type依据策略的rule看是否匹配）

(3)最终能否存取目标还是与文件系统的 rwx 权限设定有关！（文件本身对用户权限的判定）

**查看文件上下文**

```bash
[root@study ~]# ls -Z
-rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
-rw-r--r--. root root system_u:object_r:admin_home_t:s0 initial-setup-ks.cfg
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 regular_express.txt
#上面的三个文件中，系统安装主动产生的 anaconda-ks.cfs 及 initial-setup-ks.cfg 就会是 system_u，而我们自己从网络上面抓下来的 regular_express.txt 就会是 unconfined_u 这个识别啊！
```

安全性文本主要用冒号分为三个字段（文件和进程都是）

```bash
Identify:role:type
身份识别:角色:类型
```

> * **身份识别(Identify)**：
>
> 相当于账号方面的身份识别！主要的身份识别常见有底下几种常见的类型：
>
> * unconfined\_u：不受限的用户，也就是说，该文件来自于不受限的进程所产生的！如bash
> * system\_u：系统用户，大部分就是系统自己产生的文件！
>
> ```
> 基本上，如果是系统或软件本身所提供的文件，大多就是 system_u 这个身份名称，而如果是我们用户透过 bash 自己建立的文件，大多则是不受限的 unconfined_u 身份
> ```
>
> ```
> 如果是网络服务所产生的文件，或者是系统服务运作过程产生的文件，则大部分的识别就会是 system_u 啰！
> ```
>
> *   **角色 (Role)：**
>
>     透过角色字段，我们可以知道这个资料是属于进程、文件资源还是代表使用者。一般的角色有：
>
>     * object\_r：代表的是文件或目录等文件资源，这应该是最常见的啰；
>     *   system\_r：代表的就是进程啦！不过，一般使用者也会被指定成为 system\_r 喔！
>
>         你也会发现角色的字段最后面使用『 \_r 』来结尾！因为是 role 的意思嘛！
> *   **类型(Type) (最重要！)：**
>
>     在预设的 targeted 政策中， Identify 与 Role 字段基本上是不重要的！重要的在于这个类型(type) 字段！ 基本上，一个主体进程能不能读取到这个文件资源，与类型字段有关！而类型字段在文件与进程的定义不太相同，分别是：
>
>     * type：在文件资源 (Object) 上面称为类型 (Type)；
>     * domain：在主体进程 (Subject) 则称为领域 (domain) 了！
>
>     domain 需要与 type 搭配，则该进程才能够顺利的读取文件资源啦！
>
> ![image-20201007234912930](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20201007234912930.png)

\==进程的Type（Domain）与文件/目录的Type（type）的关系（即进程的安全上下文与文件/目录的安全上下文的关系）==

```bash
# 1. 先看看 crond 这个『进程』的安全本文内容：
[root@study ~]# ps -eZ | grep cron
system_u:system_r:crond_t:s0-s0:c0.c1023 1338 ? 00:00:01 crond
system_u:system_r:crond_t:s0-s0:c0.c1023 1340 ? 00:00:00 atd
# 这个安全本文的类型名称为 crond_t 格式！
# 2. 再来瞧瞧执行档、配置文件等等的安全本文内容为何！
[root@study ~]# ll -Zd /usr/sbin/crond /etc/crontab /etc/cron.d
drwxr-xr-x. root root system_u:object_r:system_cron_spool_t:s0 /etc/cron.d
-rw-r--r--. root root system_u:object_r:system_cron_spool_t:s0 /etc/crontab
-rwxr-xr-x. root root system_u:object_r:crond_exec_t:s0 /usr/sbin/crond
```

![image-20201007235215427](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20201007235215427.png)

上图的意义我们可以这样看的：

> 1. 首先，我们触发一个可执行的目标文件，那就是具有 crond\_exec\_t 这个类型的 /usr/sbin/crond 文件；
> 2. 依据某个策略，该文件的类型会让这个文件所造成的主体进程 (Subject) 具有 crond 这个领域 (domain)， 我们的策略针对这个领域已经制定了许多规则，其中包括这个领域可以读取的目标资源类型；
> 3. 由于 crond domain 被设定为可以读取 system\_cron\_spool\_t 这个类型的目标文件 (Object)， 因此默认 /etc/cron.d/ 目录下的文件都符合目标资源类型，就能够被 crond 那支进程所读取了，但要是自己在别的目录创建的移动过来的，则可能不符合；
> 4. 但最终能不能读到正确的数据，还得要看 rwx 是否符合 Linux 权限的规范！

**SELinux 三种模式的启动、关闭与观察**

目前 SELinux 依据启动与否，共有三种模式，分别如下：

> enforcing：强制模式，代表 SELinux 运作中，且已经正确的开始限制 domain/type 了；
>
> permissive：宽容模式：代表 SELinux 运作中，不过仅会有警告讯息并不会实际限制 domain/type 的存取。这种模式可以运来作为 SELinux 的 debug 之用；
>
> disabled：关闭，SELinux 并没有实际运作。
>
> ![image-20201008000009769](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%5CLinux.assets%5Cimage-20201008000009769.png)

首先，你得要知道，并不是所有的进程都会被 SELinux 所管制，因此最左边会出现一个所谓的『有受限的进程主体』！

**而其是根据 /etc/selinux/config中的SELINUXTYPE 来选择策略的，默认的为targeted。**

此时第三个字段为unconfined\_t的为不受限类型，而crond等受限服务的为crond\_t等

**getenforce**

```bash
[root@study ~]# getenforce
Enforcing <==诺！就显示出目前的模式为 Enforcing 啰！
```

**sestatus**

```bash
[root@study ~]# sestatus [-vb]
选项与参数： 
-v ：检查列于 /etc/sestatus.conf 内的文件与进程的安全性本文内容；
-b ：将目前政策的规则布尔值列出，亦即某些规则 (rule) 是否要启动 (0/1) 之意；
范例一：列出目前的 SELinux 使用哪个政策 (Policy)？
[root@study ~]# sestatus
SELinux status: enabled <==是否启动 SELinux
SELinuxfs mount: /sys/fs/selinux <==SELinux 的相关文件数据挂载点
SELinux root directory: /etc/selinux <==SELinux 的根目录所在
Loaded policy name: targeted <==目前的政策为何？
Current mode: enforcing <==目前的模式
Mode from config file: enforcing <==目前配置文件内规范的 SELinux 模式
Policy MLS status: enabled <==是否含有 MLS 的模式机制
Policy deny_unknown status: allowed <==是否预设抵挡未知的主体进程
Max kernel policy version: 28
```

**setenforce**

```bash
[root@study ~]# setenforce [0|1]
选项与参数：
0 ：转成 permissive 宽容模式；
1 ：转成 Enforcing 强制模式
范例一：将 SELinux 在 Enforcing 与 permissive 之间切换与观察
[root@study ~]# setenforce 0
[root@study ~]# getenforce
Permissive
[root@study ~]# setenforce 1
[root@study ~]# getenforce
Enforcing
#  setenforce 无法在 Disabled 的模式底下进行模式的切换喔！
```

**getsebool**

如果想要查询系统上面全部规则的启动与否 (on/off，亦即布尔值)，很简单的透过 sestatus -b 或getsebool -a 均可！

```bash
[root@study ~]# getsebool [-a] [规则的名称]
选项与参数： -a ：列出目前系统上面的所有 SELinux 规则的布尔值为开启或关闭值
范例一：查询本系统内所有的布尔值设定状况
[root@study ~]# getsebool -a
abrt_anon_write --> off
abrt_handle_event --> off
....(中间省略)....
cron_can_relabel --> off # 这个跟 cornd 比较有关！
cron_userdomain_transition --> on
....(中间省略)....
httpd_enable_homedirs --> off # 这当然就是跟网页，亦即 http 有关的啰！
....(底下省略)....
# 这么多的 SELinux 规则喔！每个规则后面都列出现在是允许放行还是不许放行的布尔值喔！
```

**seinfo**

```bash
[root@study ~]# seinfo [-Atrub]
选项与参数：
-A ：列出 SELinux 的状态、规则布尔值、身份识别、角色、类别等所有信息
-u ：列出 SELinux 的所有身份识别 (user) 种类 
-r ：列出 SELinux 的所有角色 (role) 种类 
-t ：列出 SELinux 的所有类别 (type) 种类
-b ：列出所有规则的种类 (布尔值)
范例一：列出 SELinux 在此政策下的统计状态
[root@study ~]# seinfo
Statistics for policy file: /sys/fs/selinux/policy
Policy Version & Type: v.28 (binary, mls)
 Classes: 83 Permissions: 255
 Sensitivities: 1 Categories: 1024
 Types: 4620 Attributes: 357
 Users: 8 Roles: 14
 Booleans: 295 Cond. Expr.: 346
 Allow: 102249 Neverallow: 0
 Auditallow: 160 Dontaudit: 8413
 Type_trans: 16863 Type_change: 74
 Type_member: 35 Role allow: 30
 Role_trans: 412 Range_trans: 5439
....(底下省略)....
# 从上面我们可以看到这个政策是 targeted ，此政策的安全本文类别有 4620 个；
# 而各种 SELinux 的规则 (Booleans) 共制订了 295 条！
```

**sesearch**

查看相应规则/进程/文件在当前策略下有哪些对应关系

```bash
[root@study ~]# sesearch [-A] [-s 主体类别] [-t 目标类别] [-b 布尔值]
选项与参数： 
-A ：列出后面数据中，允许『读取或放行』的相关数据 
-s : 后面还要接类别，例如 -s crond_t 主体进程能够读取的文件
-t ：后面还要接类别，例如 -t httpd_t 能供读取改目标类别的进程
-b ：后面还要接 SELinux 的规则，例如  -b httpd_enable_ftp_server   规则中，主体进程能够读取的文件SELinux类型
范例一：找出 crond_t 这个主体进程能够读取的文件 SELinux type
[root@study ~]# sesearch -A -s crond_t | grep spool
 allow crond_t system_cron_spool_t : file { ioctl read write create getattr ..
 allow crond_t system_cron_spool_t : dir { ioctl read getattr lock search op..
 allow crond_t user_cron_spool_t : file { ioctl read write create getattr se..
 allow crond_t user_cron_spool_t : dir { ioctl read write getattr lock add_n..
 allow crond_t user_cron_spool_t : lnk_file { read getattr } ;
# allow 后面接主体进程以及文件的 SELinux type，上面的数据是撷取出来的，
# 意思是说，crond_t 可以读取 system_cron_spool_t 的文件/目录类型～等等！
范例二：找出 crond_t 是否能够读取 /etc/cron.d/checktime 这个我们自定义的配置文件？
[root@study ~]# ll -Z /etc/cron.d/checktime
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 /etc/cron.d/checktime
# 两个重点，一个是 SELinux type 为 admin_home_t，一个是文件 (file)
[root@study ~]# sesearch -A -s crond_t | grep admin_home_t
 allow domain admin_home_t : dir { getattr search open } ;
 allow domain admin_home_t : lnk_file { read getattr } ;
 allow crond_t admin_home_t : dir { ioctl read getattr lock search open } ;
 allow crond_t admin_home_t : lnk_file { read getattr } ;
# 仔细看！看仔细～虽然有 crond_t admin_home_t 存在，但是这是总体的信息，
# 并没有针对某些规则的寻找～所以还是不确定 checktime 能否被读取。但是，基本上就是 SELinux
# type 出问题～因此才会无法读取的！
```

**semanage**

查看规则的状态/**默认目录的安全性本文查询与修改**

```bash
[root@study ~]# semanage boolean -l | grep httpd_enable_homedirs
SELinux boolean State Default Description
httpd_enable_homedirs (off , off) Allow httpd to enable homedirs
# httpd_enable_homedirs 的功能是允许 httpd 进程去读取用户家目录的意思～
[root@study ~]# sesearch -A -b httpd_enable_homedirs
范例三：列出 httpd_enable_homedirs 这个规则当中，主体进程能够读取的文件 SELinux type
Found 43 semantic av rules:
 allow httpd_t home_root_t : dir { ioctl read getattr lock search open } ;
 allow httpd_t home_root_t : lnk_file { read getattr } ;
 allow httpd_t user_home_type : dir { getattr search open } ;
 allow httpd_t user_home_type : lnk_file { read getattr } ;
....(后面省略)....
# 从上面的数据才可以理解，在这个规则中，主要是放行 httpd_t 能否读取用户家目录的文件！
# 所以，如果这个规则没有启动，基本上， httpd_t 这种进程就无法读取用户家目录下的文件！


# 默认目录的安全性本文查询与修改
[root@study ~]# semanage {login|user|port|interface|fcontext|translation} -l
[root@study ~]# semanage fcontext -{a|d|m} [-frst] file_spec
选项与参数：
fcontext ：主要用在安全性本文方面的用途 
-l 为查询的意思；
-a ：增加的意思，你可以增加一些目录的默认安全性本文类型设定；
-m ：修改的意思；
-d ：删除的意思。
范例一：查询一下 /etc /etc/cron.d 的预设 SELinux type 为何？
[root@study ~]# semanage fcontext -l | grep -E '^/etc |^/etc/cron'
SELinux fcontext type Context
/etc all files system_u:object_r:etc_t:s0
/etc/cron\.d(/.*)? all files system_u:object_r:system_cron_spool_t:s0
```

**setsebool**

开启/关闭规则

```bash
[root@study ~]# setsebool [-P] 『规则名称』 [0|1]
选项与参数： -P ：直接将设定值写入配置文件，该设定数据未来会生效的！
范例一：查询 httpd_enable_homedirs 这个规则的状态，并且修改这个规则成为不同的布尔值
[root@study ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> off <==结果是 off ，依题意给他启动看看！
[root@study ~]# setsebool -P httpd_enable_homedirs 1 # 会跑很久很久！请耐心等待！
[root@study ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> on
```

**chcon**

修改文件的 SELinux type 等信息

```bash
[root@study ~]# chcon [-R] [-t type] [-u user] [-r role] 文件
[root@study ~]# chcon [-R] --reference=范例文件 文件
选项与参数： 
-R ：连同该目录下的次目录也同时修改；
-t ：后面接安全性本文的类型字段！例如 httpd_sys_content_t ；
-u ：后面接身份识别，例如 system_u； (不重要) 
-r ：后面街角色，例如 system_r； (不重要)
-v ：若有变化成功，请将变动的结果列出来
--reference=范例文件：拿某个文件当范例来修改后续接的文件的类型！
范例一：查询一下 /etc/hosts 的 SELinux type，并将该类型套用到 /etc/cron.d/checktime 上
[root@study ~]# ll -Z /etc/hosts
-rw-r--r--. root root system_u:object_r:net_conf_t:s0 /etc/hosts
[root@study ~]# chcon -v -t net_conf_t /etc/cron.d/checktime
changing security context of ‘/etc/cron.d/checktime’
[root@study ~]# ll -Z /etc/cron.d/checktime
-rw-r--r--. root root unconfined_u:object_r:net_conf_t:s0 /etc/cron.d/checktime
范例二：直接以 /etc/shadow SELinux type 套用到 /etc/cron.d/checktime 上！
[root@study ~]# chcon -v --reference=/etc/shadow /etc/cron.d/checktime
[root@study ~]# ll -Z /etc/shadow /etc/cron.d/checktime
-rw-r--r--. root root system_u:object_r:shadow_t:s0 /etc/cron.d/checktime
----------. root root system_u:object_r:shadow_t:s0 /etc/shadow
```

**restorecon**

让文件恢复成预设吧v的 SELinux type

```bash
[root@study ~]# restorecon [-Rv] 文件或目录
选项与参数： 
-R ：连同次目录一起修改；
-v ：将过程显示到屏幕上
范例三：将 /etc/cron.d/ 底下的文件通通恢复成预设的 SELinux type！
[root@study ~]# restorecon -Rv /etc/cron.d
restorecon reset /etc/cron.d/checktime context system_u:object_r:shadow_t:s0->
system_u:object_r:system_cron_spool_t:s0
# 上面这两行其实是同一行喔！表示将 checktime 由 shadow_t 改为 system_cron_spool_t
范例四：重新启动 crond 看看有没有正确启动 checktime 啰！？
[root@study ~]# systemctl restart crond
[root@study ~]# tail /var/log/cron
# 再去瞧瞧这个 /var/log/cron 的内容，应该就没有错误讯息了
```

### 日志管理

**日志管理简介**

**日志服务** **在CentOS 6.x中日志服务已经由rsyslogd取代了原先的syslogd服务。rsyslogd日志服务更加先进，功能更多。但是不论该服务的使用，还是日志文件的格式其实都是和syslogd服务相兼容的，所以学习起来基本和syslogd服务一致。**

**rsyslogd的新特点:**

* **基于TCP网络协议传输日志信息;**
* **更安全的网络传输方式;**
* **有日志消息的及时分析框架;**
* **后台数据库;**
* **配置文件中可以写简单的逻辑判断;** **与syslog配置文件相兼容。**

**确定服务启动** **ps aux | grep rsyslogd 查看服务是否启动** **chkconfig --list | grep rsyslog 查看服务是否自启动** **CentOS 7 变为 systrmctl list-unit-files | grep rsyslog**

**常见日志的作用**

| 日志文件             | 说明                                                                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| /var/log/cron    | 记录了系统定时任务相关的日志。                                                                                                                      |
| /var/log/cups/   | 记录打印信息的日志                                                                                                                            |
| /var/log/dmesg   | 记录了系统在开机时内核自检的信息。也可以使用 ==dmesg命令==直接查看内核自检信息。                                                                                        |
| /var/log/btmp    | 记录错误登录的日志。==这个文件是二进制文件，不能直接vi查看，而要使用lastb命令查看==，命令如下: lastbroot tty1 Tue Jun 4 22:38 - 22:38 (00:00) 有人在6月4日22:38使用root用户，在本地终端1登录错误 |
| /var/log/lastlog | 记录系统中所有用户最后一次的登录时间的日志。这个文件也是二进制文件，不能直接vi，而要使用==lastlog命令查看。==                                                                        |
| /var/log/mailog  | 记录邮件信息。                                                                                                                              |
| /var/log/message | 记录系统重要信息的日志。这个日志文件中会记录Linux系统的绝大                                                                                                     |
| /var/log/secure  | 记录验证和授权方面的信息，只要涉及账户和密码的程序都会记录。 比如说系统的登录，ssh的登录，su切换用户，sudo授权，甚至添加 用户和修改用户密码都会记录在这个日志文件中。                                             |
| /var/log/wtmp    | 永久记录所有用户的登录、注销信息，同时记录系统的启动、重启、 关机事件。同样这个文件也是一个二进制文件，不能直接vi，==而需 要使用last命令来查看。==                                                      |
| /var/run/utmp    | 记录当前已经登录的用户的信息。这个文件会随着用户的登录和注 销而不断变化，只记录当前登录用户的信息。同样这个文件不能直 接vi，而要使用w，who，users等命令来查询。                                               |

**除了系统默认的日志之外，采用RPM方 式安装的系统服务也会默认把日志记录在/var/log/目录中(源码包安装的服务日志 是在源码包指定目录中)。不过这些日志不是由rsyslogd服务来记录和管理的，而 是==各个服务使用自己的日志管理文档来记录自身日志==。**（因此/etc/rsyslog.conf文件没有自己安装的服务的配置）

| 日志文件            | 说明                     |
| --------------- | ---------------------- |
| /var/log/httpd/ | RPM包安装的apache服务的默认日志目录 |
| /var/log/mail/  | RPM包安装的邮件服务的额外日志目录     |
| /var/log/samba/ | RPM包安装的samba服务的日志目录    |
| /var/log/sssd/  | 守护进程安全服务目录             |

**rsyslogd日志服务**

**日志文件格式** **基本日志格式包含以下四列:**

* **事件产生的时间;**
* **发生事件的服务器的主机名;**
* **产生事件的服务名或程序名;**
* **事件的具体信息。**

\==**/etc/rsyslog.conf配置文件**== **写入这个文件可以自定义需要记录日志的程序** **authpriv.\*** **/var/log/secure** **服务名称\[连接符号]日志等级 日志记录位置** **认证相关服务.所有日志等级记录在/var/log/secure日志中**

**服务名称**

| 服务名称          | 说明                                                                       |
| ------------- | ------------------------------------------------------------------------ |
| auth          | 安全和认证相关消息(不推荐使用authpriv替代)                                               |
| authpriv      | 安全和认证相关消息(私有的)                                                           |
| cron          | 系统定时任务cront和at产生的日志                                                      |
| daemon        | 和各个守护进程相关的日志                                                             |
| ftp           | ftp守护进程产生的日志                                                             |
| kern          | 内核产生的日志(不是用户进程产生的)                                                       |
| local0-local7 | 为本地使用预留的服务                                                               |
| lpr           | 打印产生的日志                                                                  |
| mail          | 邮件收发信息                                                                   |
| news          | 与新闻服务器相关的日志                                                              |
| syslog        | 有syslogd服务产生的日志信息(虽然服务名 称已经改为rsyslogd，但是很多配置都还是沿 用了syslogd的，这里并没有修改服务名)。 |
| user          | 用户等级类别的日志信息                                                              |
| uucp          | uucp子系统的日志信息，uucp是早期linux系                                               |

统进行数据传递的协议，后来也常用在新闻 组服务中。

**连接符号** **连接符号可以识别为:**

* **“\*”代表所有日志等级，比如:“authpriv.\*”代表authpriv认证信息服务产生的日志，所有的日志等级都记录**
* **“.”代表只要比后面的等级高的(包含该等级)日志都记录下来。比如:“cron.info”代表cron服务产生的日志，只要日 志等级大于等于info级别，就记录**
* **“.=”代表只记录所需等级的日志，其他等级的都不记录。比 如:“\*.=emerg”代表人和日志服务产生的日志，只要等级是 emerg等级就记录。这种用法及少见，了解就好**
* **“.!”代表不等于，也就是除了该等级的日志外，其他等级的 日志都记录。**

**日志等级**

| 等级名称    | 说明                                 |
| ------- | ---------------------------------- |
| debug   | 一般的调试信息说明                          |
| info    | 基本的通知信息                            |
| notice  | 普通信息，但是有一定的重要性                     |
| warning | 警告信息，但是还不回影响到服务或系统的运行              |
| err     | 错误信息，一般达到err等级的信息以及可以影响到服务或系统的运行了。 |
| crit    | 临界状况信息，比err等级还要严重                  |
| alert   | 警告状态信息，比crit还要严重。必须立即采取行动          |
| emerg   | 疼痛等级信息，系统已经无法使用了                   |

**日志记录位置**

* **日志文件的绝对路径，如“/var/log/secure”**
* **系统设备文件，如“/dev/lp0”**
* **转发给远程主机，如“@192.168.0.210:514” 用户名，如“root”**
* **忽略或丢弃日志，如“\~”**

**日志轮替**

* logrotate是一个日志管理程序，用来把旧的日志文件删除（或者备份），并创建新的日志文件。有两种依据来进行日志的轮替：

> * 根据日志的大小，当文件大小达到某个阈值（设定），就进行轮替。
> * 根据其规定的天数来转储。在规定时间到了之后就进行日志文件的轮替。 logrotate 的执行由crond服务实现。在/etc/cron.daily目录中，有个文件logrotate，它实际上是个shell script，用来启动logrotate。它每天由cron在指定的时间（/etc/crontab）启动。 在执行logrotate时，需要指定其配置文logrotate.conf。/etc/logrotate.d目录下面的配置文件会被主动读入到logrotate.conf。

* 可以针对所有服务，只要在logrotate文件中写明需要替换的文件的目录，通过定时任务完成

**日志文件的命名规则** **如果配置文件中拥有“dateext”参数，那么日志会用日期来作为日志文件的后缀， 例如“secure-20130605”。这样的话日志文件名不会重叠，所以也就不需要日志文 件的改名，只需要保存指定的日志个数， 删除多余的日志文件即可。**

**如果配置文件中没有“dateext”参数，那么日志文件就需要进行改名了。当第一次进行日志 轮替时，当前的“secure”日志会自动改名为 “secure.1”，然后新建“secure”日志，用来 保存新的日志。当第二次进行日志轮替时， “secure.1”会自动改名为“secure.2”，当前的 “secure”日志会自动改名为“secure.1”，然 后也会新建“secure”日志，用来保存新的日志 ，以此类推。**

**logrotate配置文件**

| 参数                      | 参数说明                                                                                      |
| ----------------------- | ----------------------------------------------------------------------------------------- |
| daily                   | 日志的轮替周期是每天                                                                                |
| weekly                  | 日志的轮替周期是每周                                                                                |
| monthly                 | 日志的轮替周期是每月                                                                                |
| rotate 数字               | 保留的日志文件的个数。0指没有备份                                                                         |
| compress                | 日志轮替时，旧的日志进行压缩                                                                            |
| create mode owner group | 建立新日志，同时指定新日志的权限与所有者和 所属组。如create 0600 root utmp                                          |
| mail address            | 当日志轮替时，输出内容通过邮件发送到指定的 邮件地址。如mail 1771566679@qq.com                                        |
| missingok               | 如果日志不存在，则忽略该日志的警告信息                                                                       |
| notifempty              | 如果日志为空文件，则不进行日志轮替                                                                         |
| minsize 大小              | 日志轮替的最小值。也就是日志一定要达到这个 最小值才会轮替，否则就算时间达到也不轮替size 大小 日志只有大于指定大小才进行日志轮替，而不是 按照时间轮替。如size 100k |
| dateext                 | 使用日期作为日志轮替文件的后缀。如secure- 20130605                                                         |

**在/etc/logrotate.conf 配置文件里修改轮替规则，下面大括号外面的变量相当于局部变量，而大括号里面的相当于全局变量，只有大括号里面没有声明，外面的才生效，一旦大括号声明了，大括号里面的优先级高于外面，优先生效**

**把apache日志加入轮替** **vi /etc/logrotate.conf /usr/local/apache2/logs/access\_log { daily create rotate 30 }** **一般只有源码包安装才需要这样加入，RPM包在安装时候会将日志放在/var/log目录下，该目录下的日志会由自动做日志轮换**

**logrotate命令** **logrotate \[选项] 配置文件名** **如果此命令没有选项，则会按照配置文件中的条件进行 日志轮替**

* **-v:显示日志轮替过程。加了-v选项，会显示日志的轮 替的过程**
* **-f: 强制进行日志轮替。不管日志轮替的条件是否已经 符合，强制配置文件中所有的日志进行轮替**

### 启动管理

**系统运行级别**

**运行级别**

| 运行级别 | 含义                                |
| ---- | --------------------------------- |
| 0    | 关机                                |
| 1    | 单用户模式，可以想象为windows的安全模式，主要用 于系统修复 |
| 2    | 不完全的命令行模式，不含NFS服务                 |
| 3    | 完全的命令行模式，就是标准字符界面                 |
| 4    | 系统保留                              |
| 5    | 图形模式                              |
| 6    | 重启动                               |

**运行级别命令**

* **runlevel** **查看运行级别命令**
* **init 运行级别** **改变运行级别命令**

**修改系统默认运行级别** **vim /etc/inittab** **id:3:initdefault:** **系统开机后直接进入哪个运行级别，就把数字改为对应的数字**

**系统启动过程**

![系统启动流程](https://img-blog.csdnimg.cn/2020053015380272.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70#pic\_center)

**initramfs内存文件系统** **CentOS 6.x中使用initramfs内存文件系统 取代了CentOS 5.x中的initrd RAM Disk。 他们的作用类似，可以通过启动引导程序加载到内存中，然后加载启动过程中所需要的内核模块，比如USB、SATA、SCSI 硬盘的驱动和LVM、RAID文件系统的驱动**

* 一个实验看initramfs文件系统
  * mkdir /tmp/initramfs 建立测试目录
  * cp /boot/initramfs-2.6.32-279.el6.i686.img /tmp/initramfs/ 复制initramfs文件
  * cd /tmp/initramfs/
  * file initramfs-2.6.32-279.el6.i686.img
  * mv initramfs-2.6.32-279.el6.i686.img initramfs-2.6.32-279.el6.i686.img.gz 修改文件的后缀名为.gz
  * gunzip initramfs-2.6.32-279.el6.i686.img.gz 解压缩
  * file initramfs-2.6.32-279.el6.i686.img cpio -ivcdu < initramfs-2.6.32-279.el6.i686.img 解读cpio文件

**调用/etc/init/rcS.conf配置文件**（ubuntu 现在是用systemd） **主要功能是两个:**

* **先调用/etc/rc.d/rc.sysinit，然后由 /etc/rc.d/rc.sysinit配置文件进行Linux系统初始化。**
* **然后再调用/etc/inittab，然后由/etc/inittab配 置文件确定系统的默认运行级别。**

**由/etc/rc.d/rc.sysinit初始化**

* **1、获得网络环境**
* **2、挂载设备**
* **3、开机启动画面Plymouth(取替了过往的 RHGB)**
* **4、判断是否启用SELinux**
* **5、显示于开机过程中的欢迎画面**
* **6、初始化硬件**
* **7、用户自定义模块的加载**
* **8、配置内核的参数**
* **9、设置主机名**
* **10、同步存储器**
* **11、设备映射器及相关的初始化**
* **12、初始化软件磁盘阵列(RAID)**
* **13、初始化 LVM 的文件系统功能**
* **14、检验磁盘文件系统(fsck)**
* **15、设置磁盘配额(quota)**
* **16、重新以可读写模式挂载系统磁盘**
* **17、更新quota(非必要)**
* **18、启动系统虚拟随机数生成器**
* **19、配置机器(非必要)**
* **20、清除开机过程当中的临时文件**
* **21、创建ICE目录**
* **22、启动交换分区(swap)**
* **23、将开机信息写入/var/log/dmesg文件中**

**调用/etc/rc.d/rc文件** **运行级别参数传入/etc/rc.d/rc这个脚本之 后，由这个脚本文件按照不同的运行级别启动/etc/rc\[0-6].d/目录中的相应的程序**

* **/etc/rc3.d/K??开头的文件(??是数字)，会按照数字顺序依次关闭**
* **/etc/rc3.d/S??开头的文件(??是数字)，会 按照数字顺序依次启动**

**Grub配置文件**

**grub中分区表示**

| 硬盘        | 分区      | Linux中设备文件 名 | Grub中设备文件名 |
| --------- | ------- | ------------ | ---------- |
| 第一块SCSI硬盘 | 第一个主分区  | /dev/sda1    | hd(0,0)    |
| 第一块SCSI硬盘 | 第二个主分区  | /dev/sda2    | hd(0,1)    |
| 第一块SCSI硬盘 | 扩展分区    | /dev/sda3    | hd(0,2)    |
| 第一块SCSI硬盘 | 第一个逻辑分区 | /dev/sda5    | hd(0,4)    |
| 第二块SCSI硬盘 | 第一个主分区  | /dev/sdb1    | hd(1,0)    |
| 第二块SCSI硬盘 | 第二个主分区  | /dev/sdb2    | hd(1,1)    |
| 第二块SCSI硬盘 | 扩展分区    | /dev/sdb3    | hd(1,2)    |
| 第二块SCSI硬盘 | 第一个逻辑   | /dev/sdb5    | hd(1,4)    |

**grub配置文件 vi /boot/grub/grub.conf**

> **default=0 默认启动第一个系统** **timeout=5 等待时间，默认是5秒** **splashimage=(hd0,0)/grub/splash.xpm.gz** **这里是指定grub启动时的背景图像文件的保存位置的** **hiddenmenu 隐藏菜单**
>
> **title CentOS (2.6.32-279.el6.i686) title就是标题的意思** **root (hd0,0) 是指启动程序的保存分区** **kernel /vmlinuz-2.6.32-279.el6.i686 ro root=UUID=b9a7a1a8-767f-4a87-8a2b-a535edb362c9 rd\_NO\_LUKS KEYBOARDTYPE=pc KEYTABLE=us rd\_NO\_MD crashkernel=auto LANG=zh\_CN.UTF-8 rd\_NO\_LVM rd\_NO\_DM rhgb quiet 定义内核加载时的选项** **initrd /initramfs-2.6.32-279.el6.i686.img 指定了initramfs内存文件系统镜像文件的所在位置**

**Grub加密与字符界面分辨率调整**

**在开机选择内核界面可以按e进入里面破解root密码，这个时候为了安全，便需要给grub加密才能进入按e界面** **grub加密** 命令： grub-md5-crypt 生成加密密码串 vi /boot/grub/grub.conf 在splashimage=（hd0，0）这一行前面写入 password --md5 刚刚生产的密码串 Password选项放在整体设置处 重启就可以了 CentOS 7.2以后使用 grub2-setpassword 直接设置密码

**纯字符界面的分辨率调整** **查询内核是否支持分辨率调整** grep “CONFIG\_FRAMEBUFFER\_CONSOLE” /boot/config-3.10.0-1127.el7.x86\_64 显示 CONFIG\_FRAMEBUFFER\_CONSOLE=y 就为可以调整

再输入命令： vim /boot/grub/grub.conf 内核的选项文件中 Kernel /vmlinuz- \*_\*_\*\*\*这句话后面加入 vga=791，便是调整1024\*768 16位的分辨率，具体数字对应分辨率见下表：

| 色深  | 640\*480 | 800\*600 | 1024\*768 | 1280\*1024 |
| --- | -------- | -------- | --------- | ---------- |
| 8位  | 769      | 771      | 773       | 775        |
| 15位 | 784      | 787      | 790       | 793        |
| 16位 | 785      | 788      | 791       | 794        |
| 32位 | 786      | 789      | 792       | 795        |

**系统修复模式**

**单用户模式** **在登陆选择内核界面，按e键进入内核选项** **单用户模式常见的错误修复**

* **遗忘root密码**
* **修改系统默认运行级别**

修改密码： CentOS7 找到linux16 这一行 在CN.UTF-8 后面加入 rd.break console=tty0 然后按ctrl+x 然后依次输入： mount -o remount,rw /sysroot chroot /sysroot/ passwd root 或者 echo 密码 | passwd --stdin root 在这之后会出现很多小方块 这行小方块是中文编码问题，不用管它。输一次密码回车，再输一次确认密码，回车。 接着输入： touch /.autorelabel sync exit exit 重启就OK了，使用新密码登陆

**光盘修复模式** **在忘记了grub密码的时候可以使用这个模式** **在虚拟机中放入光盘iso文件，在虚拟机VMware界面读条的时候，快速按F2键，苹果系统可按fn+F2，进入刚开始学习安装的界面之后，选择上面第四栏BOOT，调到光盘启动CR-Drive为首选(按+号调节)，F10保存。在安装节目选第三项Troublesooting 回车，选择第二项Rescue a CentOS system 回车，选择2 Shell模式 回车 回车 ，此时根目录已经被挂载到光盘下 /mnt/sysimage目录下** chroot /mnt/sysimage #改变主目录 grub2-setpassword 输入新密码即可

重要系统文件丢失，导致系统无法启动 假设丢了etc/inittab 文件，你可以在其他同版本的Linux查询到这个文件所在的包 chroot /mnt/sysimage #改变主目录 cd /root rpm -qf /etc/inittab #查询下/etc/inittab文件属于哪个包。 mkdir /mnt/cdrom #建立挂载点 mount /dev/sr0 /mnt/cdrom #挂载光盘 rpm2cpio /mnt/cdrom/Packages/initscripts-8.45.3-1.i386.rpm | cpio -idv ./etc/inittab #提取inittab文件到当前目录 cp etc/inittab /etc/inittab #复制inittab文件到指定位置\*\*

**在光盘修复模式下可以修改大部分问题。**

**Linux的安全性** ￼![Linux的安全性](https://img-blog.csdnimg.cn/20200530155429286.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70#pic\_center)

### 备份与恢复

**备份概述**

**Linux系统需要备份的数据**

* **/root/目录:**
* **/home/目录:**
* **/var/spool/mail/目录:**
* **/etc/目录:**
* **其他目录:**

**安装服务的数据**

* **apache需要备份的数据**
  * **配置文件**
  * **网页主目录**
  * **日志文件**
* **mysql需要备份的数据**
  * **源码包安装的mysql:/usr/local/mysql/data/**
  * **RPM包安装的mysql:/var/lib/mysql/**

**备份策略**

* **完全备份:完全备份就是指把所有需要备 份的数据全部备份，当然完全备份可以备份整块硬盘，整个分区或某个具体的目录**
* **增量备份** ￼![增量备份](https://img-blog.csdnimg.cn/20200530160126408.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70#pic\_center)
* **差异备份** ￼![差异备份](https://img-blog.csdnimg.cn/20200530160146194.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l5MTUwMTIy,size\_16,color\_FFFFFF,t\_70#pic\_center)

**dump和restore命令**

**dump命令** **dump \[选项] 备份之后的文件名 原文件或目录**

* **-level:就是我们说的0-9十个备份级别**
* **-f 文件名: 指定备份之后的文件名**
* **-u: 备份成功之后，把备份时间记录在/etc/dumpdates文件**
* **-v:显示备份过程中更多的输出信息**
* **-j: 调用bzlib库压缩备份文件，其实就是把备份文件压缩 为.bz2格式，默认压缩等级是2**
* **-W: 显示允许被dump的分区的备份等级及备份时间** **CentOS 7以前版本需要安装dump yum -y install dump下载**

**CentOS 7 以后版本使用xfsdump备份xfs文件系统**

* **XFS提供了 xfsdump 和 xfsrestore 工具协助备份XFS文件系统中的数据。xfsdump 按inode顺序备份一个XFS文件系统。**
* **centos7选择xfs格式作为默认文件系统，而且不再使用以前的ext，仍然支持ext4，xfs专为大数据产生，每个单个文件系统最大可以支持8eb，单个文件可以支持16tb，不仅数据量大，而且扩展性高。还可以通过xfsdump，xfsrestore来备份和恢复。**
* **与传统的UNIX文件系统不同，XFS不需要在备份前被卸载；对使用中的XFS文件系统做备份就可以保证镜像的一致性。XFS的备份和恢复的过程是可以被中断然后继续的，无须冻结文件系统。xfsdump 甚至提供了高性能的多线程备份操作——它把一次dump拆分成多个数据流，每个数据流可以被发往不同的目的地。**
* **使用yum -y install xfsdump下载**

**只有在备份文件系统才能执行增量备份，执行1-9级别，文件和目录只能执行0级别**

备份分区 dump -0uj -f /root/boot.bak.bz2 /boot/ 备份命令。先执行一次完全备份，并压缩和更新备份时间 cat /etc/dumpdates 查看备份时间文件 cp install.log /boot/ 复制日志文件到/boot分区 dump -1uj -f /root/boot.bak1.bz2 /boot/ 增量备份/boot分区，并压缩 dump –W 查询分区的备份时间及备份级别的

备份文件或目录 dump -0j -f /root/etc.dump.bz2 /etc/ 完全备份/etc/目录，只能使用0级别进行完全备份 ，而不再支持增量备份

**restore命令** **estore \[模式选项] \[选项]**

* 模式选项:restore命令常用的模式有以下四种，这四个模式不能混用。
  * **-C:比较备份数据和实际数据的变化。**
  * **-i: 进入交互模式，手工选择需要恢复的文件。**
  * **-t: 查看模式，用于查看备份文件中拥有哪些数据。**
  * **-r: 还原模式，用于数据还原。**
* 选项:
  * **-f: 指定备份文件的文件名**

**比较备份数据和实际数据的变化** **mv /boot/vmlinuz-2.6.32-279.el6.i686 /boot/vmlinuz-2.6.32- 279.el6.i686.bak #把/boot目录中内核镜像文件改个名字 restore -C -f /root/boot.bak.bz2 #restore发现内核镜像文件丢失**

查看模式 restore -t -f boot.bak.bz2

还原模式 还原boot.bak.bz2分区备份 先还原完全备份的数据 mkdir boot.test cd boot.test/ restore -r -f /root/boot.bak.bz2 #解压缩 restore -r -f /root/boot.bak1.bz2 #恢复增量备份数据 #还原/etc/目录的备份etc.dump.bz2 restore -r -f etc.dump.bz2 #还原etc.dump.bz2备份

## Linux的几种内核锁及其作用

**mutex(互斥锁)：** 互斥锁主要用于实现内核中的互斥访问功能。对它的访问必须遵循一些规则：同一时间只能有一个任务持有互斥锁，而且只有这个任务可以对互斥锁进行解锁。互斥锁不能进行递归锁定或解锁。

**semaphore (信号量)：** 信号量在创建时需要设置一个初始值，表示同时可以有几个任务可以访问该信号量保护的共享资源，初始值为1就变成互斥锁（Mutex），即同时只能有一个任务可以访问信号量保护的共享资源。一个任务要想访问共享资源，首先必须得到信号量，获取信号量的操作将把信号量的值减1，若当前信号量的值为负数，表明无法获得信号量，该任务必须挂起在该信号量的等待队列等待该信号量可用；若当前信号量的值为非负数，表示可以获得信号量，因而可以立刻访问被该信号量保护的共享资源。当任务访问完被信号量保护的共享资源后，必须释放信号量，释放信号量通过把信号量的值加1实现，如果信号量的值为非正数，表明有任务等待当前信号量，因此它也唤醒所有等待该信号量的任务。

**rw\_semaphore （读写信号量）：** 读写信号量对访问者进行了细分，或者为读者，或者为写者，读者在保持读写信号量期间只能对该读写信号量保护的共享资源进行读访问，如果一个任务除了需要读，可能还需要写，那么它必须被归类为写者，它在对共享资源访问之前必须先获得写者身份，写者在发现自己不需要写访问的情况下可以降级为读者。读写信号量同时拥有的读者数不受限制，也就说可以有任意多个读者同时拥有一个读写信号量。如果一个读写信号量当前没有被写者拥有并且也没有写者等待读者释放信号量，那么任何读者都可以成功获得该读写信号量；否则，读者必须被挂起直到写者释放该信号量。如果一个读写信号量当前没有被读者或写者拥有并且也没有写者等待该信号量，那么一个写者可以成功获得该读写信号量，否则写者将被挂起，直到没有任何访问者。因此，写者是排他性的，独占性的。

**Spanlock(自旋锁）：** 自旋锁与互斥锁有点类似，只是自旋锁不会引起调用者睡眠，如果自旋锁已经被别的执行单元保持，调用者就一直循环在那里看是否该自旋锁的保持者已经释放了锁，”自旋”一词就是因此而得名。由于自旋锁使用者一般保持锁时间非常短，因此选择自旋而不是睡眠是非常必要的，自旋锁的效率远高于互斥锁。 信号量和读写信号量适合于保持时间较长的情况，它们会导致调用者睡眠，因此只能在进程上下文使用（\_trylock的变种能够在中断上下文使用），而自旋锁适合于保持时间非常短的情况，它可以在任何上下文使用。如果被保护的共享资源只在进程上下文访问，使用信号量保护该共享资源非常合适，如果对共巷资源的访问时间非常短，自旋锁也可以。但是如果被保护的共享资源需要在中断上下文访问（包括底半部即中断处理句柄和顶半部即软中断），就必须使用自旋锁。 自旋锁保持期间是抢占失效的，而信号量和读写信号量保持期间是可以被抢占的。自旋锁只有在内核可抢占或SMP的情况下才真正需要，在单CPU且不可抢占的内核下，自旋锁的所有操作都是空操作。 跟互斥锁一样，一个执行单元要想访问被自旋锁保护的共享资源，必须先得到锁，在访问完共享资源后，必须释放锁。如果在获取自旋锁时，没有任何执行单元保持该锁，那么将立即得到锁；如果在获取自旋锁时锁已经有保持者，那么获取锁操作将自旋在那里，直到该自旋锁的保持者释放了锁。 无论是互斥锁，还是自旋锁，在任何时刻，最多只能有一个保持者，也就说，在任何时刻最多只能有一个执行单元获得锁。

## CPU三级调度，何时调度，如何调度

无论何种调度，都是由操作系统本身的实现决定的

（1）高级调度：又称作业调度。其主要功能是根据一定的算法，从输入的一批作业中选出若干个作业，分配必要的资源，如内存、外设等，为它建立相应的用户作业进程和为其服务的系统进程（如输入、输出进程），最后把它们的程序和数据调入内存，等待进程调度程序对其执行调度，并在作业完成后作善后处理工作。 （2）中级调度：为了使内存中同时存放的进程数目不至于太多，有时就需要把某些进程从内存中移到外存上，以减少多道程序的数目，为此设立了中级调度。特别在采用虚拟存储技术的系统或分时系统中，往往增加中级调度这一级。所以中级调度的功能是在内存使用情况紧张时，将一些暂时不能运行的讲程从内存对换到外存上等待。当以后内存有足够的空闲空间时，再将合适的进程重新换人内存，等待进程调度。引人中级调度的主要目的是为了提高内存的利用率和系统吞吐量。它实际上就是存储器管理中的对换功能。 （3）低级调度：又称进程调度。其主要功能是根据一定的算法将CPU分派给就绪队列中的一个进程。执行低级调度功能的程序称做进程调度程序，由它实现CPU在进程间的切换。进程调度的运行频率很高，在分时系统中往往几十毫秒就要运行一次。进程调度是操作系统中最基本的一种调度。在一般类型的操作系统中都必须有进程调度，而且它的策略的优劣直接影响整个系统的计能。
