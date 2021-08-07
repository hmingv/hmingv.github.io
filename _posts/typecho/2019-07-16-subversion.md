---
layout:     post
title:      "Subversion 编译安装"
subtitle:   "Subversion 编译安装"
date:       2019-07-16 14:36
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Subversion
    - Linux
---

> 注：

> /application 目录为自定义程序安装目录，可修改

> /application/codeP 为自定义程序源码包/安装包目录，可修改


> 若编译出现错误，请翻到最底部查找错误解决方法，若无方法，则百度

> 转载请注明出处！！！


> ### 开始安装

> subverion安装的依赖 apr apr-util httpd scons serf zlib sqlite openssl

> 其中openssl为加密工具，无https配置需要可不安装，本文档便未安装

> 安装 apr 和 apr-util （客户端和<a href="" target="_blank">服务器</a>需要）

```
# Apache Portable Runtime（APR）库提供了一个
# 抽象操作系统级服务，如文件 和网络I / O，内存管理等。
# 它也是为哈希表之类的东西提供方便的例程，
# 校验和和参数处理。虽然它原来是
# APR是为Apache HTTP<a href="" target="_blank">服务器</a>开发的，它是一个独立的
# Subversion和其他产品使用的库。它是一个
# 所有Subversion的关键依赖; 这是一层
# 允许Subversion客户端和<a href="" target="_blank">服务器</a>运行不同的操作系统。

[root@localhost ~] cd /application/codeP
[root@localhost src] wget http://mirror.bit.edu.cn/apache/apr/apr-1.6.3.tar.gz
[root@localhost src] tar zxvf apr-1.6.3.tar.gz
[root@localhost src] cd apr-1.6.3
[root@localhost apr-1.6.3] ./configure --prefix=/application/apr
[root@localhost apr-1.6.3]  make && make install
[root@localhost apr-1.6.3] cd /application/codeP
[root@localhost src] 
[root@localhost src] wget http://mirror.bit.edu.cn/apache/apr/apr-1.6.2.tar.gz
[root@localhost src] tar zxvf apr-util-1.6.2.tar.gz
[root@localhost src] cd apr-util-1.6.2
[root@localhost apr-util-1.6.2]  ./configure --prefix=/application/apr-util --with-apr=/application/apr
[root@localhost apr-util-1.6.2]  make && make install
```
> 安装 http 

```
[root@localhost src] wget http://mirror.bit.edu.cn/apache//httpd/httpd-2.4.29.tar.gz
[root@localhost src] tar zxvf httpd-2.4.29.tar.gz
[root@localhost src] cd httpd-2.4.29
[root@localhost httpd-2.4.29] ./configure --prefix=/usr/local/apache24 --sysconfdir=/etc/httpd/conf --with-included-apr --disable-userdir --enable-so --enable-deflate --enable-expires --enable-rewrite --enable-static-support --with-mpm=prefork
[root@localhost httpd-2.4.29] make && make install
配置系统启动项
[root@localhost httpd-2.4.29] groupadd apache # 创建apache用户
[root@localhost httpd-2.4.29] useradd -g apache -s /usr/sbin/nologin apache # 给用户设置不需登录
[root@localhost httpd-2.4.29] chown -R apache:apache /usr/local/apache24 # 绑定apache用户组和apache的关系
[root@localhost httpd-2.4.29] cd /usr/local/apache24/bin
[root@localhost bin] cp apachectl /etc/init.d/httpd
[root@localhost bin] vim /etc/init.d/httpd
在 表示文件为sh !/bin/sh 之后添加
# chkconfig:2345 61 61
# description:Apache httpd 
例如：
#!/bin/sh
# chkconfig:2345 61 61
# description:Apache httpd 
#
#
...
修改后 使用  :wq 保存退出
[root@localhost bin] chkconfig --add httpd # 添加httpd 到系统服务
[root@localhost bin] chkconfig --list # 查看列表是否添加成功
[root@localhost bin] systemctl start httpd # 启动httpd服务
[root@localhost bin] curl localhost # 访问<a href="" target="_blank">服务器</a>查看测试是否安装成功
# 具体HTTPD安装编译步骤查看 http://note.youdao.com/noteshare?id=731b233d80c267bbee4e5cd6b1be7029&sub=A7B77B7397FD4CF19E627E676219FCD1
```

