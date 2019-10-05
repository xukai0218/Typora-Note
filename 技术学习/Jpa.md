
JPA 是规范：JPA 本质上就是一种  ORM 规范，不是ORM 框架
Hibernate 是实现：Hibernate 除了作为 ORM 框架之外，它也是一种 JPA 实现
从功能上来说， JPA 是 Hibernate 功能的一个子集

@Entity 标注用于实体类声明语句之前，指出该Java 类为实体类，将映射到指定的数据库表。
		如声明一个实体类 Customer，它将映射到数据库中的 customer 表上
@Table  当实体类与其映射的数据库表名不同名时需要使用与 @Entity 标注并列使用

@Id 	标注用于声明一个实体类的属性映射为数据库的主键列  置于属性的getter方法之前@GeneratedValue  
@GeneratedValue  用于标注主键的生成策略，通过 strategy 属性指定。
				 默认情况下，JPA 自动选择一个最适合底层数据库的主键生成策略：SqlServer 对应 identity，MySQL 对应 auto increment。
@Basic  没有任何标注的 getXxxx() 方法,默认即为@Basic


@Column 当实体的属性与其映射的数据库表的列不同名时需要使用 name unique 、nullable、length
​				 
@Transient  表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性.
			如果一个属性并非数据库表的字段映射,就务必将其标示为@Transient,否则,ORM框架默认其注解为@Basic

@Temporal  进行属性映射时可使用@Temporal注解来调整精度. @Temporal(TemporalType.Date) @Temporal (TemporalType.TIMESTAMP)

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


























​				