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

Eureka 
		1 pom 添加 eureka-server
		2 yml 端口 不注册自己
			server.port=8080
			spring.application.name=
			eureka.client.register-with-eureka=false  表示不向注册中心注册自己
			eureka.client.fetch-reigstry=false			表示自己就是注册中心 职责就是维护 不需要检索
			eureka.client.service-url.dafaultZone=
			eureka.instance.hostname=localhost    eureka 服务端实例名称
			eureka.instance.instance-id:                  //主机名称修改 自定义服务名称信息
			eureka.instance.prefer-ip-address=true        访问路径可以显示IP
		3 启动类 标注注解 @EnableEurekaServer 						
						  @EnableDiscoveryClient

Eureka -client
		1 pom 添加 eureka
		2 yml 
			eureka.client.register-with-eureka=true
			eureka.client.fetch-reigstry=true
			eureka.client.service-url.dafaultZone=
		3 启动类 标注注解 @EnableEurekaClient             服务启动会自动注册进eureka服务中


​		
自我保护机制 (应对网络异常的安全保护措施)微服务不可用了  eureka不会立即清理  依旧会对该微服务的信息进行保存	恢复后 推出自我保护机制	
				宁可保留错误注册信息,也不盲目注销任何可能的健康服务实例
			eureka.server.enable-self-preservation=false  禁止自我保护



eureka 集群配置  域名映射 host   yml  1改端口 2 改名字 eureka.instance.hostname =    eureka 服务端实例名称  3 改defaultZone

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