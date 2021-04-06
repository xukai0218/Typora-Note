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

   

   # and or 

```
 public ServerResponse queryOrderByShopId(OrderQuery query) {
        Specification<OrderSaleEntity> specification = (Specification<OrderSaleEntity>) (root, criteriaQuery, criteriaBuilder) -> {
            List<Predicate> orPredicates = new ArrayList<>();
            List<Predicate> predicates = new ArrayList<>();
            Predicate orPredicate;
            if (query.getShopId() != null) {
                predicates.add(criteriaBuilder.equal(root.get("shopId"), query.getShopId()));
            }
            if (query.getStatus() != null) {
                predicates.add(criteriaBuilder.equal(root.get("orderStatus"), query.getStatus()));
            }
            // 首页 发货 支付 九宫格订单 查询状态不等于2(完成)
            if (query.getDeliveryStatus() != null) {
                if (query.getDeliveryStatus().equals(OrderStatusEnum.SHIPPED.getCode())) {
                    predicates.add(criteriaBuilder.equal(root.get("deliveryStatus"), OrderStatusEnum.SHIPPED.getCode()));
                } else {
                    predicates.add(criteriaBuilder.notEqual(root.get("deliveryStatus"), OrderStatusEnum.SHIPPED.getCode()));
                }
                predicates.add(criteriaBuilder.equal(root.get("deleted"), YesOrNoEnum.NO.getCode()));
            }
            // 备货状态 (orderStatus=1 deliveryStatus not in(2))
            if (query.getStockStatus() != null) {
                predicates.add(criteriaBuilder.equal(root.get("orderStatus"), OrderStatusEnum.TRADING_IN.getCode()));
                predicates.add(criteriaBuilder.notEqual(root.get("deliveryStatus"), OrderStatusEnum.SHIPPED.getCode()));
            }

            if (query.getPaidStatus() != null) {
                predicates.add(criteriaBuilder.notEqual(root.get("paidStatus"), query.getPaidStatus()));
                predicates.add(criteriaBuilder.equal(root.get("deleted"), YesOrNoEnum.NO.getCode()));
            }
            if (query.getStartDate() != null && query.getEndDate() != null) {
                predicates.add(criteriaBuilder.between(root.get("createdAt"), query.getStartDate(), query.getEndDate()));
            }
            if (query.getCustomerId() != null) {
                predicates.add(criteriaBuilder.equal(root.get("customerId"), query.getCustomerId()));
            }

            // 售后订单 不显示 取消作废订单
            if (query.getAfterSaleStatus() != null) {
                predicates.add(criteriaBuilder.notEqual(root.get("afterSaleStatus"), query.getAfterSaleStatus()));
                predicates.add(criteriaBuilder.between(root.get("orderStatus"), 0, 2));
            }
            // 是否欠账订单
            if (query.getDebtStatus() != null) {
                predicates.add(criteriaBuilder.equal(root.get("debtStatus"), query.getDebtStatus()));
            }
            Predicate andPredicate = criteriaBuilder.and(predicates.toArray(new Predicate[predicates.size()]));
            //模糊查询
            //判断关键字是否为联系人
            if (StringUtils.isNotBlank(query.getKeyword())) {
                //判断关键字是否为数字
                orPredicates.add(criteriaBuilder.like(root.get("consigneePhone").as(String.class), "%" + query.getKeyword() + "%"));
                orPredicates.add(criteriaBuilder.like(root.get("orderNumber").as(String.class), "%" + query.getKeyword() + "%"));
                orPredicates.add(criteriaBuilder.like(root.get("consignee").as(String.class), "%" + query.getKeyword() + "%"));
                orPredicate = criteriaBuilder.or(orPredicates.toArray(new Predicate[orPredicates.size()]));
                // 合并
                return criteriaQuery.where(andPredicate, orPredicate).getRestriction();
            }
            return andPredicate;
        };
        Pageable pageable = PageRequest.of(query.getPage(), query.getSize(), new Sort(Sort.Direction.DESC, "lastUpdatedAt"));

        Page<OrderSaleEntity> page = orderSaleRepository.findAll(specification, pageable);

        Function<OrderSaleEntity, OrderSaleVO> function = (entity) -> {
            OrderSaleVO vo = new OrderSaleVO();
            BeanUtils.copyProperties(entity, vo);
            setParm(entity, vo);
            return vo;
        };
```

# 自动生成实体类01

Generate_POJOs.groovy  schemas->选表 ->右键 scripted

set global time_zone='+8:00';

