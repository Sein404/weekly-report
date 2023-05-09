# arch Linux安装指南

> 本文配图尚未完成,自己装的时候没想着要写教程,等我帮别人装完linux再配图吧()

这是一篇基于我的安装经验所写的用`EFI`引导的电脑安装`Arch Linux`（双系统）的中文简明教程，首先感谢鲨鱼姐姐的这篇<a href="https://github.com/JunkFood02/Arch-Linux-Installation-Guide">教程</a>。~~（感觉完全在照搬）~~

鉴于我在安装中遇到的一些痛苦的问题，我会尽可能的在每一步中写下补救措施以供参考。~~如果出错建议直接重装~~

> 说明：下文步骤中如遇难以解决的bug建议查看官方指南或者Google，学习使用工具解决问题也是一项非常重要的技能。

## Arch Linux难点

- 采用命令行安装，安装过程较为繁琐

- 操作时遇到问题较难解决，有些问题哪怕你去Linux社区也难以解决
- 安装教程大多为英文，对于英文基础弱或不适应英文环境的人很不友好

## 安装前准备

1. 大于100G的硬盘空余空间
2. 一块储存空间至少为4G的U盘
3. 稳定，而不需要额外进行设备认证的有线/无线网络连接，如手机热点等。~~(校园网无感认证似乎也可以)~~
4. 至少三小时左右的时间乃至数星期的空余时间
5. 强大的心理素质
6. ~~找大佬带~~

## 安装介质准备

> 默认你使用的windows系统，如果你是Mac系统请自行搜索教程

