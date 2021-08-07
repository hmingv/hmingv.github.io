---
layout:     post
title:      "Github 无法正常加载图片怎么办？"
subtitle:   "Github 无法正常加载图片怎么办？"
date:       2020-08-18 09:00
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Github
---

#### 在国内使用Github时，会发现网络很慢且图片始终无法正常加载

##### 修改系统根 C:\\Windows\\System32\\drivers\\etc 路径下的 hosts 文件，追加以下内容

```
# GitHub Start 
192.30.253.112 Build software better, together 
192.30.253.119 gist.github.com
151.101.184.133 assets-cdn.github.com
151.101.184.133 raw.githubusercontent.com
151.101.184.133 gist.githubusercontent.com
151.101.184.133 cloud.githubusercontent.com
151.101.184.133 camo.githubusercontent.com
151.101.184.133 avatars0.githubusercontent.com
151.101.184.133 avatars1.githubusercontent.com
151.101.184.133 avatars2.githubusercontent.com
151.101.184.133 avatars3.githubusercontent.com
151.101.184.133 Build software better, together
151.101.184.133 avatars5.githubusercontent.com
151.101.184.133 avatars6.githubusercontent.com
151.101.184.133 avatars7.githubusercontent.com
151.101.184.133 Build software better, together
```

修改该文件会出现权限问题，可参考以下解决方案：

 1. 修改文件权限后重新保存
 2. 右键以管理员身份打开后编辑保存
 3. 将文件复制到桌面修改保存，再粘贴回原处覆盖掉即可