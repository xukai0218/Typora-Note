分布式部署指南：

```
https://github.com/ctripcorp/apollo/wiki/%E5%88%86%E5%B8%83%E5%BC%8F%E9%83%A8%E7%BD%B2%E6%8C%87%E5%8D%97
```



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
  eagerLoad:
    #Apollo配置加载提到初始化日志系统之前。
    enabled: true
  #使用配置的命名空间，多个以逗号分隔 默认为：application
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
  enabled: false
  eagerLoad:
    enabled: true
  namespaces: application

```

## 4、java config

zhu

```
@Bean("ApolloConfigChanged")
ApolloConfigChanged apolloConfigChanged() {
    return new ApolloConfigChanged();
}
```



```
package com.ruigu.bpos.order.config;

import com.ctrip.framework.apollo.model.ConfigChange;
import com.ctrip.framework.apollo.model.ConfigChangeEvent;
import com.ctrip.framework.apollo.spring.annotation.ApolloConfigChangeListener;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.context.environment.EnvironmentChangeEvent;
import org.springframework.cloud.context.scope.refresh.RefreshScope;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;
/**
 * Apollo 监听自动刷新
 * @author xukai
 * @date 2019-10-28 19:05
 */
@Slf4j
@Component
public class ApolloConfigChanged implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    @Autowired
    private RefreshScope refreshScope;

    /**
     * 刷新的namespace的名字：application
     */
    @ApolloConfigChangeListener("application")
    public void someChangeHandler(ConfigChangeEvent changeEvent) {
        log.info("================Apollo 自动刷新值 开始 ===========================");
        for (String changedKey : changeEvent.changedKeys()) {
            ConfigChange configChange = changeEvent.getChange(changedKey);
            String oldValue = configChange.getOldValue();
            String newValue = configChange.getNewValue();
            log.info("changedKey:【{}】,oldValue=【{}】, newValue:【{}】", changedKey, oldValue, newValue);
        }

        refreshProperties(changeEvent);

        log.info("================Apollo 自动刷新值 结束 ===========================");
    }

    public void refreshProperties(ConfigChangeEvent changeEvent) {
        // 更新相应的bean的属性值，主要是存在@ConfigurationProperties注解的bean
        this.applicationContext.publishEvent(new EnvironmentChangeEvent(changeEvent.changedKeys()));
        refreshScope.refreshAll();
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
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
1、点击创建项目 填写信息

2、点击进入相应项目填写配置信息


```



![Apollo](/uploads/0221a241062d315a2c80882852425d92/Apollo.png)

## 4、进入指定项目

# Apollo 自动刷新

```

```

在线装换工具

```
https://www.toyaml.com/index.html
```