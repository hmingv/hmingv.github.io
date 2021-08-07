---
layout:     post
title:      "Redis 编译安装"
subtitle:   "Redis 编译安装"
date:       2018-05-01 14:42
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - Redis
    - Linux
---

> 注：

> /application 目录为自定义程序安装目录，可修改

> /application/codeP 为自定义程序源码包/安装包目录，可修改


> 若编译出现错误，请翻到最底部查找错误解决方法，若无方法，则百度

> 转载请注明出处！！！


> ####  安装-配置Redis

```
$ wget http://download.redis.io/releases/redis-4.0.2.tar.gz
$ tar xzf redis-4.0.2.tar.gz
$ cd redis-4.0.2
$ make
```

> 到现在就安装成功了 下面开始测试使用

```
$ src/redis-server # 启动 redis服务
$ src/redis-cli    # 进入 redis进程 执行增删改查

redis> set index hello
OK
redis> get index
"hello"
```

> 启动了redis服务之后，当前窗口就不可以使用了，因为被服务占用，下面设置redis后台运行
> 打开 源码 根目录下 redis.conf

```
$ vim ./redis.conf
```
> 找到 daemonize no 把 no 改为 yes 就成功设置为 后台运行模式

> 设置密码 找到 # requirepass foobared 取消注释 并把 foobared 改为 自己需要的密码 及成功

> 找到 # rename-command CONFIG "" 取消注释 并在 “” 中随意添加字符 防止漏洞产生

```
################################# GENERAL #####################################

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# daemonize no
daemonize yes

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised no

################################## SECURITY ###################################

# Require clients to issue AUTH <PASSWORD> before processing any other
# commands.  This might be useful in environments in which you do not trust
# others with access to the host running redis-server.
#
# This should stay commented out for backward compatibility and because most
# people do not need auth (e.g. they run their own servers).
#
# Warning: since Redis is pretty fast an outside user can try up to
# 150k passwords per second against a good box. This means that you should
# use a very strong password otherwise it will be very easy to break.
#
# requirepass foobared
requirepass whm
# Command renaming.
#
# It is possible to change the name of dangerous commands in a shared
# environment. For instance the CONFIG command may be renamed into something
# hard to guess so that it will still be available for internal-use tools
# but not available for general clients.
#
# Example:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# It is also possible to completely kill a command by renaming it into
# an empty string:
#
# rename-command CONFIG ""
#
# Please note that changing the name of commands that are logged into the
# AOF file or transmitted to slaves may cause problems.

```

> 使用加密码的进行测试

```
$ src/redis-cli shutdown # 关闭redis服务  
$ src/redis-server ./redis.conf  # 启动redis服务 并制定conf文件 ./redis.conf 是刚才修改的conf文件目录，需要指定
$ src/redis-cli -a whm   
redis> set index hello
OK
redis> get index
"hello"

$ # 如果没有指定文件目录，或者密码输入错误，可以正常进入程序。但当发生操作的时候会报错

(error) NOAUTH Authentication required.
```

> 可选 操作    复杂操作 ====>>> 添加至系统服务

```
$ cp utils/redis_init_script /etc/init.d/redisd  # 添加到系统服务列表
$ mkdir bin                                      # 把 服务项放在bin目录中
$ cp ./src/redis-server ./bin/
$ cp ./src/redis-cli ./bin/
$ vim /etc/init.d/redisd
```

> 在第二行 添加 # 自启 不添加的话 无法添加服务
  
```
# chkconfig: 2345 90 10 
```

> 继续执行添加自启

```
$ chkconfig --add redisd  
$ 服务 redisd 不支持 chkconfig             # 可能出现的报错信息 
service redisd does not support chkconfig
```

> 如果你设置了 redis 密码 最好把 $CLIEXEC -p $REDISPORT shutdown
改为 $CLIEXEC -a "passwod" -p $REDISPORT shutdown  其中 password 为你设置的redis密码  

```
$ service redisd start   # 启动redis 服务 正常
$ service redisd stop    # 出现下面报错信息 原因是 noauth认证要求。
$ Stopping ...
$ (error) NOAUTH Authentication required.
$ Waiting for Redis to shutdown ...
$ Waiting for Redis to shutdown ...
$ Waiting for Redis to shutdown ...

$ service redisd stop -a password # 指定密码 第一个参数 必须是 start 或 stop
$ Please use start or stop as first argument # 所以按照上面标题操作即可

```

```
#!/bin/sh
# chkconfig: 2345 90 10
#
# Simple Redis init.d script conceived to work on Linux systems
# as it does use of the /proc filesystem.

REDISPORT=6379
EXEC=/usr/local/redis-4.0.2/bin/redis-server
CLIEXEC=/usr/local/redis-4.0.2/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/etc/redis/${REDISPORT}.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -a "whm" -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac
```

