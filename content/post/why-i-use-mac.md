---
title: "Why I Use Mac"
tags: ["MacOS"]
categories: ["original"]
date: 2022-01-20T23:26:36+08:00
draft: false
---

现在，我正在用Macbook Air(m1)写这篇文章，同时，面前还有一台AMD 锐龙7 4800U的小新Pro13正在重置，明天就要被发往猪王那里寄售。



## 外观

### MacOS

动画、字体渲染、高分屏优化都是最佳。

### Windows

字体渲染拉胯、动画生硬、高分屏适配不如没有。

### Ubuntu

- Unity时代还行，换回Gnome之后审美确实下降了不少，不过`Ubuntu Mono`字体确实好评。

- 高分屏优化得修改软件配置，如JB全家桶和网易云音乐。但并非完美，切换分辨率之后又是其他问题。

## 稳定性

### MacOS

- 在v站上看到有的人确实遇到挺多问题，但我目前没发现有什么稳定性方面的问题，日常就是合盖开盖，用了15款i5的MBP、20款i5带touchbar的MBP、20款m1的air，对稳定性的感受基本一致。

### Windows

- bug多不胜数，不知是Win11更多，还是MIUI12更多。OTA升级到11后，系统就变得极为卡顿，资源管理器和桌面都时常崩溃，重置之后变好很多。

### Ubuntu

- 作为服务器来说，稳定性最强，但桌面环境问题依然多多，不论是哪个DE，平时简单用两天无所谓，用久了总有卡死的情况发生，得重启解决。

## 双屏支持

### MacOS

完美

### Windows

- 已开启了仅在一块屏幕上显示，但拔线再接上，两块屏幕都会被点亮

### Ubuntu

- 小新Pro13，笔记本自带屏幕2K分辨率，需要打开2x选项使用，但外接1080P屏幕后，两块屏幕的scale比例必须要统一起来，所以无法做到笔记本屏幕2x，外接显示器1x。
- 直接拔视频输出线，窗口不会自动移回笔记本屏幕

## Coding

### MacOS

- Linux系统编程不方便，只能共享UNIX相关的部分知识点。
- Docker非原生，内存占用大。
- 其他完美

### Windows

- 用Electron写的软件基本都没什么问题。
- 没有bash shell是硬伤，好多事情做不了。
- wsl没有前途。
- 太多开源项目基于unix，nginx、redis、tornado等工具在Windows上不能有生产级的体验。
- 没在Win下用过Docker，暂不评价。

### Ubuntu

- JB全家桶OK，但相比别的平台有少量的个性化功能缺失，如跟随系统主题更改亮/暗色。
- VSCode OK，但升级不方便，或重新下载.deb安装，或`apt update`，这个问题存在于所有的deb软件包，但如果源配的好，升级软件也是`apt update`，不要再方便。
- Sublime Text OK。
- Typora，Electron，功能完备。
- Postman，Electron，功能完备。
- Linux系统编程，完美
- C/C++、Golang、Python等编程语言的编写调试部署体验都是最好。
