---
title: Swagger的配置及使用
date: 2020-05-12 14:44:25
tags: 
	- Java
	- Swagger
---

# Swagger 介绍

Swagger 用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。

总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法、参数和模型紧密集成到服务器端的代码，允许 API 来始终保持同步。Swagger 让部署管理和使用功能强大的 API 从未如此简单。

<!-- more -->

# 配置Swagger

##  添加Maven依赖

```xml
<!-- swagger -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.9.2</version>
	<exclusions>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
					</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-orm</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
		</exclusion>
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-oxm</artifactId>
		</exclusion>
	</exclusions>
</dependency>
<!-- swagger UI -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.9.2</version>
</dependency>
<!-- swagger bootstrap ui -->
<dependency>
	<groupId>com.github.xiaoymin</groupId>
	<artifactId>swagger-bootstrap-ui</artifactId>
	<version>1.9.6</version>
</dependency>
```
## Spring MVC配置Swagger

### 编写SwaggerConfig类

```java
@EnableWebMvc
@EnableSwagger2
@Configuration
@EnableSwaggerBootstrapUI
public class SwaggerConfig extends WebMvcConfigurationSupport {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(this.apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.ihuaben.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("author.ihuaben.com")
                .description("接口描述")
//                .termsOfServiceUrl("http://127.0.0.1:8080/swagger-ui.html")
                .version("1.0.0")
                .contact(new Contact("zhonghong", "", "zhonghong@liangzishidai.cn"))
                .build();
    }

}
```

### 编写SwaggerResourcesProvider的实现类

```java
@Primary
@Configuration
public class MySwaggerResourceProvider implements SwaggerResourcesProvider {

    private static final String SWAGGER_URL = "/v2/api-docs";

    @Override
    public List<SwaggerResource> get() {
        List resources = new ArrayList<>();
        resources.add(swaggerResource("book.ihuaben.com", SWAGGER_URL, "1.0"));
        resources.add(swaggerResource("author.ihuaben.com", SWAGGER_URL, "1.0"));
        resources.add(swaggerResource("function.ihuaben.com", SWAGGER_URL, "1.0"));
        return resources;
    }

    private SwaggerResource swaggerResource(String name, String location, String version) {
        SwaggerResource swaggerResource = new SwaggerResource();
        swaggerResource.setName(name);
        swaggerResource.setUrl(location);
        swaggerResource.setSwaggerVersion(version);
        return swaggerResource;
    }
}
```

### 编辑Spring的xml配置文件

```xml
<!-- 使用 Swagger Restful API 文档时，添加此注解 -->
<mvc:default-servlet-handler />
<!-- 拦截器 -->
<mvc:interceptors>
	<mvc:interceptor>
		<mvc:mapping path="/**" />
		<!-- 将swagger的请求放行 -->
		<mvc:exclude-mapping path="/swagger*/**"></mvc:exclude-mapping>
		<mvc:exclude-mapping path="/v2/**"></mvc:exclude-mapping>
		<mvc:exclude-mapping path="/webjars/**"></mvc:exclude-mapping>
		<bean class="com.ihuaben.bridge.web.BaseInterceptor" />
</mvc:interceptor>
```

### 添加访问权限控制

在web.xml中添加相应的Filter，添加如下内容

```xml
<!--SwaggerBootstrapUi提供的Swagger增强功能,Filter过滤保护Swagger资源-->
<!--生产环境Filter-->
<filter>
	<filter-name>swaggerProductionFilter</filter-name>
	<filter-class>com.github.xiaoymin.swaggerbootstrapui.filter.ProductionSecurityFilter</filter-class>
	<init-param>
		<param-name>production</param-name>
		<param-value>false</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>swaggerProductionFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
<!--Swagger资源的Basic认证保护策略-->
<filter>
	<filter-name>swaggerSecurityBasic</filter-name>
	<filter-class>com.github.xiaoymin.swaggerbootstrapui.filter.SecurityBasicAuthFilter</filter-class>
	<!--开启basic认证-->
	<init-param>
		<param-name>enableBasicAuth</param-name>
		<param-value>true</param-value>
	</init-param>
	<!--用户名&密码-->
	<init-param>
		<param-name>userName</param-name>
		<param-value>admin</param-value>
	</init-param>
	<init-param>
		<param-name>password</param-name>
		<param-value>ihuaben123@</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>swaggerSecurityBasic</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>

```

## Spring Boot配置Swagger

### 编写SwaggerConfig类

与Spring MVC一致

然后就OK了。。。（Spring Boot就是如此简单）

## 添加访问权限控制

### 1、环境权限配置

在SwaggerConfig类上添加`@Profile`注解：

```java
@Profile({"dev","test"})
```

如以上配置，则只有在dev以及test环境有效，在生产环境不可访问。

### 2、账户权限配置

针对Swagger的资源接口,SwaggerBootstrapUi提供了简单的Basic认证功能（版本1.9+）。

配置yml文件：

```yml
swagger:
  production: false
  basic:
    enable: true
    username: admin
    password: 123456
```

> 切记swagger.production 不可设置为true，否则将屏蔽所有资源



# 多个微服务Swagger进行整合

## 使用网关统一入口

### 编写SwaggerResourcesProvider的实现类

```java
/**
 * 聚合各个服务的swagger接口
 */
@Component
public class MySwaggerResourceProvider implements SwaggerResourcesProvider {
    /**
     * swagger2默认的url后缀
     */
    private static final String SWAGGER2URL = "/v2/api-docs";
 
    /**
     * 网关路由
     */
    private final RouteLocator routeLocator;
 
    /**
     * 网关应用名称
     */
    @Value("${spring.application.name}")
    private String self;
 
    @Autowired
    public MySwaggerResourceProvider(RouteLocator routeLocator) {
        this.routeLocator = routeLocator;
    }
 
    @Override
    public List<SwaggerResource> get() {
        List<SwaggerResource> resources = new ArrayList<>();
        List<String> routeHosts = new ArrayList<>();
        // 获取所有可用的host：serviceId
        routeLocator.getRoutes().filter(route -> route.getUri().getHost() != null)
                .filter(route -> !self.equals(route.getUri().getHost()))
                .subscribe(route -> routeHosts.add(route.getUri().getHost()));
 
        // 记录已经添加过的server，存在同一个应用注册了多个服务在eureka上
        Set<String> dealed = new HashSet<>();
        routeHosts.forEach(instance -> {
            // 拼接url
            String url = "/" + instance.toLowerCase() + SWAGGER2URL;
            if (!dealed.contains(url)) {
                dealed.add(url);
                SwaggerResource swaggerResource = new SwaggerResource();
                swaggerResource.setUrl(url);
                swaggerResource.setName(instance);
                resources.add(swaggerResource);
            }
        });
        return resources;
    }
}
```



# 使用

| 资源                                      | 说明                                    |
| ----------------------------------------- | --------------------------------------- |
| /doc.html                                 | SwaggerBootstrapUi提供的文档访问地址    |
| /api-docs-ext                             | SwaggerBootstrapUi提供的增强接口地址    |
| /swagger-resources                        | Springfox-Swagger提供的分组接口         |
| /api-docs                                 | Springfox-Swagger提供的分组实例详情接口 |
| /swagger-ui.html                          | Springfox-Swagger提供的文档访问地址     |
| /swagger-resources/configuration/ui       | Springfox-Swagger提供                   |
| /swagger-resources/configuration/security | Springfox-Swagger提供                   |