Generate_POJOs.groovy

```
import com.intellij.database.model.DasTable
import com.intellij.database.model.ObjectKind
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil

import java.text.SimpleDateFormat
import java.time.LocalDateTime
import java.math.BigDecimal

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */
packageName = ""
typeMapping = [
        (~/bigint/)               : "Long",
        (~/(?i)int/)              : "Integer",
        (~/(?i)float|double|real/): "Double",
        (~/decimal/)              : "BigDecimal",
        (~/(?i)bool|boolean/)     : "Boolean",
        (~/datetime|timestamp/)   : "LocalDateTime",
        (~/date/)                 : "LocalDate",
        (~/(?i)time/)             : "LocalDateTime",
        (~/(?i)char|text/)        : "String",
        (~/(?i)/)                 : "String"
]

FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
    SELECTION.filter { it instanceof DasTable && it.getKind() == ObjectKind.TABLE }.each { generate(it, dir) }
}

def generate(table, dir) {
    def className = javaName(table.getName(), true)
    def fields = calcFields(table)
    packageName = getPackageName(dir)
    new File(dir, className + "Entity.java").withPrintWriter("UTF-8") { out -> generate(out, table, className, fields) }
}

def generate(out, table, className, fields) {
    SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    def nowTime = format.format(new Date());
    def tableName = table.getName()
    out.println "package $packageName"
    out.println ""
    out.println "import lombok.Data;"
    out.println ""
    out.println "import javax.persistence.*;"
    out.println "import java.io.Serializable;"
    out.println "import java.time.LocalDateTime;"
    out.println "import java.math.BigDecimal;"
    out.println ""
    out.println "/**"
    out.println " * @author xukai"
    out.println " * @date $nowTime"
    out.println " */"
    out.println "@Data"
    out.println "@Entity"
    out.println "@Table(name = \"$tableName\")"
    out.println "public class $className" + "Entity implements Serializable {"
    out.println ""
    int i = 0
    fields.each() {
        if (it.annos != "") out.println "  ${it.annos}"
        // 输出注释
        if (isNotEmpty(it.comment)) {
            out.println "\t/**"
            out.println "\t * ${it.comment}"
            out.println "\t */"
        }
        if (i == 0) {
            // 判断自增
            if ((tableName + "_id").equalsIgnoreCase(fields[0].colum) || "id".equalsIgnoreCase(fields[0].colum)) {
                out.println "\t@Id"
                out.println "\t@GeneratedValue(strategy = GenerationType.IDENTITY)"
            }
        }
        i++
        out.println "    @Column(name = \"${it.colum}\", columnDefinition = \"${it.sqlType}\")"
        String colName = it.name
        if (colName.startsWith("is")) {
            it.name = colName.substring(2).toLowerCase()
        }
        out.println "\tprivate ${it.type} ${it.name};"
        out.println ""
    }
    out.println "}"
}

def calcFields(table) {
    DasUtil.getColumns(table).reduce([]) { fields, col ->
        def spec = Case.LOWER.apply(col.getDataType().getSpecification())
        def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
        String sqlTypeStr = spec;
        if (sqlTypeStr != null && !sqlTypeStr.isEmpty() && sqlTypeStr.contains("(")) {
            sqlTypeStr = sqlTypeStr.substring(0, sqlTypeStr.indexOf("("));
        }
        fields += [[
                           name   : javaName(col.getName(), false),
                           colum  : col.getName(),
                           type   : typeStr,
                           sqlType: sqlTypeStr,
                           comment: col.getComment(),
                           annos  : ""]]
    }
}

def javaName(str, capitalize) {
    def s = str.split(/(?<=[^\p{IsLetter}])/).collect { Case.LOWER.apply(it).capitalize() }
            .join("").replaceAll(/[^\p{javaJavaIdentifierPart}]/, "_").replaceAll(/_/, "")
    capitalize || s.length() == 1 ? s : Case.LOWER.apply(s[0]) + s[1..-1]
}
/**
 * 获取包名称
 * @param dir 实体类所在目录
 * @return
 */
def getPackageName(dir) {
    String absolutePath = dir.toString()
    def replace = absolutePath.substring(absolutePath.indexOf("java\\") + 5).replace("\\", ".");
    return replace + ";"
}

def isNotEmpty(content) {
    return content != null && content.toString().trim().length() > 0
}

```

# 02

