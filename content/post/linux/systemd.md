---
title: "systemd"
date: 2022-01-29T20:27:06+08:00
categories: ["original"]
tags: ["linux"]
draft: false
---

CentOS 7.0开始，系统的进程和自启动管理工具就变成了`systemd`，Ubuntu具体哪个版本不记得了，记得自使用16.04的时候就已经是`systemd`了。

- systemd是用来取代`init`作为系统的第一个进程，pid为1。其他进程都是它的子进程

> 目前这篇笔记中大部分资料来自于 ruanyifeng.com

## Systemd系统管理

> 只有超级管理员有权限操作

### Systemctl 命令

> 用来控制systemd system和作为一个service manager，具体man systemctl

- `systemctl reboot` 重启系统

- `systemctl poweroff` 关闭系统，切断电源

### systemd-analyze 命令

- `systemd-analyze` 查看系统启动耗时

- `systemd-analyze blame` 查看每个服务的启动耗时

- `systemd-analyze critical-chain` 显示瀑布状的启动过程流

- `systemd-analyze critical-chain 服务名` 显示指定服务的瀑布流

### hostnamectl 命令

> 查询和修改系统hostname和相关的设置

- `hostnamectl` 显示当前主机的信息

- `hostnamectl set-hostname 主机名` 设置主机名

### localectl 命令

> 用来查询和修改系统本地化和键盘布局设置

- `localectl` 查看本地化设置

- `localectl set-locale LANG=en_GB.utf8` 设置本地化参数

- `localectl set-keymap en_GB` 设置本地化参数

### timedatectl 命令

> 用来改变系统时钟设置，具体man timedatectl

- `timedatectl` 查看当前时区设置

### loginctl 命令

> login manager ，具体man loginctl

- `loginctl` 列出当前session

- `loginctl list-users` 列出当前用户

## Target

启动计算机的时候，需要启动大量的Unit。Target就是一个Unit组，包含许多相关的Unit。启动某个Target的时候，Systemd就会启动里面所有的Unit。它的概念与`init`启动模式里面的`runlevel`作用类似，不同的是，`runlevel`是互斥的，但是多个Target可以同时启动

### 命令

- `systemctl list-unit-files --type=target`：查看当前系统的所有Target

- `systemctl list-dependencies multi-user.target`：查看一个Target包含的所有Unit

- `systemctl get-default`：查看启动时的默认Target

- `sudo systemctl set-default multi-user.target`：设置启动时的默认Target

- `systemctl isolate multi-user.target`：切换target的时候，默认不关闭前一个Target启动的进程，这个命令是用来改变 这个行为。作用是：关闭前一个Target里面所有不属于后一个Target的进程

### Target与RunLevel的对应关系

| **runlevel** | **target 名称**  | **target linked to…** |
| ------------ | ---------------- | --------------------- |
| runlevel 0   | runlevel0.target | poweroff.target       |
| runlevel 1   | runlevel1.target | rescue.target         |
| runlevel 2   | runlevel2.target | multi-user.target     |
| runlevel 3   | runlevel3.target | multi-user.target     |
| runlevel 4   | runlevel4.target | multi-user.target     |
| runlevel 5   | runlevel5.target | graphical.target      |
| runlevel 6   | runlevel6.target | reboot.target         |

### 与init的主要差别

- 默认的runlevel：(/etc/inittab文件设置)现在已经被默认的Target取代，位置是`/etc/systemd/system/default.target`，通常符号链接到`graphical.target`(图形界面)或`multi-user.target`(多用户命令行)

- 启动脚本的位置：以前是`/etc/init.d`目录，符号链接到不同的runlevel目录，比如`/etc/rc3.d`、`/etc/rc5.d`等。现在存放在`/lib/systemd/system`和`/etc/systemd/system`目录

- 配置文件的位置：以前`init`进程的配置文件是`/etc/inittab`，各种服务的配置文件存放在`/etc/sysconfig`目录。现在配置文件主要放在`lib/systemd`目录，在`/etc/systemd`目录里面的修改可以覆盖原始位置。

## Unit

### Unit管理命令

- `systemctl status nginx.service` 查看nginx状态(service可以省略)

- `systemctl is-active nginx` 查看nginx是否还存活着

- `systemctl is-failed nginx` 查看nginx是否启动失败

- `systemctl is-enabled nginx` 查看nginx是否已经是随系统自启动(建立了启动链接)

- `systemctl start nginx` 启动Nginx

- `systemctl restart nginx` 重启nginx

- `systemctl stop nginx` 停止nginx

- `systemctl kill nginx` 杀死nginx的所有子进程

- `systemctl reload nginx` 重新加载nginx的配置文件

- `systemctl daemon-reload` 重新加载所有修改过的配置文件(新增或修改Unit配置文件之后使用)

