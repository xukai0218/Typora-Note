

# 一、Apollo

## 1、简介

Apollo（阿波罗）是携程框架部门研发的分布式配置中心，能够集中化管理应用不同环境、不同集群的配置，配置修改后能够实时推送到应用端，并且具备规范的权限、流程治理等特性，适用于微服务配置管理场景

服务端基于Spring Boot和Spring Cloud开发，打包后可以直接运行，不需要额外安装Tomcat等应用容器。

Java客户端不依赖任何框架，能够运行于所有Java运行时环境，同时对Spring/Spring Boot环境也有较好的支持。

## 2、运行环境

```
1) 服务端基于Spring Boot，启动脚本理论上支持所有Linux发行版，建议CentOS 7。
2) java:
        Apollo服务端：1.8+
        Apollo客户端：1.7+
        由于需要同时运行服务端和客户端，所以建议安装Java 1.8+。 java -version
3) MySQL:
		版本要求：5.6.5+
```

## 3、部署流程

```
1) 创建数据库
	Apollo服务端依赖于MySQL数据库，所以需要事先创建并完成初始化
2) 获取安装包
    Apollo服务端安装包共有3个：apollo-configservice, apollo-adminservice, apollo-portal
    可以直接下载安装包，也可以自己通过源码构建
3) 部署Apollo服务端
	获取安装包后就可以部署到公司的测试和生产环境了
```



## 4、获取安装包

1 直接下载安装包  GitHub地址：https://github.com/ctripcorp/apollo/releases



	从GitHub Release页面下载预先打好的安装包
	如果对Apollo的代码没有定制需求，建议使用这种方式，可以省去本地打包的过程
2通过源码构建

https://github.com/ctripcorp/apollo.git

	从GitHub Release页面下载Source code包或直接clone源码后在本地构建
	如果需要对Apollo的做定制开发，需要使用这种方式

## 4、maven依赖

```
 <dependency>
     <groupId>com.ctrip.framework.apollo</groupId>
     <artifactId>apollo-client</artifactId>
     <version>1.4.0</version>
 </dependency>
 
 主类添加注解 @EnableApolloConfig
```



# 二 、Apollo部署步骤



## 1 、创建数据库  

### 1.1、创建库

	apolloportaldb.sql     ApolloPortalDB只需要在生产环境部署一个即可
	apolloconfigdb.sql     ApolloConfigDB需要在每个环境部署一套，如fat、uat和pro分别部署3套								 ApolloConfigDB

### 1.2、修改值

```
PortalDB 定义开发环境
UPDATE ApolloPortalDB.ServerConfig SET ServerConfig.`Value` ='dev,test,pro' WHERE `Key`='apollo.portal.envs';
*注 环境没有就不写  否则报错  大小写 不敏感  默认  DEV,FAT,UAT,PRO  自定义 如（test） 需要改源码
```

```
ConfigDB 定义Eureka地址
UPDATE ApolloConfigDB.ServerConfig SET ServerConfig.`Value`='http://localhost:9100/eureka/' WHERE `Key`='eureka.service.url';
```

## 2、获取包

### 2 .1、打包前修改配置（可不改）

编辑scripts/build.sh 或 scripts/build.bat 或修改ApolloPortalDB和ApolloConfigDB相关的数据库连接串信息。				

	# apollo config db info   该数据库依据不同环境配置对应的数据库连接
	apollo_config_db_url=jdbc:mysql://dev:3306/ApolloConfigDB?characterEncoding=utf8
	apollo_config_db_username=XXXX
	apollo_config_db_password=XXXX
	# apollo portal db info  该数据库配置只需要配置一次，不同环境无需修改
	apollo_portal_db_url=jdbc:mysql://192.168.35.226:3306/ApolloPortalDB?characterEncoding=utf8
	apollo_portal_db_username=XXXX
	apollo_portal_db_password=XXXX
	
	注2：每个环境都需要独立部署一套config-service、admin-service和ApolloConfigDB
		实际部署时只需要修改application-github.properties和apollo-env.properties即可。


```
默认情况下，meta service和config service是部署在同一个JVM进程，所以meta service的地址就是config service的地址 看情况注释
# meta server url, different environments should have different meta server addresses
dev_meta=http://192.168.35.207:9100
fat_meta=http://192.168.35.208:9100
uat_meta=http://192.168.35.209:9100
pro_meta=http://192.168.35.210:9100
```

