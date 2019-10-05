ORM(Object/Relation Mapping): 对象/关系映射
一个框架 一个 Java 领域的持久化框架 一个 ORM 框架
为了在系统中能够找到所需对象，需要为每一个对象分配一个唯一的标识号。在关系数据库中称之为主键，而在对象术语中，则叫做对象标识(Object identifier-OID). 
面向对面概念   面向关系概念
类              表
对象			表的行
属性			表的列

ORM的思想：将关系数据库中表中的记录映射成为对象，以对象的形式展现，程序员可以把对数据库的操作转化为对对象的操作。
ORM 采用元数据来描述对象-关系映射细节, 元数据通常采用 XML 格式, 并且存放在专门的对象-关系映射文件中.


Session 接口是 Hibernate 向应用程序提供的操纵数据库的最主要的接口, 它提供了基本的保存, 更新, 删除和加载 Java 对象的方法.
Session 具有一个缓存, 位于缓存中的对象称为持久化对象, 它和数据库中的相关记录对应. Session 能够在某些时间点, 按照缓存中对象的变化来执行相关的 SQL 语句, 来同步更新数据库, 这一过程被称为刷新缓存(flush)
站在持久化的角度, Hibernate 把对象分为 4 种状态: 持久化状态, 临时状态, 游离状态, 删除状态. Session 的特定方法能使对象从一个状态转换到另一个状态. 

commit() 和 flush() 方法的区别：flush 执行一系列 sql 语句，但不提交事务；commit 方法先调用flush() 方法，然后提交事务. 意味着提交事务意味着对数据库操作永久保存下来。  


save() 和 persist 的区别    调用 persist 之前 ,不能有id ,否则不会执行 并且报错

get()  vs load;
		1) get(),立即加载对象     立即检索
		   load() 不使用该对象,不执行查询操作,返回一个代理对象  延迟检索
		2) load () 会抛出 LazyInitalizationException   在调用对象之前 已经关闭了 session
		3) 数据库没有记录,session 也没有关闭
			get 返回 null
			load 不使用 不报错,  使用该对象 则报错;

操作session 缓存的 3个方法
		flush()
缓存     --->   数据库

		reflush()			强制发送 查询数据库返回最新 更新session
缓存     <---   数据库

		clear() 清理缓存

update() 
		1)诺更新一个持久化对象,不需要显示的调用update(),因为在调用commit(),会执行flush
		2)更新一个游离对象(获得持久化,关闭session),需要显示调用update(),把游离对象变为持久化对象
		注意:
		 1)无论更新的游离对象和数据库表的记录是否一致,都会盲目的发送update语句
			select-before-update=true (默认false) 通常不需要设置
		 2) 诺表中没有记录,调用会抛出异常
		 3) 当update)() 关联一个游离对象时,如果session的缓存中已经存在相同OID的持久化对象,会抛出异常
			session 中 不能有两个 OID 相同的对象

SaveOrUpdate()  同时包含了 save() 与 update() 方法的功能
				判定对象为临时对象的标准
				Java 对象的 OID 为 null
				映射文件中为 <id> 设置了 unsaved-value  属性, 并且 Java 对象的 OID 取值与这个 unsaved-value 属性值匹配


		 1)诺OID不为null,但数据表中没有对应的记录 则会报错

delete() Session 的 delete() 方法处理过程
			计划执行一条 delete 语句
			把对象从 Session 缓存中删除, 该对象进入删除状态.
			Hibernate 的 cfg.xml 配置文件中有一个 hibernate.use_identifier_rollback 属性, 其默认值为 false, 若把它设为 true, 将改变 delete() 方法的运行行为: delete() 方法会把持久化对象或游离对象的 OID 设置为 null, 使它们变为临时对象


evict : 从session中 把指定的缓存移除	

ManyToOne  save()  先保存一的一段    插入一 维护多    1 设置inverse=true 放弃关联
			get()   get多   一的那一部分是代理对象
			delete()  可以删多的 不能删一的     有引用

OneToMany  Set<Order> orders = new HashSet<>();
			1)声明集合类型必须使用  接口类型,因为hibernate在获取集合类型时 返回的是Hibernate   内置集合类型而不是 			javaSE一个标准的集合实现
			2) 需要把集合进行初始化,防止空指针

cascade  级联	


