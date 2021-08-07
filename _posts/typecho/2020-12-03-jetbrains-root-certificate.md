---
layout:     post
title:      "Certificate used to sign the license is not signed by JetBrains root certificate (2)"
subtitle:   "Certificate used to sign the license is not signed by JetBrains root certificate (2)"
date:       2020-10-20 11:42
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - PHPStorm
---

### 在申请了JetBrands 全家桶教育版后，使用账号进行激活本地编辑器，报错

#### Certificate used to sign the license is not signed by JetBrains root certificate (2)

![](/img/typecho/3717081326.png)

##### 经网上搜索，有以下几种解决方案，若是有 PJ 过IDEA，直接看第三种解决方案即可！

### 第一种（代理问题）：
1. 找到本地 hosts 文件 C:\Windows\System32\drivers\etc hosts
2. 打开并编辑文件<br>（需要管理员权限，若无法修改，复制到桌面后修改完成再剪切回来替换文件）<br>将 0.0.0.0 account.jetbrains.com 前面添加 # 注释掉
3. 重新登陆

### 第二种（配置IDEA自动代理）（该方式基于 PHPStorm 2020.2.4 版本说明）
1. 在IDEA界面找到配置 File > Settings > Appearance & Behavior > System Settings > HTTP Proxy
2. 勾选 Auto0detect proxy settings，再勾选上 Automatic proxy configuration URL<br>并在输入框内填写 https://account.jetbrains.com，保存
![](/img/typecho/4022620643.png)
3. 重新登陆

### 第三种（原来有过 PJ 行为）
1. 再IDEA界面找到配置文件 Help > Edit Custom VM Optons... 点击该选项后会打开一个文本文件
2. 删除文本文件内的 -javaagent:C:\Program Files\JetBrains\jetbrains-agent.jar <br>（该内容可能不一致，且可能出现多行，查找关键词 -javaagent:、jetbranins-agent.jar 并存即可）
![](/img/typecho/2221733527.png)
3. **重启IDEA！！！**然后重新登陆<br>


  [1]: https://blog.twhmr.cn/usr/uploads/2020/12/3717081326.png
  [2]: https://blog.twhmr.cn/usr/uploads/2020/12/4022620643.png
  [3]: https://blog.twhmr.cn/usr/uploads/2020/12/2221733527.png