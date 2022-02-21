---
title: "Distributed Lock"
date: 2022-02-21T15:55:38+08:00
tags: ["distributed", "Redis"]
categories: ["original"]
draft: false
---

分布式应用进行逻辑处理经常会遇到并发问题。

比如一个操作要修改用户的状态，修改状态需要先读出用户的状态，在内存里修改完再存回去。如果这样的操作同时进行了，就会出现并发问题，因为读取和保存状态这两个操作不是原子的。这个时候就需要用到分布式锁来限制程序的并发执行。

## 原理

本质上要实现的目标就是在redis里面占一个位，当别的请求也进来时，只能放弃或稍后再试。

占位一般使用`setnx(set if not exist)`指令，只允许被一个客户端占。用完后用`del`指令释放。

```
setnx lock:pay true
...do something...
del lock:pay
```

但有个问题，如果逻辑执行到中间出现异常了，可能导致del指令没有被调用，这样就会出现死锁。需要在拿到锁后，给锁上一个过期时间，比如5s。

```
setnx lock:pay true
expire lock:pay 5
...do something...
del lock:pay
```

但由于使用`setnx`和`expire`两条指令不原子，setnx后到expire之间挂掉的话也会死锁。

redis2.8开始加入了set指令的扩展参数，使`setnx`和`expire`指令可以一起执行。

```
set lock:pay true ex 5 nx
...do something...
del lock:pay
```

## 超时问题

Redis 的分布式锁不能解决超时问题，如果在加锁和释放锁之间的逻辑执行的太长，以至于超出了锁的超时限制，就会出现问题。因为这时候锁过期了，第二个线程重新持有了这把锁， 但是紧接着第一个线程执行完了业务逻辑，就把锁给释放了，第三个线程就会在第二个线程逻辑执行完之间拿到了锁。

### 解决方法

为set指令的value参数设置为一个随机数，释放锁时先匹配随机数是否一致，然后再删除 key。但是匹配value和删除key不是一个原子操作，Redis也没有提供类似于delifequals这样的指令，这就需要使用Lua脚本来处理了，因为Lua脚本可以保证连续多个指令的原子性执行。

```
# delifequals
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
        else
    return 0
        end
```

## 参考

- [Redis 深度历险：核心原理与应用实践](https://book.douban.com/subject/30386804/)
