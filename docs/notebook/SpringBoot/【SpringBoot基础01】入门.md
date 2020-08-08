## SpringBoot简介

> 官网：https://spring.io/projects/spring-boot

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。

- 快速的创建独立运行的Spring项目以及与主流框架集成
- starters自动依赖与版本控制
- 嵌入式Servlet容器，应用无需打包成War包
- 大量的自动配置，简化开发，也可以修改默认值
- 无需配置XML，无代码生成，开箱即用
- 准生产环境的运行时应用监控
- 与云计算的天然集成

## 微服务

*Martin Fowler* 最先发表了一篇微服务的文章：https://martinfowler.com/microservices/

微服务：架构风格（服务微化）。一个应用应该是一组小型服务，可以通过HTTP的方式进行互通

单体应用：ALL IN ONE。牵一发而动全身

微服务：每个功能元素最终都是一个可以独立替换和升级的软件单元

## 环境

- Java：`java -version`：java version "1.8.0_144"
- Maven：`mvn -v`：Apache Maven 3.5.3
- IntelliJ IDEA 2018.3.1
- SpringBoot 2.3.1 RELEASE

### Maven 设置（未设置）

在 Maven 的 settings.xml 配置文件中的 profiles 标签添加设置：

```xml
<!-- 配置JDK版本 -->
<profile>    
    <id>jdk18</id>    
    <activation>    
        <activeByDefault>true</activeByDefault>    
        <jdk>1.8</jdk>    
    </activation>    
    <properties>    
        <maven.compiler.source>1.8</maven.compiler.source>    
        <maven.compiler.target>1.8</maven.compiler.target>    
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>    
    </properties>     
</profile>
```

### IDEA 设置

配置IDEA的Maven，指定自己下载安装的Maven仓库和配置文件

> 下面是一些常用的快捷键

- `Ctrl+D` 复制当前行至下一行
- `Ctrl+Y` 删除当前行
- `Ctrl+P` 参数提示
- `Ctrl+Alt+V` 自动补齐方法
- `Ctrl+N` 查找类方法
- `Alt+Insert` 构造器、getter/setter toString
- `Ctrl+O` 重载方法提示
- `Alt+Enter` 提示导入类etc
- `Shift+F6` 文件重命名
- `.var` 自动生成变量

## Hello World

### 使用Spring Initializer创建项目

IDEA 中有 Spring Initializer 快速向导来创建 Spring Boot 项目

选择自己需要的组件：例如：Web、MySQL 等

创建好之后，在右下角选择 Enable Auto-Import，这样后面添加依赖会自动导入

默认生成的 Spring Boot 项目：

- 主程序（DemoApplication）已经生成好了，我们只需要完成自己的逻辑
- resources 文件夹
  - static：保存所有的静态文件
  - templates：保存所有的模板页面（SpringBoot使用内嵌的Tomcat，不支持 JSP）。常用的模板引擎有 freemarker、thymeleaf 等
  - application.properties：SpringBoot 项目的配置文件，例如：server.port = 8081

### 添加处理逻辑

```java
@Controller
public class HelloController {

    @RequestMapping("hello")
    @ResponseBody
    public String hello() {
        return "Hello World";
    }

}
```

- @Controller：表明此类是一个控制器类，可以用来接受处理请求
- @RequestMapping("hello")：可以映射到客户端的 hello 请求
- @ResponseBody：将结果封装为一个 JSON 字符串返回

### 运行测试

启动项目（运行主程序的 main 方法即可），控制台打印：

```java
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.1.RELEASE)

2020-06-28 19:47:06.339  INFO 8052 --- [           main] com.example.demo.DemoApplication         : Starting DemoApplication on DESKTOP-989IK61 with PID 8052 (F:\java_eclipse_sts\workspace\SBLearn\target\classes started by Augustu in F:\java_eclipse_sts\workspace\SBLearn)
2020-06-28 19:47:06.339  INFO 8052 --- [           main] com.example.demo.DemoApplication         : No active profile set, falling back to default profiles: default
2020-06-28 19:47:08.323  INFO 8052 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-06-28 19:47:08.370  INFO 8052 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-06-28 19:47:08.370  INFO 8052 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.36]
2020-06-28 19:47:08.542  INFO 8052 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-06-28 19:47:08.542  INFO 8052 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2140 ms
2020-06-28 19:47:08.823  INFO 8052 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-06-28 19:47:09.151  INFO 8052 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-06-28 19:47:09.151  INFO 8052 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 3.86 seconds (JVM running for 6.95)
2020-06-28 19:47:35.517  INFO 8052 --- [nio-8080-exec-2] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2020-06-28 19:47:35.517  INFO 8052 --- [nio-8080-exec-2] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2020-06-28 19:47:35.525  INFO 8052 --- [nio-8080-exec-2] o.s.web.servlet.DispatcherServlet        : Completed initialization in 8 ms

```

浏览器访问 localhost:8080/hello，显示 Hello World 

## Spring Boot深入理解

### POM.xml

我们查看 pom.xml 文件，发现他依赖了一个父项目：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

按住 ctrl ，鼠标点击进入父项目，发现父项目 spring-boot-starter-parent 又依赖了一个父项目：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.1.RELEASE</version>
  </parent>
```

再次进入这个父项目，发现他定义了一些依赖的版本号：

```xml
<properties>
    <activemq.version>5.15.12</activemq.version>
    <antlr2.version>2.7.7</antlr2.version>
    <appengine-sdk.version>1.9.80</appengine-sdk.version>
    <artemis.version>2.12.0</artemis.version>
    <aspectj.version>1.9.5</aspectj.version>
    <assertj.version>3.16.1</assertj.version>
    <atomikos.version>4.0.6</atomikos.version>
    ......
```

所以，SpringBoot 项目会自动导入对应的版本，不需要我们声明，但是 properties 中没有管理的依赖就需要我们声明。

### spring-boot-starter-web

我们创建项目的时候，只导入了 web 依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

我们点进去看他，发现他管理了下面这些依赖：

```xml
<artifactId>spring-boot-starter-json</artifactId>
<artifactId>spring-boot-starter-tomcat</artifactId>
<artifactId>spring-web</artifactId>
<artifactId>spring-webmvc</artifactId>
```

所以 web 依赖主要做了 json 的处理、内嵌 Tomcat 和 SpringMVC 相关功能 

### 主程序入口

一个简单的主程序，便可以启动整个项目：

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
    
}
```

**@SpringBootApplication**：SpringBoot 项目的主配置。进入这个注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

**@SpringBootConfiguration**：表明这是一个 SpringBoot 配置类，这个注解里面其实只有 @Configuration 注解

**@Configuration**：加在配置类上，配置类 --- 配置文件，是容器中的一个组件（@Component）

**@EnableAutoConfiguration**：自动配置，SpringBoot 帮助我们自动配置的内容

```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

@AutoConfigurationPackage：自动配置包 @Import({Registrar.class})：给容器导入组件；将主程序类所在包及其子包里面的所有组件扫描到 Spring 容器

@Import({AutoConfigurationImportSelector.class})：导入组件选择器，将所有需要导入的组件以全类名的方式返回；这些组件将以字符串数组 String[] 添加到容器中

xxxAutoConfiguration 就是给容器中导入这个场景需要的所有组件，并配置 好这些组件

