```
import com.intellij.database.model.DasTable
import com.intellij.database.model.ObjectKind
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil

import java.text.SimpleDateFormat
import java.time.LocalDateTime
import java.math.BigDecimal

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */
packageName = ""
typeMapping = [
        (~/bigint/)               : "Long",
        (~/(?i)int/)              : "Integer",
        (~/(?i)float|double|real/): "Double",
        (~/decimal/)              : "BigDecimal",
        (~/(?i)bool|boolean/)     : "Boolean",
        (~/datetime|timestamp/)   : "LocalDateTime",
        (~/date/)                 : "LocalDate",
        (~/(?i)time/)             : "LocalDateTime",
        (~/(?i)char|text/)        : "String",
        (~/(?i)/)                 : "String"
]

FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
    SELECTION.filter { it instanceof DasTable && it.getKind() == ObjectKind.TABLE }.each { generate(it, dir) }
}

def generate(table, dir) {
    def className = javaName(table.getName(), true)
    def fields = calcFields(table)
    packageName = getPackageName(dir)
    new File(dir, className + "Entity.java").withPrintWriter("UTF-8") { out -> generate(out, table, className, fields) }
}

def generate(out, table, className, fields) {
    SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    def nowTime = format.format(new Date());
    def tableName = table.getName()
    out.println "package $packageName"
    out.println ""
    out.println "import lombok.AccessLevel;"
    out.println "import lombok.Data;"
    out.println "import lombok.Setter;"
    out.println "import org.springframework.data.annotation.CreatedBy;"
    out.println "import org.springframework.data.annotation.CreatedDate;"
    out.println "import org.springframework.data.annotation.LastModifiedBy;"
    out.println "import org.springframework.data.annotation.LastModifiedDate;"
    out.println "import org.springframework.data.jpa.domain.support.AuditingEntityListener;"
    out.println ""
    out.println ""
    out.println "import javax.persistence.*;"
    out.println "import java.io.Serializable;"
    out.println "import java.time.LocalDateTime;"
    out.println "import java.math.BigDecimal;"
    out.println ""
    out.println "/**"
    out.println " * @author xukai"
    out.println " * @date $nowTime"
    out.println " */"
    out.println "@Data"
    out.println "@Entity"
    out.println "@Table(name = \"$tableName\")"
    out.println "@EntityListeners(AuditingEntityListener.class)"
    out.println "public class $className" + "Entity implements Serializable {"
    out.println ""
    int i = 0
    fields.each() {
        if (it.annos != "") out.println "  ${it.annos}"
        // 输出注释
        if (isNotEmpty(it.comment)) {
            out.println "\t/**"
            out.println "\t * ${it.comment}"
            out.println "\t */"
        }
        if (i == 0) {
            // 判断自增
            if ((tableName + "_id").equalsIgnoreCase(fields[0].colum) || "id".equalsIgnoreCase(fields[0].colum)) {
                out.println "\t@Id"
                out.println "\t@GeneratedValue(strategy = GenerationType.IDENTITY)"
            }
        }
        i++
        out.println "    @Column(name = \"${it.colum}\", columnDefinition = \"${it.sqlType}\")"
        String colName = it.name
        if (colName.startsWith("is")) {
            it.name = colName.substring(2).toLowerCase()
        }
        if (colName.equals("createdBy")) {
            out.println "\t@CreatedBy"
            out.println "\t@Setter(AccessLevel.PRIVATE)"
        }

        if (colName.equals("createdAt")) {
            out.println "\t@CreatedDate"
            out.println "\t@Setter(AccessLevel.PRIVATE)"
        }

        if (colName.equals("lastUpdatedAt")) {
            out.println "\t@LastModifiedDate"
            out.println "\t@Setter(AccessLevel.PRIVATE)"
        }

        if (colName.equals("lastUpdatedBy")) {
            out.println "\t@LastModifiedBy"
            out.println "\t@Setter(AccessLevel.PRIVATE)"
        }

        out.println "\tprivate ${it.type} ${it.name};"
        out.println ""
    }
    out.println "}"
}

def calcFields(table) {
    DasUtil.getColumns(table).reduce([]) { fields, col ->
        def spec = Case.LOWER.apply(col.getDataType().getSpecification())
        def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
        String sqlTypeStr = spec;
        if (sqlTypeStr != null && !sqlTypeStr.isEmpty() && sqlTypeStr.contains("(")) {
            sqlTypeStr = sqlTypeStr.substring(0, sqlTypeStr.indexOf("("));
        }
        fields += [[
                           name   : javaName(col.getName(), false),
                           colum  : col.getName(),
                           type   : typeStr,
                           sqlType: sqlTypeStr,
                           comment: col.getComment(),
                           annos  : ""]]
    }
}

def javaName(str, capitalize) {
    def s = str.split(/(?<=[^\p{IsLetter}])/).collect { Case.LOWER.apply(it).capitalize() }
            .join("").replaceAll(/[^\p{javaJavaIdentifierPart}]/, "_").replaceAll(/_/, "")
    capitalize || s.length() == 1 ? s : Case.LOWER.apply(s[0]) + s[1..-1]
}
/**
 * 获取包名称
 * @param dir 实体类所在目录
 * @return
 */
def getPackageName(dir) {
    String absolutePath = dir.toString()
    def replace = absolutePath.substring(absolutePath.indexOf("java\\") + 5).replace("\\", ".");
    return replace + ";"
}

def isNotEmpty(content) {
    return content != null && content.toString().trim().length() > 0
}

```



