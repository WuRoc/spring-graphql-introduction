---
description: Spring Quickstart Guide
---

# Spring快速入门指南

### [https://spring.io/quickstart](https://spring.io/quickstart)

### 你会建立什么

您将构建一个经典的“ Hello World！”。任何浏览器都可以连接到的端点。您甚至可以告诉它您的名字，它将以更友好的方式响应。

### 你需要什么

集成开发人员环境（IDE）

热门选择包括 [IntelliJ IDEA](https://www.jetbrains.com/idea/)，   [Spring Tools](https://spring.io/tools), [Visual Studio Code](https://code.visualstudio.com/docs/languages/java), or [Eclipse](https://www.eclipse.org/downloads/packages/), ， 还有很多。

Java™开发套件（JDK）

我们推荐 [采用OpenJDK](https://adoptopenjdk.net/) 版本8或版本11。

### 步骤1：开始一个新的Spring Boot项目

使用 [start.spring.io](https://start.spring.io/)创建一个“web”项目。在“Dependencies”对话框中，搜索并添加“web”dependency，如屏幕截图所示。点击“Generate”按钮，下载压缩文件，然后将其解压缩到计算机上的文件夹中。

Spring Boot的当前版本会定期更改。只需选择最新版本（而不选择快照）。

 在[计算机系统中](https://en.wikipedia.org/wiki/Computer)，一个**快照**是[状态](https://en.wikipedia.org/wiki/State_%28computer_science%29)的系统的在时间上的特定点。这个名词是与[摄影中](https://en.wikipedia.org/wiki/Snapshot_%28photography%29)的类比创造的。它可以引用系统状态的[实际副本](https://en.wikipedia.org/wiki/System_image)，也可以引用某些系统提供的功能。

创建的项目 [start.spring.io](https://start.spring.io/) 包含   [Spring Boot](https://spring.io/projects/spring-boot)，这是一个框架，可让Spring准备在您的应用程序中工作，而无需太多代码或配置。Spring Boot是启动Spring项目的最快，最受欢迎的方式。

### 步骤2：添加您的代码

在您的IDE中打开项目，然后在`DemoApplication.java`文件`src/main/java/com/example/demo`夹中找到文件。现在，通过添加以下代码中所示的额外方法和注释来更改文件的内容。您可以复制并粘贴代码，也可以只键入代码。

```text
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
//该@RestController注解告诉Spring，这个代码描述应该可在网上的端点。
@RestController
public class DemoApplication {

public static void main(String[] args) {
SpringApplication.run(DemoApplication.class, args);
}

@GetMapping("/hello")
 
public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
return String.format("Hello %s!", name);
}
}
```

😄 这是在Spring Boot中创建简单的“ Hello World” Web服务所需的全部代码。

`hello()`我们添加的方法旨在采用名为的String参数`name`，然后将此参数与`"Hello"`代码中的单词组合。这意味着，如果您`“Amy”`在请求中将姓名设置为，则响应为ting`“Hello Amy”`。

该`@RestController`注解告诉Spring，这个代码描述应该可在网上的端口访问。该`@GetMapping("/hello")`告诉Spring使用我们的`hello()`方法来回答这个问题被发送到请求[~~`http://localhost:8080/hello`~~](http://localhost:8080/hello)的地址。最后，`@RequestParam`告诉Spring`name`在请求中期望一个值，但是如果不存在，默认情况下它将使用单词“ World”。

```text
value = "name" 与http://localhost:8080/hello?name=Amy里的name对应。
如果改成value = "nam"对应 http://localhost:8080/hello?nam=Amy。都可以成功。
```

### 步骤3：尝试

让我们构建并运行该程序。打开命令行（或终端），然后导航到您拥有项目文件的文件夹。我们可以通过发出以下命令来构建和运行该应用程序：

**MacOS / Linux：**  


```text
./mvnw spring-boot:run
```

Windows  


```text
mvnw spring-boot:run
```

您应该看到一些看起来非常类似于此的输出：

![](.gitbook/assets/image%20%281%29.png)

最后两行告诉我们Spring已经开始。Spring Boot的嵌入式Apache Tomcat服务器充当Web服务器，并在`localhost`port上侦听请求`8080`。打开浏览器，然后在顶部的地址栏中输入[http://localhost:8080/hello](http://localhost:8080/hello)。您应该得到一个很好的友好响应，例如：

 ![](https://spring.io/images/pop-quiz-cc3a47c446dd0c2ac70b9a1db42459e0.svg) 

![](.gitbook/assets/image%20%282%29.png)

### Pop 测验

如果添加`?name=Amy`到URL的末尾，应该怎么办？

![](.gitbook/assets/image%20%283%29.png)

### 接下来，尝试这些热门指南

您已经了解了Spring可以多么简单，但是它也非常灵活。Spring有成千上万的功能，我们有许多指南可以指导您完成最流行的选择。为什么不继续学习并尝试这些其他指南之一？

[构建一个RESTful Web服务通过在Spring中创建RESTful JSON Web服务来继续学习](https://spring.io/guides/gs/rest-service)[![](https://spring.io/images/icon-guides-start.svg)](https://spring.io/guides/gs/consuming-rest)

[消费RESTful Web服务了解如何使用Spring的RestTemplate检索网页数据。](https://spring.io/guides/gs/consuming-rest)[![](https://spring.io/images/icon-guides-start.svg)](https://spring.io/guides/gs/accessing-data-jpa)

[使用JPA访问数据了解如何使用Spring Data JPA处理JPA数据持久性。](https://spring.io/guides/gs/accessing-data-jpa)

