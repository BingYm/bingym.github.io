---
title: "Vim"
date: 2022-01-28T16:27:45+08:00
tags: ["vim"]
categories: ["original"]
draft: false
---

## 插入

- `a`在当前字符后追加。`a`在当前行最后追加

- `i`在当前字符前插入。`i`在当前行最前面插入

- `o`在当前行下面新开一行。`o`在当前行上面新开一行

## 移动

> word指以非空白符分隔的单词，word指以空白符分隔的单词

- `w` 移动到下一个word开头, 移动到下一个word开头

- `e` 移动到下一个word结尾, 移动到下一个word结尾

- `b` 回到上一个word开头，可以理解为backword, `b`移动到下一个word开头

- `0`移动到行首第一个字符，`^`移动到第一个非空白字符

- `$`移动到行尾，`g_`移动到行尾非空白字符

- `ctrl + b`向上翻页，backword

- `ctrl + f`向下翻页，forward

- `ctrl + u`向上翻半页, up

- `ctrl + d`向下翻半页, down

- `zz`光标所在行置在屏幕中央

- `zt`光标所在行置为屏幕最上

- `zb`光标所在行置为屏幕最下

- `gg`移动到文件开头，`g`移动到文件结尾，并可以使用`ctrl+o`快速返回

- `h/m/l`跳转到屏幕的开头，中间和结尾

- `ctrl + O` 后退

- `ctrl + I` 前进

### 行内搜索移动

- 使用`f{char}`可以移动到char字符上，`t`移动到char的前一个字符

- 如果第一次没搜索到，可以使用`;`继续搜索下一个，使用`,`搜索上一个

- `f`表示反过来搜索前面的字符

### 搜索替换

- `:[range]s[ubstitude]/{pattern}/{string}/[flags]`
  - `range`表示范围，比如`:10,20`表示10-20行，`%`表示全部

- `pattern`是要替换的模式，`string`是替换后的文本

- `flags`里面有几个常用的标志

- `g(global)`表示全局范围内执行

- `c(confirm)`表示确认，可以确认或拒绝修改

- `n(number)`报告匹配到的次数而不替换，可以用来查询匹配次数

## 编辑

- `ctrl+w`删除上一个单词

- `ctrl+h`删除上一个字符

- `ctrl+u`删除当前行

- `daw/dw` 删除当前单词(delete a word)

- `d$`删除到行尾，`d0`删除到行首

- `2dd`删除2行

- `dt(`删除当前字符到`(`, 删除直到

- `x`删除当前字符

- `4x`删除4个字符

- `u`撤销，undo

### 快速修改

- 常用有3个, `r(replace)`, `c(change)`, `s(substitute)`

- normal模式下使用`r`可以替换掉一个字符，`s`替换并进入插入模式 ,`ra`可以把当前字符替换成a

- 使用`c`配合文本对象，可以进行快速修改, `caw`把当前单词删掉并进行插入模式

## 分屏

- `:vs`竖分屏

- `:sp`横着分屏

## 选择

- `v`进行visual模式

- `v`行选

- `ctrl+v`进行方块选择

- `:bn` 切换到下一个文件

- `:bp` 切换到上一个文件

- `ctrl + o` 跳转到上一次光标所在的位置

- `^` 移动到当前行第一个非空字符

- `$` 移动到当前行最后一个字符

- `%d` 清空文件内容

## 查询

- 使用`/`或者`?`进行前向或反向搜索

- 使用`n/n`跳转到下一个或上个匹配

- 使用`×`或`#`进行当前单词的前向和后向匹配

## 模式切换

- `ctrl + c`或`ctrl + [`切换到normal模式

- `gi`快速跳转到最后一次编辑的地方并使用插入模式

### 插件快捷键

- `gd` goto definition 跳转到定义处

## 多文件操作

### 概念

#### buffer

- `buffer`是指打开的一个文件的内存缓冲区

- vim打开一个文件后会加载所有的文件内容到缓冲区

- 之后所有的修改都是针对内存中的缓冲区，并不会直接保存到文件

- 只有使用`:w`后才会把修改内容写入到文件里面

##### 如何在buffer中间切换

- 使用`:ls`会列举当前缓冲区，然后使用`:bn`跳转到第ｎ个缓冲区

- `:bpre`, `bnext`, `:bfirst`, `:blast`

- 或者使用`:b buffer_name`加上`tab`补全来跳转

#### 窗口

- 窗口是可视化的分割区域

- 一个缓冲区可以分割成多个窗口，每个窗口也可以打开不同的缓冲区

- 可以使用`ctrl+w`ｓ水平分割，`ctrl+w`v垂直分割．或者使用`:sp`和`:vs`

- 每个窗口可以继续被无限分割

##### 如何切换窗口

- `ctrl-w`w 在窗口间循环切换

- `ctrl-w`h 切换到左边的窗口

- `ctrl-w`j 切换到下边的窗口

- `ctrl-w`k 切换到上面的窗口

- `ctrl-w`l 切换到右边的窗口

##### 如果重排窗口

- 重排窗口可以改变窗口的大小`:h window-resize`查看文档

- `ctrl-w`= 使所有窗口等宽、等高

- `ctrl-w`_ 最大化活动窗口的高度

- `ctrl-w`| 最大化活动窗口的宽度

- `[n]<ctrl-w>_` 把活动窗口的高度设为[n]行

- `[n]<ctrl-w>|` 把活动窗口的宽度设为[n]行

#### tab

- `tab`是可以容纳一系列窗口的窗口(`:h tabpage`)

- `窗口`是指buffer可视化的分割区域

- `tab`可以组织窗口为一个工作区

- vim里面的tab和其他编辑器不太一样，可以想象成linux的虚拟桌面

- 比如一个tab全用来编辑python文件，一个tab全是html文件

## vim里面的text object(文本对象)

- vim里面也有对象的概念，比如一个单词，一个句子，一个段落

- 其他编辑器经常只能操作单个字符来修改文本，比较低效

- 通过操作文本对象来修改要比只操作单个字符高效

### 操作方式

- `[number]<command>[text object]`
  - `number`表示次数

- `command`是命令，`d(elete)`,`c(hange)`,`y(ank)`

- `text object`表示要操作的文本对象，比如单词`w`，句子`s`，段落`p`

- `iw`表示`inner word`, 如果键入`viw`命令，那么首先`v`将进入选择模式，然后`iw`将选中当前单词

- `aw`表示`a word`，它不但会选中当前单词，还会包含当前单词之后的空格

## 插件

- [https://github.com/majutsushi/tagbar](https://github.com/majutsushi/tagbar') 代码分析，快速了解当前文件结构, `f2`

- [https://github.com/bling/vim-airline](https://github.com/bling/vim-airline') 状态栏插件

- [https://github.com/kien/ctrlp.vim](https://github.com/kien/ctrlp.vim') 快速查找文件 `shift+p`

- `Plugin 'Raimondi/delimitMate'` 括号补全

- 后续
  - sudo apt install ctags

- sudo apt install pep8 pyflake pylint

- pip install rope ropevim

# 配置

```Plain%20Text
" 用空格代替tab
set expandtabs
" 统一缩进为4
set softtabstop=4
" tab键的宽度
set tabstop=4
set shiftwidth=4
" 设置缩进为4
set softtabstop=4
" 自动缩进
set autoindent
" 显示行号
set nu
" 搜索忽略大小写 
set ignorecase
```