> 安装 OpenSSL （可选用于客户端和<a href="" target="_blank">服务器</a>）

```
# 除了未加密的http：// URL之外，
# OpenSSL还允许您的客户端访问SSL加密的https：// URL（使用Apache Serf）。
# 要在Subversion的WebDAV<a href="" target="_blank">服务器</a>上使用SSL，Apache也需要
# 使用OpenSSL 进行编译。
```

> 安装 sqlite （客户端和<a href="" target="_blank">服务器</a>需要）

```
#  Subversion使用SQLite来管理一些内部数据库。

[root@localhost ~] cd /application/codeP
[root@localhost src] wget http://sqlite.org/2018/sqlite-autoconf-3230100.tar.gz
[root@localhost src] tar zxvf sqlite-autoconf-3230100.tar.gz
[root@localhost src] cd sqlite-autoconf-3230100
[root@localhost sqlite-autoconf-3230100] ./configure --prefix=/application/sqlite
[root@localhost sqlite-autoconf-3230100]  make && make install
```
> 安装 zilb （客户端和<a href="" target="_blank">服务器</a>需要）

```
#  Subversion使用zlib来压缩二进制差异。
#  这些差异流在任何地方使用 - 通过网络，
#  在存储库中，以及客户端的工作副本中。

[root@localhost ~] cd /application/codeP
[root@localhost src] wget http://www.zlib.net/zlib-1.2.11.tar.gz
[root@localhost src] tar zxvf zlib-1.2.11.tar.gz 
[root@localhost src] cd zlib-1.2.11
[root@localhost zlib-1.2.11] ./configure --prefix=/application/zilb
[root@localhost zlib-1.2.11]  make && make install
```
> 安装 scons scons也是必需的
注：安装serf 需要使用scons进行编译安装

```
[root@localhost ~] cd /application/codeP
[root@localhost src] wget http://prdownloads.sourceforge.net/scons/scons-3.0.0.tar.gz
[root@localhost src] tar zxvf scons-3.0.0.tar.gz
[root@localhost src] cd scons-3.0.0
[root@localhost scons-3.0.0] python setup.py install --prefix=/application/scons
```
> 安装 Apache serf （可选客户端）

```
# Apache Serf库允许Subversion客户端发送HTTP
# 要求。如果您希望客户端访问，则必须执行此操作
# Apache HTTP<a href="" target="_blank">服务器</a>提供的存储库。有一个
# 但是，替代'svnserve'<a href="" target="_blank">服务器</a>和客户端
# 自动知道如何说svnserve协议。
# 因此，您的客户并不一定非常必要
# 说HTTP ...虽然我们仍然建议您的客户端
# 可以构建为HTTP和svnserve协议。
         
[root@localhost ~] cd /application/codeP
[root@localhost src] wget https://www.apache.org/dist/serf/serf-1.3.9.zip
[root@localhost src] unzip serf-1.3.9.zip
[root@localhost src] cd serf-1.3.9
[root@localhost serf-1.3.9] /application/scons/bin/scons PREFIX=/application/serf ARR=/application/apr APU=/application/apr-util/
[root@localhost serf-1.3.9] /application/scons/bin/scons install
```
> 安装 subverison

