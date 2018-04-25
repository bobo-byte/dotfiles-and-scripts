# dotfiles and scripts 

## Contents

* [Intro](#intro)
* [Usage](#usage)
* [配置文件部分](#配置文件部分)
* [安装脚本部分](#安装脚本部分)
   * [ArchLinux安装](#archlinux安装)
   * [软件批量安装](#软件批量安装)
   * [Vim及插件安装](#vim及插件安装)
* [工具脚本部分](#工具脚本部分)
   * [Python虚拟环境快速切换](#python虚拟环境快速切换)
   * [aria2管理&amp;自动更新bt-tracker](#aria2管理自动更新bt-tracker)
   * [本地Docker服务项目管理](#本地docker服务项目管理)
* [Screenshot](#screenshot)
* [TODO](#todo)


## Intro

创建这个repo是为了减少重复工作，内容包含：
1.  系统/软件配置，如Vim、i3wm、tmux等
2.  安装脚本
    *   ArchLinux系统安装脚本
    *   软件编译/安装脚本，如Vim-YCM插件
3.  工具脚本
    *   工具脚本，如Docker、Aria2管理


## Usage

此repo中的安装脚本可由[install.sh](./install.sh)来统一执行，然后根据菜单提示进行操作
```bash
git clone https://github.com/Karmenzind/dotfiles-and-scripts {target_dir}
cd {target_dir} 
./install.sh  
```


## 配置文件部分

不介绍，按需拷贝
> trees are generated by [this script](./utils/build_trees.sh)

- [for XDG_CONFIG_DIR](./home/.config)
	- [.vimrc](./vim/.vimrc)
	- [.tmux.conf](./home/.tmux.conf)
    - [aria2](./home/.config/aria2)
    - [compton.conf](./home/.config/compton.conf)
    - [dunst](./home/.config/dunst)
    - [fontconfig](./home/.config/fontconfig)
    - [i3](./home/.config/i3)
    - [volumeicon](./home/.config/volumeicon)
    - [xfce4](./home/.config/xfce4)
- [other config](./others)
	- [bashrc_ext](./others/bashrc_ext)
	- [fcitx](./others/fcitx)


## 安装脚本部分

### ArchLinux安装

**文件结构：**
- [livecd_part](./scripts/install_arch/livecd_part.sh) LiveCD部分：分区、安装base package等
- [chrooted_part](./scripts/install_arch/chrooted_part.sh) 进入chroot环境之后的部分，直到重启
- [general_recommendations_part](./scripts/install_arch/general_recommendations_part.sh) 安装完成后的一些基础设置，目前内容较少，后续根据需要增加
- [graphical_env_part](./scripts/install_arch/graphical_env_part.sh) 安装图形环境，目前支持GNOME kde Xfce4 i3wm

> 命名对应了ArchWiki中的安装配置过程

**使用前提：**
*   熟悉ArchLinux安装和Bash语法
*   一块独立硬盘（暂不兼容和其他系统混装）
*   设备支持UEFI（采用GPT分区，后续可能会加入MBR支持）
*   手动配置网络（暂不提供网络配置，请参考ArchWiki）

**分区说明：**

提供了两种分区方式，用Y/N选择：
```
Do you want to use recommended partition table as follows (Y) 
or do the partition by yourself? (N)
recommended table:
1. for a disk larger than 60G:
    550MiB      ESP         for boot
    32GiB       ext4        for ROOT
    8GiB        linux-swap  for SWAP
    remainder   ext4        for HOME
2. for a disk smaller than 60G:
    550MiB      ESP         for BOOT
    remainder   ext4        for ROOT
    (you can create a swapfile by yourself after installation)
```
分别为：
*   自动分区，空间大于60G时，参考ArchWiki的推荐分区（wiki里认为`/`分区20G够用，此处改成了32G）；小于60G时，参考了Manjaro的处理方式。
*   手动分区，进入fdisk交互环境自行操作，保存退出。支持boot、root、swap、home每种用途分区最多一个。

**安装步骤：**
1. 确定阅读完上述内容。经安装介质启动进入LiveCD环境，参考ArchWiki手动配置好网络。
2. **在LiveCD环境中执行liveCD part**。<br>
    方案一，拷贝整个项目到LiveCD。执行`pacman -Sy git`尝试安装Git，然后进行clone。如果无法安装Git，建议找一台可以ssh登陆的机器（我用了树莓派）用scp传输项目，或者通过挂载其他存储介质来拷贝项目。成功拷贝项目后，参考[Usage](#usage)运行install.sh，依次选择`install ArchLinux`、`livecd part`，依照提示执行。结束后已经处于arch-chroot环境，此时进入`/dotfiles-and-scripts`目录，通过`./install.sh`脚本继续执行`chrooted part`。<br>
    方案二，获取livecd_part.sh文件。手动输入:smiling_imp:如下命令：
    ```bash
    wget https://raw.githubusercontent.com/Karmenzind/dotfiles-and-scripts/master/scripts/install_arch/livecd_part.sh
    ./livecd_part.sh
    ```
    进入arch-chroot后，按照[Usage](#usage)clone整个项目，执行`chrooted part`。
3. `chrooted part`执行结束后，重启，取出存储介质，从系统盘进入Arch。
4. 至此Arch系统已经安装完成，后续步骤为系统常用配置，对应了ArchWiki中的[General Recommendations部分](https://wiki.archlinux.org/index.php/General_recommendations)，其中图形环境部分单独分成一步。参考[Usage](#usage)分别执行`general recommendations part`、`graphical environment part`。如果需要批量安装其他软件，则查看下一节。


> 如果是在虚拟机中通过UEFI方式安装Arch，需要在ESP分区根目录创建文件`startup.nsh`写入grub的efi文件地址（注意`\`方向），如`\EFI\grub\grubx64.efi`

### 软件批量安装

- [install_apps.sh](./scripts/install_apps.sh)

`_fonts`数组中包含了ArchWiki中推荐的所有中文环境所需字体（不含AUR）

1. 自行修改脚本，根据需要添加、删除软件
2. 从install.sh进入选择`install recommended apps`

### Vim及插件安装

Vim比较特殊，尤其是YCM经常安装失败，所以单独写一个脚本

- [complete installation](./scripts/install_vim/main.sh) 按照我的Vim配置完整安装（TODO：完整编译安装）
- [compile and install YouCompleteMe](./scripts/install_vim/ycm.sh) YouCompleteMe插件编译安装

## 工具脚本部分

### Python虚拟环境快速切换

- [acpyve](./local_bin/acpyve)

方便一堆虚拟环境需要切换的场景<br>

Usage:
在脚本或环境变量中设置虚拟环境存放目录，然后
```bash
k 16:04:00 > ~ $ . acpyve
Pick one virtual environment to activate:

1  General_Py2
2  General_Py3

Input number: 2

Activating General_Py3...
/home/k

DONE :)

(General_Py3) k 16:04:16 > ~
$ 

```

### Aria2管理&自动更新bt-tracker

- [myaria2](./local_bin/myaria2)

功能：
- 启动、重启、停止、查看运行状态、查看日志
- 更新bt-tracker（从ngosang/trackerslist获取）。启动、重启时，配置周期触发更新，也可以通过`myaria2 update`主动更新
- 转存旧日志
- 其他一些简单功能

结合cron使用
配置项见脚本注释

Reference:
-   [trackerslist](https://github.com/ngosang/trackerslist)
-   [解决Aria2 BT下载速度慢没速度的问题](http://www.senra.me/solutions-to-aria2-bt-metalink-download-slowly/)
-   [配置详解](http://www.senra.me/aria2-conf-file-parameters-translation-and-explanation/)
-   [AriaNG——高颜值的Aria2 WebUI](http://www.senra.me/ariang-a-beautiful-aria2-webui-front-end/)
-   [Aria2+Plex实现支持离线下载的小型私人视频云盘](http://www.senra.me/aria2-and-plex-build-your-own-cloud-video-streaming-service/)
-   [下载工具系列——Aria2 (几乎全能的下载神器)](http://www.senra.me/awesome-downloader-series-aria2-almost-the-best-all-platform-downloader/)
-   [下载神器——Aria2，打造你自己的离线下载服务器](http://www.senra.me/download-artifact-aria2-create-your-own-offline-download-server/)

### 本地Docker服务项目管理

- [docker_manager](./local_bin/docker_manager)

方便一堆用Docker容器需要管理的场景<br>
配合cron使用


## Screenshot


i3 desktop:

![desktop.png](./pics/desktop.png)

Vim:

![vim.png](./pics/vim.png)
![vim_goyo.png](./pics/vim_goyo.png)

## TODO

- `do_synch.sh` from host to repo... vise versa

