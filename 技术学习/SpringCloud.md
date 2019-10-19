# SpringCloud

Spring Cloud Netflix 中文文档 参考手册 中文版
https://springcloud.cc/spring-cloud-netflix.html


Spring Cloud Dalston 中文文档 参考手册 中文版
https://springcloud.cc/spring-cloud-dalston.html

中国社区
http://springcloud.cn/
https://springcloud.cc/

debug: true
spring.jpa.show-sql: true
spring.jpa.properties.hibernate.format_sql: true

# 一、Eureka 服务注册与发现

Eureka 采用了 C-S 的设计架构。Eureka Server 作为服务注册功能的服务器，它是服务注册中心。

## 0、POM.xml

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>

		<!-- Ribbon相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>

<build>
   <finalName>microservicecloud</finalName>
   <resources>
     <resource>
       <directory>src/main/resources</directory>
       <filtering>true</filtering>
     </resource>
   </resources>
   <plugins>
     <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-resources-plugin</artifactId>
       <configuration>
         <delimiters>
          <delimit>$</delimit>
         </delimiters>
       </configuration>
     </plugin>
   </plugins>
  </build>
```

## 0.1、yml

```
server:
  port: 8001
  
mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml        # mybatis配置文件所在路径
  type-aliases-package: com.atguigu.springcloud.entities    # 所有Entity别名类所在包
  mapper-locations:
  - classpath:mybatis/mapper/**/*.xml                       # mapper映射文件
    
spring:
   application:
    name: microservicecloud-dept 						  # 应用名字
   datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver              # mysql驱动包
    url: jdbc:mysql://localhost:3306/cloudDB01              # 数据库名称
    username: root
    password: 123456
    dbcp2:
      min-idle: 5                                           # 数据库连接池的最小维持连接数
      initial-size: 5                                       # 初始化连接数
      max-total: 5                                          # 最大连接数
      max-wait-millis: 200                                  # 等待连接获取的最大超时时间
      
eureka:
  client: #客户端注册进eureka服务列表内
    service-url: 
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
  instance:
    #instance-id: microservicecloud-dept8001   			   #自定义服务名称信息
    instance-id=${spring.cloud.client.ip-address}:${server.port}
    prefer-ip-address: true     						  #访问路径可以显示IP地址
eureka.server.enable-self-preservation = false			    #关闭自我保护模式

info:
  app.name: atguigu-microservicecloud
  company.name: www.xxxx.com
  build.artifactId: $project.artifactId$
  build.version: $project.version$

```



## 1、三大角色

### 1、Eureka Service

提供服务注册和发现

```
server: 
  port: 7001
 eureka: 
  instance:
    hostname: eureka7001.com 		#eureka服务端的实例名称
  client: 
    register-with-eureka: false      #false表示不向注册中心注册自己。
    fetch-registry: false     		 #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url: 
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
      #单机 defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/       #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址（单机）。

