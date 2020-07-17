@Configuration  配置类 == 配置文件  告诉Spring 是一个配置类
@Bean  给容器注册一个Bean  id默认是用方法名 默认是单实例
@Scope  调整作用域

		prototype 多实例   ioc 容器启动并不会去调用方法创建对象在容器中 每次获取的时候才创建对象
		singleton 单实例   ioc 容器启动会调用方法创建对象到ioc容器中 每次获取直从容器中获取
		request   同一个请求创建一个实例
		session   同一个session创建一个实例

@Lazy  懒加载  针对单实例  创建不加载   第一次获取加载		
			//@Scope("prototype")
			@Lazy
			@Bean(persion)
			public Person person(){
				return new Person();
			}
@Conditional({Windows.class})   按照一定的条件进行判断 满足条件给容器加入 bean

给容器中注册组件
1)@ComponentScan(value="com.abc") 包扫描  自动扫描加进容器中 + 标注了@Controller. @Service,@Repository,@Component
2)@Bean [导入的第三方包里面的组件]
3)@import[快速给容器中导入一个组件] 
		@import[快速给容器中导入一个组件] 容器中就会自动注册这个组件 id组件的全类名
		@ImportSelector 返回需要导入组件的全类名数组
		@ImportBeanDefinitionRegistrar:手动注册  bean到容器中
			@Import({color.class,red.class,MyImportSelector.class})


  beanDefinition = new RootBeanDefinition(RainBow.class);
	registry.registerBeanDefiniton("rainBow",beanDefinition);

		MyImportSelector implement ImportSelector{
		AnnotaionMetadata:当前@import注解的类的所有注解信息
			@Override
			public String[] selectImports(AnnotaionMetadata importingClass)
		}


@Value
			1) @Value("zhangsan") 基本数值
			2) @Value("#{20-2}")  SpEL #{}
			3) @Value("${person.neckName}") ${} 读取配置文件

@PropertySource(Value={"classpath:/person.properties"}) 读取外部配置文件中的K/V保存到运行的环境中

	AnnotationConfigApplicationContext applicationContext = new  AnnotationConfigApplicationContext("") 		
	
		applicationContext.getEnvironment().getProperty("person.neckName");

​		
@Autowired 自动装配 自动注入:
​		1) 默认优先按照类型去容器中找对应的组件:applicationContext.getBean(BookDao.class);
​		2) 如果有多个相同类型的组件,则根据属性的名称作为组件的id去容器查找
​									applicationContext.getBean(bookDao);
​		3) @Qualifier("bookDao") 指定需要装配的组件id,而不是使用属性名
​		4) 自动装配默认一定要将属性赋值好,没有就会报错;
​			Autowired(required=false);
​		5) @Primary 让Spring进行自动装配的时候 默认使用首选的bean
​					也可以继续使用@Qualifier 指定需要装配的bean的 名字
​					
					BookService{
						Autowired
						BookDao bookDaol
					}
		 Autowired : Spring 定义的
		@Resource(JSR250) 和@Inject (JSR330)[java规范的注解]
		@Resource
			可以和@Autowired 一样实现自动装配功能; 默认按照组件名称进行装配
			没有能支持@primary功能没有支持@Autowired(reqiured=false)
		@inject:
			需要导入javax.inject的包,和Autowired功能一样 没有 required=false功能

​		
​		
​		
​		

# 	IOC	

控制反转

核心: 通过反射创建对象 操作对象

​		
​		
​		
​		
​		
​		
​		
​		