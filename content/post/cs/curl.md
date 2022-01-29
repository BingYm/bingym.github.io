---
title: "curl"
date: 2022-01-29T20:38:37+08:00
categories: ["original"]
tags: ["linux"]
draft: false
---

###### 添加UA
```
# -A
# --user-agent
curl -A myagent http://localhost
```
###### 请求显示Response头信息
```
# -i 显示Response的头信息，连同网页代码一起
# -I 只显示Response的头信息
curl -i http://localhost
```
###### 显示通信过程
```
# -v,显示一次http通信的整个过程，包括端口连接和http request头信息
curl iv http://localhost
```
###### HTTP REST请求
```
# -X
curl -X POST http://localhost
curl -X PATCH http://localhost
```
###### GET请求
```
# -G -d -G是以GET方式访问，-d是请求数据
curl -G -d "name=xiaoming" http://localost
```
###### post请求
```
# 表单
# -d 
curl -d "age=12&name=xiaoming" "http://localhost"
curl -X POST --data "age=12&name=xiaoming" http://localhost:5000/post/
```
###### referer 字段
```
# -e
# --referer,这个地址是真实地址
curl --referer http://a.com http://localhost
```
###### cookie
```
--cookie
curl --cookie "name=xiaoming" http://localhost
```
###### 增加头信息
```
--header
curl --header "Contnet-Type:application/json" http://localhost:5000
```
###### 自动跳转
```
# -L
curl -L http://localhost
```
###### 静默模式
```
-s/--silent 不输出任何东西（除了响应体）
```
###### 写入到文件
```
-o/--output	
curl http://localhost -o aaa.html
```
