# [安装指南](https://wiki.archlinux.org/title/Installation_guide)

## 基本命令(工具)

`lsblk` 查看挂载设备

`dd` - convert and copy a file 转换和复制文件

```Linux
dd if=path/to/file/file.iso of=/dev/sdb status="progress"
```

```Linux
packer arch linux scripts
```

```Linux
ls /sys/firmware/efi/efivars
```

`ping` 检查网络是否能够正常上网

~`wifi-menu` WiFi 模块~

`iwd` 包提供无线网络连接的相关工具，`iwctl` 是其客户端程序。

也可以使用 `networkmanager` 的 `nmtui`

```shell
# 进入 iwd
$ iwctl

# 列出 Wi-Fi 设备（无线网卡）
[idw]# device list

# 使用 station <DEVICE> scan 来扫描网络，以 wlan0 设备为例
[idw]# station wlan0 scan

# 列出可用网络
[idw]# station wlan0 get-networks

# 连接网络
[idw]# station wlan0 connect SSID
```

更新 Pacman 镜像源列表

```shell
reflector --country China --age 24 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

```Linux
timedatectl set-ntp true
```

`fdisk` 分区工具

```Linux fdisk
p: print the partition table 打印分区表
m: m for help (print this menu)
d: delete a partition 删除一个分区
n: add a new partition 添加一个新分区
w: write table to disk and exit 将表写入磁盘并退出
```

`pacstrap` Arch Linux 自带的安装脚本

```Linux
# 如何查看 fstab
vim /etc/fstab
```

`genfstab` 

## 安装前的准备

### 1. 获取安装镜像文件

访问 [下载](https://archlinux.org/download/) 页面，并根据需要的引导方式，获
取 ISO 文件或 netboot 镜像以及相应的 [GnuPG](https://wiki.archlinux.org/title/GnuPG) 签名。

### 2. 验证签名(非 [archlinux.org](https://archlinux.org/download/) 站点下载镜像应尽量验证)
建议使用前先验证所下载文件的签名，特别是从 *HTTP* 镜像源下载的文件，因为 HTTP 连
接一般来说容易遭到拦截而提供恶意镜像。在一台已经安装[GnuPG](https://wiki.archlinux.org/title/GnuPG) 的
系统上，可通过下载 *PGP* 签名到 ISO 文件所在的路径，然后用以下方式 [验证](https://wiki.archlinux.org/title/GnuPG#Verify_a_signature):
```Linux
gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig

# 在一台已经安装 Arch Linux 的计算机上可以通过以下方式验证:
pacman-key -v archlinux-version-x86_64.iso.sig
```

### 3. 准备安装镜像(制作启动盘)
安装映像可以通过 [USB 闪存盘](https://wiki.archlinux.org/title/USB_flash_installation_medium)、
[光盘](https://wiki.archlinux.org/title/Optical_disc_drive#Burning)和带
[PXE](https://wiki.archlinux.org/title/PXE) 的网络提供给目标机器：请按照合适的文章，使用所选映像为自己准备安装介质。

- 使用 [balenaEtcher](https://www.balena.io/etcher/) 制作 USB 启动盘

### 4. 启动实时(Live)环境
**注意:** Arch Linux 安装镜像不支持安全启动（Secure Boot）。要引导安装媒介，需
要[禁用安全启动](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Disabling_Secure_Boot)。
如果需要，可在完成安装后重新配置 [安全启动](https://wiki.archlinux.org/title/Secure_Boot)。

1. 选择从带有 Arch 安装文件的媒介启动，通常是要在 计算机开机自检 时按下某个按键，一般在启动画面会有提示。
具体情况请参考主板说明书。
2. 当引导加载程序菜单出现时，选择 Arch Linux install medium 并按 Enter 进入安装环境。
**提示:** 安装映像使用 systemd-boot 在 UEFI 模式下引导，使用 syslinux 在 BIOS 模式下引导。请参阅 README.bootparams 获取 引导参数 列表。
3. 您将会以 root 身份登录进一个虚拟控制台，默认的 Shell 是 Zsh。
4. 如果想一边安装，一边使用 Lynx 查看本指南，可以使用 Alt+箭头 快捷键切换不同的控制台。
要编辑配置文件，可以使用 mcedit(1)、nano 和 vim。请参阅 packages.x86_64 获取安装介质中包含的软件包列表。

### 5. 设置 Live 环境选项
#### 屏幕显示(分辨率调节)
```Linux
# 根据 live 界面的提示，按下相应的按键编辑选项
nomodeset video=800x450
```

#### 控制台字体
控制台字体 位于 /usr/share/kbd/consolefonts/ 目录中，设置方式请参考 setfont(8)。
```Linux
setfont /usr/share/kbd/consolefonts/LatGrkCyr-12x22.psfu.gz
```

#### 键盘布局
控制台键盘布局默认为 us（美式键盘映射）。列出所有可用的键盘布局，可以使用：

```Linux
ls /usr/share/kbd/keymaps/**/*.map.gz
```

```Linux
更改键盘布局配置文件
vim keys.conf

