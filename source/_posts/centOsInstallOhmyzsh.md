---
title: CeontOS 安装oh-my-zsh
top: false
cover: false
toc: true
mathjax: true
date: 2021-06-30 22:38:52
password:
summary:
tags: Linux
categories: shell
---

### CeontOS 安装oh-my-zsh
废话不多说，zsh配合oh-my-zsh体验相当nice，直接开干。

#### 安装
1. 安装zsh并切换zsh为默认shell
```sh
yum install zsh -y
chsh -s /bin/zsh
```

2. 安装oh-my-zsh(使用脚本安装)
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
**注意：如遇到网络问题可换下面这种方式**

3. 下载源码安装
```sh
// 下载源码
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
// 复制配置
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```
重新登录终端即可

#### 推荐插件
直接下载到zsh插件目录下
```sh
// 代码高量
git clone https://github.com/zsh-users/zsh-syntax-highlighting ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting

// 命令自动补全
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```

编辑zsh配置：`vim ~/.zshrc` 搜索`plugins`，添加这两个插件即可
```sh
plugins=(
        git
        zsh-autosuggestions
        zsh-syntax-highlighting
)
```
最后`suorce ~/.zshrc`

#### 主题
https://github.com/ohmyzsh/ohmyzsh/wiki/Themes 这里大把的主题，可直接看到效果，可直接从这里选
找到想要的主题直接编辑zsh配置：`vim ~/.zshrc` 搜索`ZSH_THEME`
```sh
// 将这个值改成主题名即可，本人目前使用的主题如下
ZSH_THEME="risto"
```

**安装完之后`conda`命令不能使用，在知道conda的路径情况下可直接使用命令`/home/user/miniconda3/bin/conda init zsh`即可**


