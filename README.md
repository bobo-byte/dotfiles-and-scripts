# dotfiles and scripts 

创建这个repo是为了减少重复工作，内容包含：
1.  系统/软件配置，如Vim、i3wm、tmux等
2.  安装脚本
    *   ArchLinux系统安装脚本
    *   软件编译/安装脚本，如Vim-YCM插件
3.  工具脚本
    *   工具脚本，如Docker、Aria2管理

内容               | 状态
:------------------|:--
ArchLinux安装脚本  | 2018-09-20 自测成功安装，机械硬盘
其他配置文件、脚本 | 自用，日常滚动更新

## TOC

<!-- vim-markdown-toc GFM -->

* [用法简述](#用法简述)
    * [安装ArchLinux或其他软件](#安装archlinux或其他软件)
    * [使用我的配置](#使用我的配置)
        * [所有配置文件和工具脚本](#所有配置文件和工具脚本)
        * [使用我的Vim配置](#使用我的vim配置)
* [配置文件部分](#配置文件部分)
* [安装脚本部分](#安装脚本部分)
    * [ArchLinux安装](#archlinux安装)
    * [软件批量安装](#软件批量安装)
    * [Vim及插件安装](#vim及插件安装)
    * [YouCompleteMe编译安装](#youcompleteme编译安装)
* [工具脚本部分](#工具脚本部分)
    * [Python虚拟环境快速切换](#python虚拟环境快速切换)
    * [Aria2管理&自动更新bt-tracker](#aria2管理自动更新bt-tracker)
    * [本地Docker服务项目管理](#本地docker服务项目管理)
* [效果截图](#效果截图)
* [创建你自己的DotFile仓库](#创建你自己的dotfile仓库)

<!-- vim-markdown-toc -->

## 用法简述

### 安装ArchLinux或其他软件

在repo根目录运行[install.sh](./install.sh)，选择所需的功能，根据提示操作。[详细介绍见下文](#安装脚本部分)。
```bash
git clone https://github.com/Karmenzind/dotfiles-and-scripts --depth=1
cd ./dotfiles-and-scripts
./install.sh  
```
:exclamation: 注意：除了下文特别说明的（如Arch安装第一步）一些功能，其他脚本都建议**从install.sh统一执行**，否则会报错

### 使用我的配置

#### 所有配置文件和工具脚本

通过以下命令来一键安装（支持交互模式选择）。[配置文件结构见下文](#配置文件部分)。

```bash
git clone https://github.com/Karmenzind/dotfiles-and-scripts --depth=1
cd ./dotfiles-and-scripts
python3 do_synch.py apply
```

如果你想创建一个自己的dotfile仓库，[参考下文](#创建你自己的dotfile仓库)

#### 使用我的Vim配置

如果只需要我的[Vim配置](./home_k/.vimrc)，Unix环境直接运行以下命令，等待自动安装完成
```bash
wget https://raw.githubusercontent.com/Karmenzind/dotfiles-and-scripts/master/home_k/.vimrc -O ~/.vimrc && vim 
```
也可以用[脚本安装](#vim及插件安装)

> 使用**root用户**安装我的配置可能会出问题。我不喜欢给root用户单独配置，采用的做法是在`/root`目录下创建`.vimrc`和`.vim`的软链接，与普通用户共用一套配置，供参考。如果要**直接**给root用户安装配置，请自行研究解决方案。


## 配置文件部分

文件跳转目录：
> trees are generated by [this script](./utils/build_trees.sh)

- [home](./home_k)
	- [.vimrc](./home_k/.vimrc)
    - [.vim](./home_k/.vim)
	- [.tmux.conf](./home_k/.tmux.conf)
    - [.zshrc](./home_k/.zshrc)
	- [.xinitrc](./home_k/.xinitrc)
	- [.config](./home_k/.config)
		- [alacritty](./home_k/.config/alacritty)
		- [aria2](./home_k/.config/aria2)
		- [compton.conf](./home_k/.config/compton.conf)
		- [conky](./home_k/.config/conky)
		- [dunst](./home_k/.config/dunst)
		- [fcitx](./home_k/.config/fcitx/data)
		- [i3](./home_k/.config/i3)
		- [neovim](./home_k/.config/nvim)
		- [rofi](./home_k/.config/rofi)
		- [shrc.ext](./home_k/.config/shrc.ext)
		- [xfce4](./home_k/.config/xfce4)

> 我使用的字体: [Monaco Nerd](https://github.com/Karmenzind/monaco-nerd-fonts)

## 安装脚本部分

### ArchLinux安装

**文件结构：**
> 命名对应了ArchWiki中的安装过程

- [livecd_part](./scripts/install_arch/livecd_part.sh) LiveCD部分：分区、安装base package等
- [chrooted_part](./scripts/install_arch/chrooted_part.sh) 进入chroot环境之后的部分，直到重启
- [general_recommendations_part](./scripts/install_arch/general_rec_part.sh) 安装完成后的一些基础设置，目前内容较少，后续根据需要增加
- [graphical_env_part](./scripts/install_arch/graphical_env_part.sh) 安装图形环境，目前支持GNOME kde Xfce4 i3wm

**使用前提：**
*   熟悉ArchLinux安装和Bash语法
*   一块独立硬盘（暂不兼容和其他系统在同一硬盘混装）
*   设备支持UEFI（采用GPT分区，后续可能会加入MBR支持）
*   手动配置网络（暂不提供网络配置，请参考ArchWiki）

**分区说明：**

提供了两种分区方式，用Y/N选择：
```
Do you want to use recommended partition table as follows (Y) 
or do the partition by yourself? (N)
recommended table:
1. for a disk larger than 60G:
    550MiB              ESP             for boot
    32GiB               ext4            for ROOT
    the same as         linux-swap      for SWAP
    your ram            
    remainder           ext4            for HOME
2. for a disk smaller than 60G:
    550MiB              ESP             for BOOT
    remainder           ext4            for ROOT
    (you can create a swapfile by yourself after installation)
```
包括：
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
    进入arch-chroot后，按照[Usage](#usage)介绍clone整个项目，执行`chrooted part`。
3. `chrooted part`执行结束后，重启，取出存储介质，从系统盘进入Arch。
4. 至此Arch系统已经安装完成，后续步骤为系统常用配置，对应了ArchWiki中的[General Recommendations部分](https://wiki.archlinux.org/index.php/General_recommendations)，其中图形环境部分单独分成一步。参考[Usage](#usage)分别执行`general recommendations part`、`graphical environment part`。如果需要批量安装其他软件，则查看下一节。

> 如果是在虚拟机中通过UEFI方式安装Arch，需要在ESP分区根目录创建文件`startup.nsh`写入grub的efi文件地址（注意`\`方向），如`\EFI\grub\grubx64.efi`

### 软件批量安装

- [install_apps.sh](./scripts/install_apps.sh)

`_fonts`数组中包含了ArchWiki中推荐的所有中文环境所需字体（不含AUR）

1. 自行修改脚本，根据需要添加、删除软件
2. 从install.sh进入选择`install recommended apps`

### Vim及插件安装

Vim比较特殊，尤其是YCM经常安装失败，所以单独列出来

用脚本安装Vim和插件：
- [complete installation](./scripts/install_vim/main.sh) 直接按照我的Vim配置一键安装Vim和各种插件，无需其他配置

如果你已经安装了Vim，需要直接使用我的配置&插件，除了上面的脚本安装外，更简单的方法为直接执行[Usage](#usage)中提到的命令

### YouCompleteMe编译安装

- [compile and install YouCompleteMe](./scripts/install_vim/ycm.sh) YouCompleteMe插件编译安装

[通过install.sh](#usage)**选择第四项单独安装YouCompleteMe插件**时，需要注意：
1.  阅读ycm.sh的开头部分
2.  修改ycm.sh中的ycm插件安装地址
3.  如果为Arch系统，直接选择任意一种方式安装；如果非Arch系统，选择`official way`可以直接安装，如果选择`my way`方式，需要手动安装python、cmake和clang，然后修改ycm.sh中的`libclang.so`地址

> `official way`是采用ycm自带安装脚本编译安装，`my way`是用我写的命令编译安装。如果用`my way`安装时`git clone`速度太慢，可以手动修改ycm.sh中的git repo地址（脚本注释中提供了国内源）

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

### 本地Docker服务项目管理

- [docker_manager](./local_bin/docker_manager)

方便一堆用Docker容器需要管理的场景<br>
配合cron使用


## 效果截图

- i3 desktop:
    ![](https://raw.githubusercontent.com/Karmenzind/i/master/dotfiles-and-scripts/float.png)
    ![](https://raw.githubusercontent.com/Karmenzind/i/master/dotfiles-and-scripts/desktop.png)

- Vim:
    ![](https://raw.githubusercontent.com/Karmenzind/i/master/dotfiles-and-scripts/vim.png)
    ![](https://raw.githubusercontent.com/Karmenzind/i/master/dotfiles-and-scripts/vim_goyo.png)

## 创建你自己的DotFile仓库

以下两个脚本用来在系统中直接应用本repo中的配置文件

[symlink.py](./symlink.py) 以创建软连接的方式（推荐）
[do_synch.py](./do_synch.py) 以复制（支持双向）的方式来更新、应用配置文件

你可以fork这个项目，然后借助上述两种方式来同步你自己的配置文件

