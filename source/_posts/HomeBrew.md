---
title: HomeBrew 安装
top: false
cover: false
toc: true
mathjax: true
date: 2021-09-14 10:45:21
password:
summary:
tags: Mac
categories: Linux
---

### `homebrew`(国内飞速安装)

`homebrew`官网推荐的命令在国内巨慢...，不管你是否FQ，都没啥速度的提升
```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
这里有个国内的源，可以使你下载速度提升百倍～
```sh
/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install)"
```

*若出现一下情况：*

如果命令执行中卡在下面信息：
```sh
==> Tapping homebrew/core
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core'...
```
请`Command + C`中断脚本执行如下命令：
```sh
cd "$(brew --repo)/Library/Taps/"
mkdir homebrew && cd homebrew
git clone git://mirrors.ustc.edu.cn/homebrew-core.git
```

成功执行之后继续执行前文的安装命令。
最后看到`==> Installation successful!`就说明安装成功了。

最最后执行：`brew update`

`cask`同样也有首次下载缓慢的问题，解决方法大致同上：
```sh
cd "$(brew --repo)/Library/Taps/"
cd homebrew
git clone https://mirrors.ustc.edu.cn/homebrew-cask.git
```

### 卸载
使用官方脚本同样会遇到uninstall地址无法访问问题，可以替换为下面脚本：
```sh
/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/uninstall)"
```

### 设置国内镜像源
`brew、homebrew/core`是必备项目，`homebrew/cask`、`homebrew/bottles`按需设置。
通过` brew config`命令查看配置信息。

*中科大源*
```sh
git -C "$(brew --repo)" remote set-url origin https://mirrors.ustc.edu.cn/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

brew update
```


### 恢复官方镜像源
```sh
git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask.git

brew update
```
`homebrew-bottles`配置只能手动删除，将` ~/.bash_profile`文件中的 `HOMEBREW_BOTTLE_DOMAIN=https://mirrors.xxx.com`内容删除，并执行` source ~/.bash_profile`