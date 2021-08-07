---
layout:     post
title:      "composer安装依赖时，出现内存错误"
subtitle:   "composer安装依赖时，出现内存错误"
date:       2020-10-20 11:42
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Composer
---

#### 在项目需要用到某个组件时，使用composer require命令快速安装依赖，报错内存超限
![](/img/typecho/2283190849.png)

解决方案：
### 找到对应运行的php版本的php.ini文件，修改配置参数 memory_limit 为 -1，保存后，重启apache或nginx即可

> 安装完成后要记得修改回来~