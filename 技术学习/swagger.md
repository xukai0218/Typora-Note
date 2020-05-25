# 一：swagger是什么？

1、是一款让你更好的书写API文档的规范且完整框架。

2、提供描述、生产、消费和可视化RESTful Web Service。

3、是由庞大工具集合支撑的形式化规范。这个集合涵盖了从终端用户接口、底层代码库到商业API管理的方方面面。



http://localhost:8080/XXX/doc.html

# 依赖

```
		 <!--swagger-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>knife4j-spring-boot-starter</artifactId>
            <version>2.0.2</version>
        </dependency>
        
        
        <dependency>
           <groupId>com.github.xiaoymin</groupId>
           <artifactId>swagger-bootstrap-ui</artifactId>
           <version>1.8.1</version>
        </dependency>
        
        
```

# 配置

```
@Configuration
@EnableSwagger2
public class Swagger2Configuration {

    /**
     * 创建API
     */
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                // 详细定制
                .apiInfo(apiInfo())
                .select()
                // 指定当前包路径
                //.apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                // 扫描所有
                //.apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }

    /**
     * 添加摘要信息
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("HelloWord")
                .description("描述：")
                .version("1.0")
                .build();
    }
}
```

# 常用注解

```
@Api(tags = {"分库 分表"})
```

```
@ApiOperation(value = "创建订单", notes = "创建订单", httpMethod = "POST")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "Authorization", value = "token 信息", required = true, paramType = "header"),
            @ApiImplicitParam(name = "x-store-id", value = "店铺 id", required = true, paramType = "header"),
            @ApiImplicitParam(name = "x-user-id", value = "当前用户 id", required = true, paramType = "header"),
            @ApiImplicitParam(name = "storeId", value = "店铺id", required = true, example = "123456")
    })
        @ApiResponses({
            @ApiResponse(code = 40401, message = "用来创建订单的资源不存在"),
            @ApiResponse(code = 403001, message = "订单实付金额不能为0")
    })
```









































































































