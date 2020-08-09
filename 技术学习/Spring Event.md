

https://www.jianshu.com/p/0f77a1252b54



# 依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

只要依赖spring-core



# 使用

## 1 定义日志发布类



```
public class LogEvent extends ApplicationEvent {

    private String log;

    public LogEvent(Object source, String log) {
        super(source);
        this.log = log;
    }

    public String getLog() {
        return log;
    }
}
```

### 2 定义日志监听类 

```
@Slf4j
@Component
public class LogListener implements ApplicationListener<LogEvent> {

    public static Queue<String> queue = new LinkedList<>();

    @Override
    public void onApplicationEvent(LogEvent logEvent) {
        log.info("LogListener: {} ", logEvent.getLog());
        // you can save db
        queue.add(logEvent.getLog());
    }
}
```

## 3 发布

```
@SpringBootApplication
@RestController
public class SpringEventApplication {

    @Autowired
    private ApplicationContext applicationContext;

    public static void main(String[] args) {
        SpringApplication.run(SpringEventApplication.class, args);
    }

    @GetMapping("/")
    public String publishLogTest() {
        applicationContext.publishEvent(new LogEvent(this, "publish Log Test It's work!"));
        return LogListener.queue.poll();
    }
}
```



1.什么是事件机制

Java的设计模式中有两种设计模式，观察者模式和监听者模式

**监听者模式：**当有一个事件发生时，通知关注此事件的对象，告诉事件发生了就发布这个事件，那怎么知道通知谁呢，所以需要知道谁关心这个事件，那么就需要对这个事件关心的对象中定义一个事件，如果事件发生了，关心的这个对象就监听到了，可以执行相应的操作。

**观察者模式：**一对多的模式，一个被观察者Observable和多个观察者Observer，被观察者中存储了所有的观察者对象，当被观察者接收到一个外界的消息，就会遍历广播推算消息给所有的观察者
例如日常生活中的订阅报纸，报纸老板A，现在小明和老板打招呼说我要订报纸（这个过程就相当于观察者的注册），老板A就会拿出自己的小本本记下小明，下次小王、小芳也是类似的操作，现在老板A就有了三个观察者了，然后老板会自动的把报纸送到三位的家里，突然有一天小明说不想订报纸了，老板就在自己的小本本上划掉小明的名字（观察者的取消注册），等到送报纸的时候就不再送到小明家里。



2.怎么去使用事件机制

 

常规的使用事件需要有三个步骤：定义事件源，监听事件，发布事件







# **事务监听器（@TransactionalEventListener）**

- 如果A方法需要B方法执行完成之后的数据，但是A和B又不在同一个事务中，B中的事务没有提交，A就获取不到数据，此时我们需要等待B提交事务之后执行的A方法才能成功获取数据。

- @TransactionalEventListener默认监听的事件默认是在事务提交之后，即是在一个事务中发布了一个事件，只有等事务成功提交之后才能监听到。

- @TransactionalEventListener的重要属性如下：

- - `phase`：指定事务的处理阶段，在`TransactionPhase`中定义了事务的四个阶段，默认的是事务成功提交之后

  - - `BEFORE_COMMIT`：事务提交之前，事务执行有异常失效
    - `AFTER_COMMIT`：事务成功提交之后，事务执行有异常失效
    - `AFTER_ROLLBACK`：事务回滚之后
    - `AFTER_COMPLETION`：事务完成之后，无论是否有异常都会执行



- - `fallbackExecution`：没有事务的时候是否应该执行，默认为false，即是必须是有事务的时候才会执行