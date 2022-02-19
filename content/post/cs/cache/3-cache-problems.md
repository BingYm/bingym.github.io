---
title: "3 Cache Problems"
date: 2022-02-19T15:35:43+08:00
tags: ["cache", "Redis"]
categories: ["original"]
draft: false
---

### 缓存雪崩(Cache Avalanche)

大量缓存数据同一时间失效，或者cache服务出现问题(如重启、宕机等)，因此所有请求冲向数据库，导致数据库被打死，系统crash

#### 解决方案

- 保证cache系统的高可用，对于redis而言，需要使用哨兵、集群化、持久化等来提高缓存系统的HA
- 另一方面除了保证缓存系统的HA之外，服务本身也需要支持降级，可以使用奈飞的Hystrix来实现服务的熔断、降级、限流来降低出现雪崩时的故障程度。
- 设置热点数据过期时间时尽量分散，在基础过期时间上加点随机差，保证不会在同一时间失效

### 缓存穿透(Cache Penetration)

如果一个请求需要的key在缓存中没有，会去请求数据库(数据库中也不存在)，如果同一时段有大量恶意的不存在的key的请求，那么服务将一直处理这些无效请求，给数据库带来很大压力。

#### 解决方案

- 如果在数据库查不到这个key，返回空值，同时在cache里保存这个key(较短失效时间)
- 布隆过滤器([参考](https://zhuanlan.zhihu.com/p/43263751))
  - 缺点：存在一定误判，假如判断存在可能不一定存在，但假如判断不存在就一定不存在

### 缓存击穿(Hotspot Invalid)

缓存数据失效，大量对该key的查询请求一起到达去查数据库，造成数据库负载增加

#### 解决方案

1. 多线程加锁，其中一个线程发现cache中查不到后进行加锁，再从数据库获取内容之后写到缓存中，其他线程获取锁失败则阻塞一定时间后再进行缓存读取，可以降低访问数据库的线程数。需要注意在单机和集群需要使用不同的锁，集群环境使用分布式锁来实现，但由于锁的存在也会影响并发效率。
2. 在业务层(如定时任务)对使用的热点数据查看是否即将过期，如果即将过期则去数据库获取最新数据再重新刷新cache，保证热点数据不过期



## 参考

- [What is cache penetration, cache breakdown and cache avalanche? ](https://www.pixelstech.net/article/1586522853-What-is-cache-penetration-cache-breakdown-and-cache-avalanche)