1. 下载最新的`Arch Linux`系统镜像，在<a href="https://archlinux.org/download/">下载地址</a>找到China的镜像源，如`tuna.tsinghua.edu.cn`下载`iso`镜像文件。

   ![image-20230509163519705](https://typora-1318125061.cos.ap-nanjing.myqcloud.com/typora/202305091635953.png)

2. 下载ventoy(一款U盘安装软件)

3. 打开后选择U盘安装

   ![image-20230509163344927](https://typora-1318125061.cos.ap-nanjing.myqcloud.com/typora/202305091633330.png)

4. 将`iso`文件复制到U盘中

## 磁盘分区

> 任何有关磁盘的操作请务必小心谨慎，数据无价。如果你用`Bitlocker`等工具对磁盘进行了锁定，请先解除

- 在windows下空出一块分区进行安装，使用Windows自带的管理工具即可

1. 右键windows图标，在菜单中选择磁盘管理（此处默认为`win11`）

   ![](https://typora-1318125061.cos.ap-nanjing.myqcloud.com/typora/202305062320115.png)

2. 右击想要删除的分区，选择删除卷（注意提前备份其中的有用数据）

> 空闲的磁盘（新磁盘）：无需进行任何操作。

不要在这里进行创建新分区的操作，进入Linux安装界面后会通过命令行完成

## BIOS设置

不同品牌的主板进入BIOS方法不同，请自行搜索

（首先忏悔我买了七彩虹将星BIOS非常难用）

然后按照以下步骤进行准备工作

1. 关闭`Secure Boot`，`Arch Linux`无法使用这个启动，你可以在安装完成后启用此功能
2. 某些品牌（如戴尔）可能默认在非windows系统关闭网卡，在设置中启用`Enable UEFI Network Stack`

## 开始安装Arch Linux

在完成以上设置之后，重启电脑，我们会进入Arch Linux的页面，选择第一个进入安装程序。加载完成进入命令行界面，恭喜你，希望接下来你预留了足够的时间在命令行界面折腾。

![image-20230509163551405](https://typora-1318125061.cos.ap-nanjing.myqcloud.com/typora/202305091636546.png)

> 接下来的你将正式接触命令行页面，其中可能会有各种各样的bug。同样饱受debug之苦，我会尽可能的诠释每一步的原理方便随时debug。

## 一些命令行的基础操作

学一些命令行的基础操作可以让你的操作轻松一些

|       按键       | 作用                   |
| :--------------: | ---------------------- |
|      `Tab`       | 自动补全当前命令       |
|     `Ctrl+C`     | 中止当前正在执行的命令 |
| `↑` `（Ctrl+P）` | 显示上一条命令         |
| `↓` `（Ctrl+N）` | 显示下一条命令         |

## 连接网络和更新系统时间

解除可能存在的软硬件锁定

```
rfkill unblock all
```

#### 有线网连接

接入网线后，执行命令进行连接

```
dhcpcd
```

测试网络是否接通

```
ping www.baidu.com
```

> 确认连通后此处可以使用`Ctrl+C`来中止该命令

#### 无线网连接

列出当前网络设备

```
ip link show
```

> 无线网卡默认名称一般为`wlan0`，以下命令中的`wlan0`请替换为你的网卡名

若状态为`down`，需要将其设置为`up`

```
ip link set wlan0 up
```

执行下列命令进入`[iwd]`开头的网络操作环境

```
iwctl
```

接着列出当前可用的所有网卡设备

```
station wlan0 scan
```

执行下列命令列出扫描到的网络

```
station wlan0 get-networks
```

最后输入下列命令连接指定网络，然后输入密码

> `Wifi-SSID`请替换为你想要连接的网络,如果有空格请用引号如"wifi ssid"
>
>  `wifi`名称请不要带中文
>
> 在命令行页面密码不会显示请注意

```
station wlan0 connect Wifi-SSID
```

使用`quit` 退出`iwc`，然后测试网络是否接通

```
ping www.baidu.com
```

#### 设置系统时间

```
timedatectl set-ntp true
```

> 请区分好`l`（字母）和`1`（数字），不然会变得不幸

## 分区与格式化

> <b>特别注意：一切涉及到硬盘的操作请勿必格外注意，在按下回车前请仔细检查自己是否存在错误，否则会造成数据丢失</b>

 #### 查看当前分区状况

```
fdisk -l
```

![image-20230509164005352](https://typora-1318125061.cos.ap-nanjing.myqcloud.com/typora/202305091640521.png)

可以看到这里会显示你的所有硬盘及硬盘，形如`/dev/nvme0n1`（请以自己的设备为准）

- 如果你要在已有硬盘中安装，你会发现在硬盘中（安装windows的硬盘）发现一个类型为`EFI`的分区（请排除U盘中可能存在的`EFI`分区），这是你的引导分区，请记下路径（`/dev/nvme0n1p1`）

- 如果你要在新硬盘中安装，请执行以下命令

  1. 创建一个引导分区(将nvme0n1替换为你要操作的磁盘)

     ```
     fdisk /dev/nvme0n1
     ```

  2. 接下来你进入了`fdisk`的操作环境（可以输入`m`来查看指南）

     1. **如果你是一块全新的硬盘**：输入`g`来创建一个全新的gpt分区表，**否则直接进行第2步**。

     2. 输入`n`创建一个新的分区，首先会让你选择起始扇区，一般直接回车使用默认数值即可，然后可以输入结束扇区或是分区大小，这里我们输入`+512M`来创建一个`512M`的引导分区。

     3. 这时我们可以输入`p`来查看新创建的分区。

     4. 输入`t`并选择新创建的分区序号来更改分区的类型，输入`l`可以查看所有支持的类型，输入`ef`更改分区的类型为`EFI`。

     5. 输入`w`来将之前所有的操作写入磁盘生效，在这之前可以输入`p`来确认自己的分区表没有错误。

     6. 输入以下命令来格式化刚刚创建的引导分区(请将nvme0n1p1替换为刚创建的EFI分区)

        ```
        mkfs.fat -F32 /dev/nvme0n1p1
        ```

- 创建分区结束

#### 创建根分区

输入以下命令进入磁盘操作

```
fdisk /dev/nvme0n1
```

1. 输入n创建一个新的分区,首先选择起始扇区(回车默认即可),然后输入结束扇区(形如`121231`)或分区大小(形如`+200G`),如果想使分区大小占满剩余空间,可以直接回车默认.

2. 输入`p`查看新创建的分区,如有错误可以直接输入`q`退出重来确认无误后,

3. 输入`w`来将之前所有的操作写入磁盘生效

4. 输入以下命令格式化刚刚创建的根分区(将nvme0n1p2替换为你创建的根目录)

   ```
   mkfs.ext4 /dev/nvme0n1p2 
   ```

## 挂载分区

执行以下命令将根分区挂载到`/mnt`

```
mount /dev/nvme0n1p2 /mnt
```

执行以下命令将引导文件挂载到上面

```
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
```

> 挂载出错可以`↑`会到挂载命令并将`mount`改成`umount`取消挂载

## vim学习

在接下来的操作中将会用到`vim`,学习<a href=http://coolshell.cn/articles/5426.html>这篇教程</a>中的存活部分作为中场休息吧

## 选择镜像源

镜像源是我们下载软件包的来源,选择一个国内的镜像源将大幅提升你的下载速度



执行以下命令,进入`vim`来编辑镜像源文件

```
vim /etc/pacman.d/mirrorlist
```

找到标有China的镜像源,将其剪切粘贴到第一行,或者直接手输

推荐清华和浙大的镜像源

```
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
```

最后按`Esc`回到normal模式后按`:wq`保存并推出

> 出问题了可以直接:q不保存退出后重进

## 安装基本包

接下来要安装`archlinux`基本包到到磁盘上.

```
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim reflector
```

接下来不断按`enter`就是了,~~反正你也看不懂这些~~

## 生成Fstab文件

Fstab文件用于自动挂载分区,输入下列命令生成

```
genfstab -L /mnt >> /mnt/etc/fstab
```

请输入下列命令检查生成是否正确

```
cat /mnt/etc/fstab
```

如果前面的挂载操作没有出错，应该输出且 **仅输出** 两条记录：（以你的磁盘分区情况为准）

- 根分区 `/` 被挂载到了此前建立的 **数据分区** `/dev/nvme0n1p2`，分区的文件系统为 `ext4`
- 引导分区 `/boot` 被挂载到了 **硬盘已有的 EFI 系统分区** `/dev/nvme0n1p1`，分区的文件系统为 `vfat`

如果`fatab`文件有任何错误,请输入下列命令删除并重复上述步骤

```
rm -rf /mnt/etc/fstab
```

## 系统的必要安装

#### Chroot

chroot(即change root),相当于进入新安装的linux系统,执行这一步之后,之后的操作都相当于在新装的系统中进行.

```
arch-chroot /mnt
```

![image-20230509163659325](https://typora-1318125061.cos.ap-nanjing.myqcloud.com/typora/202305091637879.png)

> 顺带一提，如果以后系统出现了问题，只要插入任意一个安装有 Arch Linux 镜像的 U 盘并启动，将我们的系统根分区挂载到 `/mnt` 下、EFI 系统分区挂载到 `/mnt/boot` 下，再通过这条命令就可以进入我们的系统进行修复操作。~~(救不了的话重装最快)~~

#### 开启pacman并行下载

使用vim打开pacman,开启怕pacman的并行下载功能(这里退出要强制使用`:wq!`)

```
vim /etc/pacman.conf
```

然后找到`ParallelDownloads = 5`这一行将其取消注释[^1],`5`可以调节为你想要的数值

> 盲目多开并不会提高效率,默认5即可.~~网速够快当我没说~~

[^1]:指将这一行前面的#删除

<<<<<<< HEAD
=======
运行命令以配置 `pacman` 所使用的镜像源，`Reflector` 会自动帮我们配置位于 China 的下载速度最快的镜像源

```
reflector --country China --sort rate --latest 5 --save /etc/pacman.d/mirrorlist
```

>>>>>>> 419b8ffef54476b4ea49653569b26d040771ccb3
运行下列代码安装必需软件包

```
pacman -S dialog wpa_supplicant ntfs-3g networkmanager netctl git
```

#### 设置时区,地区和语言信息

依次执行如下命令设置我们的时区为上海，并生成相关文件

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

执行如下命令，设置我们使用的语言选项

```
vim /etc/locale.gen
```

在文件中找到 `en_US.UTF-8 UTF-8`、`zh_CN.UTF-8 UTF-8` 、`zh_HK.UTF-8 UTF-8` 及 `zh_TW.UTF-8 UTF-8` 这四行，去掉行首的 # 号，保存并退出。

执行如下命令，系统会生成我们需要的本地化文件

```
locale-gen
```

打开（不存在时会创建）`/etc/locale.conf`文件：

```
vim /etc/locale.conf
```

在文件的第一行加入以下内容

```
LANG=en_US.UTF-8
```

保存并退出

### 设置主机名

打开（不存在时会创建）`/etc/hostname` 文件：

```
vim /etc/hostname
```

在文件的第一行输入你自己设定的一个 `myhostname`，这将会是你的 **计算机名**，保存并退出。

打开（不存在时会创建）`/etc/hosts` 文件：

```
vim /etc/hosts
```

在文件末添加如下内容（将 `myhostname` 替换成你自己设定的主机名），保存并退出。

```
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```

### 设置 Root 密码

`root` 账户是 `Linux` 系统中的最高权限账户，需要设置密码保护起来，以免无意间实施了破坏性的敏感操作。

```
passwd
```

### 新建用户与配置 sudo

> 关于这一步操作的说明，可以查看 [教程](https://www.viseator.com/2017/05/19/arch_setup/#新建用户)

请自行替换 `username` 为你想要使用的用户名

```
useradd -m -G wheel username
passwd username
```

为了在普通用户下使用 root 操作，需要配置 `sudo`

```
pacman -S sudo
vim /etc/sudoers
```

找到 `# %wheel ALL=(ALL:ALL) ALL`，取消注释并保存退出。

### 安装处理器微码

显然你应该根据你电脑的 CPU 型号选取一个包进行安装(英特尔还是amd)

```
pacman -S intel-ucode
pacman -S amd-ucode
```

## 配置系统引导

> <b>不要轻易的调整grub,会变得不幸</b>

先安装`grub`的必需包

```
pacman -S os-prober ntfs-3g grub efibootmgr
```

使用vim启用os-prober

```
vim /etc/default/grub
```

取消注释这一行

```
# GRUB_DISABLE_OS_PROBER=false
```

部署`grub`

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
```

生成配置文件

```
grub-mkconfig -o /boot/grub/grub.cfg
```

检查一下文件末尾的`menuenrtry`是否有Arch Linux入口

```
vim /boot/grub/grub.cfg
```

## 创建交换文件

交换文件可以在物理内存不足的时候将部分内存暂存到交换文件中，避免系统由于内存不足而完全停止工作。之前通常采用单独一个分区的方式作为交换分区，现在更推荐采用交换文件的方式，更便于我们的管理。分配一块空间用于交换文件，执行：

```
dd if=/dev/zero of=/swapfile bs=1M count=8192 status=progress
```

将 `8192` 换成需要的大小，单位 Mb，一般与计算机 RAM 大小一致即可。

更改权限，执行：

```
chmod 600 /swapfile
```

设置交换文件，执行：

```
mkswap /swapfile
```

启用交换文件，执行：

```
swapon /swapfile
```

最后我们需要编辑 `/etc/fstab` 为交换文件设置一个入口，使用 `Vim` 打开文件：

```
vim /etc/fstab
```

**注意编辑 `fstab` 文件的时候要格外注意不要修改之前的内容，直接在最后新起一行加入以下内容**：

```
/swapfile none swap defaults 0 0
```

安装图形化界面

安装 Xorg 图形服务

```
pacman -S xorg
```

这里选择安装的是`Gnome`,如果有其他需求请自行查找

安装 GNOME

```
pacman -S gnome gdm
```

设置 gdm 开机启动

```
systemctl enable gdm
```

### 网络服务

启用适用于桌面环境的网络服务 `NetworkManager`

```
systemctl disable netctl
systemctl enable NetworkManager
```

然后`exit`退出磁盘,`reboot`之后就可以进入图形界面了

## 安装后配置与提示

### 社区支持

Arch Linux 有完善的 Wiki 与活跃的社区支持，有任何问题先在 Wiki 内查找以及 Google 一下， 绝大部分问题都会找到解决方案

### 滚动更新

在使用之前，要了解 Arch Linux 使用的是激进的滚动更新策略，你的系统可以时刻保持并且也 **必须保持** 最新的内核版本与最新的软件版本。

因此在你安装软件包的时候，`pacman` 的命令应当从简单的 *安装* 变为 **安装软件包+同步软件资料库+进行全面系统更新**，也就是说：

```
sudo pacman -S package-name
```

应当改为

```
sudo pacman -Syu package-name
```

### 代理配置

如果你使用 clash 进行国际联网，可以直接下载方便使用的 [Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg)，解压后打开 `cfw` 即可

若提示依赖缺失，可以安装 gtk3，[参考](https://aur.archlinux.org/packages/clash-for-windows-bin)

```
sudo pacman -Syu gtk3
```

- 在 `System Settings` 中搜索 `proxy`，手动将代理地址设置为 `127.0.0.1`，cfw 默认端口为 `7890`

- 设置 `Git` 代理：

  ```
  git config --global http.proxy "http://127.0.0.1:7890"
  ```

- 设置终端代理：将 cfw 中的命令手动粘贴到终端中回车，但只在本次对话中生效

  ```
  export https_proxy=http://127.0.0.1:7890;
  export http_proxy=http://127.0.0.1:7890;
  export all_proxy=socks5://127.0.0.1:7890
  ```

### AUR helper: yay

Arch Linux 除了官方源之外，还拥有广大社区用户维护的 **Arch 用户软件仓库**（Arch User Repository，简称 AUR）可供使用，极大丰富了 Arch Linux 的软件库，用户体验++

安装可以让我们便捷安装 AUR 包的 `yay`

```
pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

### Code - OSS or VS Code

前者开源，后者是微软基于开源项目二次开发的软件，可以类比 Chromium 与 Google Chrome

安装 Code - OSS

```
sudo pacman -Syu code
```

或者 Visual Studio Code

```
yay -Syu visual-studio-code-bin
```

装好之后可以用 code 代替 nano 或 vim 等命令行编辑器进行文本编辑

可以修改 git 的默认编辑器为 code

```
git config --global core.editor "code --wait"
```

### zsh 与 oh-my-zsh

zsh 比系统默认搭载的 bash 更好用，搭配上社区支持的项目 [oh-my-zsh](https://ohmyz.sh/)，插件和主题都很齐全

```
yay -Syu zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

- 切换默认 shell 为 zsh

```
sudo chsh -s /bin/zsh username
```

reboot 后再次打开终端，或直接运行 zsh，就能看到带颜色的默认 robbyrussell 主题 zsh 了

- 编辑 `~/.zshrc`，找到 plugins 行，加入我推荐的几个插件

```
code ~/.zshrc
plugins=(git z sudo zsh-syntax-highlighting zsh-autosuggestions)
```

其中 [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh) 与 [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh) 需要自行安装，或者可以看这个 [gist](https://gist.github.com/dogrocker/1efb8fd9427779c827058f873b94df95)

接着在 `.zshrc` 中粘贴上文代理中的三行终端命令，zsh 每次启动都会读取 `.zshrc` 初始化，即可自动化配置终端代理

### Git

随手配配能用就行

- 代理和配置编辑器在前面提到了
- [git-credential-manager](https://github.com/GitCredentialManager/git-credential-manager)：AUR 下一个，存 GitHub HTTPS 密钥用
- 给 commit 整一下 [GPG 签名](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)

### 中文字体与中文输入法

在系统语言设置内加入中文，安装中文字体与中文输入法后重启即可

```
yay -Syu noto-fonts-cjk noto-fonts-emoji
```

可以使用 fcitx4 的搜狗输入法，或在 fcitx5 的拼音输入法中导入搜狗词库，参照 [fcitx](https://wiki.archlinux.org/title/fcitx#Chinese) 与 [fcitx5](https://wiki.archlinux.org/title/fcitx5#Chinese)

安装 fcitx5 及组件，在设置中添加输入法即可，具体参照 Arch Wiki

```
yay -Syu fcitx5-im fcitx5-chinese-addons fcitx5-qt fcitx5-gtk
```

修改 `/etc/environment` 文件，在文件开头加入五行：

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
INPUT_METHOD=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```

可以解决一些软件无法调出 `fcitx` 的问题

### 蓝牙配置

参照 [Arch wiki](https://wiki.archlinux.org/title/Bluetooth#Installation)

```
yay -Syu bluez bluez-utils blueman
systemctl start bluetooth.service && systemctl enable bluetooth.service
```

### 浏览器

安装谷歌,edge，~~Edge为什么是神~~

```
yay -Syu google-chrome
yay -Syu microsoft-edge-dev-bin
yay -Syu chromium
```

### QQ 和微信

使用 `yay`安装 `AUR` 包即可

linuxqq

[deepin-wine-wechat](https://github.com/vufa/deepin-wine-wechat-arch)
