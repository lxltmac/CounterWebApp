# CounterWebApp
helloworld web app with maven and springmvc


原文出处:[how-to-create-a-web-application-project-with-maven][1]

[完整代码][3]

以后作为最基础的java web项目模板发展

## 从Maven模板创建web项目

使用Maven的`maven-archetype-webapp`模板可以快速创建java web application. 命令行进入想要保存项目的目录下,执行以下命令:

```
mvn archetype:generate -DgroupId={project-packaging}
  -DartifactId={project-name}
  -DarchetypeArtifactId=maven-archetype-webapp
  -DinteractiveMode=false

// 如下面的例子
mvn archetype:generate -DgroupId=com.mkyong
  -DartifactId=CounterWebApp
  -DarchetypeArtifactId=maven-archetype-webapp
  -DinteractiveMode=false
```

以上命令执行完成后创建了名为`CounterWebApp`的web项目, 具有了[基于Maven的标准web目录结构][2]如下:

```
.
|____CounterWebApp
| |____pom.xml
| |____src
| | |____main
| | | |____resources
| | | |____webapp
| | | | |____index.jsp
| | | | |____WEB-INF
| | | | | |____web.xml
```

## 添加Eclipse支持

1. Maven可以生成Eclipse项目所需的配置文件, 终端中执行:

  ```
  mvn eclipse:eclipse -Dwtpversion=2.0
  ```

  `-Dwtpversion=2.0`告诉Maven将项目转化为Eclipse web项目(WAR)

2. Eclipse菜单中依次选择: File -> Import -> General -> Existing Project into workspace


## 修改POM

1. 添加Spring等框架的支持
2. 添加项目配置插件

