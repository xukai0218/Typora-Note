/dev/mapper/rhel-root

内存

```
free -m 
free -h
```

netstat -lnpt



df -h 磁盘



三、xshell连接虚拟机

1.连接xshell需要开通虚拟机的22端口

2、查看想开的端口是否已开 # firewall-cmd --query-port=6379/tcp    打印no表示未开启

3、开启永久端口号 # firewall-cmd --add-port=22/tcp --permanent   打印success 表示开启端口成功 

4、重新载入配置 # firewall-cmd --reload

5、再次查看22端口是否开启 # firewall-cmd --query-port=22/tcp    打印yes

6   查看 开启端口 firewall-cmd --list-all

7 查看端口是否占用  lsof -i:6379



解压 :unzip  xxx 

### alias 来自定义命令实现快捷命令

```
1、使用vi编辑器打开 /root/.bashrc 文件

2、增加一条 alias 。如：alias XXX=‘cd /home/walking/weblogic/devlop/script/application/.../...’

3、保存文件，退出

4、执行source /root/.bashrc 刷新
```

###脚本 start

```/
#!/bin/bash

 if [ ! -n "$1" ];then
        echo "输入参数 env = dev or test"
        exit 1
	else
		if [ ${1} != "test" ] ; then
			if [ ${1} != "dev" ]; then
				echo "输入参数不对 env = dev or test"
				exit -1
			fi
		fi
 fi
env=$1
pid=`ps -ef |grep eureka-server-0.0.1-SNAPSHOT.jar |grep ${env} |awk '{print $2}'`

 if [  -n "$pid" ];
	then
       echo "pid = $pid env=${env}  已经启动 "
	   exit -1
	else 
		nohup java -jar eureka-server-0.0.1-SNAPSHOT.jar --spring.profiles.active=${env} &
		pid=`ps -ef |grep eureka-server-0.0.1-SNAPSHOT.jar |grep ${env} |awk '{print $2}'`

		echo "pid = $pid start execute env = ${env}"
 fi
```

stop

```
env=$1
pid=`ps -ef |grep eureka-server-0.0.1-SNAPSHOT.jar |grep ${env} |awk '{print $2}'`

 if [  -n "$pid" ];
	then
        echo "kill ${pid}"
		kill -9 ${pid}
		echo "pid = $pid env=${env} eureka stop is success"
	else 
		echo "env=${env} eureka not exist stop is failed"
 fi
```

isexit

```
env=$1
pid=`ps -ef |grep eureka-server-0.0.1-SNAPSHOT.jar |grep ${env} |awk '{print $2}'`

 if [  -n "$pid" ];
	then
       echo "env=${env} 环境 已经启动"
	else 
		echo "env=${env} 环境 未启动"
 fi
```

开机启动脚本

```
cd /etc/init.d
chkconfig --add
chkconfig --list
```

开机启动eureka 服务

```
参考链接 :http://www.mamicode.com/info-detail-2240458.html

#!/bin/bash
# chkconfig: 2345 10 90 
# description: auto_eureka_test ....

env=test
# 进入到eureka的安装目录：
cd /usr/local/dev_soft/apollo/eureka_service

# 设置一下 linux系统的JAVA环境变量：
#　export JAVA_HOME=/usr/lib/jvm/jdk1.8
#　export PATH=$JAVA_HOME/bin:$PATH
#　export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# 在后台启动 eureka服务：
nohup  java -jar eureka-server-0.0.1-SNAPSHOT.jar --spring.profiles.active=${env} &
echo "-----------完成启动Eureka服务------------"



```

