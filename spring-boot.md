# Spring boot

## Chapter 10

### Upgrade from earlier Version of Spring boot

#### 更新存在的Spring Boot 命令行工具 CLI 使用安装工具 比如（brew upgrade）

#### 问题：使用@RestController 注解的类独立不写在主类里不能使用

#### spring boot 项目继承spring-boot-starter-parent 带来的特性

- java 1.8 作为默认的编译等级
- 将UTF-8作为默认的编码方式
- 一个继承自spring-boot-dependencies pom 这个pom 管理了公共依赖的版本
- 智能的资源过滤
- 智能插件配置
- 智能的application.properties application.yml 过滤器插件

### 不使用spring-boot-parent 的情况下使用

- 通过使用scope=import 你依然可以享用依赖管理的便利（对于插件管理无效）

### 配置文件

- 建议使用Java-base 配置文件
- 如果一定要使用xml配置建议写一个使用了@configuration 注解的类，并在其中使用@ImportResource注解引入配置文件

## Chapter 16

### 自动配置

#### 如何禁用特定文件的自动配置

- 使用@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
- 使用spring.autoconfigure.exclude 属性管理禁用自动配置列表
- 可以使用两种方式禁用自动配置 注解级别 和 配置级别

## Chapter 17

### 可以直接使用Spring 中的依赖注入之类的东西

## Chapter 18 Using the @SpringBootApplication Annotation

### @SpringBootApplication 注解提供了 自动配置 组件扫描以及 配置三个注解的功能

- @EnableAutoConfiguration 打开自动配置
- @ComponentScan 扫描本地组件
- @Configuration 允许在上下文中注册额外的beans以及导入额外的配置文件

等同于是用了以上三个注解

### 如果不想要使用自动扫描 只想要引入某个类那么就使用@Import注解将类引入

## Chapter 19

### 应用事件以及监听器

- 有些事件的触发是在应用上下文建立之前，所以不能使用@Bean注解去注册监听器需要使用`SpringApplciation.addListeners()`或者调用`SpringApplicationBuilder.listeners()`

#### Spring Boot 生命周期中触发的事件

- ApplicationStartingEvent 在注册事件监听器和初始化程序之后其他程序之前触发
- ApplicationEnvironmentPreparedEvent在context创建之前触发
- ApplicationPrepareEvent 在刷新开始之前bean定义加载之后
- ApplicationStartedEvent 上下文刷新之前在任意应用以及命令行被调用之前
- ApplicationReadyEvent 在应用和命令行调用之后 这个表明应用可以接受请求
- ApplicationFailedEvent在启动错误的情况下发出

#### WEB 环境的准备 上下文环境的区别

- 使用算法
- 如果现在的上下文是Spring MVC 使用AnnotationConfigServletWebServerApplicationContext 、
- 如果现在的使用的是Spring WebFlux 那么使用AnnotationConfigReactiveWebServerApplicationContext
- 如果是其他那么使用的是AnnotationConfigApplicationContext
- 可以使用setApplicationContextClass() 来明确的设置上下文环境

#### ApplicationRunner 和CommandLineRunner

- 如果你想要在程序启动之后运行特定的代码，你需要实现`ApplicationRunnier` 或者`CommandLineRunner` 接口
- 这两个接口以相同的方式工作，在SpringApplication.run() 方法之前运行
- 如果定义了一堆的类，那么实现`org.springframework.core.Ordered`或者使用`org.springframework.annotation.Order`注解

## Chapter 27 Developing Web Application 

### Spring MVC 框架

- spring mvc 允许你创建注解了@Controller 和@RestController 的bean 来处理进来的http 请求，使用@RequestMapping 注解的方法对应了具体链接
- spring MVC 是Spring 框架的核心之一

### Spring MVC的自动配置

- 自动配置提供了如下的属性
	1. 包括了 `ContentNegotiatingViewResolver` 和 `BeanNameViewResolver` 
	2. 支持静态资源服务，包括对WebJars的支持
	3. Converter,GenericConverter和Formatter beans 的自动注册
	4. 支持HttpMessageConverters
	5. MessageCodesResolver的自动注册
	6. 支持静态 index.html 
	7. 支持定制Favicon
	8. 自动使用ConfigurableWebBindingInitizlizer