```

### 2、Service Provider

服务提供方将自身服务注册到Eureka，从而使服务消费方能够找到

#### Eureka -client

```
EurekaClient是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳(默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒）
```



	1 pom 添加 eureka
	2 yml 
	  eureka.client.register-with-eureka=true
	  eureka.client.fetch-reigstry=true
	  eureka.client.service-url.dafaultZone=
	3 启动类 标注注解 @EnableEurekaClient             服务启动会自动注册进eureka服务中

​		

### 3、Service Consumer

服务消费方从Eureka获取注册服务列表，从而能够消费服务







## 2、eureka自我保护模式

某时刻某一个微服务不可用了，eureka不会立刻清理，依旧会对该微服务的信息进行保存

什么是自我保护模式？（默认90秒）

默认情况下，如果EurekaServer在一定时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例。但是当网络分区故障发生时，微服务与EurekaServer之间无法正常通信，以上行为可能变得非常危险了——因为微服务本身其实是健康的，此时本不应该注销这个微服务。Eureka通过“自我保护模式”来解决这个问题——当EurekaServer节点在短时间内丢失过多客户端时（可能发生了网络分区故障），那么这个节点就会进入自我保护模式。一旦进入该模式，EurekaServer就会保护服务注册表中的信息，不再删除服务注册表中的数据（也就是不会注销任何微服务）。当网络故障恢复后，该Eureka Server节点会自动退出自我保护模式。

在自我保护模式中，Eureka Server会保护服务注册表中的信息，不再注销任何服务实例。当它收到的心跳数重新恢复到阈值以上时，该Eureka Server节点就会自动退出自我保护模式。它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。一句话讲解：好死不如赖活着

综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留），也不盲目注销任何健康的微服务。使用自我保护模式，可以让Eureka集群更加的健壮、稳定。

禁用自我保护模式

```
eureka.server.enable-self-preservation = false
```

## 3、Eureka比Zookeeper好在哪里

服务注册中心

著名的CAP理论指出，一个分布式系统不可能同时满足

C（一致性） A（可用性） P（分区容错性）

由于分区容错性 在分布式系统中必须要保证 所以在A C 中权衡

### Zookeeper保证的是 CP

Zookeeper 注册中心查询列表时，可以容忍信息是几分钟前的，但不能接受直接dowm掉不可用。

可用性高于一致性。会出现这样一种状态，当master节点因为网络故障与其它节点失去联系时，剩余节点会重新选举leader， 问题在于，选举leader的时间太长 30 -120s,  期间zk集群都是不可用的，会导致注册服务瘫痪，

网络问题导致失去master节点较大概率会发生，虽然最终能恢复，但漫长的选举导致注册中心长期不可用是不能 容忍的。

### Eureka则是 AP

Eureka 在设计时就优先保证可用性，每个节点都是平等的，几个节点挂掉不会影响正常节点，连接失败时则会自动切换其它正常节点，只要有一台Eureka还在，就能保证注册服务可用（A可用性），只不过查到的信息可能不是最新的（C 一致性）。 Eureka 还有一种自我保护机制，如果在15分钟内超过85%的节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现网络故障，会出现以下几种情况：

1、Eureka不从注册列表中移除 因为长时间没收到心跳而应该过期的服务

2、Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其他节点上（保重当前节点仍然可用）

3、网络稳定时，当前实例新的注册信息会被同步到其它节点中

```
因此，eureka 可以很好的应对因网络故障导致部分节点失去联系的情况，而不会想Zookeeper那样使整个注册服务瘫痪。
```



# 二、Ribbon负载均衡

## 0、介绍

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套**客户端**       **负载均衡**的工具。

简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供客户端的软件负载均衡算法，将Netflix的中间层服务连接在一起。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer（简称LB）后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们也很容易**使用Ribbon实现自定义的负载均衡算法。**

Ribbon在工作时分两步

1）选择EurekaServer，它优先选择同一个区域内负载较少的server

2）根据用户指定的策略，从server取到的服务注册列表中选择一个地址。
其中Ribbon提供了多种策略：比如轮询、随机和根据响应时间加权。

## 1、添加pom

```
<!-- Ribbon相关 -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
```

## 2、.yml

eureka 服务地址

```
server:
  port: 80
  
eureka:
  client:
    register-with-eureka: false
    service-url: 
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eurek
```

## 3、注解**@LoadBalanced** @EnableEurekaClient

1、主启动类DeptConsumer80_App添加@EnableEurekaClient

2、获得Rest时加入Ribbon配置

```
@Bean
@LoadBalanced //Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端       负载均衡的工具。
public RestTemplate getRestTemplate()
{
	return new RestTemplate();
}
```

## 4、应用名 访问

微服务名字访问

private static final String REST_URL_PREFIX = "http://MICROSERVICECLOUD-DEPT";

Ribbon和Eureka整合后Consumer可以直接调用服务而不用再关心地址和端口号

```
spring:
   application:
    name: microservicecloud-dept 
```

## 5、轮询规则种类

| RoundRobinRule           | 轮询   |
| ------------------------ | ---- |
| RandomRule               | 随机   |
| AvaiabilityFilteringRule |      |
| WeightedResponseTimeRule |      |
| RetryRule                |      |
| BestAvailableRule        |      |
| ZoneAvoidanceRule        |      |

选择规则

```
@Configuration
public class ConfigBean //boot -->spring   applicationContext.xml --- @Configuration配置   ConfigBean = applicationContext.xml
{ 
	@Bean
	public IRule myRule()
	{
		//return new RoundRobinRule();
		//return new RandomRule();//达到的目的，用我们重新选择的随机算法替代默认的轮询。
		return new RetryRule();
	}
}	
```

## 6、自定义规则



# 三、Feign负载均衡





之前用ribbon进行负载均衡 功能强大 定义算法

Feign 怎么出来?

Feign是一个声明式的Web服务客户端，使得编写Web服务客户端变得非常容易，
只需要创建一个接口，然后在上面添加注解即可。

1
2 目前都习惯面向接口编程 比如webService接口 比如Dao接口 规范
	2.1 微服务名字获得调用地址
	2.2 通过接口+注解获得 服务
统一的面向接口编程的套路


Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端       负载均衡的工具。

简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供客户端的软件负载均衡算法，将Netflix的中间层服务连接在一起。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer（简称LB）后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们也很容易使用Ribbon实现自定义的负载均衡算法。


​		
LB，即负载均衡(Load Balance)，在微服务或分布式集群中经常用的一种应用。		
​		
​		
Feign是一个声明式的Web服务客户端，使得编写Web服务客户端变得非常容易，
只需要创建一个接口，然后在上面添加注解即可。		
​		
 Feign能干什么
Feign旨在使编写Java Http客户端变得更容易。
前面在使用Ribbon+RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口上面标注Mapper注解,现在是一个微服务接口上面标注一个Feign注解即可)，即可完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时，自动封装服务调用客户端的开发量。		
​		

Hystrix 断路器 避免单个出问题 导致服务挂了		

降级 整体资源快不够了,忍痛将某些服务先关掉,待度过难关,在开启回来		
​		