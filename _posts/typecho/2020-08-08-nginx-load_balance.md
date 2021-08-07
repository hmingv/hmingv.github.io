---
layout:     post
title:      "负载均衡？分流？"
subtitle:   "负载均衡？分流？"
date:       2020-08-08 14:45
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Linux
    - 负载均衡
    - Nginx
---

负载均衡，英文名称Load Balance，其含义就是将负载（工作内容）进行平衡、分摊到多个操作单元上进行运行，例如FTP<a href="https://www.twhmr.cn" target="_blank">服务器</a>、WEB<a href="https://www.twhmr.cn" target="_blank">服务器</a>、企业核心应用<a href="https://www.twhmr.cn" target="_blank">服务器</a>和其他主要任务<a href="https://www.twhmr.cn" target="_blank">服务器</a>等，从而协同完成工作内容内容

负载均衡构建在原有网络结构之上，它提供了一种透明且廉价有效的方法扩展<a href="https://www.twhmr.cn" target="_blank">服务器</a>和网络设备的带宽、加强网络数据处理能力、增加吞吐量、提高网络的可用性和灵活性

#### Nginx中使用upstream模块实现负载均衡的分流

##### 1. 在vhost.conf中，加入节点

```
upstream upstream.cn {
    server 127.0.0.1:1111;
    server 127.0.0.1:2222;
    server 127.0.0.1:3333;
}
```

##### 2. 将server节点下的location节点中的proxy_pass配置为：http://upstream名称（http://upstream.cn）

```
location / {
    proxy_pass http://upstream.cn;
}
```

> 此时，负载均衡分流的雏形已经完成

upstream将按照轮询的方式进行分流，每一个请求按时间逐一分配到不同的后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>。若后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>down掉后，可以自动剔除。

尽管这样的方法简单、成本低。但是缺点也随之而来：可靠性低一级负载分配的不均衡

> 除此之外，upstream还有其他的分配策略，例如

###### weight（权重）

指定轮询几率，weight与访问比例成正比，用于后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>性能不均的情况

```
upstream upstream.cn {
    server 127.0.0.1:1111 weight=1;
    server 127.0.0.1:2222 weight=5;
    server 127.0.0.1:3333 weight=10;
}
```

> 以上实现效果为，端口3333的访问比率高于端口2222的2倍。2222的访问比率高于1111的5倍

###### ip_hash（ip哈希）

```
upstream upstream.cn {
    ip_hash;
    server 127.0.0.1:1111;
    server 127.0.0.1:2222;
    server 127.0.0.1:3333;
}
```

> 每一个请求都按照IP地址的hash结果分配。这样每一个用户的访问都会固定一个后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>，保持正常会话，免除了Session共享的问题

###### fair（）

```
upstream upstream.cn {
    server 127.0.0.1:1111;
    server 127.0.0.1:2222;
    server 127.0.0.1:3333;
    fair;
}
```

> 与weight分配策略类似。按照后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>的响应时间来分配请求，响应越快的<a href="https://www.twhmr.cn" target="_blank">服务器</a>优先分配。

##### upstream还可以为每一个后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>设置状态值，这些状态的含义如下

1. down 表示当前的server临时参与负载分配
2. weight 值越大，负载分配的权重越大
3. max_fails 请求失败的次数，当超过设定值，返回proxy_next_upstream模块定义的错误
4. fail_timeout max_fails此失败后，暂停的时间
5. backup 当其他的所有非backup的后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>down或忙时，请求backup后端<a href="https://www.twhmr.cn" target="_blank">服务器</a>。

```
upstream upstream.cn {
    server 127.0.0.1:1111 down;
    server 127.0.0.1:2222 weight=2;
    server 127.0.0.1:3333 backup;
}
```