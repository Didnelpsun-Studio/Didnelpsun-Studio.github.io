---
layout: post
title: "计数器的实现"
date: 2020-03-17 17:34:16 +0800
categories: notes servlet base
tags: Servlet 基础 javaweb 计数器 filter
excerpt: "点击页面与全站计数器"
---

分为以下两种，但是实际上他们并没有什么不同，只是保存的变量在不同的地方而已，网页点击器会保存在单个网页上，处理也是在本页面处理的，而网站点击器的处理变量应该是全站变量。不过如果因为没有数据库用来保存点击值，所以一旦销毁或者重启Servlet，都将丢失原来的值。

虽然说现在的例子是点击计数器，但是实际上可以利用这种思想延伸到许多其他的功能。

下面会新建一个Servlet文件和一个过滤器。对于Servlet文件的xml文件配置将不再写出。

## 网页点击器

+ 在init()方法中初始化一个全局变量。
+ 每次调用doGet()或doPost()方法时，都增加全局变量。
+ 如果需要，你可以使用一个数据库表来存储全局变量的值在destroy()中。在下次初始化Servlet时，该值可在init()方法内被读取。这一步是可选的。
+ 如果你只想对一个session会话计数一次页面点击，那么请使用isNew()方法来检查该session会话是否已点击过相同页面。这一步是可选的。
+ 你可以通过显示全局计数器的值，来在网站上展示页面的总点击量。这一步是可选的。

```java
package test;
import java.io.*;
import java.sql.Date;
import java.util.*;
import javax.servlet.*;
import javax.servlet.http.*;
public class PageCounter extends HttpServlet{
    private int hitCount;
    public void init()
    {
        // 重置点击计数器
        hitCount = 0;
    }
    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException
    {
        // 设置响应内容类型
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        // 该方法在 Servlet 被点击时执行
        // 增加 hitCount
        hitCount++;
        PrintWriter out = response.getWriter();
        String title = "总点击量";
        String docType =
                "<!doctype html public >\n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<h2 align=\"center\">" + hitCount + "</h2>\n" +
                "</body></html>");

    }
    public void destroy()
    {
    }
}
```

没当我们刷新一次，那么点击量将加1。

![网页点击][pagecounter]

如果我们想连接数据库来查找和保存对应的数据，就应该在init的时候导入数据，再在destroy的时候存入数据库中。

```java
package test;

import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
import java.sql.*;

public class PageCounter extends HttpServlet{
    private int hitCount;
    static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
    static final String DB_URL = "jdbc:mysql://localhost/jdbc";
    static final String User = "root";
    static final String Pass = "root";
    Connection conn = null;
    Statement stmt = null;
    PreparedStatement pstmt =null;
    public void init()
    {
        try{
            //注册连接
            Class.forName(JDBC_DRIVER);
            System.out.println("Connection the database...");
            //打开连接
            conn = DriverManager.getConnection(DB_URL,User,Pass);
            System.out.println("Creating statement...");
            //创建接口
            stmt = conn.createStatement();
            //编写查询语句
            String sql = "SELECT value FROM Counter WHERE name = 'pagecounter'";
            //获取结果集
            ResultSet rs = stmt.executeQuery(sql);
            //如果结果集为空，证明数据里面没有pagecounter为name的值
            //那么就创建一条值
            if(!rs.next()){
                String sql2 = "INSERT INTO Counter Values('pagecounter',0)";
                //插入数据
                int rows = stmt.executeUpdate(sql2);
                //获取更新的条数，如果大于1，就抛出错误
                if (rows!=1) {
                    System.out.println("Insert cause wrong...");
                    return;
                }
            }
            else{//获取到了数据
                hitCount = rs.getInt("value");
            }
            rs.close();
            stmt.close();
            conn.close();
        }catch (SQLException se){
            se.printStackTrace();
        }
        catch (Exception e){
            e.printStackTrace();
        }finally {
            try{
                if(stmt!=null)
                    stmt.close();
            }catch (SQLException se2){
                se2.printStackTrace();
            }
            try{
                if(conn!=null)
                    conn.close();
            }catch (SQLException se3){
                se3.printStackTrace();
            }
        }
    }
    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException
    {
        // 设置响应内容类型
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        // 该方法在 Servlet 被点击时执行
        // 增加 hitCount
        hitCount++;
        PrintWriter out = response.getWriter();
        String title = "总点击量";
        String docType =
                "<!doctype html public >\n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<h2 align=\"center\">" + hitCount + "</h2>\n" +
                "</body></html>");

    }
    public void destroy()
    {
        try{
            Class.forName(JDBC_DRIVER);
            System.out.println("Connection the database...");
            //打开连接
            conn = DriverManager.getConnection(DB_URL,User,Pass);
            System.out.println("Creating statement...");
            //创建接口
            String sql = "UPDATE Counter SET value = ? WHERE name = 'pagecounter'";
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1,hitCount);
            int rows = pstmt.executeUpdate();
            if(rows!=1){
                System.out.println("Update make some wrong...");
                return;
            }
        }catch (SQLException se){
            se.printStackTrace();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            try {
                if(stmt!=null)
                    stmt.close();
            }catch (SQLException se2){
                se2.printStackTrace();
            }
            try {
                if(conn!=null)
                    conn.close();
            }catch (SQLException se3){
                se3.printStackTrace();
            }
        }
    }
}
```

