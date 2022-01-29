---
title: "Linux Perm Management"
date: 2022-01-29T19:40:21+08:00
categories: ["original"]
tags: ["linux"]
draft: false
---

## 文件和目录权限

### 权限管理命令chmod

> /bin/chmod

#### 语法

```
chmod [ {ugoa} {+-=} {rwx} ] [文件或目录] [mode=421] [文件或目录]
```

- `-R`递归修改

#### 示例

```Shell
# {ugoa}
# u代表user自己,g代表group,o代表others,a代表all

chmod u+x demo.sh  # 给当前用户添加执行权限
chmod g=rwx demo.sh  # 把用户组的权限改成可读可写可执行

# 权限数字表示
# r -> 4        w -> 2        x -> 1
```

### 权限管理命令chown

用于更改文件的所有者(只有管理员可以执行)

> /bin/chown

#### 语法

```
chown [用户] [文件或目录]
```

### 权限管理命令chgrp

> /bin/chgrp

#### 语法

```
chgrp [用户组] [文件或目录]
```

### 权限管理命令umask

用于显示、设置文件的缺省权限

> shell内置命令

> 所有用户可执行

#### 语法

```
umask [-S]
```

- `-S`以rwx形式显示新建文件缺省权限(直接在shell里面执行就会显示)

`umask`会输出类似`0022`这样的，第一位的0是特殊权限，剩下的三位`022`可以用`777-022=755`计算出真实权限

##### 如何修改umask值？

```
umask 023
```

#### 其他

- 在Linux中新建的文件都是不具备可执行权限，新建的目录有可执行权限是因为要进入目录需要可执行权限

## ACL权限

## 文件特殊权限

## 文件系统属性chattr权限

> 对root用户同样生效

### 语法

```
chattr [+-=] [选项] 文件或目录名
```

#### 选项

- `i`如果对文件设置i属性，那么不允许对文件进入删除、改名，也不能添加和修改数据；如果对目录设置i属性，那么只能修改目录下文件的数据，但不允许建立和删除文件

- `a`如果对文件设置a属性，那么只能在文件中增加数据(只能用>>，不能编辑)，但是不能删除也不能修改数据，如果对目录设置a属性，那么只允许在目录中建立和修改文件，但是不允许删除

#### 示例

```Shell
chattr +i abc        # 给abc文件添加i属性
```

### 查看文件系统属性

#### 语法

```
lsattr 选项 文件名
```

- `-a`显示所有文件和目录

- `-d`若目标是目录，仅列出目录本身的属性，而不是子文件的

## 系统命令sudo权限

## sudo权限

- root把本来只能超级用户执行的命令赋予普通用户执行

- sudo的操作对象是系统命令

## sudo使用

`visudo`实际修改的是`/etc/sudoers`文件

### 格式

```Shell
root ALL=(ALL)        ALL
# 用户名 被管理主机的地址(ALL或本机地址)=(可使用的身份) 授权命令(绝对路径)

%wheel ALL=(ALL) ALL        # 组
```

### 示例

```Shell
# 授权user1用户可以重启服务器
visudo
user1        ALL=/sbin/shutdown -r now

# 查看被赋予的sudo权限
sudo -l

# 使用sudo命令
sudo /sbin/shutdown -r now
```
