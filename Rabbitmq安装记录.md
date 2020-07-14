### 安装erlang

1、下载命令：

```
[root@localhost opt]# wget  http://erlang.org/download/otp_src_20.0.tar.gz
```

注意：安装erlang时需要确定，erlang与rabbitmq对应的版本

2、下载完成解压：

```linux
[root@localhost opt]# tar -zxvf  otp_src_20.0.tar.gz 
```

3、提前安装编译Erlang环境要求

```
 
[root@localhost usr]# yum install ncurses ncurses-base ncurses-devel ncurses-libs ncurses-static ncurses-term ocaml-curses ocaml-curses-devel -y
 
[root@localhost usr]# yum install openssl-devel zlib-devel –y
 
[root@localhost usr]# yum -y install make ncurses-devel gcc gcc-c++ unixODBC unixODBC-devel openssl openssl-devel 
```

4、进入解压目录、编译安装Erlang

```
[root@localhost opt] cd  /opt/otp_src_20.0
[root@localhost otp_src_20.0]# ./configure --prefix=/opt/erlang --with-ssl -enable-threads -enable-smmp-support -enable-kernel-poll --enable-hipe --without-javac
```

5、安装，比较耗时

```
[root@localhost otp_src_20.0]# make&&make install
```

6、测试Erlang 安装是否成功

```
[root@localhost opt] cd /opt/erlang/bin
[root@localhost opt] erl
Erlang/OTP 20 [erts-9.0] [source] [64-bit] [smp:32:32] [ds:32:32:10] [async-threads:10] [hipe] [kernel-poll:false]

Eshell V9.0  (abort with ^G)
1> 
说明安装成功
```

7、配置Erlang 环境变量

```
[root@localhost opt] vim  /etc/profile

添加环境变量
export ERLANG_HOME=opt/erlang
export PATH=$PATH:$ERLANG_HOME/bin
重新激活使环境变量生效
[root@localhost opt] source /etc/profile
查看是否配置正确
[root@localhost opt] echo $PATH
```

### 安装Rabbitmq

1、下载安装文件

```
[root@localhost opt]# wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.10/rabbitmq-server-generic-unix-3.6.10.tar
```

2、解压RabbitMQ

```
[root@localhost opt] tar -xvf rabbitmq-server-generic-unix-3.6.10.tar
```

3、启动RabbitMQ

```
[root@localhost opt]# cd /opt/rabbitmq_server-3.6.10/
[root@localhost rabbitmq_server-3.6.10]# cd sbin
[root@localhost sbin]# ./rabbitmq-server
```

具体命令如下：

直接启动 ./rabbitmq-server 
后台启动 ./rabbitmq-server -detached    
开启插件管理页面 ./rabbitmq-plugins enable rabbitmq_management 
关闭服务 ./rabbitmqctl stop

4、创建用户  用户名：admin   密码：123456

```
[root@localhost sbin]# ./rabbitmqctl add_user admin 123456
将admin添加为管理员
[root@localhost sbin]# ./rabbitmqctl set_user_tags admin administrator
为用户分配权限
[root@localhost sbin]# ./rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

5、开启插件管理页面

访问地址：[http://127.0.0.1:15672/#/](http://127.0.0.1:15672/#/)

用刚才创建的用户名密码登录

6、RabbitMQ配置说明

如果默认配置这里显示not found ![image-20200624110626212](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20200624110626212.png)

如果需要自定义配置需要到Rabbitmq 下载配置文件模板，然后跟进自己的使用场景配置

https://github.com/rabbitmq/rabbitmq-server/blob/v3.7.x/docs/rabbitmq.conf.example

修改完成后上传上图对应位置，重启Rabbitmq即可