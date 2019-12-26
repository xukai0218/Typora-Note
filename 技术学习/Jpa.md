# 一、JPA简介

JPA 是规范：JPA 本质上就是一种  ORM 规范，不是ORM 框架
Hibernate 是实现：Hibernate 除了作为 ORM 框架之外，它也是一种 JPA 实现
从功能上来说， JPA 是 Hibernate 功能的一个子集



find()      			类似于 hibernate 中 Session 的 get 方法.
						Customer customer = entityManager.find(Customer.class, 1);(实体类类型,实体的主键值)
						如果这个实体存在于当前的持久化环境，则返回一个被缓存的对象；
						否则会创建一个新的 Entity, 并加载数据库中相关信息；
						若 OID 不存在于数据库中，则返回一个 null。

getReference()			类似于 hibernate 中 Session 的 load 方法
						entityManager.getReference(Customer.class, 1);	
						不同的是：如果缓存中不存在指定的 Entity, EntityManager 会创建一个 Entity 类的代理，
						但是不会立即加载数据库中的信息，只有第一次真正使用此 Entity 的属性才加载，
						如果此 OID 在数据库不存在，getReference() 不会返回 null 值, 而是抛出EntityNotFoundException

persist (Object entity) 类似于 hibernate 的 save 方法. 使对象由临时状态变为持久化状态. 
						Entity 对象转换成持久化状态	
						如果传入 persist() 方法的 Entity 对象已经处于持久化状态，则 persist() 方法什么都不做。
						如果对删除状态的 Entity 进行 persist() 操作，会转换为持久化状态。
						如果对游离状态的实体执行 persist() 操作，可能会在 persist() 方法抛出 EntityExistException(也有可能是在flush或事务提交后抛出)。
						
						和 hibernate 的 save 方法的不同之处: 若对象有 id, 则不能执行 insert 操作, 而会抛出异常. 

remove (Object entity)：类似于 hibernate 中 Session 的 delete 方法. 把对象对应的记录从数据库中移除
						删除实例。如果实例是被管理的，即与数据库实体记录关联，则同时会删除关联的数据库记录。							
						但注意: 该方法只能移除 持久化 对象. 而 hibernate 的 delete 方法实际上还可以移除 游离对象.


临时状态(transient)(瞬态)：		刚用new 语句创建，还没有被持久化，并且不处于Sesssion 的缓存中。处于临时状态的Java 对象被称为临时对象。
持久化状态(persistent)：		已经被持久化，并且加入到Session 的缓存中。处于持久化状态的Java 对象被称为持久化对象。
游离状态(detached)：			已经被持久化，但不再处于Session 的缓存中。处于游离状态的Java 对象被称为游离对象.
删除状态(removed)：				不再处于Session 的缓存中，并且Session 已经计划将其从数据库中删除。处于删除状态的Java 对象被称为删除对象。



session对象：hibernate  一级缓存对象

        临时状态---->持久化状态：save()方法：对象给session管理

        持久化状态---->游离状态：close():session关闭，自动回收

       、clear()  、evict()：从游离状态返回到持久化状态，前提的条件是session没有被关闭，但是

        session里面的对象会被清空，就是说session就是一个空的对象

       持久化状态---->删除状态:session里面的对象被替换成一个删除对象



映射单向 n-1 的关联关系
保存多对一时, 建议先保存 1 的一端, 后保存 n 的一端, 这样不会多出额外的 UPDATE 语句.
	//不能直接删除 1 的一端, 因为有外键约束. 
	//使用 @ManyToOne 来映射多对一的关联关系
	//使用 @JoinColumn 来映射外键. 
	//可使用 @ManyToOne 的 fetch 属性来修改默认的关联属性的加载策略
	@JoinColumn(name="CUSTOMER_ID")
	@ManyToOne(fetch=FetchType.LAZY)
	public Customer getCustomer() {
		return customer;
	}

# Jpa注解

| 注解              | 作用                                       |
| --------------- | ---------------------------------------- |
| @Basic          | 基本注解，默认有                                 |
| @Entity         | 指出该Java类为实体类，将映射到指定的数据库                  |
| @Table          | 标注常用name属性，用于指定数据库的表的名称 当实体类与其映射的数据库表名不同名时需要使用与 @Entity 标注并列使用 |
| @Id             | 主键的映射                                    |
| @GeneratedValue | 用于标注主键的生成策略，通过strategy属性指定               |
| @Column         | 映射数据表的列名，指定unique，length等                |
| @Temporal       | 在属性上调整精度，比如Date                          |
| @Transient      | 忽略该属性，不需要映射到数据表的一列，否则默认为@Basic           |
| @JoinColumn     | 用来映射外键                                   |
| @JoinTable      | 插入表 多用于多对多或者一对多                          |
| @OneToOne       | 映射一对一的关系                                 |
| @OneToMany      | 映射少对多的关系                                 |
| @ManyToOne      | 映射多对少的关系                                 |
| @ManyToMany     | 映射多对对的关系                                 |

 @Modifying注解