# 交换左 CTRL 和 CapsLock
keycode 58 = Control
keycode 29 = Caps_Lock

内容
keycode 1 = Caps_Lock
keycode 58 = Escape

加载配置
loadkeys keys.conf

vim 配置文件
syntax on
noremap H 0
noremap J 5j
noremap K 5k
noremap L $
noremap ; :
noremap S :w<CR>
noremap Q :q<CR>
```

如果您想要更改键盘布局，可以将一致的文件名添加进 loadkeys(1)，但请省略路径和扩展名。
比如，要添加 German 键盘布局：
```Linux
loadkeys de-latin1
loadkeys colemak
```

### 6. 验证启动模式
要验证启动模式，请用下列命令列出 efivars 目录：
```Linux
ls /sys/firmware/efi/efivars
```

如果命令没有错误地显示了目录，则系统以 UEFI 模式启动。 如果目录不存在，系统可能
以 BIOS 模式 (或 CSM 模式) 启动。如果系统未以您想要的模式引导启动，请参考您的主板手册。

### 连接到因特网



```Linux
ip link
ip link set wlan0(网卡名) up
ip link

# 扫描存在的 WiFi
iwlist wlan0 scan
iwlist wlan0 scan | grep ESSID
iwlist wlan0 scan | grep WiFi-name

wpa_passphrase 网络 密码 > 文件名
wpa_supplicant -c internet.conf(配置文件名) -i wlan0 &
ping 无法连通，因为没有分配 IP 地址
动态分配 IP 地址
dhcpcd &
此时可以 ping 通了

更正系统时间
timedatectl set-ntp true
```

```Linux
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
swapon /dev/sad3
```

滚动更新

修改 pacman 的下载源
```Linux
vim /etc/pacman.conf

去掉 Color 批注

找到 [community]
Include = /etc/pacman.d/mirrorlist
进入文件

录制宏
/^\n
/China
Shift-v 选中， d
gg
/^\n
p
结束录制宏
```

使用宏


## 操作流程

1. `lsblk` 查看设备情况，设备的标识名。
2. `fdisk` 对要安装系统的设备进行分区。
- 删除分区
- 新建分区
	- 新建主分区1作为 `boot` (启动)分区。
	- 新建主分区2作为 `swap` (交换)分区，如果内存比较大，根据实际需求设置；
如果内存小的，建议设置为两倍内存，如: 内存为 2GB，则设置交换分区空间为 4GB。
	- 新建主分区3作为 `/` (根)分区，根据个人实际需求，我设置 30GB。
	- 新建主分区4作为 `home` (主)分区，我设置为剩余空间。
- 查看刚才新建好的分区表，确认分区是否正确。
- 将分区表写入磁盘并退出。
3. 格式化分区
```Linux
mkfs.ext4 /dev/sdb1
mkfs.ext4 /dev/sdb3
mkfs.ext4 /dev/sdb4
mkswap /dev/sdb2
# 开启交换分区
swapon /dev/sdb2
```
4. 挂载分区
```Linux
# 挂载设备的根分区到一个工作目录(此处为 /mnt)
mount /dev/sdb3 /mnt

