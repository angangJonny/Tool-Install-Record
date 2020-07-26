### 查看系统自带jdk

查看命令：rpm -qa|grep java

```
[root@localhost bin]# rpm -qa | grep java
java-1.6.0-openjdk-1.6.0.0-1.66.1.13.0.el6.x86_64
java-1.7.0-openjdk-1.7.0.45-2.4.3.3.el6.x86_64
tzdata-java-2013g-1.el6.noarch
```

2.卸载已有jdk:

```
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.45-2.4.3.3.el6 java-1.6.0-openjdk-1.6.0.0-1.66.1.13.0.el6.x86_64 tzdata-java-2013g-1.el6.noarch
```

注：如果使用 rpm -qa|grep java 命令没有查到jdk包，但是使用 java -version 命令却可以看到jdk版本，可以使用 which java 命令查看 java 命令的执行路径，然后找到对应的jdk文件并删除。

### 下载jdk安装包

官网下载地址：https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

说明：需要登录oracle官网，登录后可以直接下载

从其他服务器拷贝

```
[root@inter java]# scp jdk-8u171-linux-x64.tar.gz root@192.168.172.60:/usr/java/
va/
```

本地上次（通过rz命令选择下载好的安装包）

```
[root@inter java]# rz 
```

### 安装jdk

解压安装包：

```
[root@inter java]# tar -zxvf jdk-8u211-linux-x64.tar.gz
```

如果当前目录是安装目录，那么无需拷贝，如果不是需要拷贝安装目录

```
[root@inter java]# mv jdk1.8.0_171 /usr/java/
```

修改环境变量

```
[root@localhost java]# vim /etc/profile

最后插入如下配置：
export JAVA_HOME=/usr/java/jdk1.8.0_171
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$PATH:${JAVA_PATH}
```

修改完成后需要通过命令让配置文件立即生效

```
[root@localhost java]# source /etc/profile
```

### 验证安装是否成功

验证命令

```
[root@localhost java]# javac
[root@localhost java]# java -version
```

