---
layout: post
title: "自动装配（注解）"
date: 2020-03-29 13:18:37 +0800
categories: notes spring base
tags: Spring 基础 自动扫描 context:annotation-config context:component-scan @Autowired @Qualifier @Resourse @Inject @Named 
excerpt: "@Autowired注解"
---

所以你会发现xml的自动装配并没有我们想象的好用。不谈对于一些实例命名的约束下甚至不如按原来的ref属性，而且基本上不会简便代码。实际上我们在实际运用中也不会用这个属性，而更多是采用的注释的方式，因为这种方式更简便。

使用[Spring项目模板文件：spring/spring](https://github.com/Didnelpsun/notes/tree/master/spring/spring)。

注解连线在默认情况下在Spring容器中不打开。因此，在可以使用基于注解的连线之前，我们将需要在我们的Spring配置文件中启用它：`<context:annotation-config/>`

## 自动扫描标签

在此之前，如果我们要使用这些注释应该还要配置好，要使用自动扫描标签来扫描对应的注释，因为它是默认关闭的，如果不开启则注释是不会有作用的。

### &emsp;\<context:annotation-config/>

一般采用的是这个标签，但是如果你加入这个标签后它会报错，说The prefix "context" for element "context:annotation-config" is not bound。

这是因为xml配置文件头部缺少`xmlns:context="http://www.springframework.org/schema/context"`。但是它还是会报错说虽然你配置的很完善但是不知道指向是什么。完整的代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-4.2.xsd">
    <context:annotation-config/>
</beans>
```

我们现在也可以不管它，也可以详细了解一下[注解原理]({% post_url notes/spring/2020-03-28-spring-annotation-principle %})。

我们使用的时候只用在里面加内容就可以了。如果某个类的头上带有特定的注解@Component、@Repository、@Service或@Controller，就会将这个对象作为Bean注入进spring容器。

### &emsp;\<context:component-scan/>

<span style="color:aqua">格式：</span>`<context:component-scan base-package="扫描的包名"/>`。如我的就是\<context:component-scan base-package="org.didnelpsun" />。

我们推荐使用的是这个标签，因为这个标签功能更多。它提供两个子标签：\<context:include-filter>和\<context:exclude-filter>。各代表引入和排除的过滤器。

而\<context:annotation-config/>仅能够在已经在已经注册过的bean上面起作用。对于没有在spring容器中注册的bean，它并不能执行任何操作。但是\<context:component-scan/>自动将带有@component,@service,@Repository等注解的对象注册到spring容器中的功能。  

因为\<context:annotation-config>和\<context:component-scan>同时存在的时候，前者会被忽略。如@autowire，@resource等注入注解只会被注入一次！

&emsp;

## @Autowired

由Spring提供，通过@Autowired的使用可以应用到bean属性的setter方法，非setter方法，构造函数和属性。在使用@Autowired之前，我们对一个bean配置起属性时，是用的`<property name="属性名" value=" 属性值"/>`。当Spring发现@Autowired注解时，将自动在代码上下文中找到与其匹配（默认是类型匹配）的Bean，并自动注入到相应的地方去。

@Autowired注释对在哪里和如何完成自动连接提供了更多的细微的控制。可以在setter方法中被用于自动连接bean，就像@Autowired注释，容器，一个属性或者任意命名的可能带有多个参数的方法。它会默认使用byType类型的识别。

那么我们应该如何使用呢？首先还是将所有文件都恢复到最开始的样子。我们使用`<property name="helloWorld" ref="HelloWorldBean"></property>`来配置依赖，我们当然会想把这个配置给删除，甚至想在xml文件中都不实例化依赖的而不被直接使用的HelloWorld类。

### &emsp;xml混合模式

首先我们根据上面说的xml文件配置中，加入下面的内容：

```xml
<context:component-scan base-package="org.didnelpsun" />
<bean id = "HelloWorldBean" class="org.didnelpsun.test.HelloWorld"/>
<bean id="UserBean" class="org.didnelpsun.test.User"/>
```

请注意这里再也没有说明HelloWorld类和User类两个实例的依赖关系了。因为我们目标输出的实例没有HelloWorld实例，所以也不用获取了。如果你想设置这个实例的words属性值，那么可以在xml中配置：`<property name="words" value="hello"/>`。

```java
//User.java
package org.didnelpsun.test;

import org.springframework.beans.factory.annotation.Autowired;

public class User {
    private static final String username = "Didnelpsun";
    private HelloWorld helloworldAttri;
    @Autowired
    public void setHelloWorld(HelloWorld helloworldPara){
        this.helloworldAttri = helloworldPara;
    }
    public void Say(){
        System.out.println(this.username);
        this.helloworldAttri.SayHello();
    }
}
```

因为我们要依赖的类的setter函数是setHelloWorld，所以就在它上面加上@Autowired。

最后结果：

![output][output]

### &emsp;纯注释模式

在xml与注释混合的模式下，我们可以看到我们使用@Autowired的流程，首先在xml中配置好资源和所有要用到的实例，然后在依赖的类的setter方法上加上@Autowired就可以了。这样我们就不用管哪个类依赖哪个类，而只用把所有的类都实例化就可以了。

但是有时候我们也会觉得很麻烦，我们还要知道哪些类需要用到，如果不用到的类要删掉，那么我们有没有方法不用管哪些类被使用到呢？那就是使用纯注释的方式。即@Autowired与@Compontent以及@CompontentScan混合。

首先我们将xml文件中的配置除了扫描标签的标签都删掉。

然后修改Java文件：

```java
//修改App.java
//删除xml配置取出实例部分
welcomeContext = new AnnotationConfigApplicationContext(App.class);
User Didnelpsun = (User) welcomeContext.getBean(User.class);
HelloWorld hello = (HelloWorld) welcomeContext.getBean(HelloWorld.class);
hello.setWords("Hello");
Didnelpsun.Say();
//User.java
@Component
public class User {
    private static final String username = "Didnelpsun";
    private HelloWorld helloworldAttri;
    @Autowired
    public void setHelloWorld(HelloWorld helloworldPara){
        this.helloworldAttri = helloworldPara;
    }
...
//HelloWorld.java
@Component
public class HelloWorld {
...
```

![output][output]

### &emsp;属性与构造器

我们之前是在setter方法上注释的，而如果我们将注释放在需要注入的属性上也是没有问题的。

```java
//User.java
@Autowired
private HelloWorld helloworldAttri;
//@Autowired
public void setHelloWorld(HelloWorld helloworldPara){
    this.helloworldAttri = helloworldPara;
}
```

比如这样，将setHelloWorld对应的helloworldAttri属性上加上注释也可以。

```java
//User.java
private HelloWorld helloworldAttri;
@Autowired
public User(HelloWorld helloWorldPara){
    helloworldAttri = helloWorldPara;
}
```

构造函数也可以自动连接，不过其实<span style="color:red">构造器下如果你不加注释也可以！</span>这是因为扫描标签会扫描所有实例，然后根据构造器来加入依赖（而setter函数则不会这样做）。但是你要注意加入扫描标签，且在与xml混合模式下要加入依赖实例的标签。

### &emsp;required=false

现在有两种情况：

假如xml文件的bean标签里面有property，而对应的Java文件中里面却去掉了属性的getter/setter，并使用@Autowired注解标注这两个属性会怎么样？答案是Spring会按照xml优先的原则去Java文件中寻找这两个属性的getter/setter，导致的结果就是初始化bean报错。因为xml的配置是在后置处理器前面处理的。如果属性找不到又不想让Spring容器抛出异常，就是显示null，那应该怎么做呢？

又假如你要设置一个类，比如Person类，有一个属性Home来表示房子的住址，但是并不是所有人都有住址，所以这个依赖类并不是必要的，所以我们就想如果我们不传入依赖它也不会报错，我们又应该如何做呢？

默认情况下，@Autowired注释意味着依赖是必须的，它类似于@Required注释，然而，你可以使用@Autowired的（required=false）选项关闭默认行为。

虽然我们可以在我们定义的类上使用这个，但是它还是会报错，我们一般只会在简单类型的属性上使用这个注释方式。

比如我们在User.java中修改：

```java
private static String username;
@Autowired
public void setUsername(String name){
    username = name;
}
```

这时候就会报错，因为你没有传入一个String类型的参数进入。如果你不想将这个参数作为必要传入的参数，可以加上required=false（但是我觉得这个并不是必要，你完全可以不加注释，这样它就不会报错，并且直接输出null）。

&emsp;

## @Named

由JavaEE提供，使用方式和@Qualifier类似。如我们使用Hi类型的Bean就用`@Named("HiBean")`，同理如果使用HelloWorld就用：

```java
@Inject
@Named("HelloWorldBean1")
public void setHelloWorld(HelloWorld sayword){
    say = sayword;
}
```

其实@Name的注释就等价于name属性配置。

&emsp;

## @Qualifier

由Spring提供，可能会有这样一种情况，当你创建多个具有相同类型的bean时，并且想要用一个属性只为它们其中的一个进行装配，在这种情况下，你可以使用@Qualifier注释和@Autowired注释通过指定哪一个真正的bean将会被装配来消除混乱。

### &emsp;纯注释模式

我们首先将原来的HelloWorld变成一个实现接口的类，然后定义一个接口Say，最后再实例化一个Hi类。

```java
//HelloWorld.java
package org.didnelpsun.test;

import org.springframework.stereotype.Component;

@Component
// @Named("HelloWorld")
public class HelloWorld implements Say {
    private String words;
    //重写构造方法
    public HelloWorld(){ System.out.println("HelloWorldClass..."); }
    @Override
    public void setWords(String sayword){ this.words = sayword; }
    @Override
    public void SayWord() { System.out.println(this.words); }
}
```

```java
//Hi.java
package org.didnelpsun.test;

import org.springframework.stereotype.Component;

@Component
// @Named("Hi")
public class Hi implements Say {
    private String words;
    //重写构造方法
    public Hi(){
        System.out.println("HiClass...");
    }
    @Override
    public void setWords(String sayword){
        this.words = sayword;
    }
    @Override
    public void SayWord() {
        System.out.println(this.words);
    }
}
```

```java
//Say.java
package org.didnelpsun.test;

public interface Say {
    public String words = null;
    abstract public void setWords(String words);
    public void SayWord();
}
```

```java
//User.java
package org.didnelpsun.test;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class User {
    private static final String username = "Didnelpsun";
    private Say say;
    @Autowired
    // @Qualifier("HelloWorld")
    public void setHelloWorld(Say sayword){
        say = sayword;
    }
    public void Say(){
        System.out.println(this.username);
        say.SayWord();
    }
}
```

```java
//App.java
package org.didnelpsun;
//项目入口
import org.didnelpsun.test.HelloWorld;
import org.didnelpsun.test.Hi;
import org.didnelpsun.test.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.support.ClassPathXmlApplicationContext;

@ComponentScan
public class App
{
    //获取私有属性，这个属性是应用文档属性
    private static ApplicationContext welcomeContext;
    public static void main(String args[]){
        welcomeContext = new AnnotationConfigApplicationContext(App.class);
        User Didnelpsun = (User) welcomeContext.getBean(User.class);
        HelloWorld hello = (HelloWorld) welcomeContext.getBean(HelloWorld.class);
        hello.setWords("Hello");
        Hi hi = (Hi)welcomeContext.getBean(Hi.class);
        hi.setWords("Hi");
        Didnelpsun.Say();
    }
}
```

请注意我们没有将接口也注释为@Component，因为它不会是一个实例，所以没必要注释到。我们在App.java中将两个Say实例都已经配置好了。而这时User.java中的Say sayword参数传入依赖会报错：Could not autowire. There is more than one bean of 'Say' type.Beans:helloWorld(HelloWorld.java) hi(Hi.java)。

我们只用在这里加上@Qualifier("HelloWorld")标注要注入的实例是HelloWorld，然后再将两个Say实例分别加上@Named("HelloWorld")和@Named("Hi")给它们取好名字就可以了。如我在上面注释掉的那几行。

### &emsp;xml混合模式

我们可能会觉得上面这个例子和我们想象的不太一样。我们修改成原来的样子：

```xml
<context:component-scan base-package="org.didnelpsun" />
<bean id = "HelloWorldBean" class="org.didnelpsun.test.HelloWorld">
    <property name="words" value="hello"/>
</bean>
<bean id="UserBean" class="org.didnelpsun.test.User">
</bean>
```

将所有的@Qualifier和@ComponentScan、@Component删除：

```java
//App.java
welcomeContext = new ClassPathXmlApplicationContext("SpringBeans.xml");
User Didnelpsun = (User) welcomeContext.getBean("UserBean");
Didnelpsun.Say();
```

```java
//User.java
private static final String username = "Didnelpsun";
private Say say;
@Autowired
public void setHelloWorld(Say sayword){say = sayword;}
public void Say(){
    System.out.println(this.username);
    say.SayWord();
}
```

这样运行是没有问题，而如果我们实例两个HelloWorld类的bean呢？

```xml
<bean id = "HelloWorldBean1" class="org.didnelpsun.test.HelloWorld">
    <property name="words" value="hello1"/>
</bean>
<bean id = "HelloWorldBean2" class="org.didnelpsun.test.HelloWorld">
    <property name="words" value="hello2"/>
</bean>
```

同样会注入出问题，应该使用@Qualifier来标注到底注入哪个实例：`@Qualifier("HelloWorldBean1")`，位置就是@Autowired下面。这样就没有问题了。

### &emsp;单独使用@Qualifier

我们上面也已经使用过了@Qualifier注解，但是我们可以注意到，我们使用这个注解都是伴随着@Autowired注解一同使用的。因为在给类成员注入时不能单独使用，必须配合@Autowired，但是注入方法参数时可以单独使用。

```java
@Autowired
public void setHelloWorld(@Qualifier("HelloWorld") HelloWorld sayword){
    say = sayword;
}
```

但是这个单独使用不是说就不用@Autowired了，而是指将他们两个注释分开，将@Qualifier放到方法参数前。

&emsp;

## @Resource

由JavaEE提供，是JSR250中的规范，你可以在字段中或者setter方法中使用@Resource注释，它和在Java EE5中的运作是一样的。基本和@Autowired用法一致。通过这个注释也可以取代@Autowired和@Qualifier很多用法。

1. @Resource后面没有任何内容，默认通过name属性去匹配bean，找不到再按type去匹配。
2. 指定了name或者type则根据指定的类型去匹配bean。
3. 指定了name和type则根据指定的name和type去匹配bean，任何一个不匹配都会报错。

我们将使用上面定义的三个实例HelloWorldBean1、HelloWorldBean2、HiBean来判断。

### &emsp;1. @Resource默认

如果我们使用@Resource而不加任何参数，它会默认按照依赖参数的类型名来匹配。如参数是Hi sayword，则它匹配的是Hi，而如果是HelloWorld类型，则会报错，因为有两个实例，同样Say也会报错，因为有三个实例。

### &emsp;2. type属性

如果我们传入的参数是Hi类型，那么如果使用type属性来配置又应该如何做呢？应该<span style="color:aqua">格式：</span>`@Resource(type = 类完整路径.class)`，如我的就是`@Resource(type = org.didnelpsun.test.Hi.class)`。这样没有问题。

同样如果我们使用的是@Resource(type = org.didnelpsun.test.HelloWorld.class)它还是会报错，因为有两个实例。

### &emsp;3. description属性

而我们使用description属性，和上面的使用方式是差不多的，但是这个路径将以字符串的形式来呈现，并使用的是相对路径，后面也没有class后缀。<span style="color:aqua">格式：</span>`@Resource(description = "相对路径")`，如我的是`@Resource(description = "Hi")`。

不过从这里我们会发现，如果我们的参数配置出错，它是会默认以默认方式来配置的，比如我们这里，将参数类保持为Hi，但是改成@Resource(description = "HelloWorldBean1")或者@Resource(description = "HelloWorld")，它是不会报错的。这是比较智能的一点。

### &emsp;4. name属性

而上面几个都不能解决我们多个同类实例的问题，我们应该如何做呢？使用name属性，<span style="color:aqua">格式：</span>`@Resource(name = "实例名")`，我的是`@Resource(name = "HelloWorldBean1")`，这样就可以使用了。

&emsp;

## @Inject

由JavaEE提供，JSR330的规范，默认是按照类型匹配。如果要按照名字来匹配需要使用@Named。需要导入javax.inject.Inject的jar包 ，才能实现注入。

因为JRE无法决定构造方法注入的优先级，所以规范中规定类中只能有一个构造方法带@Inject注解。

如果是字段上注释不能是final类型，而在方法上注释不能是抽象方法。

你会在[Maven中心仓库](https://mvnrepository.com/artifact/org.glassfish.hk2.external/javax.inject)找到。点击对应的版本，并找到maven就可以得到代码：

```xml
<!-- https://mvnrepository.com/artifact/org.glassfish.hk2.external/javax.inject -->
<dependency>
    <groupId>org.glassfish.hk2.external</groupId>
    <artifactId>javax.inject</artifactId>
    <version>2.5.0-b62</version>
</dependency>
```

然后粘贴到pom.xml重新导入就可以了。

因为是按照依赖参数类型来匹配的，所以如果传入的参数类型是Hi类型就不会报错，因为Hi类型的实例就只有一个，而HelloWorld类型有两个。这时候你就需要@Name的注释了。

&emsp;

## 总结

### &emsp;自动装配注释使用位置

1. 属性字段域
2. 构造方法
3. 设值函数

属性字段域就是我们要注入的相关属性，就比如我们之前的HelloWorld hello。我们就可以把它改成`@Autowired private HelloWorld hello;`。请注意因为是依赖注入，所以这个属性字段一定不能是final类型。且构造方法一定是非抽象的。

+ 从来源：
1️. @Autowired是Spring自带的，通过AutowiredAnnotationBeanPostProcessor类实现的依赖注入，Spring属于第三方的。
2️. @Resource是JSR250规范的实现，J2EE的注解，在javax.annotation包下，根据导入注解的包名就可以知道。J2EE是Java自己的东西。因此，建议使用@Resource注解，以减少代码和Spring之间的耦合。
3️. @Inject是JSR330规范实现的，需要导入javax.inject.Inject jar包 ，才能实现注入。
+ 从作用域：
1️. @Autowired可以作用在constructor、method、parameter、field、annotation_type上。
2️. @Resource可以作用method、field、type上。
3️. @Inject可以作用constructor、method、field上。
+ 从默认装配方法：
1️. @Autowired默认按照byType方式进行bean匹配。
2️. @Resource默认根据属性名称(byName)方式进行自动装配。如果有多个类型一样的Bean候选者，则可以通过name指定进行注入。
3️. @Inject是根据类型进行自动装配的，如果需要按名称进行装配，则需要配合@Named。
+ @Autowired如果有多个类型一样的Bean候选者，需要指定按照名称(byName)进行装配，则需要配合@Qualifier。指定名称后，如果Spring IOC容器中没有对应的组件bean抛出NoSuchBeanDefinitionException。也可以将@Autowired中required配置为false，当找不到相应bean的时候，系统不会抛异常。
+ @Autowired、@Inject用法基本一样，不同的是@Inject没有一个request属性。

[自动装配的文件：spring/annotation](https://github.com/Didnelpsun/notes/tree/master/spring/annotation)

[output]:data:image/png;base64,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
