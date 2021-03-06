---
layout: post
title: "依赖注入（下）"
date: 2020-03-25 23:04:22 +0800
categories: notes spring base
tags: Spring 基础 @Component @ComponentScan @ComponentScans @Configuration @Import @Scope @Bean @Repository @Service @Controller @RestController
excerpt: "注释依赖注入"
---

在Spring中，尽管使用XML配置文件可以实现Bean的装配工作，但如果应用中Bean的数量较多，会导致XML配置文件过于臃肿，从而给维护和升级带来一定的困难。

Java从JDK5.0以后，提供了Annotation（注解）功能，Spring也提供了对Annotation技术的全面支持。Spring3中定义了一系列的Annotation（注解），常用的注解如下：

1. @Component：可以使用此注解描述Spring中的Bean，但它是一个泛化的概念，仅仅表示一个组件（Bean），并且可以作用在任何层次。使用时只需将该注解标注在相应类上即可。如果不好分类就用这个注解。一般是针对普通的POJO。
2. @Repository：用于将数据访问层（DAO层）的类标识为Spring中的Bean，其功能与@Component相同。
3. @Service：通常作用在业务层（Service层），注入DAO层，用于将业务层的类标识为Spring中的Bean，其功能与@Component相同。
4. @Controller：通常作用在控制层（如Struts2的Action），用于将控制层的类标识为Spring中的Bean，注入服务，其功能与@Component相同。
5. 其他的相关配置在后面一节会谈到。

我们这里会讲到[三层架构]({% post_url notes/spring/2020-01-29-spring-introduction-and-build %})，我们暂时只会用到前面三种注释，对于三层结构的注释会在后面使用。

## @Component、@ComponentScan和@ComponentScans

### &emsp;@Component

我们之前在[基本容器与实例注入的注释]({% post_url notes/spring/2020-03-19-spring-container-and-instance-injection %})已经用过了@Component和@ComponentScan来配置，这里我们并不会着重谈到。

在xml文件中写入`<context:component-scan base-package="扫描的包名">`引入Component的扫描组件。

相当于配置文件中applicationContext.xml中的一句：\<bean id="XXX" class="XXXXX"></bean>，即将这个类实例化变成一个实例放入实例池中被容器操纵。

同时我们也可以从此知道，我们在接口上添加这个注解是没有用的，因为接口没有构造方法也不可能被实例化。

对于@Controller，@Service，@Repository注解，查看其源码你会发现，他们中有一个共同的注解@Component，没错@ComponentScan注解默认就会装配标识了@Controller，@Service，@Repository。它们并没有本质的区别，只是为了标注不同的功能罢了。

### &emsp;@ComponentScan

@ComponentScan的作用就是根据定义的扫描路径，把符合扫描规则的类装配到spring容器中，如我们在App.java就是在最上面加上这个注释，表明在这里使用扫描器，来查看加载类。与ComponentScan注解相对应的XML配置就是\<context:component-scan/>

如<span style="color:aqua">格式：</span>`@ComponentScan(value="要扫描的包名")`