- 如果你既想要保持Spring Boot MVC 特性也想要添加额外的Spring MVC 配置 ，添加自己的使用了`@Configuration`注解的类但是不能使用`@EnableWebMvc`注解
- 如果你想要定制一个处理器替代`RequestMappingHandlerMaping ，RequestMappingHandlerAdapter`或者`ExceptionHandlerExceptionResolver`,定义一个`WebMvcRegistrationsAdapter`.

#### HttpMessageConverters

- Spring MVC 使用了`HttpMessageConverters`接口来转化HTTP请求和返回报文。包含了智能的开箱即用的配置，比如对象可以自动的序列化为JSON对象或者XML，默认的编码格式是`UTF-8`
- 如果你想要定制转化器，你可以使用Spring Boot‘s `HttpMessageConverters`class 

``` Java
import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.spring.http.converter.*;

@Configuration
public class MyConfiguration{
	@Bean
	public HttpMessageConverters customConverters(){
	
		HttpMessageConverters<?> additional = ...
		HttpMessageConverters<?> another = ...
		return new HttpMessageConverters(additional,another);
	}
}

```

### 定制JSON的序列化和反序列化

- 定制序列化和反序列化通常通过一个模块注册Jackson，但是Spring Boot 提供了一个`@JsonCompent` 注解来使得这个功能更加的直观和便利。

``` Java
import java.io.*;
import com.fasterxml.jackson.core.*;
import com.fasterxml.jackson.databind.*;
import org.springframework.boot.jackson.*;
@JsonCompent
public class Example{
	public static class Serializer extends JsonSerializer<SomeObject>{
	
		//...
	}
	public static class DeSerializer extends JsonDeSerializer<SomeObject>{
	
		//...
	}
}
```

- 所有的有`@JsonCompent`注解了的beans都会被应用上下文自动加载因为这个注解被`@Compent`注解了

### MessageCodesResolver

错误信息监听器，Spring MVC 有一个策略从`MessageCodesResolver`生产错误消息
如果你设置了`spring.mvc.message-codes-resolver.format`属性为`PREFIX_ERROR_CODE`或者`POST_ERROR_CODE`,Spring Boot 将会为你创建一个

### 静态内容

- 默认情况下，spring boot 从`/static` (或者`/public`或者`/resources`或者`/META-INF/resources`) 加载静态资源文件它使用了Spring MVC 的`ResourcesHttpRequestHandler`所以你可以通过添加`WebMvcConfigurer`和重写`addResourceHandlers`方法来改变默认行为
- 有些未理解

### 欢迎界面

- Spring Boot 支持两种欢迎界面包括静态界面和样板界面，首先他会查找一个index.html在定义的静态资源路径下，如果没有找到那么就以index 为模板找 比如index.jsp 等，如果还是没有找到那么就自动设置一个界面。
- 定制Favicon ，首先查找在静态资源文件中定义的icon路径如果没有找到那么使用默认的

### 路径匹配和资源协商

- Spring boot 选择默认禁用后缀模式，"Get /projects/spring-boot.json"不会和@GetMapping("/projects/spring-boot")匹配。这被认为是spring MVC程序的最佳实践。这个功能对于没有发送"accept"请求头的HTTP客户端来说非常的有用。我们需要确保发送真确的内容类型给客户端。现在内容协商更加的可靠
- 有另一种方法处理没带accept内容头的http 请求。我们使用在参数中带有内容参数来替代禁用后缀模式
- 如果你知道不禁用后缀模式的风险而且依然先要使用后缀模式，你需要配置如下内容

```xml
spring.mvc.contentnegotiation.favor-path-extension=true

```

### ConfigurableWebBindingInitizlizer

Spring MVC 使用一个WebBindingInitializer 来为一个特定的请求初始化一个WebDataBinder,如果你创建了一个ConfigurableWebBindingInitizlizer Bean ,Spring boot 会自动配置并使用

### Temmlate Engines

和REST web 服务一样，你也可以使用spring MVC 来提供动态html 内容服务。

- 如果你使用默认的配置使用模板那么你的默认模板位置是在`src/main/resources/template`

## Chapter 28 Security