### 2.2、 打包后修改配置

##### apollo-portal-1.5.0.zip

```
1) /config/apollo-env.properties
local.meta=http://localhost:8080
dev.meta=http://192.168.2.12:9100
fat.meta=http://192.168.2.13:9100
uat.meta=
lpt.meta=${lpt_meta}
pro.meta=http://192.168.2.14:9100
没有则不写 多个 ，号隔开

2)/config/application-github.properties 数据库配置
# DataSource
spring.datasource.url = jdbc:mysql://192.168.1.108:3306/ApolloPortalDB?characterEncoding=utf8
spring.datasource.username = root
spring.datasource.password = 1qwe32!QWE#@

修改端口 日志路径 jvm	
3）/script/startup.sh
SERVER_PORT=9100
LOG_DIR=/opt/logs/100003172

```





打包慢

```
    <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror> 
```



##### apollo-configservice-1.5.0.zip

```

```



##### apollo-adminservice-1.5.0.zip

```

```









## 3、部署Apollo服务端

### 3.1、启动顺序

```
config  -> admin  
再启动 portal
```





## 4、配置文件说明

```
# apollo 配置应用的 appid
app.id=springboot-apollo-demo1
# apollo meta-server地址，一般同config-server地址
apollo.meta=http://192.168.0.153:8080
#启用apollo配置开关
apollo.bootstrap.enabled=true
apollo.bootstrap.eagerLoad.enabled=true
# apollo 使用配置的命名空间，多个以逗号分隔
apollo.bootstrap.namespaces = application
```



## 5、Jvm内存 设置



```
export JAVA_OPTS="-Xms512m -Xmx512m -XX:PermSize=128m -XX:MaxPermSize=512m"

```

前十

```
ps aux | sort -k4,4nr | head -n 10
```









#### 1、apollo-configservice

```
export JAVA_OPTS="-server -Xms6144m -Xmx6144m -Xss256k -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=384m -XX:NewSize=4096m -XX:MaxNewSize=4096m -XX:SurvivorRatio=18"

1)/config/application-github.properties

修改端口 日志路径 jvm	
2）/script/startup.sh
同上
```

#### 2、apollo-adminservice

```
export JAVA_OPTS="-server -Xms2560m -Xmx2560m -Xss256k -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=384m -XX:NewSize=1024m -XX:MaxNewSize=1024m -XX:SurvivorRatio=22"

1)/config/application-github.properties

修改端口 日志路径 jvm	
2）/script/startup.sh
同上
```

#### 3、apollo-portal 

```
export JAVA_OPTS="-server -Xms4096m -Xmx4096m -Xss256k -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=384m -XX:NewSize=1536m -XX:MaxNewSize=1536m -XX:SurvivorRatio=22"
```





# 三、Java客户端使用

## 1、必选设置 

Apollo客户端依赖于`AppId`，`Apollo Meta Server` （apollo-configservice）等环境信息来工作

### 1.1、AppID设置

```
是应用的身份信息，是从服务端获取配置的一个重要信息。 格式为string  优先级重高到低
1)	Apollo 0.7.0+支持通过System Property传入app.id信息，如 System Property
-Dapp.id=YOUR-APP-ID

2) 操作系统的System Environment
Apollo 1.4.0+支持通过操作系统的System Environment APP_ID来传入app.id信息，如
APP_ID=YOUR-APP-ID

3) Apollo 1.0.0+支持通过Spring Boot的application.properties文件配置，如
app.id=YOUR-APP-ID
该配置方式不适用于多个war包部署在同一个tomcat的使用场景

4)  resources\META-INF\app.properties  配置 哪个项目的   例如  app.id=test
```

### 1.2、Apollo Meta Server

