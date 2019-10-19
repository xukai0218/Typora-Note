# 客户端使用

## 1、导入pom.xml

```
<dependency>
  <groupId>com.ruigu.rbox.cloud.config</groupId>
  <artifactId>rbox-cloud-config-client</artifactId>
  <version>1.4.0</version>
</dependency>
```

## 2、启动类增加注解

```
@EnableRBoxConfigClient
```

## 3、写配置文件

*注意

```
rbox.app.id= 唯一编号
apollo.meta:= http://192.168.1.68:9101  开发环境 
apollo.meta:= http://192.168.1.68:9103  测试环境 
本地缓存路径 开发设置
apollo.cacheDir= /opt/data/dev          linux 
apollo.cacheDir= C:/opt/data/dev 		win  
测试
apollo.cacheDir= /opt/data/test         linux 
apollo.cacheDir= C:/opt/data/test 		win 
```

bootstrap-dev.yml

```
rbox:
  app:
    id: rbox-search
apollo:
  meta: http://192.168.1.68:9101
  cacheDir: /opt/data/dev
bootstrap:
  #在应用启动阶段，向Spring容器注入被托管的application.properties文件的配置信息
  enabled: true
  eagerLoad:
    #Apollo配置加载提到初始化日志系统之前。
    enabled: true
  #使用配置的命名空间，多个以逗号分隔
  namespaces: application
```

bootstrap-test.yml

```
rbox:
  app:
    id: rbox-search
apollo:
  meta: http://192.168.1.68:9103
  cacheDir: /opt/data/test
bootstrap:
  enabled: true
  eagerLoad:
    enabled: true
  namespaces: application

```

# Portal使用

## 1、登入地址

```
http://192.168.1.68:9100
```

[](http://192.168.1.68:9100/)

## 2、用户

```
用户/密码    apollo/admin
```

## 3、创建项目

```
填写相关信息
```

## 4、进入指定项目

### 1、