# 多数据源配置



## 回滚失效

````
@Transactional( value = "platformTransactionManagerRboxPayment",rollbackFor = Exception.class)
````



## pom 文件

```
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <!--        <dependency>-->
        <!--            <groupId>com.alibaba</groupId>-->
        <!--            <artifactId>druid-spring-boot-starter</artifactId>-->
        <!--            <version>1.1.10</version>-->
        <!--        </dependency>-->
        <!--jpa-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!--queryDsl-->
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-apt</artifactId>
        </dependency>
        
        
        
        
         <!--该插件可以生成querysdl需要的查询对象，执行mvn compile即可-->
            <plugin>
                <groupId>com.mysema.maven</groupId>
                <artifactId>apt-maven-plugin</artifactId>
                <version>1.1.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>process</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>target/generated-sources/java</outputDirectory>
                            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                            <options>
                                <querydsl.entityAccessors>true</querydsl.entityAccessors>
                                <querydsl.createDefaultVariable>true</querydsl.createDefaultVariable>
                                <querydsl.packageSuffix>.qdsl</querydsl.packageSuffix>
                            </options>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
```



## yaml 文件

```
spring:
  datasource:
    one:
      driver-class-name: com.mysql.cj.jdbc.Driver
      jdbc-url: jdbc:mysql://192.168.1.68:3306/skoyi_store?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
      username: ruigu
      password: Y3HIcmKeryJ6yjhPKSnV
      type: com.alibaba.druid.pool.DruidDataSource
    two:
      driver-class-name: com.mysql.cj.jdbc.Driver
      jdbc-url: jdbc:mysql://192.168.1.68:3306/obm_base?tinyInt1isBit=true&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&serverTimezone=Asia/Shanghai
      username: ruigu
      password: Y3HIcmKeryJ6yjhPKSnV
      type: com.alibaba.druid.pool.DruidDataSource

  jpa:
    properties:
      hibernate:
        hbm2ddl:
          auto: validate
        dialect: org.hibernate.dialect.MySQL5InnoDBDialect
    show-sql: true
```


​		



## config

### DataSourceConfig

```
package com.datasource.demo.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;

/**
 * @author xukai
 * @create 2020-07-10 11:07
 */
@Configuration
public class DataSourceConfig {

//    DruidData 数据源
//    @Bean("dsOne")
//    @ConfigurationProperties(prefix = "spring.datasource.one")
//    @Primary
//    DataSource dsOne() {
//        return DruidDataSourceBuilder.create().build();
//    }
//
//    @Bean("dsTwo")
//    @ConfigurationProperties(prefix = "spring.datasource.two")
//    DataSource dsTwo() {
//        return DruidDataSourceBuilder.create().build();
//    }


    @Primary
    @Bean(name = "dsOne")
    @ConfigurationProperties(prefix = "spring.datasource.one")
    public DataSource dsOne() {
        return DataSourceBuilder.create().build();
    }

    @Bean("dsTwo")
    @ConfigurationProperties(prefix = "spring.datasource.two")
    public DataSource rboxPaymentDataSource() {
        return DataSourceBuilder.create().build();
    }
}

```

### JpaOneConfig

