# CounterWebApp
helloworld web app with maven and springmvc


原文出处:[how-to-create-a-web-application-project-with-maven][1]

[完整代码][3]

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

[3]: https://github.com/qiu-deqing/CounterWebApp
[2]: http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html
[1]: http://www.mkyong.com/maven/how-to-create-a-web-application-project-with-maven/
