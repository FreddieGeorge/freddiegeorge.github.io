---
title: Manjaro从KDE更换为dwm
date: 2023-01-31 14:28:21
tags:
- Manjaro
- dwm
categories: 
- Linux
- Manjaro
excerpt: Manjaro KDE桌面环境替换为dwm基本环境,并安装ranger资源管理器
---

# 前言

看到一个[知乎答主的dwm桌面配置](https://www.zhihu.com/question/399967127/answer/1805622525)，感觉很有趣，便决定对自己kde桌面的manjaro笔记本改造一番。而且KDE桌面有几个问题让我有点难以接受，首先是加载时间太长了，我的笔记本在登录用户之后还需要读条一段时间才能进入桌面，而且现在对于我来说桌面环境也没怎么用上过了，开机第一件事情就是打开终端。所以就

这篇博客主要是自己折腾的记录，结合了很多博客，更多的还是查阅arch的wiki，如果想要自己学的话，多看官方文档和wiki等资料。

## 参考资料

首先还是推荐仔细查看官方文档，文档对dwm的文件作用等写得很详细

[dwm官方文档](https://dwm.suckless.org/)

[Arch Linux dwm 文档](https://wiki.archlinux.org/title/dwm)

[Manjaro/Arch Linux 下基于dwm打造完美桌面环境 (二): 安装基础组件 - 彭亚伦](https://zhuanlan.zhihu.com/p/395307199)

> (这篇后续咕咕咕了，但是还是很感谢大佬)

[入坑dwm——原来窗口管理器还可以这样用 - 阿州](https://zhuanlan.zhihu.com/p/183861786)

[Simple DWM install on Manjaro XFCE/KDE - YouTube](https://www.youtube.com/watch?v=dP8OKP-r1tw)(我大体上是根据这个视频来操作的，但是需要魔法)

## 介绍

我现在的桌面用的KDE，如果有用过unbuntu的话应该也听过GNOuME，而GNOME，KDE等都是叫做桌面环境 *(Desktop Enviroment,DE)*，同时这几个都带了自己的窗口管理器 *(WindowS Manager,WM)*，主要通过鼠标操作；而dwm,i3wm等只有WM，主要通过键盘操作。

而我们用来登录的界面，叫做显示管理器 *(Display Manger,DM)*,负责引导用户输入账号密码进入桌面，接下来就交给KDE了。所以我们在从KDE切换到dwm的时候，只需要重启登录的时候选择对应的KDE或者dwm就行了。

> 如果你折腾过 Arch Linux那你肯定知道配置sddm等显示管理器。

dwm *(Dynamic window manager)* 是 suckless开发的一个用于 [X](https://www.x.org/wiki/) 的动态桌面管理器。从他的英文原名的 *Dynamic* 也能看出来它的一个特点是动态，在dwm上的布局都可以动态应用。dwm的可定制性非常高，资源占用极低，非常轻量，源码大小只有80多KB。如果有一定的编程和linux基础的话可以尝试一下dwm。

> 我想要更换KDE的一个原因也是KDE的占用有点高，而且会卡顿

## 环境介绍

* cpu: i5-8250U
* 操作系统：Manjaro with Plasma(X11)

## 安装过程

### 安装dwm

1. `sudo pacman -S yay git vim base-devel`
2. `git clone https://git.suckless.org/dwm --depth=1`
3. `cd dwm`
4. `sudo make clean install`

### 安装st

st是一个在X上的简单终端实现

1. `git clone https://git.suckless.org/st --depth=1`
2. `cd st`
3. `sudo make clean install`

### 安装dmenu

dmenu是suckless开发的一个基建的应用程序选择器。安装dmenu只需要终端输入 `sudo pacman -S dmenu` 即可

### 安装Ranger

`sudo pacman -S ranger`

## 配置dwm到dm

安装好之后便可以开始把桌面环境给配置dwm。我们首先需要了解一下系统是怎么知道和选择我们的桌面环境。

这里就需要回顾刚刚所说的 dm *(display manger)*。从[dm的wiki](https://wiki.archlinux.org/title/Display_manager#Session_configuration)中可以看到一个关键目录 `/usr/share/xsessions/`。从wiki中可以知道如果我们需要新增桌面环境的话需要在这个目录下面添加一个*.desktop 文件。

> To add/remove entries to your display manager's session list, create/remove the .desktop files in /usr/share/xsessions/ as desired.

那我们只需要在这个目录下指定新建一个DWM.desktop文件，指定编译出来的可执行文件就可以了。

我新建的DWM.desktop 内容如下

```shell
[Desktop Entry]
Encoding=UTF-8
Name=DWM
Comment=Dynamic Window Manager developed by suckless.org
Exec=/usr/local/bin/dwm
Icon=
Type=Application
```

> Exec 目录可以从dwm源码的Makefile找到

## 启动DWM

重启之后就可以在登录界面的右下角看到桌面环境的选择按钮了。

> 不同dm的桌面环境选择按钮是不一样的，需要仔细寻找一下，如果`/usr/share/xsessions/DWM.desktop`没有问题的话应该是有DWM的选项的，但是位置不一样。比如Ubuntu18.04的环境按钮选择是需要先选择用户之后才能看到设置的按钮。

选择DWM，登陆账户，就可以看到这个黑乎乎的桌面了。输入 `Alt` + `Shift` + `Enter` 就能看到终端界面了

![DWM桌面初见](https://raw.githubusercontent.com/FreddieGeorge/blogImg/main/img/2023-01-31_20-06.png)

> 说实话刚看到这个桌面的时候我是想放弃的，真的是太丑陋了xD


至此DWM安装完成，接下来便开始配置我的DWM桌面了

## 配置

配置方面我参考了b站up主[TheCW](https://space.bilibili.com/13081489)的视频和他的github仓库，然后跟着b站[9thSummit的视频](https://www.bilibili.com/video/BV1pr4y1U78u/?share_source=copy_web&vd_source=7f70bc35f201f823b29deb345ca6ea83)完成了基本的配置，可以直接看这个视频学习。

我自己也将`config.h`做了一些简单的修改，比如更换了`*fonts[]`和`dmenufont[]`的字体和字号，由于更换的字体是NerdFont系列的，我再将`tags`，也就是dwm界面左上角代表不同窗口的1-9更换成了五个图标，还有不同窗口layouts的表示方式也更换为了图标。

### 更改终端模拟器

修改`config.h`，将`Alt`+`Shift`+'Enter`的打开的终端改为我想要的`alacritty`终端

```c
// static const char *termcmd[] = { "st", NULL };
static const char *termcmd[] = { "alacritty", NULL };
```

我的具体alacritty配置和安装可以看[另一篇博客](https://freddiegeorge.github.io/2023/02/14/alacritty%E9%85%8D%E7%BD%AE%E8%BF%87%E7%A8%8B/)。

再次`sudo make clean install`然后重启就可以看到终端更换了。

### 添加patches

patches在[suckless的官网中](https://dwm.suckless.org/patches/),在左侧能看到有许多patches,如果不想要啃英文一个个找的话，这里有个很优秀的[github仓库](https://github.com/Katzeee/dwm-patches-chinese)将patches翻译为中文。

补丁看自己喜欢的来安装，我个人安装了`uselessgap`,`autostart`,`barpadding`三个补丁，合入的方式为

```shell
cd /your/path/to/dwm_soucre

patch < /path/to/patches.diff
```

补丁在合并的时候可能会与其他补丁产生冲突，要手动对比.rej文件解决冲突。

### 添加脚本

这个脚本是在安装了`autostart`的基础上使用的。在合入这个脚本之后，可以在`dwm.c`中看到`scan()`函数中增加了一句`runautostart()`和增加了该函数实现。在官网中也可以看到对该脚本的介绍

> This patch will make dwm run "~/.dwm/autostart_blocking.sh" and "~/.dwm/autostart.sh &" before entering the handler loop. One or both of these files can be ommited.

脚本这些我暂时还没有研究，等后面再补充自己想要的功能，目前为是照搬了[jzz777](https://gitee.com/jzz777/dwm)的脚本，后续我有计划将脚本改造一下，符合我自己的使用习惯。

### 动画效果

目前只是简单安装了一个picom，暂时还没有研究

### rofi代替dmenu

dmenu实在太简陋了,我还是喜欢颜值高一点的rofi。直接使用`yay -S rofi`安装即可，然后将dwm中的`config.h`修改一下`*dmenucmd[]`的值.然后简单配置一下rofi的主题即可

```c
static const char *dmenucmd[] = { "rofi", "show", "drun", "-theme", "/home/flork/.config/rofi/launchers/tyep-3/style-10.rasi", NULL };
```

### 壁纸

壁纸软件使用的是`nitrogen`，直接用`nitrogen /path/to/wallpaper`即可设置壁纸，然后在autostart脚本添加`nitrogen --restroe &`即可

## 成果

最终成果如图所示。

[!dwm配置图](tbd)

## 总结

一直以来无论是上班还是自己玩，我用linux也很少使用gui，偶尔看到了dwm便大为喜爱，目前除了vim还是不熟悉之外体验和颜值都上升了很多，这篇博客的后半段就是在为配好dwm的manjaro笔记本上使用的，整体还是相当满意的。