# 查看是否挂载成功
ls /mnt
lsblk

# 在挂载了根分区的工作目录下创建一个 home 目录
mkdir /mnt/home

# 在挂载了根分区的工作目录下创建一个 boot 目录
mkdir /mnt/boot

# 挂载设备的启动分区到工作目录下的 boot 目录(此处为 /mnt/boot)
mount /dev/sdb1 /mnt/boot

# 挂载设备的主分区到工作目录下的 home 目录(此处为 /mnt/home)
mount /dev/sdb4 /mnt/home

# 检查是否挂载正确
lsblk
```
5. 使用 pacstrap 脚本给系统安装一些基本工具
```Linux
pacstrap /mnt base base-devel vim linux linux-firmware
```

6. 生成文件系统
```Linux
genfstab /mnt
genfstab -U /mnt
genfstab -U /mnt >> /mnt/etc/fstab

# 查看生成的 UUID 是否正确
vim /mnt/etc/fstab
```

7. 通过 `arch-chroot` 命令切换到安装的系统。
```Linux
arch-chroot /mnt
vim /etc/fstab
```

8. 时区(设置地区时间)
```Linux
ls /usr/share/zoneinfo
ls /etc/localtime

ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

或 timedatectl set-timezone Asia/Shanghai

同步系统时间
hwclock --systohc
```

12. 本地化设置
```Linux
vim /etc/locale.gen

en_US.UTF-8 UTF-8

# 生成本地化设置
locale-gen

# 设置语言
vim /etc/locale.conf

LANG=en-US.UTF-8
```

13. 设置主机名
```Linux
/etc/hostname

# hosts 配置
127.0.0.1    localhost
::1          localhost
127.0.1.1    mx.localdomain  mx
```

9. 在新系统上安装一些软件
```Linux
# 网络管理工具
pacman -S networkmanager

# 启动引导程序
pacman -S grub

pacman -S neovim zsh wpa_supplicant dhcpcd
```

10. 开启网络管理工具 networkmanager
```Linux
systemctl enable NetworkManager
```

10. 给设备安装合适的引导程序以引导系统
在启用多系统的电脑时，需要一个引导程序选择启动哪一个系统
```Linux
pacman -S grub efibootmgr intel-ucode(为电脑的CPU更新一些驱动) os-prober(寻找其他系统)

uname -m

mbr 分区 BIOS
grub-install --target=i386-pc /dev/sdb(整个磁盘)

grub-mkconfig -o /boot/grub/grub.cfg

uefi 
mkdir /boot/grub
grub-install --target=x86_64-efi --efi-directory=/boot
```

11. 修改 Grub 配置

```shell
$ vi /etc/default/grub

# 不显示 GRUB 页面
# GRUB_TIMEOUT=5 -> 0

# 删除 quiet 显示登陆日志
# GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3"
```

更新后需要使用 `grub-mkconfig` 重新生成配置

```shell
# grub-mkconfig -o /boot/grub/grub.cfg
```

12. 修改 root 用户密码
```Linux
passwd
```


15. 卸载
```Linux
umount -R /mnt
```



### 修改配置文件

```shell
$ cd /etc/skel

# 一些 shell 的配置文件

export EDITOR=vim

# 让 grep 输出有颜色
alias grep='grep --color=auto'
alias egrep='egrep --color=auto' => -e 选项
alias fgrep='fgrep --color=auto' => -F 选项

