---
layout:     post
title:      Tomcat 和 HttpServlet 是如何交互的？ | Java 学习笔记
subtitle:   request和response对象是从哪来的？ | Java 学习笔记
date:       2019-05-11
author:     Shaw
header-img: img/post-bg-altitude.jpg
catalog: true
tags:
---

# Servlet处理方法中的两个参数：request和response对象是从哪来的？
---   

```java
package com.example.simplejavaweb;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@SpringBootApplication
@ServletComponentScan
@WebServlet
public class SimpleJavaWebApplication extends HttpServlet {

    public static void main(String[] args) {
        SpringApplication.run(SimpleJavaWebApplication.class, args);
    }

    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setCharacterEncoding("UTF-8");
        PrintWriter out = resp.getWriter();
        out.write("hello world!");
    }

}
```

这是一个使用`Spring Boot`构建的直接操作Servlet的最简单的 Java Web 应用程序。    

程序运行起来之后，`Spring Boot`会自动帮我在`8080`端口起一个`Tomcat`，访问`localhost:8080`即可看到`hello world!`。   

看到这个结果，我不禁要提出一系列灵魂拷问：   

- 是谁调用了`doGet`方法？   

- `req` 和 `resp` 从哪来的？    

- `HttpServletRequest` 和 `HttpServletResponse` 只是接口，那么是谁实现了这两个接口？  

- `Tomcat`是怎么和我编写的这个应用程序交互的？   

## 在`doGet`方法上下个断点，跟进去看看发生了什么有趣的事情  
---   

![avatar](/img/2019-05-11-p1.png)    

调用链路一目了然。（图中以`org.apache`开头的包中的就是`Tomcat 9.0`的类）     

让我们看看几个关键点：    

最开始，定位到了类`NioEndpoint`，他是一个`Enclosing Class`，他的内部有几个`Nested Class`：   

- `Acceptor`：The background thread that listens for incoming TCP/IP connections and hands them off to an appropriate processor.
- `SocketProcessor`：This class is the equivalent of the Worker, but will simply use in an external Executor thread pool.
- `NioSocketWrapper`   
- `Poller`   


(未完待续....
