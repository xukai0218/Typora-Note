**1、什么是Spring MVC ？简单介绍下你对springMVC的理解?**

```
Spring MVC是一个基于Java的实现了MVC设计模式的请求驱动类型的轻量级Web框架，通过把Model，View，Controller分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合。
```

**2、SpringMVC的流程？**

```
（1）用户发送请求至前端控制器DispatcherServlet；
（2） DispatcherServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handler；
（3）处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet；
（4）DispatcherServlet 调用 HandlerAdapter处理器适配器；
（5）HandlerAdapter 经过适配调用 具体处理器(Handler，也叫后端控制器)；
（6）Handler执行完成返回ModelAndView；
（7）HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；
（8）DispatcherServlet将ModelAndView传给ViewResolver视图解析器进行解析；
（9）ViewResolver解析后返回具体View；
（10）DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）
（11）DispatcherServlet响应用户。
原文链接：https://blog.csdn.net/a745233700/java/article/details/80963758
```

**4、Spring MVC的主要组件？**

```
（1）前端控制器 DispatcherServlet（不需要程序员开发）

作用：接收请求、响应结果，相当于转发器，有了DispatcherServlet 就减少了其它组件之间的耦合度。

（2）处理器映射器HandlerMapping（不需要程序员开发）

作用：根据请求的URL来查找Handler

（3）处理器适配器HandlerAdapter

注意：在编写Handler的时候要按照HandlerAdapter要求的规则去编写，这样适配器HandlerAdapter才可以正确的去执行Handler。

（4）处理器Handler（需要程序员开发）

（5）视图解析器 ViewResolver（不需要程序员开发）

作用：进行视图的解析，根据视图逻辑名解析成真正的视图（view）

（6）视图View（需要程序员开发jsp）

View是一个接口， 它的实现类支持不同的视图类型（jsp，freemarker，pdf等等）

```



# 拦截器配置

```
@Slf4j
//public class aa implements HandlerInterceptor {
public class aa extends HandlerInterceptorAdapter {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String pageStr = request.getParameter("page");
        String sizeStr = request.getParameter("size");
log.info("132");
        return true;
    }
}
```

```
@Configuration
public class WebAppConfigure implements WebMvcConfigurer {

    @Bean
    public Converter stringToLocalDateTimeConverter() {
        return new StringToLocalDateTimeConverter();
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new PageInterceptor());
        registry.addInterceptor(new aa());
    }
}
```

```
public final class PageHelper {

    private static final ThreadLocal<PageRequest> PAGE_REQUEST = new InheritableThreadLocal<>();

    public static PageRequest getPageRequest() {
        return PAGE_REQUEST.get();
    }

    static void setPageRequest(int page, int size) {
        PAGE_REQUEST.set(PageRequest.of(page, size));
    }

    static void clear() {
        PAGE_REQUEST.remove();
    }
}
```

# 验证

```
@Validated

@RestController

@Api(value = "测试使用validation验证参数")

public class TestController {

 

    /**

     * 测试get方法,手动if进行判空,校验失败时手动抛出自定义异常

     *

     * @param username 姓名

     * @return ResponseResult

     */

    @ApiOperation(value = "测试get方法", notes = "输入用户名")

    @GetMapping("/testGet")

    public ResponseResult testGet(String username) {

        if (username == null || "".equals(username)) {

            throw new ParamaErrorException("username 不能为空");

        }

        return new ResponseResult(ResultEnum.SUCCESS);

    }

 

    /**

     * 使用注解校验get请求平面参数,需要在Controller类头部添加@Validated注解,否则不能成功校验，这种方法不用手动抛出异常

     *

     * @param username

     * @return

     */

    @ApiOperation(value = "测试get方法", notes = "输入用户名")

    @GetMapping("/testGetByValidated")

    public ResponseResult testGetByValidated(@Length(max = 4) @RequestParam("username") String username) {

        return new ResponseResult(ResultEnum.SUCCESS);

    }

 

 

    /**

     * post方法传入单个对象进行校验,在参数前添加@Valid注解,校验失败时会抛出异常并使用全局异常进行处理

     *

     * @param userInfo 用户信息

     * @return ResponseResult

     */

    @ApiOperation(value = "post方法传入单个对象", notes = "传入json对象")

    @PostMapping("/testUserInfo")

    public ResponseResult testUserInfo(@Valid @RequestBody UserInfo userInfo) {

        return new ResponseResult(ResultEnum.SUCCESS);

    }

 

    /**

     * post方法传入对象,手动校验,此时参数前没有添加@Valid注解,所以不会自动进行校验,手动调用validate方法进行校验,失败时会抛出异常

     *

     * @param userInfo

     * @return ResponseResult

     */

    @ApiOperation(value = "post方法传入对象,手动测试", notes = "单个对象")

    @PostMapping("/checkByMethod")

    public ResponseResult checkByMethod(@RequestBody UserInfo userInfo) {

        //调用api校验

        MyValidationUtils.validate(userInfo);

        return new ResponseResult(ResultEnum.SUCCESS);

    }

 

    /**

     * post方法传入多个对象,当使用@Valid校验对象集合时,要在控制层添加@Validated注解,否则不会对集合中的每个对象进行校验

     *

     * @param userInfo

     * @return ResponseResult

     */

    @ApiOperation(value = "post方法传入多个对象", notes = "多个对象")

    @PostMapping("/testUserList")

    public ResponseResult testUserList(@Valid @RequestBody List<UserInfo> userInfo) {

        return new ResponseResult(ResultEnum.SUCCESS);

    }

 

    /**

     * 测试对象中嵌套对象的情况,此时也要在对象属性上添加@Valid注解

     *

     * @param user

     * @return

     */

    @ApiOperation(value = "测试对象中嵌套对象的情况")

    @PostMapping("/checkUser")

    public ResponseResult checkUser(@Valid @RequestBody User user) {

        return new ResponseResult(ResultEnum.SUCCESS);

    }

 

    /**

     * 将校验结果放进BindingResult里面,用户自行判断并处理

     *

     * @param userInfo

     * @param bindingResult

     * @return

     */

    @PostMapping("/testBindingResult")

    public String testBindingResult(@RequestBody @Valid UserInfo userInfo, BindingResult bindingResult) {

        // 参数校验

        if (bindingResult.hasErrors()) {

            String messages = bindingResult.getAllErrors()

                    .stream()

                    .map(ObjectError::getDefaultMessage)

                    .reduce((m1, m2) -> m1 + "；" + m2)

                    .orElse("参数输入有误！");

            //这里可以抛出自定义异常,或者进行其他操作

            throw new IllegalArgumentException(messages);

        }

        return "操作成功！";

    }

}
```

