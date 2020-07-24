### 环境准备

1、准备两台集群 ，hostname分别为：lanweb78、lanweb79 （hostname文件位置：/etc/sysconfig/network，修改完要重启机器）

2、IP地址分别为：192.168.192.78，192.168.192.79

3、修改两个两个机器hosts 文件，hosts文件目录为：/etc/hosts

```
[root@lanweb ~]# vim  /etc/hosts
127.0.0.1 localhost lanweb78

## isp系统mq 新增如下两行
192.168.192.78 lanweb78
192.168.192.79 lanweb79
```

4、测试两台机器之间是否能互相通信，两台集群相互ping一下

```
[root@lanweb ~]# ping lanweb78
PING localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.035 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.020 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.020 ms
64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.020 ms
```

5、两台机器分别已近成功安装Rabbitmq，如没有安装参考Rabbitmq安装记录

### 搭建Rabbitmq集群

​         在上述的两台机器上安装rabbitmq完成之后，你可以看到你的机器中有.erlang.cookie文件。路径在$HOME中或者在/var/lib/rabbitmq中，文件名称为.erlang.cookie,他是一个隐藏文件。

- 如果我们使用解压缩方式安装部署的rabbitmq，那么这个文件会在${home}目录下，也就是$home/.erlang.cookie。
- 如果我们使用rpm等安装包方式进行安装的，那么这个文件会在/var/lib/rabbitmq目录下。

我们可以通过rabbitmq的启动日志查看其home目录是哪里，就可以知道.erlang.cookie存放在哪里，rabbitmq日志目录可以通过rabbitmq管理后台查看

```
Log file	/opt/rabbitmq_server-3.6.10/var/log/rabbitmq/rabbit@inter.log
```

1、必须使集群中也就是lanweb78，lanweb79这两台机器的.erlang.cookie文件中cookie值一致

2、停止当前机器中rabbitmq的服务 

```
[root@lanweb79 sbin]# ./rabbitmqctl stop_app
```

3、把lanweb79中的rabbitmq加入到集群中来  

```
[root@lanweb79 sbin]# ./rabbitmqctl join_cluster  rabbit@lanweb78
```

4、开启当前机器的rabbitmq服务

```
[root@lanweb79 sbin]# ./rabbitmqctl start_app
```

5、查看集群状态

```
[root@lanweb79 sbin]# ./rabbitmqctl cluster_status
```

6、打开网页管理页面查看nodes，可以看到两个nodes节点

### 搭建Rabbitmq的镜像高可用模式集群

首先镜像模式要依赖policy模块，配置policy模式

```
[root@lanweb78 sbin]# ./rabbitmqctl set_policy ha-all "^" '{"ha-mode":"all"}'
```

参数意思为：

ha-all：为策略名称。

^：为匹配符，只有一个^代表匹配所有，^zlh为匹配名称为zlh的exchanges或者queue。

ha-mode：为匹配类型，他分为3种模式：all-所有（所有的queue），exctly-部分（需配置ha-params参数，此参数为int类型比如3，众多集群中的随机3台机器），nodes-指定（需配置ha-params参数，此参数为数组类型比如["lanweb78"]这样指定为一台机器。）。