#### &emsp;&emsp;源码

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    /**
     * 对应的包扫描路径 可以是单个路径，也可以是扫描的路径数组
     * @return
     */
    @AliasFor("basePackages")
    String[] value() default {};
    /**
     * 和value一样是对应的包扫描路径 可以是单个路径，也可以是扫描的路径数组
     * @return
     */
    @AliasFor("value")
    String[] basePackages() default {};
    /**
     * 指定具体的扫描的类
     * @return
     */
    Class<?>[] basePackageClasses() default {};
    /**
     * 对应的bean名称的生成器 默认的是BeanNameGenerator
     * @return
     */
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;
    /**
     * 处理检测到的bean的scope范围
     */
    Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;
    /**
     * 是否为检测到的组件生成代理
     * Indicates whether proxies should be generated for detected components, which may be
     * necessary when using scopes in a proxy-style fashion.
     * <p>The default is defer to the default behavior of the component scanner used to
     * execute the actual scan.
     * <p>Note that setting this attribute overrides any value set for {@link #scopeResolver}.
     * @see ClassPathBeanDefinitionScanner#setScopedProxyMode(ScopedProxyMode)
     */
    ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;
    /**
     * 控制符合组件检测条件的类文件   默认是包扫描下的  **/*.class
     * @return
     */
    String resourcePattern() default ClassPathScanningCandidateComponentProvider.DEFAULT_RESOURCE_PATTERN;
    /**
     * 是否对带有@Component @Repository @Service @Controller注解的类开启检测,默认是开启的
     * @return
     */
    boolean useDefaultFilters() default true;
    /**
     * 指定某些定义Filter满足条件的组件 FilterType有5种类型如：
     *                                  ANNOTATION, 注解类型 默认
                                        ASSIGNABLE_TYPE,指定固定类
                                        ASPECTJ， ASPECTJ类型
                                        REGEX,正则表达式
                                        CUSTOM,自定义类型
     * @return
     */
    Filter[] includeFilters() default {};
    /**
     * 排除某些过来器扫描到的类
     * @return
     */
    Filter[] excludeFilters() default {};
    /**
     * 扫描到的类是都开启懒加载 ，默认是不开启的
     * @return
     */
    boolean lazyInit() default false;
}
```

#### &emsp;&emsp;源码参数

+ basePackages与value:  用于指定包的路径，进行扫描
+ basePackageClasses: 用于指定某个类的包的路径进行扫描
+ nameGenerator: bean的名称的生成器
+ useDefaultFilters: 是否开启对@Component，@Repository，@Service，@Controller的类进行检测
+ includeFilters: 包含的过滤条件
1. FilterType.ANNOTATION：按照注解过滤
2. FilterType.ASSIGNABLE_TYPE：按照给定的类型
3. FilterType.ASPECTJ：使用ASPECTJ表达式
4. FilterType.REGEX：正则
5. FilterType.CUSTOM：自定义规则
+ excludeFilters: 排除的过滤条件，用法和includeFilters一样

如：

```java
@ComponentScan(value = "com.xhx.spring.service",
        useDefaultFilters = true,
        basePackageClasses = HelloController.class
)
```

#### &emsp;&emsp;常用的方法

+ 自定扫描路径下边带有@Controller，@Service，@Repository，@Component注解加入spring容器
+ 通过includeFilters加入扫描路径下没有以上注解的类加入spring容器
+ 通过excludeFilters过滤出不用加入spring容器的类
+ 自定义增加了@Component注解的注解方式

#### &emsp;&emsp;常用参数

+ value：指定要扫描的package；
+ includeFilters=Filter[]：指定只包含的组件
+ excludeFilters=Filter[]：指定需要排除的组件；
+ useDefaultFilters=true/false：指定是否需要使用Spring默认的扫描规则：被@Component, @Repository, @Service, @Controller或者已经声明过@Component自定义注解标记的组件；

#### &emsp;&emsp;详细的例子

1. 扫描指定类文件：`@ComponentScan(basePackageClasses = Person.class)`
2. 扫描指定包，使用默认扫描规则，即被@Component, @Repository, @Service, @Controller或者已经声明过@Component自定义注解标记的组件：`@ComponentScan(value = "com.yibai")`
3. 扫描指定包，加载被@Component注解标记的组件和默认规则的扫描（因为useDefaultFilters默认为true）：`@ComponentScan(value = "com.yibai", includeFilters = { @Filter(type = FilterType.ANNOTATION, value = Component.class) })`
4. 扫描指定包，只加载Person类型的组件：`@ComponentScan(value = "com.yibai", includeFilters = { @Filter(type = FilterType.ASSIGNABLE_TYPE, value = Person.class) }, useDefaultFilters = false)`
5. 扫描指定包，过滤掉被@Component标记的组：`@ComponentScan(value = "com.yibai", excludeFilters = { @Filter(type = FilterType.ANNOTATION, value = Component.class) })`
6. 扫描指定包，自定义过滤规则：`@ComponentScan(value = "com.yibai", includeFilters = { @Filter(type = FilterType.CUSTOM, value = ColorBeanLoadFilter.class) }, useDefaultFilters = true)`

### &emsp;@ComponentScans

可以一次声明多个@ComponentScan。

#### &emsp;&emsp;源码

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Repeatable(ComponentScans.class)  //指定ComponentScan可以被ComponentScans作为数组使用
public @interface ComponentScan {
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
public @interface ComponentScans {
    ComponentScan[] value();

}
```

#### &emsp;&emsp;具体例子

```java
@ComponentScans(value = { @ComponentScan(value = "com.yibai.spring.annotation"),
        @ComponentScan(value = "com.yibai.spring.annotation", includeFilters = {
                @Filter(type = FilterType.CUSTOM, value = ColorBeanLoadFilter.class) }) })
public class MainConfig {

    @Bean(name = "pers", initMethod = "init", destroyMethod = "destory")
    public Person person() {
        return new Person();
    }
}
```

