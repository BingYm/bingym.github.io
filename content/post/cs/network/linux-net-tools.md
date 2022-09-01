---
title: "Linux Net Tools"
date: 2022-09-01T22:54:48+08:00
draft: false
---

##　ethtool

### Parameters

| Parameter |                         Description                         |
| :-------: | :---------------------------------------------------------: |
|   ethX    | 查看ethx网口基本设置。其中x是对应网卡的编号，如eth0、eth1等 |
|    -k     |                    查询网卡的Offload信息                    |
|    -K     |                    修改网卡的Offload信息                    |
|    -c     |                     查询网卡的聚合信息                      |
|    -C     |                     修改网卡的聚合信息                      |
|    -l     |                       查看网卡队列数                        |
|    -L     |                       设置网卡队列数                        |