> 简单操作 ====>>> 添加至环境变量

```
$ vim /etc/profile  # 最后一行添加 PATH=$PATH:/usr/local/redis-4.0.2/bin
$ source /etc/profile # 导入脚本 快速生效 这样就可以在任何在任意目录 打开redis操作了

$ redis-server -v  # 查看版本信息
$ redis-server /etc/redis/6379.conf # 打开服务 并使用指定的配置文件
$ redis-cli -a whm # 使用密码登录访问

redis> set index hello
OK
redis> get index
"hello"
```

> 之后即可正常启动

```
$ service redisd start   # 启动redis 服务 正常
$ ps -aux | grep redis   # 查看是否启动正常
root      42795  0.1  0.2  41668  2212 ?        Ssl  21:09   0:03
/usr/local/redis-4.0.2/bin/redis-server 127.0.0.1:6379
root      42910  0.0  0.0 112676   980 pts/1    S+   21:45   0:00 grep --color=auto redis

$ service redisd stop    # 正常关闭
$ Stopping ...
$ Redis stopped       

$ service redisd start
$ ./bin/redis-cli -a whm
127.0.0.1:6379> set whm 123
OK
127.0.0.1:6379> get whm
"123"
``` 

==如需要修改密码，请修改 /etc/init.d/redisd
内指定的 配置文件 修改完密码后 将 /etc/init.d/redisd 内 关闭时密码 同步修改==

> #### 配置phpredis扩展 -常用

```
$ wget https://github.com/phpredis/phpredis/archive/3.1.4.tar.gz
$ cd phpredis-3.1.4                      # 进入 phpredis 目录
$ /usr/local/php/bin/phpize              # php安装后的路径
$ ./configure --with-php-config=/usr/local/php/bin/php-config
$ make && make install

$ vim /etc/php/php.ini                      # 修改php.ini文件，添加扩展
  extension=redis.so                    
$ systemctl restart httpd                  # 重启apache
$ vim /usr/local/apache24/htdocs/redis.php # 编辑 redis文件 添加以下代码-测试是否走通
<?php
    //连接本地的 Redis 服务
   $redis = new Redis();
   $redis->connect('127.0.0.1', 6379);
   echo 'Connection to server sucessfully';
         //查看服务是否运行
   echo 'Server is running: ' . $redis->ping();
?>

# 上面是无密码，以下为有密码的访问
<?php
    //连接本地的 Redis 服务
   $redis = new Redis();
   $redis->connect('127.0.0.1', 6379);
   $redis->auth('password');
   echo 'Connection to server sucessfully'.'<br/>';
         //查看服务是否运行
   echo 'Server is running: ' . $redis->ping();
?>
```

> 输出结果为  出现 PONG 即为成功

```
Connection to server sucessfully
Server is running: PONG
```

> #### 配置 predis 链接到 php 使用 - 以下为使用 predis 快速扩展 不建议使用

```
$ wget https://github.com/nrk/predis/archive/v1.1.1.tar.gz
$ tar zxvf v1.1.1.tar.gz
$ cp predis-1.1.1 /usr/local/apache24/htdocs/predis # copy 到项目目录
$ vim redis.php # 编写测试文件

<?php
namespace Predis;
use Predis;

require './predis/autoload.php';
$redis = new Predis\Client();

$redis->set('index','hello');
$value = $redis->get('index');
echo $value;

$ curl localhost/redis.php # 访问页面 输出
hello
```

> 报错 # 原因 没有指定密码 链接不上

```

Fatal error: Uncaught Predis\Response\ServerException: 
NOAUTH Authentication required. in
/usr/local/apache24/htdocs/predis/src/Client.php:370 Stack trace:
#0 /usr/local/apache24/htdocs/predis/src/Client.php(335):  
Predis\Client->onErrorResponse(Object(Predis\Command\StringSet), Object(Predis\Response\Error))
#1 /usr/local/apache24/htdocs/predis/src/Client.php(314):
Predis\Client->executeCommand(Object(Predis\Command\StringSet)) 
#2 /usr/local/apache24/htdocs/redis.php(8): 
Predis\Client->__call('set', Array) 
#3 {main} thrown in /usr/local/apache24/htdocs/predis/src/Client.php on line 370
```

> 带密码的访问

```
$ vim redis.php # 编写测试文件

<?php
namespace Predis;
use Predis;

require './predis/autoload.php';
$redis = new Predis\Client(['127.0.0.1:6379'],['parameters'=>['password'=>'whm1']]);

$redis->set('index','hello');
$value = $redis->get('index');
echo $value;

$ curl localhost/redis.php # 访问页面 输出
hello
```