- `systemctl show nginx` 显示nginx这个unit的所有底层参数

- `systemctl list-dependencies ngixn` 查看nginx这个unit的所有依赖

### Unit配置文件

> Systemd默认从目录/etc/systemd/system读取配置文件，但是里面大多数文件都是软链接，到/usr/lib/systemd/system，存放真正配置文件的地方

#### 配置文件的状态

- `systemctl list-unit-files`：列出所有配置文件

- `systemctl list-unit-files --type=service`：列出指定类型的配置文件

##### 状态项

- `enabled`：已建立启动链接

- `static`：该配置文件没有([install])部分(无法执行)，只能作为其他配置文件的依赖

- `disabled`：没建立启动链接

- `masked`：该配置文件被禁止建立启动链接

#### 配置文件的格式

- `systemctl cat`：用来查看配置文件的内容配置文件的区块

#### 配置文件的区块

##### [Unit]

> [Unit]区块通常是配置文件的第一个区块，用来定义Unit的元数据，以及配置与其他Unit的关系。启动顺序与依赖关系。

- `description`：该Unit的简短描述

- `Documentation`：文档地址

- `Requires`：当前Unit依赖的其他Unit，如果他们没有运行，当前Unit会启动失败，强依赖关系。

- `Wants`与当前Unit配合的其他Unit，如果它们没有运行，当前Unit不会启动失败，弱依赖关系。和Requires一样，只涉及依赖关系，与启动顺序无关，默认情况下是同时启动的。

- `BindsTo`：与Requires类似，它指定的Unit如果退出，会导致当前Unit停止运行

- `Before`这里写的Unit必须在当前Unit启动之后启动

- `After`：这里写的Unit必须在当前Unit启动之前启动，和Before一样，只涉及启动顺序，不涉及依赖关系。

- `Conflicts`：这里写的Unit不能与当前Unit同时运行

- `Condition`：当前Unit运行必须满足的条件，否则不会运行

- `Assert`：当前Unit运行必须满足的条件，否则会报启动失败

##### [Install]

> 通常是配置文件的最后一个区块，用来定义如何安装这个配置文件，以及怎样做到开机启动。

- `WantedBy`：它的值是一个或多个Target，当前Unit激活(enable)时符号链接会放入`/etc/systemd/system`目录下面以`Target名 + .wants`子目录中，表示该服务所在的Target

- `RequiredBy`：它的值是一个或多个Target，当前Unit激活时，符号链接会放入/etc/systemd/systemd目录 下面以`Target名 + .required`子目录中

- `Alias`：当前Unit可用于启动的别名

- `Also`：当前Unit激活(enable)时，会同时激活的其他Unit

##### [Service]

> 用于存放Service的配置，只有Service类型的Unit才有这个区块。定义启动行为，定义如何启动当前服务。

- `Type`：定义启动时的进程行为。有以下几种值
  - `Type=Simple`：默认值。执行ExecStart指定的命令，启动主进程

- `Type=forking`：以fork方式从父进程创建子进程，创建后父进程会立即退出，子进程成为主进程

- `Type=oneshot`：类似simple，但只执行一次，是一次性进程，Systemd会等当前服务执行完，再继续往下执行

- `Type=dbus`：类似于simple，但会等待D-Bub信号后启动。即当前服务通过D-Bus启动

- `Type=notify`：类似于simple，启动结束后会发出通知信号，然后Systemd再启动其他服务。

- `Type=idle`：若有其他任务执行完毕，会通知Systemd，再继续才会运行。类似于simple，但是要等到其他任务都执行完，才会启动该服务。一种使用场合是为了让该服务的输出，不与其他服务的输出相混合

- `ExecStart`：启动当前服务的命令(所有路径都要写成绝对路径，包括bash和sh)

- `ExecStartPre`：启动当前服务之前执行的命令

- `ExecStartPost`：启动当前服务之后执行的命令

- `ExecReload`：重启当前服务时执行的命令

- `ExecStop`：停止当前服务时执行的命令

- `ExecStopPost`：停止当前服务之后执行的命令

- `RestartSec`：自动重启当前服务间隔的秒数

- `Restart`：定义何种情况Systemd会自动重启当前的服务。退出守护进程，推荐设为`on-failure`，对于那些允许发生错误退出的服务，可以设为`on-abnormal`
  - `no`：默认。退出后不重启

- `on-success`：只有正常退出时(退出状态码为0)，才会重启

- `on-failure`：非正常退出时(退出码非0)，包括被信号终止和超时，才会重启

- `on-abnormal`：只有被信号终止和超时，才会重启

- `on-abort`：只有在收到没有捕捉到的信号终止时，才会重启

- `on-watchdog`：超时退出，才会重启

- `always`：不管是什么退出原因，总是重启

