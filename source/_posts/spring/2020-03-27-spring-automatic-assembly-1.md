---
layout: post
title: "自动装配（属性）"
date: 2020-03-27 10:57:02 +0800
categories: notes spring base
tags: Spring 基础 autowire byName byType constructor default
excerpt: "autowire属性"
---

依赖注入和自动装配息息相关。依赖注入的本质就是装配，装配是依赖注入的具体行为。

依赖注入有两种形式：构造器注入和setter注入。也就是我们在xml中写的一堆\<bean></bean>，我们每一个依赖bean就要写一个实例，如果bean太多我们还这样写就会非常麻烦，更何况我们还有把有关联的bean装配起来，一旦bean很多，就不好维护了。

为此Spring使用自动装配解决这个问题，开发人员不用关心具体装配哪个bean的引用，识别工作由Spring来完成，因此一般配有自动监测来和自动装配配合完成。自动装配其实就是将依赖注入"自动化"的一个简化配置的操作。也就是说我们只用实例化我们在应用程序中要使用的实例，而它依赖的实例将由Spring来实例化，如之前一直在使用的HelloWorld类。

自动配置有两大类方式，跟之前的依赖注入和实例化一样，一种是xml，一种是注解。

xml的\<bean>配置中通添加autowire属性实现四种形式的自动装配，byName, byType, constructor, autodetect。byName就是会将与属性的名字一样的bean进行装配。byType就同属性一样类型的bean进行装配。constructor就是通过构造器来将类型与参数相同的bean进行装配。autodetect是constructor与byType的组合，会先进行constructor，如果不成功，再进行byType。

Spring2.5之后提供了注解方式的自动装配。但是要使用这些注解，需要在配置文件中配置<context:annotation-config />。只有加上这一配置，才可以使用注解进行自动装配，默认情况下基于注解的装配是被禁用的。常用的自动装配注解有以下几种：@Autowired，@Resource，@Inject，@Qualifier，@Named。

<span style="color:orange">注意：</span>@Autowired注解是byType类型的，因此会将接口的实现类取代接口，自动装配给控制类。

不过自动装配也有比较大的问题：

当自动装配始终在同一个项目中使用时，它的效果最好。如果通常不使用自动装配，它可能会使开发人员混淆的使用它来连接只有一个或两个bean定义。不过，自动装配可以显著减少需要指定的属性或构造器参数，但你应该在使用它们之前考虑到自动装配的局限性和缺点。

限制|描述
:--:|:--
重写的可能性|你可以使用总是重写自动装配的\<constructor-arg>和\<property>设置来指定依赖关系。
原始数据类型|你不能自动装配所谓的简单类型包括基本类型，字符串和类。
混乱的本质|自动装配不如显式装配精确，所以如果可能的话尽可能使用显式装配。