# 文件与文件夹也有颜色
# 如果每个用户的主目录下没有 .dircolors 则运行下面的命令将 dircolors 打印到 ~/.dircolors
[ ! -e ~/.dircolors ] && eval $(dircolors -p > ~/.dircolors)
# 若 /bin/dircolors 存在，则将其预设应用到 ~/.dircolors
[ -e /bin/dircolors ] && eval $(dircolors -b ~/.dircolors)
```

保留原文件属性的前提下复制文件，到 root 主目录 `cp -a . ~`

### 安装字体

只装文泉驿相关的也基本可以了

```shell
# Xorg 经常使用到的字体
# 英文
$ sudo pacman -S ttf-dejavu ttf-droid ttf-hack ttf-font-awesome otf-font-awesome ttf-lato ttf-liberation ttf-linux-libertine ttf-opensans ttf-roboto ttf-ubuntu-font-family
# 中文
$ sudo pacman -S ttf-hannom noto-fonts noto-fonts-extra noto-fonts-emoji noto-fonts-cjk adobe-source-code-pro-fonts adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-hk-fonts adobe-source-han-sans-tw-fonts adobe-source-han-serif-cn-fonts wqy-zenhei wqy-microhei
```

### 文字渲染

可选

```shell
# 启用 freetype2 渲染引擎，渲染出媲美 MacOS 的字体效果
$ vim /etc/profile.d/freetype2.sh
```

### 显卡驱动

核显在 CPU 中，集显在主板上

AMD

```shell
# 2D
$ sudo pacman -S xf86-video-amdgpu xf86-video-ati
# 3D
$ sudo pacman -S mesa vulkan-radeon
```

Intel

```shell
$ sudo pacman -S xf86-video-intel vulkan-intel mesa
```

NVIDIA

```shell
$ sudo pacman -S nvidia nvidia-settings nvidia-utils
```

### 桌面环境

```shell
$ sudo pacman -S gnome gnome-extra gdm

# 设置登录管理器开机自启
$ systemctl enable gdm.service
```

### 声音

Alsa 是 OSS 的继任，Arch 自带，但需要安装一些配置工具，以及代理工具、组件、打印机

```shell
pacman -S alsa-utils pulseaudio pulseaudio-bluetooth cups
```

清空安装软件时，留下的缓存、源

```shell
pacman -Scc
```

### AUR Helpers

```shell
git clone https://aur.archlinux.org/paru.git

cd paru
# makepkg 配置文件
sudo vim /etc/makepkg.conf
# 修改
MAKEFLAGS="-j$(nproc)"

# paru 安装成功后，修改配置，让搜索结果从下往上
BottomUp
```

### 输入法

```shell
sudo pacman -S fcitx5-im fcitx5-chinese-addons fcitx5-material-color

# 主题
sudo pacman -S fcitx5-nord
# paru fcitx5 ...
```

字库安装：云字库 & 离线字库

```shell
sudo pacman -S fcitx5-pinyin-moegirl fcitx5-pinyin-zhwiki
```






### 杂
pgrep ffmpeg


### 改键位

直接修改 scancode -> keycode 映射表

- [Linux通用键位修改（上）-理论基础](https://b23.tv/mI8vzPD)
- [Linux通用键位修改（中）-实际操作](https://b23.tv/b5EqUFc)

- 工具
  - evtest（查看某个按键的具体 scancode）
  - udevadm（更新自定义配置，查看配置内容，集成在 systemd 中）
  - evemu 提供的工具 evemu-describe，查看设备信息

- 文件
  - `/etc/udev/hwdb.d/` 用户自定义硬件数据库存放位置
  - `/usr/lib/udev/hwdb.d/` 默认的硬件数据库位置。

```
evdev:atkbd:dmi:*
 KEYBOARD_KEY_3a=leftctrl    # bind leftctrl to capslock
 KEYBOARD_KEY_1d=capslock    # bind capslock to leftctrl
```

第一条命令是重新编译二进制的数据库内容，第二条命令表示立刻重新触发所有输入设备，让更改立刻生效：

```shell
# udevadm hwdb --update (已废弃)
systemd-hwdb update
udevadm trigger
```