检索方式		
​		
		Hibernate 提供了以下几种检索对象的方式
		导航对象图检索方式:  根据已经加载的对象导航到其他对象
		OID 检索方式:  按照对象的 OID 来检索对象
		HQL 检索方式: 使用面向对象的 HQL 查询语言
		QBC 检索方式: 使用 QBC(Query By Criteria) API 来检索对象. 这种 API 封装了基于字符串形式的查询语句, 提供了更加面向对象的查询接口. 
		本地 SQL 检索方式: 使用本地数据库的 SQL 查询语句

HQL(Hibernate Query Language) 是面向对象的查询语言, 它和 SQL 查询语言有些相似. 在 Hibernate 提供的各种检索方式中, 	HQL 是使用最广的一种检索方式. 它有如下功能:
		在查询语句中设定各种查询条件
		支持投影查询, 即仅检索出对象的部分属性
		支持分页查询
		支持连接查询
		支持分组查询, 允许使用 HAVING 和 GROUP BY 关键字
		提供内置聚集函数, 如 sum(), min() 和 max()
		支持子查询
		支持动态绑定参数
		能够调用 用户定义的 SQL 函数或标准的 SQL 函数

HQL vs SQL:
HQL 查询语句是面向对象的, Hibernate 负责解析 HQL 查询语句, 然后根据对象-关系映射文件中的映射信息, 把 HQL查询语句翻译成相应的 SQL 语句. HQL   		  查询语句中的主体是域模型中的类及类的属性
SQL 查询语句是与关系数据库绑定在一起的. SQL 查询语句中的主体是数据库表及表的字段. 
​		
1 创建Query对象 基于位置的参数
	基于位置的参数
	String hql = "FROM Employee e where e.salary > ?  and e.email like ? and e.dept =?"
	基于命名参数
	String hql = "FROM Employee e where e.salary > :sal  and e.email like :email"			
		
	Query query =session.createQuery(HQl);

2 绑定参数 Query对象 调用 setXXX 方法支持方法链的编程风格		
​	
	query.setFloat(0,6000)
			.setString(1,"%A%")
			.setEntity(2,dept)
			
			query.setFloat("sal",6000)
			.setString("email","%A%")

3 查询数据库
				 List<Employee> emps = query.list();

​		
​		
​		
​		
​		
session 缓存
          在 Session 接口的实现中包含一系列的 Java 集合, 这些 Java 集合构成了 Session 缓存. 只要 Session 实例没有结束生命周期, 且没有清理缓存，则存放在它缓存中的对象也不会结束生命周期
    		Session 缓存可减少 Hibernate 应用程序访问数据库的频率。

flush : 使数据库表中的记录和Session缓存中的对象	状态保持一致,为了保持一致,则可能会发送相应的sql语句(状态一样 不发送)
		1) transaction.commit()    先调用 session.flush方法,在提交事务
		2) 显示掉 flush()方法 可能会发送sql, 但不会提交事务
		3) 注意: 在未提交事务或没有显示调用session.flush() 之前,也可能会进行 flush()操作  
			1)执行HQL 或 QBC 查询, 会进行flush()操作,来得到数据表的最新记录
			2)诺记录的ID 石油底层数据库使用自增的方式生成的,则save()方法后,就会立即生成inset sql语句
				来保证id存在


			持久化对象的状态
临时对象（Transient）: 
	在使用代理主键的情况下, OID 通常为 null
	不处于 Session 的缓存中
	在数据库中没有对应的记录
持久化对象(也叫”托管”)（Persist）：
	OID 不为 null
	位于 Session 缓存中
	若在数据库中已经有和其对应的记录, 持久化对象和数据库中的相关记录对应
	Session 在 flush 缓存时, 会根据持久化对象的属性变化, 来同步更新数据库
	在同一个 Session 实例的缓存中, 数据库表中的每条记录只对应唯一的持久化对象
删除对象(Removed)
	在数据库中没有和其 OID 对应的记录
	不再处于 Session 缓存中
	一般情况下, 应用程序不该再使用被删除的对象
游离对象(也叫”脱管”) （Detached）：
	OID 不为 null
	不再处于 Session 缓存中
	一般情况需下, 游离对象是由持久化对象转变过来的, 因此在数据库中可能还存在与它对应的记录
			
		new 语句  临时对象  
					|
				持久化状态 --------删除对象
					||				|	
				游离状态  ------------

​			