- `TimeoutSec`：定义Systemd停止当前服务之前等待的秒数

- `Environment`：指定环境变量

- `EnvironmentFile`：指定当前服务的环境参数文件。该文件`key=value`键值对，可以用`$key`的形式，在当前配置文件中获取。

- `KillMode`：定义了重启行为。
  - `Control-group`：默认值。当前控制组里的所有子进程，都会被杀掉

- `process`：只杀主进程，不停止任何子进程

- `mixed`：主进程收到SIGTERM信号，子进程收到SIGKILL信号

- `none`：没有进程会被杀掉，只是执行服务的stop命令

## 日志管理

systemd统一管理所有Unit的启动日志。带来的好处是：可以只用`journalctl`一个命令查看所有日志(内核日志和应用日志)，日志的配置文件是`/etc/systemd/journald.conf`

### 命令

- `journalctl`：查看所有日志(默认情况一，只保存本次启动的日志)

- `journalctl -k`：查看内核日志，不显示应用日志(root)

- `journalctl -b`：查看系统本次启动的日志

- `journalctl -b -0`：查看系统本次启动的日志

- `journalctl -b -1`：查看上一次启动的日志

- `journalctl -n 行数`：查看尾部指定行数

- `journalctl -f`：滚动查看最新日志

- `journalctl /usr/lib/systemd/systemd`：查看指定服务的日志(这里面的参数是可执行文件的地址，比如虚拟环境里面的gunicorn，或者golang项目目录下面的入口可执行文件)

- `journalctl -u nginx.service`：查看某个Unit的日志(.service可以省略)

- `journalctl -u nginx.service --since today`：查看某个Unit的日志

- `journalctl -u nginx.service -f`：实时滚动显示某个Unit的最新日志

- `journal -u nginx.service -u sshd.service --since today`：合并显示多个Unit的日志

- `journalctl -b -u nginx.service -o json`：以单行JSON格式输出日志

- `journalctl -b -u nginx.service -o json-pretty`

- `journalctl --disk-usage`：显示日志占据的硬盘空间

- `journalctl --vacuum-size=1G`：指定日志文件占据的最大空间

- `journalctl --vacuum-time=1years`：指定日志文件保存多久

- `journalctl -p err -b`：查看指定优先级(及以上级别)的日志，共有8级
  - ererg

- alert

- crit

- err

- warning

- notice

- info

- debug

- `journalctl _PID=1`：查看指定进程的日志

- `journalctl --since=时间/时间标示`

```Shell
  journalctl --since="2012-10-30 18:17:16"
  journalctl --since "20 min ago"
  journalctl --since yesterday
  journalctl --since "2015-01-10" --until "2015-01-11 03:00"
  journalctl --since 09:00 --until "1 hour ago"
```

## 定时任务

需要一个Timer单元，也是在``/usr/lib/systemd/system`或`/etc/systemd/system`目录下，新九建一个`timer单元名.timer`文件，写入单元配置，比起Service单元，需要写一个叫`Timer`的section。

### Timer section的字段

[字段填写方法参考：官方文档](https://www.freedesktop.org/software/systemd/man/systemd.time.html)

- `OnActiveSec`：定时器生效后，多长时间开始执行任务

- `OnBootSec`：系统启动后，多久开始执行任务

- `OnStartupSec`：Systemd进程启动后，多久开始执行任务

- `OnUnitActiveSec`：该单元上次执行后，要多久再次执行

- `OnUnitInactiveSec`：定时器上次关闭后多久，再次执行

- `OnCalendar`：基于绝对时间执行

- `AccuracySec`：如果因为某些原因，任务要推迟进行，推迟的最大秒数，默认60秒

- `Unit`：真正要执行的任务，默认是同名的带有`.service`后缀的单元

- `Persistent`：如果设置了该字段，即使定时器到时没有启动，也会自动执行相应的单元

- `WakeSystem`：如果系统休眠，是否自动唤醒系统

### 定时器相关命令

- `systemctl start 定时器.timer`：启动某个定时器

- `systemctl status 定时器.timer`：查看某定时器的状态

- `systemctl list-timers`：查看所有正在运行的定时器

- `systemctl stop 定时器.timer`：关闭某个定时器

- `systemctl enable 定时器.timer`：开机启动这个定时器

- `systemctl disable 定时器.timer`：

## 示例

- 一个Golang写的Web应用

```Shell
[Unit]
Description=Jzfeed Golang Macaron Server
After=network.target


[Service]
Type=simple
User=jzhan
Group=jzhan
WorkingDirectory= /home/jzhan/code/project/gocode/src/jzfeed
ExecStart= /home/jzhan/code/project/gocode/src/jzfeed/jzfeed
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
PrivateTmp=true


[Install]
WantedBy=multi-user.target
```
