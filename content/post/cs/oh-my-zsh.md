---
title: "On My Zsh Install & Configure"
date: 2022-01-28T16:31:47+08:00
tags: ["linux"]
categories: ["original"]
draft: false
---

ubuntu安装oh-my-zsh

项目地址: `https://github.com/robbyrussell/oh-my-zsh`

### 安装oh-my-zsh

#### 安装zsh

1. 安装zsh

```
sudo apt install zsh
```

1. 将zsh设置成默认shell

```
chsh -s /bin/zsh
```

#### 安装oh my zsh

1. 两条命令都可以使用

```Bash
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh(墙)
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh && bash ./install.sh(墙)
```

#### 字体配置

1. 安装**nerd-fonts**

```Bash
   mkdir -p ~/.local/share/fonts
   cd ~/.local/share/fonts && curl -fLo "Droid Sans Mono for Powerline Nerd Font Complete.otf" https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/DroidSansMono/complete/Droid%20Sans%20Mono%20Nerd%20Font%20Complete.otf
```

1. 安装`Powerlevel9k`

```Bash
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

### 插件安装

1. autojump(目录跳转)

```
sudo apt install autojump
```

1. zsh-autosuggestions(历史命令插件)

```Bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

1. `zsh-syntax-highlighting`(命令行语法高亮)

```Bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

#### 配置插件

```Shell
# 打开~/.zshrc，并修改plugins为以下内容

plugins=(
  git extract autojump zsh-autosuggestions zsh-syntax-highlighting
)
```

### zsh配置

```Shell
POWERLEVEL9K_MODE='nerdfont-complete'
ZSH_THEME="powerlevel9k/powerlevel9k"

ENABLE_CORRECTION="true"
COMPLETION_WAITING_DOTS="true"
```

### 安装过程中可能出现的问题

- `_arguments:448: _vim_files: function definition file not found`

在使用vim命令补全的时候，可能遇到这个问题，这个时候

- 删除 `~/.zcompdump-hostname-5.0.5`( 这个名字不同系统里面不一样，需要具体看，具体删除)
