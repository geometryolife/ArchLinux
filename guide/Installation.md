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

`wifi-menu` WiFi 模块

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

### 获取安装镜像文件

访问 [下载](https://archlinux.org/download/) 页面，并根据需要的引导方式，获
取 ISO 文件或 netboot 镜像以及相应的 [GnuPG](https://wiki.archlinux.org/title/GnuPG) 签名。

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
pacstrap /mnt/ base base-devel vim 
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
vim /etc/fstab
```

8. 在新系统上安装一些软件
```Linux
# 网络管理工具
pacman -S networkmanager

# 启动引导程序
pacman -S grub
```

9. 开启网络管理工具 networkmanager
```Linux
systemctl enable NetworkManager
```

10. 给设备安装合适的引导程序以引导系统
```Linux
grub-install --target=i386-pc /dev/sdb

grub-mkconfig -o /boot/grub/grub.cfg
```

11. 修改 root 用户密码
```Linux
passwd
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

13. 设置地区时间
```Linux
ls /usr/share/zoneinfo
ls /etc/localtime

ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

14. 设置主机名
```Linux
/etc/hostname
```

15. 卸载
```Linux
umount -R /mnt
```










### 杂
pgrep ffmpeg