```
1) 通过Java System Property apollo.meta
可以通过Java的System Property apollo.meta来指定
在Java程序启动脚本中，可以指定-Dapollo.meta=http://config-service-url
如果是运行jar文件，需要注意格式是java -Dapollo.meta=http://config-service-url -jar xxx.jar
也可以通过程序指定，如System.setProperty("apollo.meta", "http://config-service-url");

2) 通过Spring Boot的配置文件
可以在Spring Boot的application.properties或bootstrap.properties中指定apollo.meta=http://config-service-url
该配置方式不适用于多个war包部署在同一个tomcat的使用场景

3)	过操作系统的System EnvironmentAPOLLO_META
可以通过操作系统的System Environment APOLLO_META来指定
注意key为全大写，且中间是_分隔

4)	通过server.properties配置文件
可以在server.properties配置文件中指定apollo.meta=http://config-service-url
对于Mac/Linux，文件位置为/opt/settings/server.properties
对于Windows，文件位置为C:\opt\settings\server.properties

5)	通过app.properties配置文件
可以在classpath:/META-INF/app.properties指定apollo.meta=http://config-service-url

6)	通过Java system property ${env}_meta
如果当前env是dev，那么用户可以配置
-Ddev_meta=http://config-service-url
使用该配置方式，那么就必须要正确配置Environment，详见1.2.4.1 Environment

7)	通过操作系统的System Environment ${ENV}_META (1.2.0版本开始支持)
如果当前env是dev，那么用户可以配置操作系统的System Environment 
DEV_META=http://config-service-url
注意key为全大写
使用该配置方式，那么就必须要正确配置Environment，详见1.2.4.1 Environment

8)	通过apollo-env.properties文件
用户也可以创建一个apollo-env.properties，放在程序的classpath下，或者放在spring boot应用的config目录下
使用该配置方式，那么就必须要正确配置Environment，

```

### .yml配置

```
app:
  id: "00001"
apollo:
  meta: http://192.168.2.12:9100
bootstrap:
  #在应用启动阶段，向Spring容器注入被托管的application.properties文件的配置信息
  enabled: true
  eagerLoad:
    #将Apollo配置加载提到初始化日志系统之前。
    enabled: true
    #使用配置的命名空间，多个以逗号分隔
  namespaces: application
```



## 2、可选设置

Env

```
通过Java System Property

可以通过Java的System Property env来指定环境
在Java程序启动脚本中，可以指定-Denv=YOUR-ENVIRONMENT
如果是运行jar文件，需要注意格式是java -Denv=YOUR-ENVIRONMENT -jar xxx.jar
注意key为全小写
通过操作系统的System Environment

还可以通过操作系统的System Environment ENV来指定
注意key为全大写
通过配置文件

最后一个推荐的方式是通过配置文件来指定env=YOUR-ENVIRONMENT
对于Mac/Linux，文件位置为/opt/settings/server.properties
对于Windows，文件位置为C:\opt\settings\server.properties
文件内容形如：

env=DEV
```





# 四、Apollo 注解

postProcessBeanFactory

在一般情况下，Apollo会在Spring的postProcessBeanFactory阶段将大多数配置注入至Spring容器中。这个阶段早于bean的初始化阶段，已经能够很好地满足普通的bean注入配置场景。

### @EnableApolloConfig

```
@Configuration
@EnableApolloConfig(value = "redis")//不写 默认为 application
public class ConfigLoader {
}
这个注解会在示例工程启动时，将Apollo服务端的redis命名空间中的配置注入Spring容器，也就相当于加载传统的redis.properties配置文件。

*注意：@EnableApolloConfig注解要和@Configuration注解一起使用，否则不会生效。

```



### @ApolloConfig

```
将Apollo服务端的redis命名空间（也就是redis.properties文件）中的配置注入这个类的bean实例。
用来自动注入Config对象
```



### @ApolloConfigChangeListener

```
监听redis命名空间的配置更新事件，若该事件发生，则调用onRedisChange方法，处理该事件。
用来自动注册ConfigChangeListener
```



### @ApolloJsonValue

```
用来把配置的json字符串自动注入为对象
```

#### 示例

