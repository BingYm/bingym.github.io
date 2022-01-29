---
title: "curl"
date: 2022-01-29T20:38:37+08:00
categories: ["original"]
tags: ["linux"]
draft: false
---

## User-Agent

```shell
# -A
# --user-agent
curl -A myagent http://localhost
```

## Show Response Header

```shell
# -i 显示Response的头信息，连同网页代码一起
# -I 只显示Response的头信息
curl -i http://localhost
```

## 显示通信过程

```shell
# -v,显示一次http通信的整个过程，包括端口连接和http request头信息
curl iv http://localhost
```

## HTTP REST Request

```shell
# -X
curl -X POST http://localhost
curl -X PATCH http://localhost
```

### GET

```shell
# -G -d -G是以GET方式访问，-d是请求数据
curl -G -d "name=xiaoming" http://localost
```

### POST

```shell
# 表单
# -d 
curl -d "age=12&name=xiaoming" "http://localhost"
curl -X POST --data "age=12&name=xiaoming" http://localhost:5000/post/
```

## referer

```shell
# -e
# --referer,这个地址是真实地址
curl --referer http://a.com http://localhost
```

## cookie

```shell
--cookie
curl --cookie "name=xiaoming" http://localhost
```

## add header

```shell
--header
curl --header "Contnet-Type:application/json" http://localhost:5000
```

# Auto Redirect

```shell
# -L
curl -L http://localhost
```

## Slient Mode

```shell
-s/--silent 不输出任何东西 #(除了响应体)
```

## output to file

```shell
-o/--output
curl http://localhost -o aaa.html
```
