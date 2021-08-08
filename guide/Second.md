# 已经安装完 ArchLinux 开始美化

```Linux
pacman -Syyu
pacman -S man

```

## 添加用户
```Linux
useradd -m -G wheel joe
passwd joe

visudo
%wheel ALL=(ALL) ALL 去掉批注
默认使用 vi 编辑器
可以创建编辑器的软连接到 vi
ln -s /usr/bin/vim /usr/bin/vi

update-alternatives --config editor 更换默认编辑器
```


