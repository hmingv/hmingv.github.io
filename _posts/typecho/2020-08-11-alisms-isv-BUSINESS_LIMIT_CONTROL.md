---
layout:     post
title:      "阿里云短信服务接口触发天级流控Permits:10"
subtitle:   "阿里云短信服务接口触发天级流控Permits:10"
date:       2020-08-11 16:26
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
---

#### 在使用阿里云短信接口发送短信时，个别客户收到错误提示 "触发天级流控Permits:10"

错误原因是因为短信发送有默认的频率限制：

![Snipaste_2020-08-11_16-32-50.png][1]

##### 设置地址：控制台=》短信服务=》系统设置=》国内消息设置=》发送频率设置

解决方法：重新设置发送频率即可，不过需要注意的是，账号非企业认证无法设置！


  [1]: https://blog.twhmr.cn/usr/uploads/2020/08/3559859733.png