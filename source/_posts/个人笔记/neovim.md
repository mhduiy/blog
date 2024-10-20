---
title: neovim
date: 2024-10-16 17:03:59
tags: tools
categories: 个人笔记
---
## NeoVim的配置

- 可以将NeoVim的配置文件写在~/.config/nvim/中

参考大神的做法-> https://www.bilibili.com/video/BV1Td4y1578E/?spm_id_from=333.337.search-card.all.click
以及其github配置-> https://github.com/eggtoopain/Neovim-Configuration-Tutorial即可

我自己的配置相对不那么完善，已经放在这个笔记的根目录下

## LunarVim的配置

当我还在纠结该如何配置neovim的时候，我看到了LunarVim，直接把几乎所有的配置全部搞定了，这算是一个neovim的开箱即用版，基本上把所有该用的配置都已经配置好了。

可以直接使用下面的命令来安装LunarVim

```bash
LV_BRANCH='release-1.3/neovim-0.9' bash <(curl -s https://raw.githubusercontent.com/LunarVim/LunarVim/release-1.3/neovim-0.9/utils/installer/install.sh)

```
如果遇到需要配置python环境的可以选择n,不影响正常使用

### 图标不显示

默认字库不支持一些特殊图标的显示，可能会直接显示为方框，可以安装[Nerd font](https://www.nerdfonts.com/font-downloads)来正常显示这些图标

Windows可以直接点击链接进行下载安装，Linux使用[getnf](https://github.com/getnf/getnf)进行安装，具体可以直接使用下面的命令进行安装

```bash
curl -fsSL https://raw.githubusercontent.com/getnf/getnf/main/install.sh | bash
```

会默认安装在`~/.local/bin/`中，可以将这个目录添加到PATH环境变量

```bash
export PATH="$HOME/.local/bin:$PATH"
```
之后运行getnf命令来安装字体，推荐下载FiraCode字体，自我感觉还不错，下载完成之后可以直接在终端模拟器中将字体切换为自己下载的字体，之后重新打开lvim，就发现图标都已经正常显示了

### 默认快捷键说明

#### 主要功能

- 打开目录树 主键+e
- 切换左右窗口 CTRL + H & CTRL + L
- 打开LunarVim交互式面板 主键
- 快捷搜索文件（不是内容） 主键+f
- 快捷搜索文件内容 主键 + s + f
- 跳转到主页 主键 + ;
- 新建一个交互式终端 CTRL + \
- 清除搜索结果高亮 主键 + h
- 搜索最近打开过的文件 主键 + sr
- 查看已经安装的插件 主键 + ps


#### LSP

- 悬停信息 K
- 跳转到定义 gd
- 跳转到声明 gD  可以相互切换
- 跳转到引用 gr 可以显示当前函数在哪里被使用了，还不知道怎么切换窗口
- 跳转到实现 gI 显示有多个实现的时候比较好用
- 跳转到签名帮助 gs
- 显示当前行的诊断信息 gl

#### 编辑

- 添加注释 主键 + /
- 块注释 选中后gb
- 上下移动行  ALt + J/K


#### 补全

- 显示不下的时候上下滚动文本 CTRL + d/f
- 取消补全CTRL + e
- 光标移动到上下左右窗口 CTRL + K/J/H/L
- 增大减小窗口宽高 CTRL + 上下左右键

#### 树状目录

- 显示帮助 g?
- 复制 y 
- 剪切 x
- 重命名 r
- 粘贴 p
- 删除 d

#### vim 快捷键

- 关闭所有文件 :qa
- 关闭文件并退出 :x :wq ZZ
- 退出但不检查文件改变 ZQ
- 插入 i a
- 上一行插入（添加新行）O
- 下一行插入（添加新行）o
- 行末尾插入 A
- 删除这个字符并进入编辑模式 s
- 删除这一行并进入编辑模式 S
- 删除从这里开始到行末并进入编辑模式 C
- 撤销和恢复 u和CTRL + R
- 删除一个字符 x
- 剪切一行 dd(cut)
- 复制一行 yy(yank)
- 跳转到下一页 CTRL + U
- 跳转到上一页 CTRL + D
- 跳转到首行 gg
- 跳转到尾行 G
- 将当前编辑的位置放在屏幕上边 中间 下边 zt zz zb
- 将鼠标的光标移动到当前屏幕的上边 中间 下边 H M L
- 跳转到下一个搜索结果 n
- 跳转到上一个搜索结果 N
- 跳转buffeer 主键 + bb / 主键 + bn

## 遇到node js安装模块会到系统中，导致无权限

```bash
mkdir ~/.npm-global
npm config set prefix 'mkdir ~/.npm-global'
```