```
[root@localhost ~] cd /application/codeP
[root@localhost src] wget http://mirrors.shu.edu.cn/apache/subversion/subversion-1.9.7.tar.gz
[root@localhost src] tar zxvf subversion-1.9.7.tar.gz
[root@localhost src] cd subversion-1.9.7
[root@localhost subversion-1.9.7] ./configure --prefix=/application/subversion --with-apr=/application/apr --with-apr-util=/application/apr-util --with-apxs=/application/apache24/bin/apxs --with-sqlite=/application/sqlite --with-zlib=/application/zlib --with-serf=/application/serf
[root@localhost subversion-1.9.7]  make && make install
```

> 配置环境变量

```
[root@localhost ~] vim /etc/profile
# 添加 PATH=$PATH:/usr/local/subversion/bin
[root@localhost ~] source /etc/profile
[root@localhost ~] svn --version # 出现以下版本内容即正常
```
```
svn, version 1.9.7 (r1800392)
   compiled Feb 21 2018, 16:32:27 on x86_64-unknown-linux-gnu

Copyright (C) 2017 The Apache Software Foundation.
This software consists of contributions made by many people;
see the NOTICE file for more information.
Subversion is open source software, see http://subversion.apache.org/

```

> ### 配置用户信息


>  创建版本库目录（为创建版本库提供存放目录）

```
[root@localhost ~] mkdir -p /wodata/svndata/svnrepos
```
> 创建版本库

```
[root@localhost ~] svnadmin create /wodata/svndata/svnrepos
```

> 启动subversion版本库 通过netstat -ntpl查询正在运行的端口，subversion默认3690

```
[root@localhost ~] svnserve -d -r /wodata/svndata/svnrepos
```

```
# 创建成功，进入svnrepos目录
# 进入 conf 修改配置文件
# authz：负责账号权限的管理，控制账号是否读写权限
# passwd：负责账号与密码的用户名单管理
# svnserve.conf：svn<a href="" target="_blank">服务器</a>配置文件
```

> 修改 authz，passwd 文件

```
# authz  权限控制文件
# passwd 用户管理文件
# 修改配置文件， = 左右空格必须删除，不能存在空格

# 在末尾添加
[\] # 此处必须是反斜杠
账号1=rw # rw 赋予此账号可读写的权限
账号2=rw
```
```
# 修改passwd文件
# 直接追加账号密码信息到文件中，格式如下
# 账号=密码
# 例如 
admin=admin
```
> 修改svnserve.conf（必须）
将以下的行全部取消注释

```
# 修改配置文件， = 左右空格必须删除，不能存在空格

# 匿名用户可读，建议修改为none 匿名用户不可读
# auon-access=read
# 授权用户可写，只需打开注释无需修改
# autn-access=write
# 使用哪个文件作为账号密码文件，只需打开注释无需修改
# password-db=passwd
# 使用哪个文件作为权限文件，只需打开注释无需修改
# authz-db=authz
# 认证空间名，版本库所在目录，需要修改成版本库所在位置
# realm=My First Repository
```

> ### 配置HTTP访问

