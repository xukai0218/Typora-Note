```

```

![image-20210919204602356](../image/spring常用注解/image-20210919204602356.png)





```
在@Transactional注解中如果不配置rollbackFor属性,那么事物只会在遇到RuntimeException的时候才会回滚,加上rollbackFor=Exception.class,可以让事物在遇到非运行时异常时也回滚如IOException、SQLException等以及用户自定义的Exception异常
```

![image-20210922203039500](../image/spring事务/image-20210922203039500.png)



![image-20210922203324807](../image/spring事务/image-20210922203324807.png)



![image-20210922205155488](../image/spring事务/image-20210922205155488.png)

动态代理

![image-20210922205348303](../image/spring事务/image-20210922205348303.png)





1.自己注入自己

2.

![image-20210922210909259](../image/spring事务/image-20210922210909259.png)

![image-20210922210638190](../image/spring事务/image-20210922210638190.png)





