---
title: "Zip"
date: 2022-01-28T15:51:40+08:00
draft: false
---

# gzip/gunzip

## gzip

压缩文件(只能压缩文件，且压缩完成之后不保留原文件)，压缩后文件格式`.gz`

### 语法

```shell
gzip 1.txt
```

## gunzip

解压缩`.gz`的压缩文件

### 语法

```shell
gunzip 1.txt.gz
```

# tar

打包目录，打包后格式：`.tar`

```
tar 选项[-zcf] [压缩后文件名] [目录]
```

- `-c`打包

- `-v`显示详细信息

- `-f`指定文件名

- `-z`打包同时压缩

- `-x`解包

### 示例

```shell
tar -cvf demo.tar demo  # 把demo目录打包成demo.tar这个文件

tar -zcf demo.tar.gz demo   # 把demo目录打包并压缩成demo.tar.gz

tar -zxvf demo.tar.gz  # 解压
```

# zip/unzip

## zip

压缩文件或目录，文件格式为`.zip`


zip 选项[-r] [压缩后文件名] [文件或目录]

-  `-r` 压缩目录

### 示例

```shell
zip demo.zip demo  # 把demo文件压缩成demo.zip

zip -r demo.zip demo  # 把demo目录压缩成demo.zip
```

## unzip

解压`.zip`的压缩文件

### 示例

```shell
unzip 1.zip
```

# bzip2/bunzip2

## bzip2

压缩文件，压缩后格式：`.bz2`

bzip2 选项[-k] [文件]
- `-k` 压缩文件后保留原文件

### 示例

```shell
bzip2 -k demo
```

## bunzip2

解压缩`.bz2文件`

### 示例

```shell
tar -xjf demo.tar.bz2  # 解压demo.tar.bz2
```