---
title: ArchLinux
date: 2024-10-16 16:41:16
tags: linux
categories: 个人笔记
---
## 安装 archlinux

rfkill unblock 网卡名 打开wifi

<https://wk.baidu.com/view/9666b35ba16925c52cc58bd63186bceb18e8ed51?_wkts_=1678008300143&bdQuery=archlinux中文乱码解决>

[ archlinux 安装chrome浏览器\_archlinux chrome\_ITKEY\_的博客-CSDN博客 chromium虽然也可以用，但是无法同步数据，不方便。那么怎么可以安装chrome并且可以同步数据的那种呢？如何安装 yay依次运行以下命令以在 Arch Linux 系统上安装。sudo pacman -S git go base-develgit clone https://aur.archlinux.org/yay.gitcd yaymakepkg -si如何使用 yayyay 语法与 p https://blog.csdn.net/lxyoucan/article/details/124287072](https://blog.csdn.net/lxyoucan/article/details/124287072 " archlinux 安装chrome浏览器_archlinux chrome_ITKEY_的博客-CSDN博客 chromium虽然也可以用，但是无法同步数据，不方便。那么怎么可以安装chrome并且可以同步数据的那种呢？如何安装 yay依次运行以下命令以在 Arch Linux 系统上安装。sudo pacman -S git go base-develgit clone https://aur.archlinux.org/yay.gitcd yaymakepkg -si如何使用 yayyay 语法与 p https://blog.csdn.net/lxyoucan/article/details/124287072")

[新页面](https://www.wolai.com/mKAqgoyptiQdKsbKzj9oKN "新页面")

## 安装 Arch

使用最新版本的archinstall脚本安装即可

## 安装KDE Plasma桌面

```bash
sudo pacman -S --needed xorg sddm
sudo pacman -S --needed plasma kde-applications
sudo systemctl enable sddm
sudo systemctl enable NetworkManager
reboot
```

### 中文化

#### 安装中文字体，解决字库不全

```bash
sudo pacman ttf-dejavu wqy-microhei

```

#### 编辑/etc/locale.conf

```bash
LANG_ALL=zh_CN.UTF-8
```

#### 编辑/etc/locale.gen

取消注释zh\_CN.utf-8的那行，之后运行`sudo locale-gen`

#### 编辑\~/.config/plasma-localerc

把Formats下的zh\_CN 改成 zh\_CN.UTF-8，注销重启

### 更换国内源

编辑`/etc/pacman.d/mirrorlist`

#### 安装yay

```bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si  # 需要挂上代理
```

### pacman 使用

```bash
# 刷新软件包信息
sudo pacman -Sy
# 搜索软件包
pacman -Ss <package>
# 查看软件包信息
pacman -Si <package>
# 安装一个包，或者更新一个包
sudo pacman -S <package>
# 安装一个本地包
# 这个路径是软件包的缓存路径
cd /var/cache/pacman/pkg/
sudo pacman -U vlc-3.0.11-2-x86_64.pkg.tar.zst
# 更新系统
sudo pacman -Syu
# 卸载一个包以及他的依赖
sudo pacman -Rs vlc
# 卸载并删除配置文件
sudo pacman -Rns vlc
# 删除孤立的软件包
sudo pacman -Rns $(pacman -Qdtq)
# 搜索已经安装的软件包
pacman -Qs vlc
# 查看系统已经安装的所有软件包
pacman -Q
# 查看软件包所拥有的所有文件
pacman -Ql vlc
# 搜索文件是谁安装的
pacman -Qo /usr/bin/vlc
# 下载包
pacman -Sw vlc
# 清理软件包缓存/var/cache/pacman/pkg/
sudo pacman -Sc
# 删除缓存中的所有文件
sudo pacman -Scc

```

### yay 使用

```bash
# 在pacman 和 AUR上搜索
yay -Ss <package>
# 只在AUR上搜索
yay <package>
# 由AUR更新所有包
yay -Sua
# 查看软件包的占用情况
yay -Ps
# 删除不必要的依赖项目
yay -Sc
# 删除不必要的依赖项目以及所有缓存的包
yay -Scc

```

### 安装steam

[https://linuxiac.com/how-to-play-games-on-arch-linux-using-steam/](https://linuxiac.com/how-to-play-games-on-arch-linux-using-steam/ "https://linuxiac.com/how-to-play-games-on-arch-linux-using-steam/")

### 原神启动

[https://blog.fascode.net/2023/05/01/genshin-linux-proton/](https://blog.fascode.net/2023/05/01/genshin-linux-proton/ "https://blog.fascode.net/2023/05/01/genshin-linux-proton/")

### xrdp

[https://alvin.red/2021/11/06/archlinux-xrdp/](https://alvin.red/2021/11/06/archlinux-xrdp/ "https://alvin.red/2021/11/06/archlinux-xrdp/")

### 路由转发

参考文章：[https://nyac.at/posts/archlinux-router](https://nyac.at/posts/archlinux-router "https://nyac.at/posts/archlinux-router")

我的wan是enp1s0,  lan是enp2s0

##### 配置wan口

```bash
sudo vim /etc/systemd/network/20-ext-dhcp.network
```

```ini
[Match]
Name=enp1s0

[Network]
DHCP=ipv4
Ipv6AcceptRA=yes

[DHCPv4]
UseHostname=true
```

##### 配置lan口

```bash
sudo vim /etc/systemd/network/21-int.network
```

```bash
[Match]
Name=enp2s0

[Link]
Multicast=yes

[Network]
Address=10.0.0.1/16  # 分配固定ip
MulticastDNS=yes
IPMasquerade=both
IPv6SendRA=yes
DHCPv6PrefixDelegation=yes

[IPv6SendRA]
Managed=yes
OtherInformation=yes 
```

##### 开启DNS服务器

```bash
sudo vim /etc/systemd/resolved.conf.d/listen-on-internal.conf
```

```ini
[Resolve]
DNSStubListenerExtra=10.0.0.1
```

##### 确保服务自启动

```bash
systemctl enable systemd-networkd
systemctl enable systemd-resolved
```

##### 设置DHCP

```bash
sudo pacman -S dhcp
sudo vim /etc/dhcpd.conf


```

添加子网规则

```tcl
# add mhduiy
subnet 10.0.0.0 netmask 255.255.0.0 {
    range 10.0.1.4 10.0.1.250;
    option domain-name-servers 10.0.0.1;
    option routers 10.0.0.1;
}
# add end mhduiy

```

##### 修改服务文件，使其支持单网口配置

```bash
sudo cp /usr/lib/systemd/system/dhcpd4.service /etc/systemd/system/dhcpd4@.service
sudo vim /etc/systemd/system/dhcpd4@.service

```

```ini
  ...
  [Service]
  Type=forking
- ExecStart=/usr/bin/dhcpd -4 -q -cf /etc/dhcpd.conf -pf /run/dhcpd4/dhcpd.pid
+ ExecStart=/usr/bin/dhcpd -4 -q -cf /etc/dhcpd.conf -pf /run/dhcpd4/dhcpd.pid %I
  ...
```

##### 开启网络转发

```bash
sudo vi /etc/sysctl.d/30-ipforward.conf
```

```bash
net.ipv4.ip_forward=1
net.ipv6.ip_forward=1

```

##### 配置NAT规则

```bash
# 添加 NAT 规则
sudo iptables -t nat -A POSTROUTING -o enp1s0 -j MASQUERADE

# 允许相关和已建立连接的流量
sudo iptables -A FORWARD -i enp2s0 -o enp1s0 -m state --state RELATED,ESTABLISHED -j ACCEPT

# 允许从 LAN 到 WAN 的流量
sudo iptables -A FORWARD -i enp1s0 -o enp2s0 -j ACCEPT
```

##### 重启网络服务

```bash
sudo systemctl restart systemd-networkd
sudo systemctl restart dhcpd4@enp2s0
```