如果Spring Security 在路径中，那么wen app 默认开启了安全功能。Spring Boot 依赖于Spring Security 的内容协商策略来决定决定使用httpbasic 或者formLogin。你可以在你希望的设置中添加@EnableGlobalMethodSecurity.额外的信息可以在Spring Security Reference Guide 中查看。

默认的UserDetailsService 有一个单独的用户。这个用户的用户名是user，它的密码是随机的，他会在app启动的时候输出，是一个INFO级别的日志
Note：
如果你调整的你的日志配置，确认org.springframework.boot.autoconfigure.security 的日志级别设置为INFO级别，否则，默认的随机密码将不能输出

你可以更改用户名和密码通过设置spring.security.user.name 和 spring.security.user.password

在web 程序中你的得到的默认特性是：

- 一个内存存储的 UserDetailService bean 和一个 代码随机生成的单用户

- form-base login 或者http Basic security 作用于全部的应用
- 一个用于发布身份验证事件的DefaultAuthenticationEventPublisher

你可以通过编写一个bean定义一个不同的验证事件发布者

### MVC Security

默认的安全配置在SecurityAutoConfiguration 和 UserDetailsServiceAutoConfiguration 中实现。SecurityAutoConfiguration 为web安全和UserDetailsServiceAutoConfiguration 配置授权，这在非web项目中也有用。为了完全关闭默认的安全配置，你可以编写一个bean继承WebSecurityConfigurerAdapter(这样做不能禁止UserDetailsServicecongfiguration 或者 执行器安全)
为了关闭UserDetailsService 配置，可以编写一个bean继承自UserDetailsService，AuthenticationProvider或者Authenticationmanager。有一些常用的安全app在spring boot simple 供你查看。

访问规则可以通过添加一个自定义的WebSecrityConfigurerAdapter来覆盖。Spring Boot 提供了一些方便的方法来覆盖执行器节点和静态资源。EndpointRequest 可以被用来创建一个基于management.endpoints.web.base-path属性的RequestMatcher。PathRequest 可以被用来在常用的资源位置创建一个RequestMatcher

### OAuth2.0

OAuth2 是一个被广泛使用的授权框架，spring 提供了对于它的支持

#### Client

如果你已经将spring-security-oauth2-client 加入到你的Java路径之中，你可以利用一些自动配置来轻松的配置一个OAuth2客户端。这个配置使用OAuthClientProperties下的属性。
你可以注册多个OAuth2 客户端和提供者通过使用spring.security.oauth2.client 前缀，如下所示：

```xml
spring.security.oauth2.client.registration.my-client-1.client-id=abcd
spring.security.oauth2.client.registration.my-client-1.client-secret=password
spring.security.oauth2.client.registration.my-client-1.client-name=Client for user scope
spring.security.oauth2.client.registration.my-client-1.provider=my-oauth-provider
spring.security.oauth2.client.registration.my-client-1.scope=user
spring.security.oauth2.client.registration.my-client-1.redirect-uri-template=http://my-redirect-uri.com
spring.security.oauth2.client.registration.my-client-1.client-authentication-method=basic
spring.security.oauth2.client.registration.my-client-1.authorization-grant-type=authorization_code

spring.security.oauth2.client.registration.my-client-2.client-id=abcd
spring.security.oauth2.client.registration.my-client-2.client-secret=password
spring.security.oauth2.client.registration.my-client-2.client-name=Client for email scope
spring.security.oauth2.client.registration.my-client-2.provider=my-oauth-provider
spring.security.oauth2.client.registration.my-client-2.scope=email
spring.security.oauth2.client.registration.my-client-2.redirect-uri-template=http://my-redirect-uri.com
spring.security.oauth2.client.registration.my-client-2.client-authentication-method=basic
spring.security.oauth2.client.registration.my-client-2.authorization-grant-type=authorization_code

spring.security.oauth2.client.provider.my-oauth-provider.authorization-uri=http://my-auth-server/oauth/authorize
spring.security.oauth2.client.provider.my-oauth-provider.token-uri=http://my-auth-server/oauth/token
spring.security.oauth2.client.provider.my-oauth-provider.user-info-uri=http://my-auth-server/userinfo
spring.security.oauth2.client.provider.my-oauth-provider.jwk-set-uri=http://my-auth-server/token_keys
spring.security.oauth2.client.provider.my-oauth-provider.user-name-attribute=name


```

