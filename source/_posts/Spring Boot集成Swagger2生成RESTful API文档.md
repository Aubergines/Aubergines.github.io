---
title: Spring Boot集成Swagger2生成RESTful API文档
tags: spring boot、Swagger2
date: 2018-1-05 20:11:10
grammar_cjkRuby: true
---

* 使用第三方工具的一般思路：
     1. 引入Jar包
     2. 进行配置（可选）
     3. 使用API
     4. 验证功能是否正常运行
* 使用Swagger2的思路：
	1. 使用Maven引入Swagger的jar包
	2. 对Swagger2进行配置：Swagger2.java
	3. 使用Swagger的API对类(@Api)、方法(@ApiOperation)、参数(@ApiImplicitParam)进行注解。
	4. 打开浏览器验证API文档是否能够正常显示

## 1、添加Swagger2 Maven依赖 ##
```xml
<!-- Swagger2依赖 -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.7.0</version>
</dependency>
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.7.0</version>
</dependency>
```

## 2、添加Swagger2配置类 ##

在`com.configure`包下添加`Swagger2.java`文件,如下图：

![enter description here][1]

`Swagger2.java`关键代码如下：

```java
@Configuration   //（1）让Spring来加载该类配置。
@EnableSwagger2  //（2）启用Swagger2
public class Swagger2 {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())  //（3）创建该Api的基本信息（这些基本信息会展现在文档页面中）
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.business")) //(4)填写Controller的目录【必须修改】
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() { // (5)依据项目进行修改
        Contact contact = new Contact("author","url","email");
        return new ApiInfoBuilder()
                .title("标题")
                .description("描述")
                .contact(contact)
                .version("1.0")
                .build();
    }
}
```

`Swagger2.java`完整代码如下：

```java
package com.configure;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class Swagger2 {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.business"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        Contact contact = new Contact("author","url","email");
        return new ApiInfoBuilder()
                .title("标题")
                .description("描述")
                .contact(contact)
                .version("1.0")
                .build();
    }
}
```
	`@Configuration注解，让Spring来加载该类配置。`
	`@EnableSwagger2注解来启用Swagger2。`
## 3、在Controller层添加API文档说明 ##

import三个类：

	import io.swagger.annotations.Api;
	import io.swagger.annotations.ApiImplicitParam;
	import io.swagger.annotations.ApiOperation;

使用类、方法、参数三种类型的注释：

	@Api(tags="Y-用户-头像") //对Controller类的注释
	@ApiOperation(value="用户上传头像", notes="Implementation Notes") //方法的注释
	@ApiImplicitParam(name = "paramMap", value = "一个paramMap", required = true, dataType = "Map") //方法参数的注释

示例：

```java
package com.business.comm.data.logo.url.controller;

import com.business.comm.data.logo.url.service.CommDataLogoUrlService;
import com.util.BaseResponseObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiOperation;

@Api(tags="Y-用户-头像")
@RestController
@RequestMapping("/comm/data/logo/url")
@SuppressWarnings("rawtypes")
public class CommDataLogoUrlController extends BaseResponseObject {
    @Autowired
    private CommDataLogoUrlService service;

    @ApiOperation(value="用户上传头像", notes="")
    @ApiImplicitParam(name = "paramMap", value = "一个paramMap", required = true, dataType = "Map")
    @RequestMapping(value = "/v1/create", method = RequestMethod.POST)
    public BaseResponseObject create(@RequestBody Map paramMap) {
        return service.create(paramMap);
    }
}
```

## 4、API文档访问 ##

启动Spring Boot程序，访问：http://localhost:8091/swagger-ui.html

![enter description here][2]

其中，端口号8091，可以通过`application.yml`文件中的`server.port`参数查询，如下图：

![enter description here][3]


  [1]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/1/16/20180111135947.png "配置类"
  [2]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/1/16/20180111153828.png "UI"
  [3]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/1/16/20180111153538.png "展示"