[Spring项目模板文件：spring/spring](https://github.com/Didnelpsun/notes/tree/master/spring/spring)来搭建。

我们要创建一个实例依赖的案例，然后将参数和属性更改：

```java
//HelloWorld.java
package org.didnelpsun.test;

public class HelloWorld {
    private String words;
    //重写构造方法
    public HelloWorld(){
        System.out.println("HelloWorldClass...");
    }
    public void setWords(String sayword){
        this.words = sayword;
    }
    //动作方法
    public void SayHello(){
        System.out.println(this.words);
    }
}
```

我们的HelloWorld是简单的类，含有一个私有变量，当构造这个实例会打印字符串。然后使用设置函数初始化实例，这个setter函数会接受一个参数，然后下面的SayHello方法会打印这个方法。

```java
//User.java
package org.didnelpsun.test;

public class User {
    private static final String username = "Didnelpsun";
    private HelloWorld helloworldAttri;
    public void setHelloWorld(HelloWorld helloworldPara){
        this.helloworldAttri = helloworldPara;
    }
    public void Say(){
        System.out.println(this.username);
        this.helloworldAttri.SayHello();
    }
}
```

然后在User类中定义一个私有常量和一个HelloWorld类型的属性，这里的setter函数也要传入一个HelloWorld类型的参数，<span style="color:orange">User依赖HelloWorld</span>。然后有一个Say方法，打印属性并调用HelloWorld实例的方法。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id = "HelloWorldBean" class="org.didnelpsun.test.HelloWorld">
    </bean>
    <bean id="UserBean" class="org.didnelpsun.test.User">
        <property name="helloWorld" ref="HelloWorldBean"></property>
    </bean>
</beans>
```

定义两个bean一个是HelloWorld一个是User，如果我们要实现依赖，那么我们要使用ref属性来配置。

```java
//App.java
package org.didnelpsun;
//项目入口
import org.didnelpsun.test.HelloWorld;
import org.didnelpsun.test.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App
{
    //获取私有属性，这个属性是应用文档属性
    private static ApplicationContext welcomeContext;
    public static void main(String args[]){
        welcomeContext = new ClassPathXmlApplicationContext("SpringBeans.xml");
        HelloWorld hello = (HelloWorld) welcomeContext.getBean("HelloWorldBean");
        hello.setWords("welcome!");
        //或者在xml中配置<property name="words" value="hello"/>
        User Didenelpsun = (User) welcomeContext.getBean("UserBean");
        Didnelpsun.Say();
    }
}
```

我们也可以不在App.java中实例化这个HelloWorld类。这些是不影响对应的依赖的。如果我们不在xml中配置依赖，也就是删除`<property name="helloWorld" ref="HelloWorldBean"></property>`，然后再加上`Didenelpsun.setHelloWorld("hello");`也可以配置成功依赖。但是你要注意这是因为在应用程序中配置的，所以这种实例的依赖不是由Spring容器控制的，而是由应用程序控制的，所以你需要关注这些依赖的创建和销毁，当然这是不被建议使用的。

## autowire属性

名称|说明
:--:|:--
no|这是默认的设置，它意味着没有自动装配，你应该使用显式的bean引用来连线。你不用为了连线做特殊的事。在依赖注入章节你已经看到这个了。
byName|由属性名自动装配。Spring容器看到在XML配置文件中bean的自动装配的属性设置为byName。然后尝试匹配，并且将它的属性与在配置文件中被定义为相同名称的beans的属性进行连接。
byType|由属性数据类型自动装配。Spring容器看到在XML配置文件中bean的自动装配的属性设置为byType。然后如果它的类型匹配配置文件中的一个确切的 bean 名称，它将尝试匹配和连接属性的类型。如果存在不止一个这样的bean，则一个致命的异常将会被抛出。
constructor|类似于byType，但该类型适用于构造函数参数类型。如果在容器中没有一个构造函数参数类型的bean，则一个致命错误将会发生。
autodetect|Spring首先尝试通过constructor使用自动装配来连接，如果它不执行，Spring尝试通过byType来自动装配。

可以使用byType或者constructor自动装配模式来连接数组和其他类型的集合。

但是实际上这种xml配置并不会经常使用！因为配起来和一般的方法一样，且有一定的限制！

### &emsp;1. byName

按定义来说这种模式由属性名称指定自动装配。Spring容器看作beans，在XML配置文件中beans的auto-wire属性设置为byName。然后，它尝试将它的<span style="color:red">属性类名，即id或name</span>与配置文件中定义为相同名称的beans进行匹配和连接。如果找到匹配项，它将注入这些beans，否则，它将抛出异常。

针对上面的例子，我们应该如何改动呢？注意这个属性值为byName是依赖名字来查找对应的实例的。xml中将参数的ref那行删除，然后改成：`<bean id="User" class="org.didnelpsun.test.User" autowire="byName">`，并且一定要注意改变<span style="color:orange">实例名字</span>！`<bean id = "helloWorld" class="org.didnelpsun.test.HelloWorld">`，因为我们依赖的类为HelloWorld，所以它会找实例名为helloWorld的实例，即将依赖类名的首字母小写，与property属性的ref属性值一样。并且应该在App.java中更改实例的名字来获取实例，并可以来更改被依赖的实例的相关参数。这是一个规范。所以说上面说的按id或者name来匹配是实际上不成立的！它只会按照依赖类名驼峰式名字来匹配！

```xml
<bean id = "helloWorld" class="org.didnelpsun.test.HelloWorld">
    <property name="words" value="hello"/>
</bean>
<bean id="UserBean" class="org.didnelpsun.test.User" autowire="byName">
<!--        <property name="helloWorld" ref="helloWorld"/>-->
</bean>
<!-- <bean id = "helloworldAttri" class="org.didnelpsun.test.HelloWorld">
    <property name="words" value="one"/>
</bean> -->
```

虽然下面的实例的bean是依赖的属性名，但是它是不会匹配到这个属性的，而只会匹配最上面的。重新证明了这个按照name或者id来匹配是不对的。实际上也是按照依赖的class来匹配。

### &emsp;2. byType

定义说这种模式由属性类型指定自动装配。Spring容器看作beans，在XML配置文件中beans的autowire属性设置为byType。然后，如果它的<span style="color:red">类名，即class</span>恰好与配置文件中beans类型名称中的一个相匹配，它将尝试匹配和连接它的属性。如果找到匹配项，它将注入这些beans，否则，它将抛出异常。

如果你将上面的byName属性变为byType属性，那么也可以运行。原因上面已经说了。但是你要注意了，如果你也加入了helloworldAttri这个实例，那么它会报错，因为byType只能拥有一个类的实例，如果有多个同一个类的实例，那么它就不知道找哪个实例了，无论实例名是什么它都会报错。

### &emsp;3. constructor

这种模式与byType非常相似，但它应用于构造器参数。Spring容器看作beans，在XML配置文件中beans的autowire属性设置为constructor。然后，它尝试把它的构造函数的参数与配置文件中beans名称中的一个进行匹配和连线。如果找到匹配项，它会注入这些bean，否则，它会抛出异常。即根据构造方法的参数的数据类型，进行byType模式的自动装配。

```xml
<bean id = "HelloWorldBean" class="org.didnelpsun.test.HelloWorld">
    <property name="words" value="two"/>
</bean>
<bean id="UserBean" class="org.didnelpsun.test.User" autowire="constructor">
<!--<property name="helloWorld" ref="helloWorld"/>-->
</bean>
```

这种情况下实例叫什么都无所谓，因为它是按照class来识别。

```java
public User(HelloWorld helloworldPara){
    helloworldAttri = helloworldPara;
}
```

同时你要注意，如果你在类中加入了构造函数，那么无论你使用什么方式来实例化这个类，你都需要先用构造函数来构造这个实例，都需要先满足构造函数所需要的参数，否则会报错：警告: Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'UserBean' defined in class path resource \[SpringBeans.xml]: Instantiation of bean failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate \[org.didnelpsun.test.User]: No default constructor found; nested exception is java.lang.NoSuchMethodException: org.didnelpsun.test.User.\<init>()

### &emsp;4. default

由上级标签\<beans>的default-autowire属性确定。在配置bean时，\<bean>标签中Autowire属性的优先级比其上级标签高，即是说，如果在上级标签中定义default-autowire属性为byName，而在\<bean>中定义为byType时，Spring IoC容器会优先使用\<bean>标签的配置。