在默认情况下，Spring Security's OAuth2LoginAuthenticationFilter 仅仅处理以下形式的URI /login/oauth2/code/*。如果你想要定制新的模式，你需要提供新的配置。比如你可以添加你自己的WebSecurityConfigAdapter 类似于如下：

```Java
public class OAuth2LoginSecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
			.anyRequest().authenticated()
			.and()
			.oauth2Login()
			.redirectionEndpoint()
			.baseUri("/custom-callback");
			}
}
```

对于常见的OAuth2和OpenID提供者，包括Google，GitHub，Facebook，以及Okta，我们提供了一系列的提供者定义
如果你不需要定制这些提供者，你可以将这些值设置成推断设置这些提供者属性的默认值。同样如果你的客户端ID匹配默认支持的提供器，spring boot 同样会这样推断。
换句话说，下面的两个配置使用了Google的提供者：

``` xml
spring.security.oauth2.client.registration.my-client.client-id=abcd
spring.security.oauth2.client.registration.my-client.client-secret=password
spring.security.oauth2.client.registration.my-client.provider=google

spring.security.oauth2.client.registration.google.client-id=abcd
spring.security.oauth2.client.registration.google.client-secret=password

```

#### Server

当前，Spring Security没有为实现一个OAuth2授权服务或者资源服务提供支持。然而这个功能可以Spring Security OAuth 项目中找到，这个项目最终将被Spring Security 完全支持。到那时候，你就可以使用spring-security-oauth2-autoconfigure模块快速搭建一个OAuth2认证服务器。

#### Actuator Security

为了安全起见，除了/health 和 /info 其他所有的执行器都默认停用。management.endpoints.web.exposure.include属性可以被用来激活actoators
如果Spring Security 在classpath 中并且没有其他的WebSecurityConfigAdapter 存在，执行器被被Spring Boot 自动配置给保护着。如果你定义了一个WebSecurityConfigurerAdapter,Spring Boot 自动配置会退化你将拥有对于执行器权限规则的完全控制权。

- 注意
	在设置managent.endpoints.web.exposure.include 之前，确认暴露在外的执行器不包含敏感的信息或者被防火墙或者类似于spring Security 的东西保护着。

#### Cross Site Request Forgery Protection

因为Spring Boot 是依赖于Spring Security 的设置，CSRF 保护默认开启。这意味着在使用默认配置的时候当需要POST PUT 或者DELETE的执行器会得到一个403错误。

- 注意

我们推荐仅仅在你创建一个没有浏览器的客户端服务器的时候才能完全关闭CSRF保护。

## Chapter 29 Working with SQL DataBase

Spring framework 提供了和SQL database 交货的拓展支持，从使用直接的jdbc 链接的JdbcTemplate 到完全的orm技术比如说hibernate。spring data 提供了一个额外层级的功能:直接继承接口实现仓库按照公约根据你的方法名生成查询方法。

### Configure a DataSource

Java的javax.sql.DataSource 接口提供了一个作用于数据库连接的标准方法。传统意义上，一个数据库源使用URL和一些凭证来建立数据库连接。

- 提示

在how to 章节查看跟多的高级例子，典型的通过配置来获取数据源的完全控制权

### Embedded Database Support 内嵌数据库支持

我们常常使用内嵌式的内存数据库来进行方便的开发。显然，内存数据库不提供持久化存储。当你的application 开始或结束时准备丢弃数据你需要迁移你的数据库。
Spring Boot 就可以自动配置内嵌的H2,HSQL和Derby 数据库。你不需要提供任何的链接URL。你只需要包含你先要使用的数据库的构建依赖。

- 注意

如果你在你的测试中使用这个特性，你会注意到相同的数据会被你的真个测试用例重复使用。如果你想确定每一个测试用例使用单独的内嵌数据库，你需要将spring.datasource.generate-unique-name 设置为true

以下是一个例子：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
	<groupId>org.hsqldb</groupId>
	<artifactId>hsqldb</artifactId>
	<scope>runtime</scope>