```
public class TestApolloAnnotationBean {
  @ApolloConfig
  private Config config; //inject config for namespace application
  @ApolloConfig("application")
  private Config anotherConfig; //inject config for namespace application
  @ApolloConfig("FX.apollo")
  private Config yetAnotherConfig; //inject config for namespace FX.apollo
  @ApolloConfig("application.yml")
  private Config ymlConfig; //inject config for namespace application.yml
 
  /**
   * ApolloJsonValue annotated on fields example, the default value is specified as empty list - []
   * <br />
   * jsonBeanProperty=[{"someString":"hello","someInt":100},{"someString":"world!","someInt":200}]
   */
  @ApolloJsonValue("${jsonBeanProperty:[]}")
  private List<JsonBean> anotherJsonBeans;

  @Value("${batch:100}")
  private int batch;
  
  //config change listener for namespace application
  @ApolloConfigChangeListener
  private void someOnChange(ConfigChangeEvent changeEvent) {
    //update injected value of batch if it is changed in Apollo
    if (changeEvent.isChanged("batch")) {
      batch = config.getIntProperty("batch", 100);
    }
  }
 
  //config change listener for namespace application
  @ApolloConfigChangeListener("application")
  private void anotherOnChange(ConfigChangeEvent changeEvent) {
    //do something
  }
 
  //config change listener for namespaces application, FX.apollo and application.yml
  @ApolloConfigChangeListener({"application", "FX.apollo", "application.yml"})
  private void yetAnotherOnChange(ConfigChangeEvent changeEvent) {
    //do something
  }

  //example of getting config from Apollo directly
  //this will always return the latest value of timeout
  public int getTimeout() {
    return config.getIntProperty("timeout", 200);
  }

  //example of getting config from injected value
  //the program needs to update the injected value when batch is changed in Apollo using @ApolloConfigChangeListener shown above
  public int getBatch() {
    return this.batch;
  }

  private static class JsonBean{
    private String someString;
    private int someInt;
  }
}
--------------------------------------------------------------------------------------------
@Configuration
@EnableApolloConfig
public class AppConfig {
  @Bean
  public TestApolloAnnotationBean testApolloAnnotationBean() {
    return new TestApolloAnnotationBean();
  }
}
```





```
ConfigChangeEvent参数：虽然示例代码没有使用这个参数，但是它很实用，可以获取被修改配置项的key集合，以及被修改配置项的新值、旧值和修改类型等信息。
```





# 五、代码中使用

## 1：通过Placeholder方式获取

```
@Date
public class TestJavaConfigBean {

    @Value("${timeout:100}")
    private int timeout;

    private int batch;
  }
 
  @Configuration
  @EnableApolloConfig
  public class AppConfig {
      @Bean
      public TestJavaConfigBean javaConfigBean() {
        return new TestJavaConfigBean();
      }

}
---------------------------------------------------------------------------------------------
@RestController
public class TestController {

	@Value("${timeout:200}")
	private Integer timeout;
	
	@RequestMapping("/test")
	public String test(){
		return "获取超时时间： "+timeout;
	}
}
```

## **2：通过**@ ConfigurationProperties

````
@ConfigurationProperties(prefix = "redis.cache")
public class SampleRedisConfig {

  private int expireSeconds;

  private int commandTimeout;

  public void setExpireSeconds(int expireSeconds) {
    this.expireSeconds = expireSeconds;
  } 

  public void setCommandTimeout(int commandTimeout) {
    this.commandTimeout = commandTimeout;
  }
}
--------------------------------------------------------------------------------------------- 

@Configuration
@EnableApolloConfig
public class AppConfig {

  @Bean
  public SampleRedisConfig redisConfigBean() {
    return new SampleRedisConfig ();
  }
}
````

## 3： 通过Spring 结合 API 方式

```
@RestController
public class TestController3 {
 
 	// 获取整个namespace的配置
	@ApolloConfig
	private Config config;
	
	@RequestMapping("/test3")
	public String test() {
		return "获取超时时间："+config.getIntProperty("timeout", 200);
	}
```









# 六、已有配置迁移

```
在Apollo为应用新建项目
在应用中配置好META-INF/app.properties
建议把原先配置先转为properties格式，然后通过Apollo提供的文本编辑模式全部粘帖到应用的application namespace，发布配置
如果原来格式是yml，可以使用YamlPropertiesFactoryBean.getObject转成properties格式
如果原来是yml，想继续使用yml来编辑配置，那么可以创建私有的application.yml namespace，把原来的配置全部粘贴进去，发布配置
需要apollo-client是1.3.0及以上版本
把原先的配置文件如bootstrap.properties/yml, application.properties/yml从项目中删除
如果需要保留本地配置文件，需要注意部分配置如server.port必须确保本地文件已经删除该配置项
如：

spring.application.name = reservation-service
server.port = 8080

logging.level = ERROR

eureka.client.serviceUrl.defaultZone = http://127.0.0.1:8761/eureka/
eureka.client.healthcheck.enabled = true
eureka.client.registerWithEureka = true
eureka.client.fetchRegistry = true
eureka.client.eurekaServiceUrlPollIntervalSeconds = 60

eureka.instance.preferIpAddress = true

```

在线互转 ：https://www.toyaml.com/index.html

idea插件 ：Properties to YAML Converter













































































