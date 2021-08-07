---
layout:     post
title:      "MySQL无法启动解决方法"
subtitle:   "MySQL无法启动解决方法"
date:       2020-08-23 09:22
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - MySQL
---

#### 报错信息
2020-08-23T01:19:50.366546Z 0 [ERROR] E:\phpstudy_pro\COM\..\Extensions\MySQL5.7.26\\bin\mysqld.exe: Error while setting value 'STRICT_TRANS_TABLES, NO_ZERO_IN_DATE, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO, NO_AUTO_CREATE_USER, NO_ENGINE_SUBSTITUTION' to 'sql_mode'
2020-08-23T01:19:50.366616Z 0 [ERROR] Aborting

##### 解决方案

打开MySQL配置文件，my.ini
搜索找到 sql_mode 配置项
将该属性的值中逗号 ","后的空格全部删除，重新启动MySQL就可以了