</dependency>
```

### Connection to a Production Databas

生产数据库连接可以通过使用数据池自动配置。Spring Boot 使用如下算法选择一个特定的实现

1. 出于性能和并发的考虑我们更加倾向于使用HikariCP，如果可以使用HikariCP我们就选择他
2. 否则如果Tomcat 数据库连接池可用，我们就选择Tomcat 数据库连接池
3. 如果以上两个都不可选而且Commons DBCP2 可选那我们使用它

如果你使用 spring-boot-starter-jdbc 或者spring-boot-starter-jpa 你就自动获得HikariCP的依赖。

数据库配置被spring.datasource.* 在那个的额外属性控制。举个例子，你可以在application.properties 中定义如下内容 

```xml
spring.datasource.url=jdbc:mysql://localhost/tes
spring.datasource.username=dbuser
spring.datasource.password=dbpas
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

- 注意：你至少需要配置好spring.data.datasource.url 属性，否则，spring boot 会尝试自动使用一个内存数据库
- 提示： 你常常不需要指定driver-class-name,因为spring boot 会从url 中推断出
- 注意：因为一个数据库连接池已经被创建了，我们需要能够验证数据库驱动应该可用。换句话说，如果你设置了spring.datasource.driver-class-name=com.mysql.jdbc.Driver,那么这个包就应该已经加载进来

查看DataSourceProperties获取更多的支持选项。通过使用他们指定的前缀微调数据库连接池配置（spring.datasource.hikari.*,spring.datasource.tomcat.*,spring.datasource.dbcp2.*）参考数据库连接池实现文档，你可以获得很多有用的细节。
如果你使用Tomcat 数据库连接池，你可以定制许多额外的设置，如下面所示：
spring.datasource.tomcat.max-wait=10000
spring.datasource.tomcat.max-active=50
spring.datasource.tomcat.text-on-borrow=true

### Connection to a JNDI DataSource

如果将你的Spring Boot 程序部署到了应用服务器，你可能想要通过使用JNDI应用服务内建的属性和授权来配置和管理你的数据源。
spring.datasource.jndi-name 属性可以被用来替代spring.datasource.url,spring.datasource.username和spring.datasource.passwords属性来访问数据库通过一个指定的JNDI地址。举个例子，下面的章节在applicattionproperties

```xml
spring.datasource.jndi-name=java:jboss/datasource/customers
```

### Using JdbcTemplate

Spring 的JdbcTemplate 和NamedParameterJdbcTemplate 这两个类已经被自动配置好了，可以通过@Autowire注解自动注入到你的bean里面如下所示：

```Java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Comonent;

@Component
public class MyBean{
	private final JdbcTemplate;

	@Autowire
	public MyBean(JdbcTemplate jdbcTemplate){
		this.jdbcTemplate  = jdbcTemplate;
	}
	// ...
}
```

你可以通过spring.jdbc.template.* 属性为template 定制一些属性，下面是一个例子：

``` java
spring.jdbc.template.max-rows=500
```

- 注意：NameParameterJdbcTemplate 在背后重复使用JdbcTemplate。如果不止一个JdbcTemplate被定义而且没有主要的候选者存在，那么NameParameterJdbcTemplate不会被自动配置。

### JPA and "Spring Data"

JPA(Java Persistence API) 是一个让你的对象匹配关系型数据库的标准技术。spring-boot-starter-data-jpa 依赖提供了一个快速开始的方式，者提供了以下的关键依赖：

- Hibernate :最流行的JPA实现之一
- Spring Data JPA:使得实现JPA-base 仓库更加的简单
- Spring ORMs：来之Spring Framework 的核心ORM 支持

- 贴士：我们在这里不会深入探讨JPA 或者Spring Data ，你可以跟着“Accessing Data With JPA”然后阅读Spring Data JPA 和Hbernate

### Entity Classes

传统做法是，JPA 实体类在一个persistence.xml 文件中指定。自从有了Spring boot ，这个文件便不再需要，现在使用实体扫描来代替它。默认的，所有的在你配置文件下的类会被搜索。
任何一个被@Entity，@Embeddable或者@MappedSuperclass 注解的类都会被识别为实体，下面展示一个典型的实体类：

