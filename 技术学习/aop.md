https://www.jianshu.com/p/a5942b5062ae

# 依赖

```
<!-- aop 依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

# 自定义日志注解

```
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD}) 
@Documented 
public @interface WebLog {
    /**
     * 日志描述信息
     *
     * @return
     */
    String description() default "";

}
```

```
https://blog.csdn.net/yb546822612/article/details/88116654?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase
```



```

@Target(ElementType.TYPE)   //接口、类、枚举、注解
@Target(ElementType.FIELD) //字段、枚举的常量
@Target(ElementType.METHOD) //方法
@Target(ElementType.PARAMETER) //方法参数
@Target(ElementType.CONSTRUCTOR)  //构造函数
@Target(ElementType.LOCAL_VARIABLE)//局部变量
@Target(ElementType.ANNOTATION_TYPE)//注解
@Target(ElementType.PACKAGE) ///包   

@Retention(RetentionPolicy.SOURCE) —— 这种类型的Annotations只在源代码级别保留,编译时就会被忽略
@Retention(RetentionPolicy.CLASS) —— 这种类型的Annotations编译时被保留,在class文件中存在,但JVM将会忽略
@Retention(RetentionPolicy.RUNTIME) —— 这种类型的Annotations将被JVM保留,所以他们能在运行时被JVM或其他使用反射机制的代码所读取和使用.

```



几个注解的解释

```
几个注解的解释
@Around 环绕切点,在进入切点前,跟切点后执行

@After 在切点后,return前执行,

@Before 在切点前执行方法,内容为指定的切点
@Aspect 将一个类定义为一个切面类
```



# 连接点对象 JoinPoint

```
AspectJ使用org.aspectj.lang.JoinPoint接口表示目标类连接点对象，如果是环绕增强时，使用org.aspectj.lang.ProceedingJoinPoint表示连接点对象，该类是JoinPoint的子接口。任何一个增强方法都可以通过将第一个入参声明为JoinPoint访问到连接点上下文的信息。我们先来了解一下这两个接口的主要方法：

1.JoinPoint  
* java.lang.Object[] getArgs()：获取连接点方法运行时的入参列表； 
* Signature getSignature() ：获取连接点的方法签名对象； 
* java.lang.Object getTarget() ：获取连接点所在的目标对象； 
* java.lang.Object getThis() ：获取代理对象本身； 

2.ProceedingJoinPoint  
* ProceedingJoinPoint继承JoinPoint子接口，它新增了两个用于执行连接点方法的方法： 
* java.lang.Object proceed() throws java.lang.Throwable：通过反射执行目标对象的连接点处的方法； 
* java.lang.Object proceed(java.lang.Object[] args) throws java.lang.Throwable：通过反射执行目标对象连接点处的方法，不过使用新的入参替换原来的入参。 
```



# execution切入点表达式

https://blog.csdn.net/u012887385/article/details/54600706

```

```

1. *//方法的返回值注入给ret*
2. ​    @AfterReturning(returning = "ret", pointcut = "weblog()")