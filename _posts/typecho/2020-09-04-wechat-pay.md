---
layout:     post
title:      "微信沙箱支付问题"
subtitle:   "微信沙箱支付问题"
date:       2020-09-04 09:39
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Pay
    - Wechat
---

#### 沙箱环境支付时报错 ERROR_GATEWAY: Get Wechat API Error:沙箱支付金额(***)无效，请检查需要验收的case

该原因是因为沙箱环境支付金额为固定金额，需要通过微信官方查询支付方式的对应额度

### 关注公众号 "《微信支付商户接入验收助手》"
### 在底部菜单 验收case中选择对应的支付方式获取支付金额
