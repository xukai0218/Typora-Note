# Lombok

能以简单的注解形式来简化java代码，提高开发人员的开发效率。例如开发中经常需要写的javabean，都需要花时间去添加相应的getter/setter，也许还要去写构造器、equals等方法，而且需要维护，当属性多时会出现大量的getter/setter方法，这些显得很冗长也没有太多技术含量，一旦修改属性，就容易出现忘记修改对应方法的失误。

1) @Data注解在类上，会为类的所有属性自动生成setter/getter、equals、canEqual、hashCode、toString方法，如为final属性，则不会为该属性生成setter方法。
2) @Getter/@Setter，此注解在属性上，可以为相应的属性自动生成Getter/Setter方法

3) @NonNull 该注解用在属性或构造器上，Lombok会生成一个非空的声明，可用于校验参数，能帮助避免空指针。

4) @Cleanup 该注解能帮助我们自动调用close()方法，很大的简化了代码。

5) @EqualsAndHashCod 会使用所有非静态（non-static）和非瞬态（non-transient）属性来生成equals和hasCode，也能通过exclude注解来排除一些属性

6) @ @ToString 类使用@ToString注解，Lombok会生成一个toString()方法，默认情况下，会输出类名、所有属性（会按照属性定义顺序），用逗号来分割。
	通过将includeFieldNames参数设为true，就能明确的输出toString()属性。
7) @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor	  无参构造器、部分参数构造器、全参构造器。Lombok没法实现多种参数构造器的重载。

@NoArgsConstructor: 自动生成无参数构造函数。 
@AllArgsConstructor: 自动生成全参数构造函数。
优点：

能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
让代码变得简洁，不用过多的去关注相应的方法
属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等
缺点：

不支持多种参数构造器的重载
虽然省去了手动创建getter/setter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度