&emsp;

## @Configuration和@Bean

### &emsp;基本使用

Spring帮助我们管理Bean分为两个部分，一个是注册Bean，一个装配Bean。完成这两个动作有三种方式，一种是使用自动配置的方式，一种就是使用XML配置的方式，一种是使用JavaConfig的方式，即通过注释。

而@Configuration和@Bean就是最后一种方式来注册装配Bean。

Spring的带有@Configuration的注解类表示这个类可以使用Spring IoC容器作为bean定义的来源。@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。这样的注释的配置方法，就将一些xml的类似\<property>配置标签直接用Java代码取代。

SpringIoC容器管理一个或者多个bean，这些bean都需要在@Configuration注解下进行创建，在一个方法上使用@Bean注解就表明这个方法需要交给Spring进行管理。

首先我们还是使用[Spring标注模板](https://github.com/Didnelpsun/notes/tree/master/spring/spring)

然后我们把xml文件全部注释掉，不让xml配置起作用。

然后新建一个HelloWorldConfig.java文件：

```java
//HelloWorldConfig.java
package org.didnelpsun.test;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HelloWorldConfig {
    @Bean
    public HelloWorld HelloWorld(){
        return new HelloWorld();
    }
}
```

请注意这个代码的意思，首先这个文件并不是一个实例类，而是一个配置类，它只是起到了配置实例类的作用，@Configuration告诉容器这里是配置，可能会设置参数，然后返回实例。@Bean对应的方法将返回一个实例交给容器管理，所以这里的HelloWorld方法就是一个返回实例的方法，而具体的实现是依赖HelloWorld类。

```java
//App.java
package org.didnelpsun;
//项目入口
import org.didnelpsun.test.HelloWorld;
import org.didnelpsun.test.HelloWorldConfig;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App
{
    private static ApplicationContext welcomeContext;
    public static void main(String args[]){
        welcomeContext = new AnnotationConfigApplicationContext(HelloWorldConfig.class);
        HelloWorld hello = welcomeContext.getBean(HelloWorld.class);
        hello.saySomeThing();
    }
}
```

![null][null]

### &emsp;配置参数

如果我们不想光溜溜就返回一个没有配置的实例，我们也可以返回一个配置好的实例：

```java
//HelloWorldConfig.java
@Configuration
public class HelloWorldConfig {
    @Bean
    public HelloWorld HelloWorld(){
        HelloWorld hello = new HelloWorld();
        hello.setWords("Hello");
        return hello;
    }
}
```

![hello][hello]

### &emsp;配置依赖

我们这里只使用了HelloWorld类实例，而如果我们要使用User类实例怎么办呢？如何实现User对HelloWorld的依赖呢？很简单，讲它们都用@Bean注释，然后讲被依赖类的配置方法传入依赖类的配置方法中。

```java
//App.java
welcomeContext = new AnnotationConfigApplicationContext(HelloWorldConfig.class);
User hello = welcomeContext.getBean(User.class);
hello.SayHello();
```

```java
//HelloWorldConfig.java
package org.didnelpsun.test;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HelloWorldConfig {
    @Bean
    public HelloWorld HelloWorld(){
        HelloWorld hello = new HelloWorld();
        hello.setWords("Hello");
        return hello;
    }
    @Bean
    public User User(){
        User user = new User();
        user.setHelloWorld(HelloWorld());
        user.setAge(19);
        user.setPassword("PassWord!");
        user.setWords("Hi");
        return user;
    }
}
```

![配置依赖][ref]

如果你使用的是构造函数而非设置函数进行依赖注入，那么你可以直接`return new User(HelloWorld());`。

### &emsp;生命周期

可以使用`@Bean(initMethod = "方法名", destroyMethod = "方法名")`的方法来规定生命周期处理函数。

[@Configuration和@Bean实例：spring/configuration-bean](https://github.com/Didnelpsun/notes/tree/master/spring/configuration-bean)

### &emsp;说明

@Configuration注解的配置类有如下要求：

1. @Configuration不可以是final类型；
2. @Configuration不可以是匿名类；
3. 嵌套的configuration必须是静态类。

### &emsp;@Configuration与@Components

我们最开始使用注释注册Bean是使用@Components注释的，那么他们有什么区别和联系呢？

共同点：都可以用于创建Bean；

不同点：

+ 实现原理不同，@Configuration基于CGlib代理实现，@Component基于反射实现；
+ 使用场景：@Configuration用于全局配置，比如数据库相关配置，MVC相关配置等；业务Bean的配置使用注解配置(@Component,@Service,@Repository,@Controller)。
+ 功能本质不同：@Components的本质是创建实例，而@Configuration的本质是取代xml文件。@Components和扫描器必须要配合使用，可以使用\<context:component-scan/>，也可以使用@ComponentScan，可以使用xml文件来配置，也可以使用@Configuration配置。

### &emsp;@Configuration的必要性

对于配置类作为AnnotationConfigApplicationContext对象传入的参数时，@Configuration可以不写。因为创建容器时会必然按照传入的这个配置类参数来去查找依赖。如上面的`welcomeContext = new AnnotationConfigApplicationContext(HelloWorldConfig.class);`，这时候上面的@Configuration注释就不用写了。

但是如果你要扫描多个多个包，也就是说如果你传入容器的配置类中要扫描多个别的配置类，那么你就要将所有要扫描的配置类都加上@Configuration。否则它将不知道要扫描哪一个。或者你可以通过`AnnotationConfigApplicationContext(someConfig.class,someConfig.class,someConfig.class...);`，这样Spring就知道你具体要传入哪些配置类了。

&emsp;

## @Import

针对上面的@Configuration必要性的问题，如果你既不想在配置容器时传入一大堆的配置类，也不想写一个总配置文件扫描其他子配置文件，那么可以使用@   Import来导入配置。

@Import注解允许从另一个配置类中加载@Bean定义。如别的类我们需要使用到一个实例HelloWorld，只用`@Import(HelloWorld.class)`就可以了。而且如果你在一个配置文件A的a类中引用了B的实例b类，那么你要在主函数文件中使用a类和b类，你就只用引入A文件，而不用引入B文件，因为B文件所需要的类已经通过@Import被导入了。

使用@Import导入后，有@Import注解的类就是父配置类，导入的就是子配置类。

&emsp;

## @Scope

与xml的scope属性一样也是规定实例作用域的，值都是一样的。如@Scope("prototype")。

&emsp;

## @Repository

@Repository会被作为持久层操作（数据库）的bean来使用@Repository注解修饰哪个类，则表明这个类具有对对象进行CRUD（增删改查）的功能，而且@Repository是@Component注解的一个派生品，所以被@Repository注解的类可以自动的被@ComponentScan 通过路径扫描给找到。（这也在一定程度上解释了，为什么被@Repository注解的类也能@Autowired，这个注释后面会提到）

为什么@Repository只能标注在 DAO 类上呢？这是因为该注解的作用不只是将类识别为Bean，同时它还能将所标注的类中抛出的数据访问异常封装为 Spring 的数据访问异常类型。 Spring本身提供了一个丰富的并且是与具体的数据访问技术无关的数据访问异常结构，用于封装不同的持久层框架抛出的异常，使得异常独立于底层的框架。

实际的应用将在后面讲到。

&emsp;

## @Service

@Service标注业务层组件，标注将这个类交给Spring容器管理，spring容器要为他创建对象。

如果只用@autowired注解来注入bean，不在业务层使用@Service
启动服务会报错：Field xxxService in xx.xxx.controller.xxxController required a bean of type 'xx.xxx.utils.xxxService' that could not be found。

对于实际的应用将在后面讲到。

&emsp;

## @Controller

@Controller 注解的bean会被spring-mvc框架所使用。

@Controller用于标记在一个类上，使用它标记的类就是一个SpringMvc Controller对象，分发处理器会扫描使用该注解的类的方法，并检测该方法是否使用了@RequestMapping注解。
@Controller只是定义了一个控制器类，而使用@RequestMapping注解的方法才是处理请求的处理器。
@Controller标记在一个类上还不能真正意义上说它就是SpringMvc的控制器，应为这个时候Spring还不认识它，这个时候需要把这个控制器交给Spring来管理。有两种方式可以管理：

```xml
<!--基于注解的装配-->
<!--方式一-->
<bean class="com.HelloWorld"/>
<!--方式二-->
<!--路径写到controller的上一层-->
<context:component-scan base-package="com"/>
```

```java
package com;
@Controller
public class HelloWorld{

    @RequestMapping(value="/showRegUser")
    public String printHello() {
        return "hello";
    }

    @Autowried
    private IocSerevce service;
    public void add(){
        service.add();
    }
}
```

### &emsp;@Controller和@RestController的区别

1. 如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，或者html，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return里的内容。
2. 如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。
3. 例如使用@Controller注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面若返回json等内容到页面，则需要加@ResponseBody注解
4. @RestController注解，相当于@Controller+@ResponseBody两个注解的结合，返回json数据不需要在方法前面加@ResponseBody注解了，但使用@RestController这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面

对于实际的应用将在后面讲到。

[null]:data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMQAAAAeCAIAAAD8YSZ1AAAEo0lEQVR4Ae1aP07rTBBfP72eCtlFOAGOQgEnsCUa3gnsjvTJk1PACaBIRNJDl5yANEjOCeLie4pvQApHqTiB36w3rDfxerLmE/74wqaA9czs/PntZGbWinF6ekoKn6OjowJNEzQCexD4sYev2RoBZQR+SiXf3t6kdE3UCCAI6MqEgKNZ1RDQyVQNLy2NIKCTCQFHs6ohoJOpGl5aGkFAJxMCjmZVQ0AnUzW8tDSCgPzVALKBs9Lz65HfTML+/XTJiTuL8+sHj0x+P0U7dPVHZoXJL8adp8hQ36sl9yKAwIuwytTKkyltXN0GjmVsTi5Nk9ngbrr8Dw7SiJ66EUnT8/bQK4tB0z+MAAIvwiozJ08mJs0rAU3S3sjeLkLMWJleTf+GCCjNTJA3/TAxncvzNP2GGOmQFRHAKpOo4vWfeOU4rQsSQdPJpiXG5dWLC4tcIKaLDQfmJ3c1CM3Ab9J2maaLSfcx4p1U0JkuxipjFq6wcXXTcy1mW7S1M8aJj7jCTRiyf2W2aJhiXEk4uHteGgabItaTLh8BQWzoHfNZAlEos09piPNijEzyXw6yZT6oJhN5TdaEHFsnhCyRbgooBM563OmwLMnCyE1bbs9bjLvdiM1A7q+TKBvexV1p2vh1GzxcE5V8EhTSXV77IsqGfTibzI07nqzkPWtzb2QrQSGd0riHMtkNDbFFk6b1p9t9AlEWctBOIC5jOQ1jB7Q35jS3gHvRskk8YVMpohBxA1gfcB5XWJWr1OYUlQJel44ZT/J6s7ORlxzDiP7ExDRp5YDsObPN1eyFHbxhLJ/DmNgtlZYqKNzdZRhNqKNVP4LC3MO9SspsQdLcv99kWchc1ZzGb5/BdxMQaFy5NgAw59wyhVxAuviY81JVHyMqV6YT65iQdfJKSPmdjslUdsQyTWJZvaGb74TGlD9UWLHSSaB2dhK4kA6HPvUWf39RQb1MFLHFqqxr5YjlYc1fZm4A2TRdLk/gyxSH9++XZUShzP4XoqkmEw2YxCF8eXJkCmFkrbBAVSIUZy/FxrStPc92WhV+T4GbDSjBDRkg78O2lVR+ktpimeSQWb+z6WVi02cF2HHPGs/WJQgNtpCVKqzsVu0blNocmweR/sXcZpXchlEghQ9semCzNh4U7HqZrWyvrdLXylTRicSjrSIfkrgovTvwB5KsVryHwumqeJhv3rvatkXF1wmbimAS2rUFxYk4l+2WHYel7/CKCvf6UBD43JAFc1hlavqjoU9loemMO/kwu3UGmQzv1vPHSWvo9Ua0Y0Fz6cd+YArWSpZQRAbkpjcaZdaoEFeI2AIxo+lDJ2NawcX79/fjLPsZHf6Kbe4VJjLH8zNbYKUfuioeclXFRZktWnvGMzvwmIdpEo7DY09AAwQgzXtuczF+FAt+mcKiaUUKEjICL8Iqs2tIfwNeJv2l6BDtJ11xawsT8iawY/a+oDajn2dIqc19nvnvrBkGAR8uv+FmojoAKLA2dwDhfc0QeCOTXDu+psdqXv2P25xagFqqPgR0m6sP64O3pJPp4I+4vgB1MtWH9cFb0sl08EdcX4A6merD+uAt6WQ6+COuL8C/wv+qN1ecpIwAAAAASUVORK5CYII=

[hello]:data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAM8AAAAgCAIAAAA68btfAAAFTklEQVR4Ae1bsW7jOBClDtenCizgnC+IDKdI7j5AAtJkqyvlLu7tQC6SL0gKG7H7uLPba+ImgNwfYBW3sP4gXkCCq3yBbihKlBRRIuXI60VCN0uRwzfDx+HMUMoqp6enqPR3dHRUOv6rDwbNv/7+8w9qZRD8WP3z70ZRaI9s/DQGlE/vbT+NSqmIy8BvXAkpIBmoiwHpbXUxKXH4DEhv43MkJepi4Hcu0NvbG1dGCkgGRBiQsU2EJSlTDwPS2+rhUaKIMCC9TYQlKVMPA9Lb6uFRoogwIL1NhCUpUw8D0tvq4VGiiDDAfwMighKcX086Lc8ePiw2RfLn148mmt9MnSIBbj/RQsTWs97Ukd86uZxVECiht2SoggKE+N4WNK/uLF2NP2MHgbcc3S82B9hpxZn2HRQE592xWWmRX0qY7Nd23qenEXxlbB5zd62E3pKhStzyvY3A0ViC3Xww0bJhjFhTSbEU/oIMVK7bwLGGttfQL8+D4AvyJZf8EQZEY1tax+t/rq/r7QvkQF4LKzYySuMfFU6PQmewjkaghjP8kd2wOi2ckYNgPe8/OTRZpzCD9Uyk1CsHbF7dDgyV6E7reldKph/LAaNlsP4p0oWXmV6XZ4/un+HP7LiJrwSQpZ/flzFDjN5y0IyF8bqYU3bxNvTqbRE6Vk8Q2pRkdDDC0rezXo+4UbiXiQ2qMTDXs37fIXWY8e3ECW8Y6VlB0Px2Zz1eIxGHSwHiWWb3wglvJEBuaMY99WYUu3ViDauVAsSVIrWQJRv1lejCXtX+3u9PQZQs2ep6sC5ls7BdHdCbK+x8MHrR1pA7J5VxCWCJGTDU6kzGnUQEqm3ysDO9CVa2hbdVc2c9TC/ZL+sOkYOUFcRPlTNpHoLZA4Re6g13nkSsd2I0aCmK891FjQaOPWDumdbwly/EMxRl82y7SGuLZO0U4PtZitKCSFz1lwJMLOSCFOkCr3qI7+NkyRRqhdevncHhBQaaV4YGBKzoaBEgFWA2IM/0419vFuWUj9DL1BJaC0cj2mW8X7OlH68lP2Wn2HaiHiO09V4RKr6ZEpm8Qk6P2mggVR2MjUQOcl/yUKFFgi+C6Nvz4Fo9HnewteWvaSrAs0RLdJFzb6gJY8myVi9LwwJ3W2w2J3DaXPshvvKXALL0c/tqpJfq8v0oboY9OO/pOHRsqEDS2MXbMCPIteH4JdQliFErzLa5XqGOfP0nmPuy6MlxwHHlZgGjYZFk3aJRyXvBLEjlJ6Yu4mo6Wg57UbpM1xUkhOvGWfNZvQShUYZZJmBls1ITaqKXIoZpifoWjjK+m/Y/KrhDJiUlIQ2eKahMkyQLDcqRAH4w6ZFcCDJCuQeY9bL0NbMrkjpzs6MOXBWZOBslhRoVxRcc+oA836dpGrZfxMJkMreV1YXFtx6pzKAae68LwhvSL7ttzbUL32XmAbk2ZAVqoTcNCScBVzrxfuFD1YH6qXAJorGNVp2Q10hJSLRmNimsTGm5s3qat8fmYGKAJOSvoduxGmlT2W0IQyN0O5hMaI1LAUt0AZbS6kCyJKBg4kP8pYEcD6osnUlfoSrUzU6oC7QMbUPEQgqVbxTpIgWNZpnEwsCzZ/axmWIDBOAcDIzWevaUThlFgHnVgj270VvCvDO9QXBQ4/2im8W0h/9/rpjTfrVOoOODn8UOviJwLEtzi25zBzevFgP2dSetxbivAwK1RpiCoqrusy5cNJN+1vUffF00VzKK94MbV7cBnyST1k2LxNsLAzKT7oVWCcpkQHobkxbZuRcGpLfthVYJymRAehuTFtm5Fwakt+2FVgnKZOB/9kEH6TX6/1sAAAAASUVORK5CYII=

[ref]:data:image/png;base64,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