完整pom.xml如下

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
  http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mkyong</groupId>
  <artifactId>CounterWebApp</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>CounterWebApp Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <properties>
    <jdk.version>1.7</jdk.version>
    <spring.version>4.1.1.RELEASE</spring.version>
    <jstl.version>1.2</jstl.version>
    <junit.version>4.11</junit.version>
    <logback.version>1.0.13</logback.version>
    <jcl-over-slf4j.version>1.7.5</jcl-over-slf4j.version>
  </properties>

  <dependencies>

    <!-- Unit Test -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>${junit.version}</version>
    </dependency>

    <!-- Spring Core -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>${spring.version}</version>
      <exclusions>
        <exclusion>
          <groupId>commons-logging</groupId>
          <artifactId>commons-logging</artifactId>
        </exclusion>
      </exclusions>
    </dependency>

    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>jcl-over-slf4j</artifactId>
      <version>${jcl-over-slf4j.version}</version>
    </dependency>

    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>${logback.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <!-- jstl -->
    <dependency>
      <groupId>jstl</groupId>
      <artifactId>jstl</artifactId>
      <version>${jstl.version}</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>CounterWebApp</finalName>

    <plugins>
        <!-- Eclipse project -->
      <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-eclipse-plugin</artifactId>
      <version>2.9</version>
      <configuration>
              <!-- Always download and attach dependencies source code -->
        <downloadSources>true</downloadSources>
        <downloadJavadocs>false</downloadJavadocs>
        <!-- Avoid type mvn eclipse:eclipse -Dwtpversion=2.0 -->
        <wtpversion>2.0</wtpversion>
      </configuration>
      </plugin>

      <!-- Set JDK Compiler Level -->
      <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>${jdk.version}</source>
        <target>${jdk.version}</target>
      </configuration>
      </plugin>

      <!-- For Maven Tomcat Plugin -->
      <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
      <configuration>
        <path>/</path>
      </configuration>
      </plugin>

    </plugins>

  </build>
</project>
```

web.xml 修改为JSP2.2 / Servlet 3.0使用的XSD

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
      version="3.0">


  <display-name>Counter Web Application</display-name>

  <servlet>
    <servlet-name>mvc-dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>mvc-dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/mvc-dispatcher-servlet.xml</param-value>
  </context-param>

  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

</web-app>

```

## 增加源代码

创建如下文件:

```
.
|____pom.xml
|____src
| |____main
| | |____java
| | | |____com
| | | | |____mkyong
| | | | | |____controller
| | | | | | |____BaseController.java
| | |____resources
| | | |____logback.xml
| | |____webapp
| | | |____WEB-INF
| | | | |____mvc-dispatcher-servlet.xml
| | | | |____pages
| | | | | |____index.jsp
| | | | |____web.xml
```

点击[完整源码][3]可下载

## 部署运行

有三种方法可以部署项目

1. 执行`mvn package`, 生成war文件然后部署到web 容器,部署阶段推荐
2. 配置eclipse部署到web 容器
3. 前面添加了maven tomcat plugin, 可以直接运行`mvn tomcat7:run`启动一个本项目的tomcat,查看效果,非常方便,开发阶段推荐


## spring mvc基础

本部分主要来自[spring mvc官方文档][4]

### Spring MVC简介

Spring MVC 框架围绕`DispatcherServlet`进行设计, 用于将请求转发到处理程序, 可以灵活配置处理程序,视图解析,locale, 时区,主题以及文件上传.

默认请求处理程序基于`@Controller`和`@RequestMapping`注解, 提供了一系列灵活的处理方法. 在Spring3.0中`@Controller`通过`@PathVariable`支持RESTful网站的创建.

Spring的视图解析非常灵活`Controller`通常负责准备Model和选择视图, 也可以直接发送结果.

### DispatcherServlet

`DispathcerServlet`是一个`Servlet`, 通过在web.xml中进行配置.

```
<web-app>
    <servlet>
        <servlet-name>example</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>example</servlet-name>
        <url-pattern>/example/*</url-pattern>
    </servlet-mapping>

</web-app>
```

在以上的配置中, 所有以`/example`开头的请求都会由名为`example`的`DispatcherServlet`实例处理

配置好DispatcherServlet之后, 需要配置`WebApplicationContext`, 每一个DispatcherServlet都有它自己的WebApplicationContext, 继承root WebApplicationContext定义的所有bean. 这些继承bean可以被servlet scope的笨啊所覆盖.

当`DispatcherServlet`实例化之后, Spring MVC会在`WEB-INF`目录下寻找`[servlet-name]-servlet.xml`配置文件,并创建里面定义的bean用于覆盖全局bean. 如前面的例子, 需要定义`/WEB-INF/example-servlet.xml`文件,其中包含所需bean.

可以在只有一个DispatcherServlet的环境中只使用一个root context, 通过将`contextConfigLocation`设置为空:

```
<web-app>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```

### WebApplicationContext中的特殊Bean Type

DispatcherServlet使用特殊bean处理请求并渲染视图.可以在WebApplicationContext中配置这些bean. 以下是Spring MVC自动使用的bean

|---------------------------|---------------------------------------------|
| HandlerMapping | 将请求映射到处理程序 |
| HandlerAdapter |

太枯燥,回头再写

### DispatcherServlet默认配置

`DispatcherServlet.properties`包含DispatcherServlet的默认bean配置, 配置自定义bean会替换掉默认bean.


### DispatcherServlet处理流程

请求到达之后Spring MVC按照以下逻辑操作

1. 搜索`WebApplicationContext`并且绑定到`DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE`, 在整个请求流程中控制器和其他元素都可以访问.
2. locale解析器绑定到request,以支持locale的解析
3. theme解析器绑定到request,以支持主题选择
4. 如果制定了multipart文件解析器, 请求将会检测multiparts输入, 如果检测到这样的数据, 请求将被包裹到`MultipartHttpServletRequest`中已支持后续处理, 如文件上传.
5. 搜索对应的handler. 如果找到了, 顺序执行handler相关的逻辑链(预处理, 后处理, 控制器)用于准备model或者render
6. 如果handler返回了model, 将会执行视图渲染. 如果没有model返回, 视图将不会渲染.

在处理过程中的异常将由`WebApplicationContext`中配置的异常解析器进行处理. 可以通过异常解析器自定义异常处理逻辑.

`DispatcherServlet`也可以通过Servlet API返回**last-modification-date**.DispatcherServlet会查看handler是否实现`LastModified`接口. 如果实现了, `long getLastModified(request)`方法的返回值将返回给客户端.

可以通过web.xml使用`init-param`自定义DispatcherServlet实例. 以下是支持的参数:

- `ContextClass`:
- `contextConfigLocation`:
- `namespace`:

## 实现Controller

controller接收用户输入并且准备反馈model, 使用`@RequestMapping`, `@RequestParam`,`@ModelAttribute`这样的注解实现Controller不需要继承特定基类或者实现特定接口:

```
@Controller
public class HelloWorldController {

  @RequestMapping("/helloWorld")
  public String helloWorld(Model model) {
    model.addAttribute("message", "Hello World!");
    return "helloWorld";
  }
}
```

前面的方法接收一个`Model`作为参数并且返回`String`表示视图名, 也可以使用其他参数和返回值.

### 使用@Controller定义控制器

`@Controller`注解表示这个类扮演控制器的角色. Spring不强制继承任何基类.


dispatcher搜寻带有这个注解的泪并且寻找它的`@RequestMapping`注解函数.


### 使用`@RequestMapping`映射请求

使用`@RequestMapping`将类似`/appointments`这样的URL指定特定的类或者方法进行处理, 通常来说类级别的注解将一个特定请求路径映射到**form controller**, 然后使用方法级别的注解指定不同的HTTP方法相应(如GET, POST), or an HTTP request parameter condition.

```
@Controller
@RequestMapping("/appointments")
public class AppointmentsController {

  private final AppointmentBook appointmentBook;

  @Autowired
  public AppointmentsController(AppointmentBook appointmentBook) {
    this.appointmentBook = appointmentBook;
  }

  @RequestMapping(method = RequestMethod.GET)
  public Map<String, Appointment> get() {
    return appointmentBook.getAppointmentsForToday();
  }

  @RequestMapping(value = "/{day}", method = RequestMethod.GET)
  public Map<String, Appointment> getForDay(
      @PathVariable @DateTimeFormat(iso=ISO.DATE) Date day,
      Model model) {

    return appointmentBook.getAppointmentsForDay(day);
  }

  @RequestMapping(value = "/new", method = RequestMethod.GET)
  public AppointmentForm getNewForm() {
    return new AppointmentForm();
  }

  @RequestMapping(method = RequestMethod.POST)
  public String add(@Valid AppointmentForm appointment,
      BindingResult result) {

    if (result.hasErrors()) {
      return "appointments/new";
    }
    appointmentBook.addAppointment(appointment);
    return "redirect:/appointments";
  }
}
```

- 类级别的`@RequestMapping`表示这个控制器所有方法处理的请求都相对于`/appointments`路径
- `get()`方法也有一个`@RequestMapping`指定它只处理GET请求, 也就是说HTTP GET请求`/appointments`会调用此方法处理
- `add()`也具有同样的设置.
- `getNewForm()`同时限制了HTTP方法和路径: 只有GET请求`/appointments/new`会调用此方法处理
- `getForDay()`方法展示了**URI template**的用法(下一章介绍)
-



[4]: http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html
[3]: https://github.com/qiu-deqing/CounterWebApp
[2]: http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html
[1]: http://www.mkyong.com/maven/how-to-create-a-web-application-project-with-maven/