![result1][result1]

![数据库][database]

&emsp;

## 网站点击器

+ 在过滤器的 init() 方法中初始化一个全局变量。
+ 每次调用 doFilter 方法时，都增加全局变量。
+ 如果需要，你可以使用一个数据库表来存储全局变量的值在过滤器的 destroy() 中。在下次初始化过滤器时，该值可在 init() 方法内被读取。这一步是可选的。

```xml
<filter>
   <filter-name>WebCounter</filter-name>
   <filter-class>test.WebCounter</filter-class>
</filter>

<filter-mapping>
   <filter-name>WebCounter</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

```java
package test;
// 导入必需的 java 库
import javax.servlet.*;
public class WebCounter implements Filter{
    private int hitCount;
    public void  init(FilterConfig config)
            throws ServletException{
        // 重置点击计数器
        hitCount = 0;
    }
    public void  doFilter(ServletRequest request,
                          ServletResponse response,
                          FilterChain chain)
            throws java.io.IOException, ServletException {

        // 把计数器的值增加 1
        hitCount++;
        // 输出计数器
        System.out.println("网站访问统计："+ hitCount );
        // 把请求传回到过滤器链
        chain.doFilter(request,response);
    }
    public void destroy()
    {
        // 这一步是可选的，但是如果需要，你可以把 hitCount 的值写入到数据库
    }
}
```

![网页点击器][webcounter]

我们可以看到虽然我们是第一次访问页面，但是它就显示我们访问三次，这是因为什么呢？我觉得可能是因为HTTP三次握手，所以产生了三次，相关的猜想在[JSP的生命周期]({% post_url notes/jsp/2020-02-12-jsp-structure-and-life-cycle %})的例子中就已经提过，因为生命周期的例子中也是响应三次，而JSP也是一种Servlet。

如果想使用数据库来保存数据，上面的例子已经演示过了。

[pagecounter]:data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMYAAACJCAIAAAD499CzAAAPhklEQVR4Ae1db2jbZhrX7g5q2MBh/aDRgwlyENNBXDaIez2IIDCLHtSmY3HXIxMdrLoVZjfQOjvIaf0QTKCdW2jjQntqoYcWrosKC3bhinwQUOG2c2ClDrQoHzI8uDIdFCJYQYMMTo4jS7JlS7LlVGofE+L3z/M+7/P+np/ff3olvbK5uYnABxDwDoFfeacKNAECdQSAUsADjxEASnkMKKgDSgEHPEYAKOUxoKAOKAUc8BgBoJTHgII6oBRwwGMEgFIeAwrqgFLAAY8R+I3H+qzUyetCLYRH37TKM6QpsoyEwyFDimfBLUV+9rNR255Xw6HdaDqiNspUMbJnQE00tu75hgfZSz0V+UWaOhTBYkn8LFvr2lDl2xyBYW8MRQ4myJl5hitXREnpWsJN5oMCZv4UHrgp3rusVJo2VzxdknrXFoySg/qpSnco/CSnw1fOUBcixc9i1p3Qs0p+Jl+tIyaJ90vqH4OgqRs8M4kFA8VdtFK6R1PXt6EaQKXRT5jcYbRPxYOiFDpJ019xmbJuXmU+m8d5+vftpFKE82R+TZdUQ7FZdgH4ZIJEi/xUE1YELeLxd3jKA4WDohSCYOS5HFumK7qR1fyJz2P//oLYqyepIfne59QVvTtTU9DjDNOpPzMV7T2Siw/lXJWe5Tc/i7kqsUvCaAR/q/d+RXokiCbsPbB6cJRCkNGT+XNFfM5AKonJnMOFqwkdgyel7DRjahSaZi6mYMBz6ttDWeZWSsfTabGGnMR9FKGW3ZWxlfaUUm0LK2ySIucqbMOKESJ7JktOREKyurRrfH7k57KGCZeaGElfpg5s6RJ1Od8tkiR+lio8bjTB5r/0yCzwTZ56bwcPc0Z7LJH7moq2J7ekuFDYUrIebTXPQsR1kqeUUhdW8c7jyTqfP8XnbSwUC8cPFMwyiRsiO9nr79CsyquY/EQQVnpSJomCJDoriTta8bpQ6KzavqU8pVTf1oAC1wiMprKpA0OuizUKbD7k8px5YdSjJkOxl5RSdHkzO2aAIbjB3xHU6T7mUt8BpYLr+wFZvkxFhqgB6e5N7WB7KWwMx15zaZi677LafafdpULvxcP4dLH4sRO9snCJzBtnXRNZ9gwedlIUQSOOxHwnNFhKkfNF1+PLan6oyxzfAkAH66+fWjnKziYFB1zvsJscQt/Gna0XJOmm2eJwJDbusKy5oBYLYURu7qAW8/g7jLVvRLuuYrCUcm1OTwV6WH/VVoVWlllV7cluspXi3tPCY2Ta37PAF4FSvfsnKCWfcORbVGl3re15BTPIkwi7CwHU5hMEXsRequfLXlvqOYiul7wknj5VcHUMoHV72vVmdzR9LUf8OoxN4PjuUibcKzV6Leesecqm+dKKk1KbjjaNu2nq+bKX7fjyi1xbEfo6BuB6sztM/oIg+4jc10S3Jvspb7CUyqcwuyswfgIjCLa8vOelguCdYNr4Ep+XCqbDgmY1uh+PvGFhtFITKht6eicx5EdReGw6W6SX6TU02IGvV6v8Wu51fKZUdHP5o5/d8wYIQ5HXu6ERO8tYHtOoXBgi5vWCncSkO2TkpMe7E55Sam80N5fT29EhpIjF3KJ6Lg8nZ4mIg91a11u6rldVmqHqik8LWn+H0Oi4s23znfLe7563GCavV4T7Fhd4xP+aBDuJyeva0TWTeF8RTyk1TKRP2yxM5G/z5NXGOU9BfJrOnie8P8DpelXVF4LPt7BwgRQu2JvgUMxekQMJTyllrO+pKDwyDNKvRWJvo9LtTPKUfvdV5fqx5E9s8WrCe1YZLRlIuO2I7VFG7P3ALtIyTiH9aRtIix0r9Wr3vFpIUfRiqfpE21XaKCUTSf3vsqD2sNgfSdJ8faq2SBKnuG6X2xRZLDMzJxlXG4yOmx94QfXIq/q01fYPP2tqWicx8UbCJOdFxKNeak1YKnPVMqee8UX3p1JpambEyrpwLHuTEeOU8by5dJtSB0v+WustDNJaqXiLLdzktwmHhk+Q0XEHMy+ral/ktGfqbrIFLK0bxp3EnnmPjTeUqq4sNXsR6TFXEEnaklKq/W+mFkoKksiYWLXCcquJ7JgRGrlyjZxZbDZYyt8uZ8YTFhNR9SbSW5uppuBLFihNHyxN27fZoZi9IgcSngx8VYFrMkqtE83GO9xVvG1QaIRc+JLWb4pDVZItbfOpJtwVtRVIOH48a1pcLbJFw0aLoWkyf4FmV7vd7q5oo7GhVD2oPKmW7la0GvVMeZ1nzh47EKGFAfyI9Wpe0JAHvZRyv7hgPBKPkrja3xhT2rALjWWZGyKh3uE+li3epvG92xKyWPrwWHI4kTufI+NYeDyZGc3Tuh6euVslT7fdhrTG5ucLlflCZpigPklnT+Boo7N7JonfCeXlYnGlVJlYql0kmj2c8kOltMxx/yjy9V0+YuG7JXK4YaJSK7OFvxWYcmN2xxfU2/BP+Gvx4PAIXu0+zRhu9Y4ezx57y/6mB9f7NW2eVRP6p5RSKecNSzsk+mkSN45gVrWqadjkAvszHjpKRl/VJDaqRTW4UaJTpYX9qcwcjZ8kkGley0aqV4vCn6Nm5YrALezce7rBM3/hmS9pYSUb/YFNvpPRr+9ulIRZItEgLlLjzhKGO+uNTA3Jj1mNT/Vq+XlGmMzhTQubpjy/gMMjeBXFRCnsXSq9Wzeu9T3wPS1zV4wAR49NtHUkxnw9HIpNGfiEIDXxYZOa6oSMTnPKeMK0zSXluX+Zh6kNrmC++Z04lYqqP5NhPDWh14Qg7NJKUzeWPEEa8+pM1UbG6NSMKU8qFO50W48a9Qw+rO5cDDn8GLfOVcNKJyMOCw4N5Xd+or22p19K1e6xpptn41Ri1MqWb0RRc5tVNoJsVUs3S6asD2LR4SRpfvAD+8/6ZoT2UYRbOb0TU1PRbPr9xjiF4ZMmNpa+aqwc60XD76ZM0zQjU/fGyVnTT6LeUTmZUdXvVGn/tN0ebi1muniitS6o3/0NfEqFu2byKXEUt556SPnMqRA9FduZ6LTAJYvc9Ty7akpNT6hz/BCeoJBFRs9QJ+lnEztTn/Yu6hzZHBaxQ2oPx+vGlUvCBrlTMIQnP43mz+lLCvbvxewRctvyUGySIuYzekG1o1qm8CnrZumG7UYopN7MnXvHUU29zaW2VY+YVkWOajMJ9UUp5XtR3FINaI4pJHlYg/63mLqJZux2ass5F090QLPEofqMLHwoTiFMk1NYHFOeysiwOtWW+auduqjtFg7jiTjC61NUnrtfI4d3zItOHIsiVZ1TZaa0RqYb/etwKn0mx19qNgrh51jhfbpJVhN+uxoJx6bS+kq5a9XPcS7VF6VC+0nmGzK3yrHX88wdETmTiu9MgdWDiJGDo0hprWu7O2cSs1p/E8YTpxHmLkF9lEpNJmL7tJm/utC7qXtd1UQYuqhtxRh+BEfK+hxdWBZqJxpdkfpUmQQ1QWdWmhZUF74WTo42aBPC38tEL9E64eojYwY/0lwyNkvtVuABk5wz/jzt61XMM8DKRSqpb/LZF1clEueK1NuOJFuE+qJUQxc6lsqOpdJzlfUt43ZUlPwrtXDc/KCflso7Rcdo+gOtt0NCsbO1H+danuEpcReNT65SKUJnd2ZRulJsPIUjhnO9K5w+9iHbk62V5viGDj0S1xU82mDsKDkzRZMGH7CXWepI2jTJ0usZfGhL7vMxZdJjQXrszk581p18U9oDSjV0hfbFWhAPH/6C/3JP5mxBMPUmzaotA2hkMstcpHZcuy0SCrd1DwqCvpPAlkvNn2JimjTSeUf18AFiFBHUnnI4lphIJg/j+D690vpkC60hR8jU0TgxFjFzdnujdXFncwSLU9QHLXNENHG5Vrusa+sz9J/L2LFLferwS/FX1CuOg7VFkaqrlYcPaoaVmmWFe9D9B2N/iGLON4GeVrnLudwVvjaWE8rWXYi8Xt1ERzAzXyyrb0usFhLUw/Es9aFhtG0T8irhRTqJMHhKeYV6Jz2SwP8vRoxqc6xOYj5PfybLW0YTffeYNqNx3cPBp1T39kHuriPQ71bnrhsMFfodAaCU3z0UOPuAUoFzmd8NBkr53UOBsw8oFTiX+d1goJTfPRQ4+4BSgXOZ3w0GSvndQ4GzDygVOJf53WCglN89FDj7gFKBc5nfDQZK+d1DgbMPKBU4l/ndYKCU3z0UOPuAUoFzmd8NBkr53UOBsw8oFTiX+d1goJTfPRQ4+4BSgXOZ3w0GSvndQ4GzDygVOJf53WCglN89FDj7gFKBc5nfDQZK+d1DgbMPKBU4l/ndYKCU3z0UOPuAUoFzmd8NBkr53UOBsw8oFTiX+d1goJTfPRQ4+4BSgXOZ3w0GSvndQ4GzDygVOJf53WCglN89FDj7gFKBc5nfDQZK+d1DgbMPKBU4l/ndYKCU3z0UOPuAUoFzmd8N9uyFH35vqAv75MoiW3nqvMBI4jTRfOWN82IvqiRQqt2z6huOaXq5Pb1TCh0DShmwgYHPAAYEvUAAKNU3inG0z9ds9m2BvxTAO2Qs/KHI8s8WyVqSVMzGMtxOLEqX+exYwF+KpLXMk2+YS1nAqL4BsDNHFOFiTuMTgn5MU8AnM4Qw8JnxsIspDwq5K81XVsYyHxFt75+0U/Gi5wOlXHm4xs3nKloJ9DR9svGGbS0FvlUEgFIuaCDfK+T0V7oT9Ec+eCu7C/N3SRQo5RzoKntRf/s3ejqdGnZe9iWSBEo5dbZ8l6FXm8LQRTWhaA0ApVoR6RCvsudZPWuKTEIXpcNhCgGlTHB0iij3iwtrzUw0ezwOC70mHC0BoFQLIJbRGncl39w5QEYzyfHO+1aWCl6mRKCUA29vCJy+0EOif8KjDgq9tCJAKXvXV+8ygi6FU4eBUToc7SGgVDsmLSlVgavqSRMpfFiPQagdAaBUOybmlDVhSZ+YI9H4AThtZwaoNQaUakWkJV69v2Too9DkGIx6LQi1RoFSrYiY47WHZQOjkGR0xJwPsTYEgFJtkBgTpKqwYohPRCOwH2XAwzIIlLKERUv8/mHzaFQ9aSwCEykNmo7fQKmO0KgZNbF5kqUulhgBRnWDq5EHB4XtMQIJVwhAL+UKLhC2RwAoZY8RSLhCACjlCi4QtkcAKGWPEUi4QgAo5QouELZHAChljxFIuEIAKOUKLhC2RwAoZY8RSLhCACjlCi4QtkcAKGWPEUi4QgAo5QouELZHAChljxFIuEIAKOUKLhC2RwAoZY8RSLhCACjlCi4QtkcAKGWPEUi4QgAo5QouELZHAChljxFIuEIAKOUKLhC2RwAoZY8RSLhCACjlCi4QtkcAKGWPEUi4QuD/kP/El9iwYIsAAAAASUVORK5CYII=

[result1]:data:image/png;base64,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

[database]:data:image/png;base64,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

[webcounter]:data:image/png;base64,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