```
package com.datasource.demo.config;

/**
 * @author xukai
 * @create 2020-07-10 11:11
 */

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.autoconfigure.orm.jpa.JpaProperties;
import org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.orm.jpa.JpaTransactionManager;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.persistence.EntityManager;
import javax.sql.DataSource;

@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(basePackages = "com.datasource.demo.repository.one",
        entityManagerFactoryRef = "localContainerEntityManagerFactoryBeanOne",
        transactionManagerRef = "platformTransactionManagerOne")
public class JpaOneConfig {

    @Autowired
    @Qualifier(value = "dsOne")
    DataSource dsOne;

    @Autowired
    JpaProperties jr;

    @Bean
    @Primary
    LocalContainerEntityManagerFactoryBean localContainerEntityManagerFactoryBeanOne(EntityManagerFactoryBuilder builder) {
        return builder.dataSource(dsOne)
                .properties(jr.getProperties())
                //设置实体类所在位置
                .packages("com.datasource.demo.entity.one")
                .persistenceUnit("pu1")
                .build();

    }

    @Bean(name = "one")
    @Primary
    public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
        return localContainerEntityManagerFactoryBeanOne(builder).getObject().createEntityManager();
    }

    @Bean
    @Primary
    PlatformTransactionManager platformTransactionManagerOne(EntityManagerFactoryBuilder builder) {
        LocalContainerEntityManagerFactoryBean localContainerEntityManagerFactoryBean = localContainerEntityManagerFactoryBeanOne(builder);
        return new JpaTransactionManager(localContainerEntityManagerFactoryBean.getObject());
    }
}

```



### JpaTwoConfig

````
package com.datasource.demo.config;

/**
 * @author xukai
 * @create 2020-07-10 11:11
 */

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.autoconfigure.orm.jpa.JpaProperties;
import org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.orm.jpa.JpaTransactionManager;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.persistence.EntityManager;
import javax.sql.DataSource;

@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(basePackages = "com.datasource.demo.repository.two",
        entityManagerFactoryRef = "localContainerEntityManagerFactoryBeanTwo",
        transactionManagerRef = "platformTransactionManagerTwo")
public class JpaTwoConfig {

    @Autowired
    @Qualifier(value = "dsTwo")
    DataSource dsTwo;

    @Autowired
    JpaProperties jr;

    @Bean
    LocalContainerEntityManagerFactoryBean localContainerEntityManagerFactoryBeanTwo(EntityManagerFactoryBuilder builder) {
        return builder.dataSource(dsTwo)
                .properties(jr.getProperties())
                //设置实体类所在位置
                .packages("com.datasource.demo.entity.two")
                .persistenceUnit("pu2")
                .build();

    }

    @Bean(name = "two")
    public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
        return localContainerEntityManagerFactoryBeanTwo(builder).getObject().createEntityManager();
    }

    @Bean
    PlatformTransactionManager platformTransactionManagerTwo(EntityManagerFactoryBuilder builder) {
        LocalContainerEntityManagerFactoryBean localContainerEntityManagerFactoryBean = localContainerEntityManagerFactoryBeanTwo(builder);
        return new JpaTransactionManager(localContainerEntityManagerFactoryBean.getObject());
    }
}

````



### JpaQueryFactory

```
package com.datasource.demo.config;

import com.querydsl.jpa.impl.JPAQueryFactory;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.persistence.EntityManager;

/**
 * @author xukai
 * @create 2020-04-10 15:03
 */
@Configuration
public class JpaQueryFactory {
    @Bean({"oneFactory"})
    public JPAQueryFactory jpaQueryFactoryOne(@Qualifier("one") EntityManager entityManager) {
        return new JPAQueryFactory(entityManager);
    }


    @Bean({"twoFactory"})
    public JPAQueryFactory jpaQueryFactoryTwo(@Qualifier("two") EntityManager entityManager) {
        return new JPAQueryFactory(entityManager);
    }
}

```



## 结构

<img src="E:\Users\kai.xu\Desktop\My\Typora-Note\image\Jpa多数据源.png" alt="image-20200710154540306" style="zoom:200%;" />



# Jpa 审计

操作数据库映射实体类时，通常需要记录createTime和updateTime，如果每个对象新增或修改去都去手工操作创建时间、更新时间，会显得比较繁琐。Spring Data JPA提供了自动填充字段的功能，简单配置一下即可



```
实体类上添加 @EntityListeners(AuditingEntityListener.class)

在需要的字段上加上 @CreatedDate、@CreatedBy、@LastModifiedDate、@LastModifiedBy 等注解。

在Xxx Application 启动类上添加 @EnableJpaAuditing

实现 AuditorAware 接口来返回你需要插入的值。重点！
```



```
@Component
@EnableJpaAuditing
public class UserIdAuditorAware implements AuditorAware<Integer> {

    @Override
    public Optional<Integer> getCurrentAuditor() {
        return Optional.of(111);
    }
}
```