```Java
import java.io.Serializable;
import javax.persistence.*;

@Entity
public class City implements Serializable{
	@Id
	@GenerateValue
	private Long id;

	@Column(nullable=false)
	private String name;

	@Column(nullable = false)
	private String state;

	protected City(){

	}

	public City(String name,String state){
		this.name = name;
		this.state = state;
	}

	public String getName(){
		return this.name;
	}

	public String getState(){
		return this.state;
	}
}
```

- 提示：你可以定义实体类的扫描位置通过使用@EntityScan注解

### Spring Data JPA Repositories

Spring Data JPA 仓库是一个你可以定义范文数据的接口。JPA 查询根据你的方法名自动创建。举个例子，一个CityRepository 接口可能顶一个一个`findAllByState(String state)`方法来查询所在州的所有城市，在复杂查询中，你就可以在方法上使用Spring Data 查询注解

Spring Data 仓库 通常继承自Repository 或者CurdRepository 接口。如果你使用自动配置那么 会在包含了你的主配置的包中搜索 。
下面展示了一个典型的 reponsitory 定义

```Java
package com.example.myapp.domain;

import org.springframework.data.domain.*;
import org.springframework.data.repository.*;

public interface CityRepository extends Repository<City,Long>{
	Page<City> finaAll(Pageable pageable);

	City findByNameAndCountryAllIgnoringCase(String name,String country);
}
```

- 小贴士：我们现在大致了解了Spring Data JPA 的皮毛，如果想要完整的细节 查看Spring Data JPA 的参考文档。

### Creating and Dropping JPA Database

默认情况下，JPA 数据库只有在及使用内嵌式数据库的时候会自动创建。你可以使用spring.jpa.* 属性明确的配置JPA。举个例子，你可以在application.properties 添加下面的行来创建一个数据库
`spring.jpa.hibernate.ddl-auto=create-drop`

- 注意： hibernate 有自己的属性叫做 hibernate.hbm2ddl.auto。你可以设置hibernate 属性，通过使用`spring.jpa.properties`下面一行显示了一个设置了jpa 属性的hibernate

spring.jpa.properties.hibernate.globally_quoted_identifiers=true
默认情况下，ddl 执行器推迟到spring上下文加载之后。同样有一个 `spring.jpa.generate` 标志，但是实际上他没有被用到hibernate的自动配置中，以为ddl-auto 更加的细致。

### Open EntityManager in View 

如果你在跑一个web 应用，Spring Boot 默认加载 OpenEntityManagerInViewInterceptor 来适配 open
 EntityManager in View 模式，来统一web view 的懒加载。如果你不想要这个行为那么，你需要将 `spring,.jpa.open-in-view` 属性设置为false 在你的application.properties 文件中。

## Chapter 30. Working with NoSQL Technologies

 Spring Data 提供了额外的项目帮助你使用一系列的NoSql 技术，包含了MongoDB,Neo4J,Elasticsearch,Solr,Redis,Gemfire,Cassandra,Couchbase和LDAP。spring Boot 为redis mongodb Neo4J Elasticsearch,Solr,Cassandra,Couchbase和 LDAP提供了自动配置。你可以使用其他的项目，但是你必须要亲自配置他们，在文档中查看具体的细节。

### Redies

Redies 是一个缓存，消息代理服务器，一个富有特性的键值存储器。Spring Boot 提供了基本的自动配置 如果需要使用redis 那么添加依赖spring-boot-starter-data-redis，

### MongoDB

需要引入spring-boot-starter-data-mongodb 和 spring-boot-starter-data-mongodb-reactive
引入MongoDB时使用的配置是

```xml
ssspring.data.mongodb.uri=mongodb://user:secret@mongol.example.com:12345,mongo2.example.com:23456/test

spring.data.mongodb.host=mongoserver
spring.data.mongodb.port=27017
```

## Chapter 31. Caching 缓存

- spring boot 中如何使用缓存

通过使用@Cacheable 注解可以使用缓存

- spring boot 中使用缓存时的默认配置

在默认情况下spring boot 提供了一个简单的concurrent maps 作为缓存提供者。

- 缓存机制

