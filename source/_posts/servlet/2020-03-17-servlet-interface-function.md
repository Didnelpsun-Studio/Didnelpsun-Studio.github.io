---
layout: post
title: "接口方法"
date: 2020-03-17 22:52:01 +0800
categories: notes servlet senior
tags: Servlet 高级 javaweb 接口 getServletInfo ServletInfo getServletConfig ServletConfig ServletContext
excerpt: "接口方法"
---

以上已经完成了基本的Servlet学习，但是如果想继续了解，但是还有许多值得学习。

Servlet接口方法一共有五个，除了[Serlvet生命周期]({% post_url notes/servlet/2020-02-12-servlet-life-cycle %})的提过的三个生命周期方法，还有两个非生命周期方法。一般使用方法：

```java
package ;
import ;
public class MyServlet implements Servlet {
    ServletConfig config = null;
    public void init(ServletConfig config) {
    }
    public void service(ServletRequest req, ServletResponse res) throws IOException, ServletException {
    }
    public void destroy() {
    }
    public ServletConfig getServletConfig() {
    }
    public String getServletInfo() {
    }
}
```

## ServletInfo

`public String getServletInfo()`，返回有关servlet的信息，如作者，版权，版本等。

返回字符串。一般都是将一串字符串保存在info中。

&emsp;

## ServletConfig

在Servlet容器初始化Servlet时，Servlet容器将ServletConfig传给Servlet的init方法。ServletConfig封装可以通过@WebServlet或部署描述符传给一个Servlet配置信息。以这种方式传递的每一条信息都称作某个初始参数。初始参数由两个组成：键和值。

想要获取初始参数的值，调用ServletConfig对象的`getInitParameter`方法。另外，getInitParameter方法返回的是初始参数名称的字符串类型。同时如果一个name值保存的不是一个值，还可以使用`getInitParameterNames()`方法，返回是枚举类型。

```java
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;
import javax.servlet.annotation.WebInitParam; //引入
@WebServlet(
            name = "ServletConfigDemo",
            urlPatterns = {"/ServletConfigDemo"},
            initParams = {
                @WebInitParam(name="Name",value="LaoYe"),
                @WebInitParam(name="sex",value="Male")
            }
        )
public class ServletConfigDemo implements Servlet {
    private transient ServletConfig servletConfig;
    public void init(ServletConfig servletConfig) throws ServletException {
        this.servletConfig = servletConfig;
    }
    public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
        ServletConfig servletConfig = getServletConfig();
        String name = servletConfig.getInitParameter("Name");
        String sex = servletConfig.getInitParameter("sex");
        response.setContentType("text/html;charset=utf-8");
        PrintWriter writer = response.getWriter();
        writer.print("初始化参数Name = "+name+" , Sex = "+sex);
    }
    public void destroy() {
        System.out.println("执行销毁Servlet");
    }
    public ServletConfig getServletConfig() {
        return servletConfig;
    }
    public String getServletInfo() {
        return "My Servlet";
    }
}
```

```xml
<!--也可以在xml文件中配置参数，而不是在java文件中-->
<servlet>
    <servlet-name>ServletConfigDemo</servlet-name>
    <servlet-class>page.ServletConfigDemo</servlet-class>
    <init-param>
        <param-name>Name</param-name>
        <param-value>Laoye</param-value>
    </init-param>
    <init-param>
        <param-name>Sex</param-name>
        <param-value>Male</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>ServletConfigDemo</servlet-name>
    <url-pattern>/ServletConfig</url-pattern>
</servlet-mapping>
```

在类名外部的 @WebServlet 注解中定义了初始化函数Name和Sex。在service方法中通过ServletConfig的实例对象调用getInitParameter()方法来获取指定的参数化参数的名称。

@WebServlet注解里面的路径对应浏览器的路径，例如：@WebServlet("/ServletConfigDemo")对应路径 xxx/app01/ServletConfig 。它和配置文件里面的`<url-pattern>/ServletConfig</url-pattern>`目的是一样的，两者都是告知浏览器，如果URL的请求是ServletConfig，则由ServletConfigDemo类来提供服务。所以，两种配置方式如果路径重名会使Servlet容器启动不了，一般来说配置一种就好了。

&emsp;

## ServletConfig与ServletContext

我们已经学过了全文对象ServletContext，表示Servlet应用，每个Web应用只有一个ServletContext对象。如果示分布式的环境，那么一个程序会布局在多个容器中，且每台Java虚拟机都有一个ServletContext对象。

在ServletConfig中也可以使用getServletContext方法获得ServletContext对象。

那他们之间有什么区别呢？

+ gServletContext()相当于Application，被所有Servelt所共享，使用getServletContext（）方法得到web应用的servletContext，即而使用getServletContext的一些方法来获得一些值，比如说getServletContext().getRealPath("/")来获得系统绝对路径，getServletContext().getResource("WEB-INF/config.xml")来获得xml文件的内容 getServletContext()取得的是\<context-param>配置的参数；ServletConfig()是某个servlet的配置，但是它具有对于ServletContext的引用。
+ getServletContext()取得的是\<context-param>配置的参数；getServletConfig()取得的是\<servlet>\<init-param>配置的参。
