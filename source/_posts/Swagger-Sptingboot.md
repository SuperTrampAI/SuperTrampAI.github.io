---
title: Swagger Sptingboot
categories: Coder
date: 2019-05-17 00:38:06
tags: swagger2
description: SpringBoot集成Swagger，方便前后端分离，UI接口文档
---

在pom中引入依赖：
~~~
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.6.1</version>
        </dependency>
~~~

还有一个依赖可以更改swagger ui 的样式，如果需要更改，需要把springfox-swagger-ui
改为如下：

~~~
      <dependency>
           <groupId>com.github.caspar-chen</groupId>
           <artifactId>swagger-ui-layer</artifactId>
           <version>1.1.3</version>
       </dependency>
~~~
这是依赖，而后在Appliction同级目录添加两个类
~~~
name : SwaggerConfig.java

/**
 * @Auther: lxh800109@gmail.com
 * @Date: 2019/5/15 23:40
 * @Description:
 */

@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket ProductApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .genericModelSubstitutes(DeferredResult.class)
                .useDefaultResponseMessages(false)
                .forCodeGeneration(false)
                .pathMapping("/")
                .select()
                .build()
                .apiInfo(productApiInfo());
    }

    private ApiInfo productApiInfo() {
        ApiInfo apiInfo = new ApiInfo("fantasticfunicular 数据接口文档",
                "Thinking , Doing , Being",
                "1.0.0",
                "API TERMS URL",
                "lxh800109@gmail.com",
                "license",
                "https://supertrampai.github.io/");
        return apiInfo;
    }
}

~~~
如果出现了404 ， 下面解决页面404问题

~~~
name : WebConfig.java
/**
 * @Auther: lxh800109@gmail.com
 * @Date: 2019/5/15 23:40
 * @Description:
 */
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/statics/**").addResourceLocations("classpath:/statics/");
      // 解决 SWAGGER 404报错
        registry.addResourceHandler("/swagger-ui.html").addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
    }

   @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {

   }

}


~~~
在Application启动类上添加如下注解：@EnableSwagger2
~~~

/**
 * @Auther: lxh800109@gmail.com
 * @Date: 2019/5/15 23:40
 * @Description:
 */

@EnableSwagger2
@SpringBootApplication
@ServletComponentScan
public class FantasticFunicularApplication  extends SpringBootServletInitializer {

    public static void main(String[] args) {
        SpringApplication.run(FantasticFunicularApplication.class, args);
    }

    @Override
    public SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(FantasticFunicularApplication.class);
    }

}

~~~

在Controller类添加如下注解：
~~~
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiOperation;
import org.apache.catalina.User;
import org.springframework.web.bind.annotation.*;
/**
 * @Auther: lxh800109@gmail.com
 * @Date: 2019/5/15 23:40
 * @Description:
 */


@Api(value="/test", tags="测试接口模块")
@RestController
@RequestMapping("/test")
public class TestSwaggerController {

    @ApiOperation(value="展示首页信息", notes = "展示首页信息")
    @GetMapping("/show")
    public Object showInfo(){
        return "hello world";
    }

    @ApiOperation(value="添加用户信息", notes = "添加用户信息")
    @ApiImplicitParam(name="user", value="User", required = true, dataType = "User")
    @PostMapping("/addUser")
    public Object addUser(@RequestBody User user){
        return "success";
    }
}


~~~

在domain/dto类上，加入如下注解：
~~~

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;

import java.io.Serializable;

@Data
@ApiModel(description = "user--输出对象")
public class UserOutputDto implements Serializable {

    private static final long serialVersionUID = 1L;

    @ApiModelProperty(value = "id")
    private Integer id;
    @ApiModelProperty(value = "name")
    private String name;
}

~~~


Swagger 常用注解：
~~~
Api
ApiModel
ApiModelProperty
ApiOperation
ApiParam
ApiResponse
ApiResponses
ResponseHeader
~~~

1. api标记
Api 用在类上，说明该类的作用。可以标记一个Controller类做为swagger 文档资源，使用方式：
~~~
@Api(value = "/user", description = "description")

value    url的路径值
tags 如果设置这个值、value的值会被覆盖
description 对api资源的描述
basePath 基本路径可以不配置
position 如果配置多个Api 想改变显示的顺序位置
produces For example, "application/json, application/xml"
consumes For example, "application/json, application/xml"
protocols Possible values: http, https, ws, wss.
authorizations 高级特性认证时配置
hidden 配置为true 将在文档中隐藏
~~~
2. ApiOperation标记
ApiOperation：用在方法上，说明方法的作用，每一个url资源的定义,使用方式：
~~~
@ApiOperation(
          value = "Find purchase order by ID",
          notes = "For valid response try integer IDs with value <= 5 or > 10. Other values will generated exceptions",
          response = User,
          tags = {"Pet Store"})
          value
          url的路径值

          tags    如果设置这个值、value的值会被覆盖
          description    对api资源的描述
          basePath  基本路径可以不配置
          position    如果配置多个Api 想改变显示的顺序位置
          produces  For example, "application/json, application/xml"
          consumes  For example, "application/json, application/xml"
          protocols      Possible values: http, https, ws, wss.
          authorizations    高级特性认证时配置
          hidden  配置为true 将在文档中隐藏
          response  返回的对象
          responseContainer这些对象是有效的 "List", "Set" or "Map".，其他无效
          httpMethod"GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" and "PATCH"
          codehttp的状态码 默认 200
          extensions扩展属性
~~~

补充：
[常用注解](https://www.jianshu.com/p/12f4394462d5)
[Swaggger](https://swagger.io/)
[Swagger-ui-layer GitHub](https://github.com/caspar-chen/swagger-ui-layer)