```
[root@localhost ~] cp /application/subversion/libexec/mod_authz_svn.so /application/apache24/modules/
[root@localhost ~] cp /application/subversion/libexec/mod_dav_svn.so /application/apache24/modules/
[root@localhost ~] vim /etc/httpd/conf/httpd.conf
# 去掉下面两行的注释
LoadModule dav_module modules/mod_dav.so
LoadModule dav_fs_module modules/mod_dav_fs.so
# 追加下面两行，使apache加载模块
LoadModule dav_svn_module modules/mod_dav_svn.so
LoadModule authz_svn_module modules/mod_authz_svn.so
# 去掉此行注释，使apache服务调用配置文件
Include /etc/httpd/conf/extra/httpd-vhosts.conf
[root@localhost ~] vim /etc/httpd/conf/extra/httpd-vhosts.conf
# 添加内容
# svn <a href="" target="_blank">服务器</a>http访问
<Location /svn>
    DAV svn
    SVNPath /wodata/svndata/svnrepos
    AuthName "Subversion repository"
    AuthType Basic
    AuthUserFile /wodata/svndata/svnrepos/passwordfile
    Require valid-user
</Location>

# "SVNPath /wodata/svndata/svnrepos/" 表示 /wodata/svndata/svnrepos/ 下的每个子目录都是一个版本库。可以通过 http://<a href="" target="_blank">服务器</a>IP/svn来访问。
#如果想要指定多个版本库，可以用多个 Location 标签，也可以使用 SVNParentPath 代替 SVNPath，例如在 /wodata/svndata/svnrepos 下有多个版本库 repos1，repos2 等等，用如下方式指定：
<Location /svn>
    DAV svn
    SVNParentPath /wodata/svndata/svnrepos
    AuthName "Subversion repository"
    AuthType Basic
    AuthUserFile /wodata/svndata/svnrepos/passwordfile
    Require valid-user
</Location>
# 现在你的版本库任何人都可以访问，并且有完全的写操作权限。也就是说任何人都可以匿名读取，修改，提交，以及删除版本库中的内容，Apache 提供了基本的权限设置：
# 首先需要创建一个用户文件。Apache 提供了一个工具 htpasswd，用于生成用户文件，可以在 Apache 的安装目录下找到。具体使用方法如下：

[root@localhost ~] htpasswd -c /wodata/svndata/svnrepos/passwordfile root # 创建用户root，敲回车之后自动提示输入密码
# 如果 passwordfile 不存在，可以加上 -c 选项让 htpasswd 新建一个。创建好的文件内容是用户名加上密码的 MD5 密文。

# 接下来修改 httpd.conf，在 Location 标签中加入如下内容：
AuthType Basic
AuthName "Subversion repository"  # 登陆时提示信息
AuthUserFile /wodata/svndata/svnrepos/passwordfile # 密码文件路径
Require valid-user

[root@localhost ~] systemctl restart httpd # 重新启动 Apache， 打开浏览器访问版本库。
# Apache 会提示你输入用户名和密码来认证登陆了，现在只有 passwordfile 文件中设定的用户才可以访问版本库。也可以配置只有特定用户可以访问，替换上述 "Require valid-user" 为 "Require user tony robert" 将只有 tony 和 robert 可以访问该版本库。

# 有的时候也许不需要这样严格的访问控制，例如大多数开源项目允许匿名的读取操作，而只有认证用户才允许写操作。为了实现更为细致的权限认证，可以使用 Limit 和 LimitExcept 标签。例如：
<LimitExcept GET PROPFIND OPTIONS REPORT>
    require valid-user
</LimitExcept>
#如上配置将使匿名用户有读取权限，而限制只有 passwordfile 中配置的用户可以使用写操作。如果这还不能满足你的要求，可以使用 Apache 的 mod_authz_svn 模块对每个目录进行认证操作。
### 遗留问题：设置密码文件之后，不知subversion原来的authz文件是否还起作用
### 参考 https://www.ibm.com/developerworks/cn/java/j-lo-apache-subversion/
```
> http方式通过用 mod_authz_svn 获取目录控制

```
# 需要确保以下模块成功加载
LoadModule authz_svn_module modules/mod_authz_svn.so
# 现在需要在 Location 标签中指定 authz 的路径。配置如下：

<Location /svn>
    DAV svn
    SVNPath /wodata/svndata/svnrepos
    AuthName "Subversion repository"
    AuthType Basic
    AuthUserFile /wodata/svndata/svnrepos/passwordfile
    AuthzSVNAccessFile /wodata/svndata/svnrepos/accesspolicy
    Satisfy Any 
    Require valid-user
</Location>

# AuthzSVNAccessFile 指向的是 authz 的策略文件，详细的权限控制需要在这个文件指定:

# 该配置项复制与 subversion /cnof 目录下的authz文件
# 用户组，本人未使用
[groups]
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe

# 设置根目录下所有人拥有读权限 * 代表所有人
[/]
* = r
# sole目录下，zc,whm,wmy 拥有去写权限，root 用户禁止访问
[/sole]
zc = rw
root =
whm = rw
wmy = rw

# 禁止所有用户访问 parivate 目录，但admin组可读
[/private] 
* = 
# 给 admin 组用户读权限
@admin= r
```


