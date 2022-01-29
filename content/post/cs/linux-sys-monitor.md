---
title: "Linux Sys Monitor"
date: 2022-01-29T19:35:06+08:00
categories: ["original"]
tags: ["linux"]
draft: false
---

## vmstat命令监控系统资源

```
vm [刷新延时 刷新次数]
```

### 示例

```Shell
vmstat 1 3        # 监控3次系统资源，每次间隔1秒
```

## dmesg开机时内核检测信息

```
dmesg
```

### 示例

```Shell
dmesg | grep CPU        # 查看开机自检时的CPU信息
```

## free命令查看内存使用状态

```
free [-b|-k|-m|-g]
```

### 选项

- `-b` 以字节为单位显示

- `-k` 以kb为单位显示，默认就是以kb为单位显示

- `-m` 以mb为单位显示

- `-g` 以GB为单位显示

## 查看cpu信息

```
cat /proc/cpuinfo
```

## uptime命令

显示系统的启动时间和平均负载，也就是top命令的第一行。w命令也可以看到这个数据

## 查看系统与内核相关信息

```
uname [选项]
```

### 选项

- `-a` 查看系统所有相关信息

- `-r` 查看内核版本

- `-s` 查看内核名称

## 判断当前系统的位数

```
file /bin/ls
```

## 查看发行版本

```
lsb_release -a
```

## 列出进程打开或使用的文件信息

```
lsof [选项]
```

### 选项

- `-c` 字符串：只列出以字符串开头的进程打开的文件

- `-u` 用户名：只列出某个用户的进程打开的文件

- `-p` pid：列出某个pid进程打开的文件
