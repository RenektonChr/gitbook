# Linux开发环境初准备

## 零、认识Linux

+ 什么是Linux？
+ Linux和windows的区别？
+ 常用的Linux发行版？

### I、什么是Linux？

真正的Linux是指[Linux Kernel](http://kernel.org)，也就是我们常说的Linux内核，我们平时用的Linux操作系统，比如Ubuntu，CentOS，RedHat等等，这些是Linux的发行版。  

### 1. Linux体系架构

#### 1.1 Linux体系架构图

<img src="../assets/images/chapter8/linux.jpg" alt="node-app.png" style="zoom:80%;" />

#### 1.2 Linux体系结构粗解

Linux Kernel也就是我们常说的Linux内核，它要通过系统调用向外提供Linux的功能，我们用户一般使用shell和应用程序来使用Linux系统，Shell就是Linux的外壳，shell在英文中的本意就是外壳的意思。我们一般很少与公用函数库打交道。系统调用本质上就是API，也就是说我们通常把操作系统的API称作系统调用。

### II、LInux与Windows的对比

对于Windows我们都不陌生，Windows是成熟最早的带有图形界面的操作系统，Linux虽然也带有图形界面（比如带有图形界面的Ubuntu系统），但是相比于windows的图形界面，Linux的图形界面不稳定，原因是Linux的图形界面是一个应用程序，属于应用程序层，但是Windows的图形界面是嵌入到系统内核中的。哪个更稳定显而易见！但是在没有图形界面的黑框口下的Linux是异常稳定的，基本不会崩溃，这一点Windows是比不了的，Windows崩溃就蓝屏，这个我想大家都应该经历过。

## 一、安装Linux环境

### I、通过虚拟机安装（这里我们推荐VMWare）

+ [VMWare官方网站](https://www.vmware.com/)
+ 根据使用的操作系统不同分为Windows和Mac，那么下载的VMWare的软件包也不同，下载之前需要登录VMWare。
  + [Windows下载WorkStation Pro](https://www.vmware.com/cn/products/workstation-pro.html)
  + [Mac下载Fusion](https://www.vmware.com/cn/products/fusion.html)
+ 下载CentOS镜像
  + [CentOS官方网站](https://www.centos.org/)
  + [CentOS7下载](http://isoredirect.centos.org/centos/7/isos/x86_64/)
+ 不要下载CentOS的Stream版本！！已经被开源社区抛弃！
+ 国内下载的时候选择阿里云的镜像。下载的时候选择minimal版本的.ios文件下载，这个是最小的服务器版本，没有乱七八糟的东西，因为我们不需要图形界面（进来就是黑窗口，B格是不是很高😁）。
+ 老笔记本需要打开计算机硬件的虚拟化支持（现在的电脑基本上遇不到这个问题了）。
+ 最后CentOS镜像的安装请自行百度，网上相当多，这里就不赘述了。

### II、直接安装在电脑的硬盘中

这个办法实际上是最简单的，博主就是采用的这个办法，我把一个不用的旧电脑安装上了CentOS7。安装方法请自行百度。

### III. VPS

如果有阿里云或者腾讯云服务器，我们可以直接在我们的服务器上操作Linux系统。

### III、操作虚拟机的方式

操作虚拟机的方式一般分为两种：

+ 直接在虚拟机中的CentOS的窗口中去操作。
+ 远程连接虚拟机中运行的CentOS系统进行操作。

第一种方式自然不用多说，我们主要是解释一下第二种方法。

如何远程连接虚拟机？

使用终端通过网络，使用ssh命令来连接虚拟机中的CentOS，在连接之前我们需要对虚拟机的网络进行配置，因为虚拟机的网络是通过宿主机的网络虚拟（通过虚拟网卡）出来的一个网络（配置方法见下文）。网络配置好之后，在远程连接虚拟机之前我们还要知道虚拟机的IP地址，毫无疑问我们通过网络远程连接虚拟机必然是需要知道虚拟机的IP地址的（具体方法见下文）！得到了虚拟机的IP地址之后我们就可以使用ssh命令远程连接虚拟机了： [Linux 开发环境初准备讲义.pdf](../../../../Downloads/Linux 开发环境初准备讲义.pdf) 

```
$ ssh root@192.168.x.xxx
```

然后输入你设置的密码，登录完成之后我们就连接上了。

对于windows系统来说一般是没有支持shell命令的终端的，这里介绍几种windows下可以下载使用的终端：

+ Putty
+ Xshell
+ [Cmder](https://cmder.net/)
+ PowerShell

对于Mac有自带的终端，但是一般情况下我们推荐使用iTerm2

如何配置虚拟机的网路？

配置虚拟机的网络一般有两种方式：

+ 直接映射在宿主机的硬件网卡上，虚拟机的IP和宿主机的IP是相同的，缺点是如果宿主机切换网络，可能会失去映射关系。
+ 使用NAT虚拟一个局域网，会分配一个独立的IP，虚拟机和宿主机的网络不在一个网段下，建议使用这种方式。

如何知道虚拟机的IP地址？

在虚拟机的黑窗口下输入ifconfig或者ip addr

```c
$ ifconfig
// 或者
$ ip addr
```

找到eth0网卡的IP地址，就是虚拟机的IP地址。



## 二、认识Linux环境

Linux系统是一个文件系统，也就是说系统中所有的内容都会被当做文件处理，Linux中的文件是以目录树的结构进行安排的，所有的目录都有一个共同的根目录（这里可以联想树形结构）。根目录也叫root目录。在Linux终端，可以使用`cd /`命令进入根目录。在根目录下执行 `ls -l`，就会看见根目录下的所有目录，以及详细信息。

### I、**Linux**下的⽬目录都是做什什么用的

+ bin目录：

  bin目录是存放命令程序的目录，主要有: cat,chmod(修改权限), chown, date, mv, mkdir, cp, bash等等常⽤用的指令。

+ boot：

  存放的是Linux操作系统内核

+ dev：

  存放设备文件的地方，映射的是硬件。linux里面把所有的硬件都当做文件处理。在这里面可以看到我们的硬盘。

+ etc：

  存放配置文件的目录，如果要改动这里的文件要非常小心，如果害怕改错，一定要在更改配置文件之前对原文件进行备份，如果改坏了，还可以用备份文件快速更改。如果想固定IP就是在etc中的网络配置文件中更改，`/etc/sysconfig/nwtwork-scripts`  中修改，更改BOOTPROTO为static，更改ONBOOT为yes，启动网卡，然后设置IPADDR，最后设置DNS和GATEWAY。

+ home：

  Linux系统是一个多用户系统，Linux的用户分为两类，一类是超级管理员也就是root，另一类就是普通用户。root有一个自己的专用目录就是root目录，换句话说，在根路径下的root目录就是超级管理员root用户的专用目录！如果我们现在在根目录下执行 `cd root` 命令，那么我们会进入home目录，这时会有一些不一样，目录的提示符成了`~` ，现在有一个新的概念就是根目录下的root目录是超级管理员root用户的home目录！除root用户的其他用户的home目录集中放在根目录下的home目录下。综上所述，home目录是Linux系统上用户的专用私有目录。

+ lib：

  存放公共函数库

+ media：

  存放外部设备，比如U盘、移动硬盘。

+ mnt：

  media的前身

+ opt：

  存放用户自己的程序的，相当于windows的programsFiles。

+ proc：

  虚拟目录，只存在于内存中，硬盘上是找不到proc的，存放了操作系统在运行时的状态。

+ root：

  root用户的home目录。

+ usr：

  usr是用户目录，用yum命令安装的软件都会存放在这个目录中。

+ usr/src：

  存放操作系统源代码，默认是空的，只有在对内核进行编程的时候才会把代码放在里面。

+ var：

  存放日志，apacha默认的网站目录等以及niginx和mysql的日志文件。

有的目录后面会带一个箭头，这种目录说明它不是一个真正的目录，而是一个快捷方式（软连接），这个快捷方式指向箭头所指的目录。

### II、Linux下的常用命令

这一部分可以参考《鸟哥的Linux私房菜》

如果想要知道Linux下的命令都是干什么用的，可以使用Linux系统自带的帮助系统，输入`dir --help`命令即可，但是这要求我们的英文必须要过关。还可以使用 man 命令。

其实一本《鸟哥的Linux私房菜》就OK了。

另外，奉劝一句，学习英文，英文补得越早越有优势。

Linux下常用的基本命令，以后会专门总结一下，写一篇文章。



## 三、前端开发必须要懂得知识

#### I、网络端口

+ 什么是端口？

  在网络上，当你需要网络的时候必须要打开一个叫端口的东西，只有端口打开了，网络才能通，同时端口是和程序绑定的。本质上端口是和进程绑定的

+ 端口冲突是怎么回事儿？

  当你想使用80端口启动一个进程的时候，如果80端口被其他的进程使用，那么这是后就会出现端口冲突，当出现端口冲突的时候，我们可以通过命令查看当前都占用了哪些端口，然后可以杀死占用你想要使用的端口的进程，也可以使用一个没有被占用的端口来启动你的进程。

有很多查看网络端口的方法，下面我们介绍几种：

+ `netstate -an` 查看当前端口的情况，查看本地地址`Local Address`对应的端口，就是当前系统已经使用的端口。
+ `netstate -anp` 带有了pid，我们就可以知道占用端口的是什么进程。从而决定能不能杀死，或者换一个端口号。
+ `ps aux` 查看进程，得到pid，然后使用`kill -9 pid` 强制杀死进程。但是我们要注意一点，当我们杀死进程的时候，要杀死父进程，如果只杀死子进程不杀死父进程，当父进程发现子进程死掉，会马上复活子进程。



### II、服务

在Linux中，服务的概念和web服务或者服务器的这种服务是不一样的，Linux中的服务叫做守护进程。



## 四、安装Linux开发环境

### I、安装NodeJS

+ yum安装方法

  ```javascript
  # 添加官方的yum源
  # As root
  curl -sL https://rpm.nodesource.com/setup_16.x | bash -
  
  # yum命令安装
  yum install -y nodejs
  # 查看安装的版本
  node -v
  ```

+ 源码编译安装

### II、安装XAMPP集成开发环境

+ [点击此处下载](https://www.apachefriends.org/index.html)
+ Windows和Mac的安装版本有所不同
  + windows，直接下载windows最新版本就可以
  + Mac下有两个版本，一个是普通版本（xxx.dmg），一个是vm版本（xxx.vm.dmg），安装普通版本的最新版即可，不要安装vm版本，vm版本是打包到虚拟机中的。

### III、学会使用vim编辑器

我们工作以后会碰到在服务器中更改配置文件的情况，服务器上不会有图形界面，这就需要我们会使用vim编辑器。


## 五、关于某些网站无法访问的解决方法（你懂得）