> ### 错误

> 1. 使用./svn命令报错
```
#    svn: error while loading shared libraries: libserf-1.so.1: cannot open shared object file: No such file or directory
#    解决方法:
#    把serf安装后的 libserf-1.so.1 复制到svn的安装目录下的lib目录
    
[root@localhost src] cp ./serf/lib/libserf-1.so.1 ./subversion/lib/
```

> 2. 启动程序时报错
```
svn: E200002: /root/.subversion/servers:113: Option expected
    
#   原因：subversion读取配置文件svnserve.conf时，无法 识别有前置空格的配置文件
#   所以，svn配置文件顶格写
#   解决方法: 修改报错文件内未定格写的代码
```

> 3. 编译serf时报错
```
[root@localhost serf-1.3.9] /application/scons/bin/scons PREFIX=/application/serf ARR=/application/apr/bin/apr-1-config APU=/application/apr-util/bin/apu-1-config
scons: Reading SConscript files ...
  File "/application/codeP/svn/serf-1.3.9/SConstruct", line 186

    print 'Warning: Used unknown variables:', ', '.join(unknown.keys())

                                           ^

SyntaxError: invalid syntax

# 这里只是个警告打印，并没有在if分支里退出编译，说明如果没有这个打印，是不影响结果的，vim修改文件，注释掉了这个打印分支：
unknown = opts.UnknownVariables()  
#if unknown:  
#  print 'Warning: Used unknown variables:', ', '.join(unknown.keys())  
...  

```

> 4. 编译serf时报错

```
[root@localhost serf-1.3.9] /application/scons/bin/scons PREFIX=/application/serf ARR=/application/apr/bin/apr-1-config APU=/application/apr-util/bin/apu-1-config
scons: Reading SConscript files ...
Checking for GNU-compatible C compiler...yes
/bin/sh: /usr/bin/apr-1-config: No such file or directory
OSError: '/usr/bin/apr-1-config --cflags --cppflags --ldflags --includes --link-ld --libs' exited 127:
  File "/application/codeP/svn/serf-1.3.9/SConstruct", line 360:
    env.ParseConfig('$APR --cflags --cppflags --ldflags --includes'
  File "/application/scons/lib/scons-3.0.0/SCons/Environment.py", line 1557:
    return function(self, self.backtick(command))
  File "/application/scons/lib/scons-3.0.0/SCons/Environment.py", line 594:
    raise OSError("'%s' exited %d" % (command, status))

# /bin/sh: /usr/bin/apr-1-config: 没有这样的文件或目录
# 解决方法:复制一份 apr-1-config到/usr/bin目录

[root@localhost serf-1.3.9] cp /application/apr/bin/apr-1-config /usr/bin/apr-1-config

```

> 5. 客户端连接<a href="" target="_blank">服务器</a>时报错
> 
```
# Can't connect to host '*.*.*.*': 由于连接方在一段时间后没有正确答复或连接的主机没有响应，连接尝试失败。
# 以下多方面原因：
# <a href="" target="_blank">服务器</a>防火墙拦截
# <a href="" target="_blank">服务器</a>代理商限制端口访问，可通过控制台开启
```

> 6. configure: error: Subversion requires LZ4 >= r129, or use --with-lz4=internal

```
./configure命令添加 --with-lz4=internal选项
```

> 7. configure: error: Subversion requires UTF8PROC

```
./configure命令添加  --with-utf8proc=internal
```

> 8. 启动时 : svnserve: Can't bind server socket: Address already in use

```
# <a href="" target="_blank">服务器</a>：无法绑定<a href="" target="_blank">服务器</a>套接字：地址已在使用中
# 先是关闭subversion<a href="" target="_blank">服务器</a>，重新启动
# killall svnserve
```