　　　　1、在@Query注解中编写JPQL实现DELETE和UPDATE操作的时候必须加上@modifying注解，以通知Spring Data 这是一个DELETE或UPDATE操作。

　　　　2、UPDATE或者DELETE操作需要使用事务，此时需要 定义Service层，在Service层的方法上添加事务操作。

　　　　3、注意JPQL不支持INSERT操作。





实体类上增加：

```
@Entity
@DynamicInsert
@DynamicUpdate
```

## 常用属性详解

- @Table的常用属性：
  - name : 用来命名 当前实体类 对应的数据库 表的名字
  - uniqueConstraints : 用来批量命名唯一键
  - catalog : 实体指定的目录名或是数据库名
  - schema : 实体指定的目录名或是数据库名
- @Entity的常用属性：
  - name : 实体的名称
- @GeneratedValue的常用属性:
  - strategy : ID的生成策略
    - GenerationType.IDENTITY 主键由数据库自动生成（主要是自动增长型）
    - GenerationType.AUTO 主键由程序控制
    - GenerationType.TABLE 使用一个特定的数据库表格来保存主键
    - GenerationType.SEQUENCE 根据底层数据库的序列来生成主键，条件是数据库支持序列
- @Column相关属性：
  - name : 对应的列表的名称
  - unique : 是否是唯一
  - nullable : 是否为空
  - length : 长度
  - insertable : 是否插入表
  - updatable :是否更新表
- @Temporal
  - value : TemporalType.DATE,TemporalType.TIME,TemporalType.TIMESTAMP,以上三个参数分别表示年月日、时分秒、年与日时分秒
- @JoinColumn部分参数:
  - name : 表的名称，一般情况下默认，但是多对多的时候第三方表的时候需要填写
  - referencedColumnName : 产生外键，依据的列的名称
- @OneToOne、@OneToMany、@ManyToMany、@ManyToOne相关参数：
  - targetEntity : 变量对应的类
  - cascade : 关联级别
  - fetch : 加载方式 FetchType.EAGER数据同步加 FetchType.LAZY 懒加载，使用的时候才会加载
  - optional : 当其为false 真实关系必须存在 当其为true 可以不存在
  - mappedBy : 放弃对外键的维护，数值为本类中在外键维护类中的变量名称
  - orphanRemoval : 当执行级联操作删除时，如果此为true 那么就把关系被维护端的数据实体删除，false 不删除，删除的只是关系

http://www.manongjc.com/article/25284.html









## jpa 配置

```
spring.datasource.url=jdbc:mysql://localhost:3306/my_jpa?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=mysql
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver


spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update

```

```
create
删除除上一次的生成的表，然后根据你的model类再重新来生成新表
create-drop ： 
每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。 
update： 
最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据 model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等 应用第一次运行起来后才会。 
validate ： 
每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值

ddl-auto:create----每次运行该程序，没有表格会新建表格，表内有数据会清空

ddl-auto:create-drop----每次程序结束的时候会清空表

ddl-auto:update----每次运行程序，没有表格会新建表格，表内有数据不会清空，只会更新

ddl-auto:validate----运行程序会校验数据与数据库的字段类型是否相同，不同会报错

none
```

# @query的传入参数是对象匹配参数

**:#{#req.totalAmount}**

 @Param(value = "**req**") 可写可不写

```
    @Modifying
    @Transactional(rollbackFor = Exception.class)
    @Query(value = "update OrderSaleEntity orders set " +
            "orders.totalAmount = :#{#req.totalAmount} ," +
            "orders.discount=:#{#req.discount},orders.remark=:#{#req.remark},orders.lastUpdatedAt=current_timestamp  " +
            "where orders.id = ?1")
    void updateOrder(Long orderId, @Param("req") UpdateOrderReq req);
```



## 使用原生的sql 进行数据查询

```
 //
    @Query(value = "select * from  student where  class_num = ?1",nativeQuery = true)
    public List<Student> selectStudentByClassNum(String classNum);
```



## 排序

```
 Sort sort = new Sort(Sort.Direction.fromString(orderType), orderField);
  PageRequest pageable = PageRequest.of(pageIndex, pageSize, new Sort(Sort.Direction.DESC, "createdAt"));
```



## 手动回滚事务

1. ```
   @Transactional(rollbackFor = Exception.class)

   TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();  
   ```

   ​

   ​








​				