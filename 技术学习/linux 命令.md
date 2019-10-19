/dev/mapper/rhel-root





df -h 磁盘



三、xshell连接虚拟机

1.连接xshell需要开通虚拟机的22端口

2、查看想开的端口是否已开 # firewall-cmd --query-port=6379/tcp    打印no表示未开启

3、开启永久端口号 # firewall-cmd --add-port=22/tcp --permanent   打印success 表示开启端口成功 

4、重新载入配置 # firewall-cmd --reload

5、再次查看22端口是否开启 # firewall-cmd --query-port=22/tcp    打印yes



解压 :unzip  xxx 