当你需要缓存是（在注解中使用了缓存注解）会在缓存提供器里面为你创建一个缓存，我们不推荐在正式的产品中使用默认的缓存提供者，但是他可以让你开始和了解这个特性。当你决定要使用那个缓存提供者的时候，请你阅读一下相关的文档以确保你知道怎么在你的应用中配置这个缓存提供者。几乎所有的提供者都要求你明确的配置每一个你在应用中使用的缓存。一些缓存提供者给你提供了一个通过spring.cache.cache-names属性来自定义你的缓存。

### Supportted Cache Providers 支持的缓存提供器

- 通过什么定义缓存提供者

缓存抽象并不提供实际的存储，它依赖于抽象接口org.springframework.cache.Cache 和 org.springframework.cache.CacheManager 的实现。

- 如果你没有关于缓存服务器的定义spring boot 会有哪些行为：

如果你没有定义一个类实现自CacheManager 或者CacheResolvever,那么spring boot 会自动按以下的顺序使用提供者：

1. Generic
2. JCache
3. EhCachev
4. Hazelcast
5. Infinispan
6. Couchbase
7. Redisv
8. Caffeine
 simple

- 如何在项目中添加相关的支持

在依赖中添加 `spring-boot-starter-cache`依赖来快熟的添加缓存依赖。这个starter依赖引入了`sping-context-support`如果你是手动添加的那么记得同时引入`spring-context-support`这样才能引入JCache，EHCache 2.X 或者Guava 支持

- 如果CacheManager 在spring-boot 中自动配置了，你可以进一步通过CacheManagerCustomizer来定制下面是一个例子

```Java
@Bean
public CacheManagerCustomizer<ConcurrentMapCachemanager> cacheManagerCustomizer(){
	return new CacheManagerCustomizer<ConcurrentMapCachemanager>(){
		@Override
		public void customize(ConcurrentMapCachemanager cacheManager){
		
			cacheManager.setAllowNullValues(false);
		}
	}
}
```

- 注意：如果你使用了自己的配置或者使用了一个不同的缓存提供者那么，这个方法不会被调用，你可以创建很多的自定义配置，你可以使用@order来决定他们的顺序
- 各种提供者的介绍
	- Generic
	如果上下文定义了至少一个org.springframework.cache.Cache 那么通用缓存提供者将被使用。一个包含了所有这种类型的bean的Cachemanager将被创建
	- JCache 这个缓存提供者通过类路径中存在的javax.cache.spi.CachingProvider引导，JCacheCacheManager是由`spring-boot-starter-cache`提供。很多符合条件的仓库可以获得，spring boot 提供了Ehcache 3，Hazelcast和Indinispan的依赖管理，任何另外的符合条件的仓库可以被添加。可能会有这样的情况存在：同时存在不值个的提供者，在这种情况下必须要明确指定提供者。即使是JSR-107标准也没有强制规定存放配置文件的标准位置，spring boot 尽可能地提供设置cache 的配置：如下所示

```Java
spring.cache.jcache.provider=com.acme.MyCachingProvider
spring.cache.jcache.config=classpath:acme.xml
```

- 注意，如果一个缓存依赖提供了原生实现和JSR-107 标准支持，那么spring boot 更加倾向于使用JSR-107 因为这样相同的可以可以在切换到另一个不同的JSR-107 实现的情况下生效
- 提示：spring boot 有对于Hazelcast 的通用支持，如果一个HazelcastInstance 可以得到，塔尖会被自动重用，除非`spring.cache.jcache.config`属性被指定。
- 两种自定义以下javax.cacher.cachemanager:
	- 缓存可以通过设置spring.cache.cacheManager属性在开始被创建，如果一个自定义

## Chapter 32.Messaging 消息队列

spring 框架提供了广泛的和消息系统集成的消息系统，从简单的比如JMS API 到完整的可以接受同步信息的基础设施。Spring AMQP 为高级消息队列协议提供了一个相似的特性集合。Spring Boot 也为RabbitTemplate 和 RabbitMQ 提供了自动配置选项。Spring WebSocket 对STOMP 消息提供了原生的支持，Spring‘ Boot 也通过starters 和一些auto-configuration 提供了对它的支持。Spring Boot 也支持 Apache kafka。

### JMS

javax.jms.ConnectionFactory 接口提供了一个用于为配合JMS中间件创建javax.jms.Connection 的标准方法。
