---
title: Ubuntu 手工添加 Swap 分区
date: 2022-10-02 18:56:43
tags:
  - Ubuntu
  - swap分区
---

# 本文是在 Ubuntu 中手工添加 Swap 分区的教程。

## 准备工作

首先，检查你的系统是否已经有 Swap 分区：



```bash
swapon -s
```

或



```bash
free -m
```

如果没有返回结果或者 `free -m` 中 `Swap` 一列数值是 `0`，则表示你的系统没有 Swap 分区。

## 创建 SWAP 分区

我们可以使用 `fallocate` 命令创建一个 1GB 大小的 Swap 分区：



```bash
fallocate -l 1G /swapfile
```

如果这个命令无法使用，请安装 `util-linux` 包：



```bash
apt install util-linux
```

然后设置这个文件的权限：



```bash
chmod 600 /swapfile
```

然后激活 SWAP 分区



```bash
mkswap /swapfile
swapon /swapfile
```

此时，你可以使用 `swapon -s` 或 `free -m` 命令查看 Swap 分区是否已经激活。

## 设置开机自启

我们需要编辑 `/etc/fstab` 这个文件，加入下面的内容即可：



```bash
echo "/swapfile swap swap defaults 0 0" >> /etc/fstab
```

大功告成，使用 `free -m` 命令查看 Swap 分区是否正确：

![image1.png](https://note.zhangyirui.cn/images/Ubuntu-manually-add-Swap-partition/image1.png)

## 调整系统内核 Swappiness 值

Swapiness 是 Linux 内核的一个属性，定义了系统使用交换空间的频率，Swapiness 的值在 0 到 100 之间 (默认是 60)，一个低的值会使内核尽可能地避免交换，而一个高的值会使内核更积极地使用交换空间。

这个值默认是 `60`，我们可以使用 `cat /proc/sys/vm/swappiness` 命令查看当前值。

一般我们可以给他改成 `10`：



```bash
echo "vm.swappiness=10" >> /etc/sysctl.conf
```

然后使用 `sysctl -p` 命令使其生效。

## 关闭 Swap

有时候我们需要关闭 Swap 分区，可以使用下面的命令：

首先，停用 Swap 分区：



```bash
swapoff -v /swapfile
```

然后检查 `/etc/fstab`，删除 `/swapfile swap swap defaults 0 0` 这一行。

最后删除 `/swapfile` 这个文件：



```bash
rm /swapfile
```
