---
layout: ../../layouts/post.astro
title: debian13服务器初始化配置
description: 服务器初始配置
dateFormatted: Jun 6, 2025
---


## vi编辑器配置修改


在某些Debian的发行版本中，vi编辑器启用了兼容模式，就是兼容更古老版本的vi编辑器，导致无法正常按i键后输入字符，回退键也不好用，需要设置一下。

### 按i键后输入字符问题解决办法

编辑vimrc.tiny文件

```sh
vi /etc/vim/vimrc.tiny
```

打开后，将代码

```sh
set compatible
```
改为：
```sh
set nocompatible
```

因为光标和输入都不好用，反正挺费劲的。最好把光标移到准确的位置，再输入no就行了。

此时，保存后退出，再用vi打开，发现输入就正常了。

### 回退键不好用问题解决办法
如果回退键不好用，在刚才的文件中添加：
```sh
set backspace=2
```

再退出就可以了。



## ssh安装与配置

```sh
--1.安装ssh服务
sudo apt install openssh-sercer

--2.启动ssh服务
sudo systemctl start ssh

--3.设置ssh服务开机自启
sudo systemctl enable ssh

--4.查看ssh状态
sudo systemctl status ssh
```



## 配置静态ip

```sh
--1.查询网卡名称(ens33)
ip a

--2.编辑网卡配置文件
vi /etc/network/interfaces

--3.配置文件

配置文件中之前的配置保持不变，将dhcp修改为static
新增配置如下
address 192.168.2.237
netmask 255.255.255.0
gateway 192.168.2.1
```

## 设置dns

```sh
-- debian13安装后默认没有/etc/resolv.conf
vi /etc/resolv.conf

--添加以下内容
nameserver 8.8.8.8
```





## 更换系统软件源

### 修改软件源

方法1:手动配置

```sh
--1.修改源
vi /etc/apt/sources.list
--2.导入中科大源

deb https://mirrors.ustc.edu.cn/debian/ bookworm main non-free non-free-firmware contrib
deb-src https://mirrors.ustc.edu.cn/debian/ bookworm main non-free non-free-firmware contrib
deb https://mirrors.ustc.edu.cn/debian-security/ bookworm-security main
deb-src https://mirrors.ustc.edu.cn/debian-security/ bookworm-security main
deb https://mirrors.ustc.edu.cn/debian/ bookworm-updates main non-free non-free-firmware contrib
deb-src https://mirrors.ustc.edu.cn/debian/ bookworm-updates main non-free non-free-firmware contrib
deb https://mirrors.ustc.edu.cn/debian/ bookworm-backports main non-free non-free-firmware contrib
deb-src https://mirrors.ustc.edu.cn/debian/ bookworm-backports main non-free non-free-firmware contrib
```



方法2：脚本安装（linuxmirrors.cn 网站脚本）

```sh
bash <(curl -sSL https://linuxmirrors.cn/main.sh)
```



## docker安装与配置

### 安装（linuxmirrors.cn 网站脚本）

```sh
bash <(curl -sSL https://linuxmirrors.cn/docker.sh)
```

### 配置

添加用户至 docker 用户组以无 root 权限的方式使用 docker，Docker daemon 绑定的是 Unix socket，这就导致 docker 需要 root 权限才能使用，但这十分麻烦，因为其他用户必须经常使用 `sudo`。为此，docker daemon 创建 Unix socket 时，会允许所有 `docker` 组内成员访问，所以我们只需要将用户加入 `docker` 组内就可以避免使用 `sudo`。

1. 创建 `docker` 组：

   ```sh
   sudo groupadd docker
   ```

   

2. 将用户加入 `docker` 组内：

   ```sh
   sudo usermod -aG docker $USER`
   ```

   

3. 重新登录


