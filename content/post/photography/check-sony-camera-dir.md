---
title: "Check Sony Camera Dir"
date: 2022-02-17T11:50:09+08:00
tags: ["photography", "sony camera"]
categories: ["original"]
draft: false
---

## 目标

1. 删除空目录
2. 列出非空目录里的文件数

## 做法

我的A7R2存储卡名是`sd4a7rm2`，在MacOS上照片的路径是`/Volumes/sd4a7rm2/DCIM`

把下面这段代码放在这个目录下，起名叫`app.py`，然后在terminal里面输入`python app.py`，效果如图

![](http://stc-cdn.threestories.cn/Snipaste_2022-02-17_11-54-11.png)

```python
import os

dirs = os.listdir('.')
dirs = [i for i in dirs if i.isdigit()]

for _dir in dirs:
    abspath = os.path.abspath(_dir)
    child_files = os.listdir(abspath)
    if not child_files:
        os.rmdir(abspath)
        continue
    print('----------------------------------------------')
    print(_dir, f'{len(child_files)} files')

```

