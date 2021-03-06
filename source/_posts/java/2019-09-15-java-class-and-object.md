---
layout: post
title:  "类与对象"
date:   2019-09-15 20:17:31 +0800
categories: notes java base
tags: Java 基础 类 成员 构造 this 静态 final 对象 包 不定长参数
excerpt: "类、对象、包与参数"
---

## 类

面向对象程序设计有三个特征：封装：将对象的属性和行为封装起来使用户只能使用，而很难操作类内部的数据结构，就避免了外部操作对内部操作的影响；继承：子类继承父类所共有的属性和方法，并具有自己独有的方法；多态：定义抽象类只定义规范而不定义具体操作方式。

### &emsp;1. 成员与变量

#### &emsp;&emsp;1.1 成员变量

是对象的属性，在类体中定义，在整个类中都有效。  

1. 成员变量定义在类中，在整个类中都可以被访问。
2. 成员变量随着对象的建立而建立，随着对象的消失而消失，存在于对象所在的堆内存中。  
3. 成员变量有默认初始化值。

成员变量仅使用一般的变量声明定义，仅能在本类中使用，类外不能使用。

#### &emsp;&emsp;1.2 成员方法

是类对象的行为。定义全局方法

<span style="color:aqua">格式：</span>

```java
权限修饰符 返回值类型 方法名(参数类型 参数名){  
    方法体  
    return 返回值;  
}
```

一个成员方法可以有参数，参数可以是对象也可以是基本数据类型，成员方法也可以有返回值也可以不返回，如果需要返回就return返回值，如果无返回值那方法就是void类型。

在成员方法中可以调用其他成员方法和类成员变量。

如果一个方法中含有与成员变量同名的局部变量，那么该方法的访问是针对局部变量而不是类成员变量。

类成员变量和成员方法可以统称为类成员。

#### &emsp;&emsp;1.3 权限修饰符

权限修饰符控制着对类和类的成员变量和成员方法的访问：

1. `friendly`默认缺省权限，即不写权限，表示友好变量和友好方法，同一个包的类中，可以访问友好成员变量和友好方法。
2. `private`仅对于本类是可见的，其他任何类都无法访问。  
3. `protected`对于本类可见，专为继承设计，同一个包或为其子类，就是public权限，而其他包的非子类都是等于private权限。  
4. `public`对于本类、同包的类以及其他包的类都是可见的。

如果没有权限修饰符修饰，就默认为包存取范围，即只有一个包中的类可以调用这个类的成员变量或成员方法。  

如果类的权限修饰符与类成员的权限修饰符不同，则全部依照类的权限修饰符。  

一般是使用public和private。

#### &emsp;&emsp;1.4 局部变量

在类的方法即成员方法中定义，仅能在成员方法中使用。  

1. 局部变量只定义在局部范围内，如：函数内，语句内等，只在所属的区域有效。
2. 局部变量存在于栈内存中，作用的范围结束，变量空间会自动释放。
3. 局部变量没有默认初始化值。局部变量使用时必须赋值或初始化（因为没有默认初始化），否则会编译错误。  

由于局部变量仅在方法使用中有效，所以局部变量使用结束后在互不嵌套的区域可以重新声明一个新的重名的局部变量。互相嵌套的区域则不允许。

#### &emsp;&emsp;1.5 this关键字

`this`代表本类对象实例的引用，一般用于隐式引用对象的成员变量与方法。

在局部变量或者方法参数覆盖了成员变量时就需要`this`来代表类成员。如：`this.name = name;`
this也可以作为对象来返回：`return this;`

### &emsp;2. 构造类

类中除了成员方法外，还有用来创建实例对象的构造方法，每当类实例一个对象时都会自动调用该方法。特点：

1、构造方法没有返回值 （普通没有返回值的方法需要使用public void 方法名()的方式来定义，但是构造方法不需要`void`来修饰）2、构造方法的名称与本类的名称相同。  

构造方法<span style="color:aqua">格式：</span>

```java
权限修饰符 class 构造方法名称(参数){  
    方法体  
}
```

在构造方法中可以为成员变量赋值，这样当实例化一个本类的对象时，相应的成员变量也将被初始化。如果类中没有明确定义构造函数，编译器会自动创建一个不带参数的默认构造方法。所以一种是自己定义的构造方法，一种是编译器默认生成的无参构造方法。  
如：  

```java
public class CreateObject{
    public CreateObject(){ //构造方法
        System.out.println("创建对象");
    }
    public static void main(String[] args){ //主方法
        new CreateObject(); //创建对象
    }
}
```

#### &emsp;&emsp;2.1 无参构造类

默认构造函数根据类型为对象提供默认值，如：0，null等。

```java
class Student {
    int id;
    String name;

    void display() {
        System.out.println(id + " " + name);
    }

    public static void main(String args[]) {
        Student s1 = new Student3();
        Student s2 = new Student3();
        s1.display();
        s2.display();
    }
}
>>>>>
0 null
0 null
```

#### &emsp;&emsp;2.2 有参构造类

带有参数的构造函数能动态创建对象：

```java
class Student {
    int id;
    String name;

    Student(int i, String n) {
        id = i;
        name = n;
    }

    void display() {
        System.out.println(id + " " + name);
    }

    public static void main(String args[]) {
        Student s1 = new Student(111, "Karan");
        Student s2 = new Student(222, "Aryan");
        s1.display();
        s2.display();
    }
}
>>>>>
111 Karan
222 Aryan
```

#### &emsp;&emsp;2.3 构造函数重载

构造方法重载是Java中的一种技术，一个类可以有任何数量的参数列表不同的构造函数。编译器通过构造函数参数列表中的参数数量及其类型来区分这些构造函数。

构造函数重载示例:

```java
class Student {
    int id;
    String name;
    int age;

    Student(int i, String n) {
        id = i;
        name = n;
    }

    Student(int i, String n, int a) {
        id = i;
        name = n;
        age = a;
    }

    void display() {
        System.out.println(id + " " + name + " " + age);
    }

    public static void main(String args[]) {
        Student s1 = new Student(111, "Karan");
        Student s2 = new Student(222, "Aryan", 25);
        s1.display();
        s2.display();
    }
}
>>>>>
111 Karan 0
222 Aryan 25
```

### &emsp;3.静态变量和方法

`static`： 关键字，是一个修饰符，用于修饰成员(成员变量和成员函数)。被声明为`static`的变量、常数、方法被称为静态成员。被类所有，所以在本类或者其他类中可以调用静态成员。

静态即一直存在。

方便在没有创建对象的情况下来进行调用（方法/变量）。就是没有`this`的方法，不能调用非静态方法。  

被`static`关键字修饰的方法或者变量不需要依赖于对象来进行访问，只要类被加载了，就可以通过类名去进行访问。

特点：  

1. 想要实现对象中的共性数据的对象共享。可以将这个数据进行静态修饰。
2. 被静态修饰的成员，可以直接被类名所调用。也就是说，静态的成员多了一种调用方式。类名.静态方式。
3. 静态随着类的加载而加载。而且优先于对象存在。

通过`static`修饰的成员为静态成员，且Java的static只能修饰类的成员而不能修饰局部变量。因为局部变量使用过就会自然地被销毁而不能被保留。

静态成员不属于任何一个类的对象，因为它不保存在对象的内存空间中，而保存在类的公共区域，所以任何一个对象都可以直接访问该类的静态成员，获取的都是同一个值，修改也是在公共区域修改。

无论一个类生成了多少对象，所有对象都共同使用一份静态成员。

<span style="color:aqua">格式：</span>`类名.静态类成员。`  

可以使用对象.静态成员来调用，但是一般不建议使用该形式，因为容易混淆静态成员和非静态成员。所以虽然实例对象可以寻找到类共有的成员，但是还是建议用类名调用。

#### &emsp;&emsp;3.1静态变量

静态变量：也叫为类变量，由static修饰的变量称为静态变量，其实质上就是一个全局变量，可以跨类使用，甚至在整个程序都可以使用，以`类名.静态变量名`的方式。

如果某个内容是被所有对象所共享，那么该内容就应该用静态修饰；没有被静态修饰的内容，其实是属于对象的特殊描述。不同的对象的实例变量将被分配不同的内存空间， 如果类中的成员变量有类变量，那么所有对象的这个类变量都分配给相同的一处内存，改变其中一个对象的这个类变量会影响其他对象的这个类变量，也就是说对象共享类变量。  

实例变量和类变量的区别：

1. 两个变量的生命周期不同：  
      实例变量随着实例的创建而存在，随着实例的回收而释放。  
      静态变量随着类的加载而存在，随着类的消失而消失。  

2. 调用方式不同：  
      实例变量只能被对象调用。  
      静态变量可以被对象调用，还可以被类调用。  

3. 数据存储位置不同：  
      实例变量存储在堆内存的对象中，所以也叫对象的特有数据。  
      静态变量数据存储在方法区（共享数据区）的静态区，所以也叫对象的共享数据。

#### &emsp;&emsp;3.2静态方法

`static`方法一般称作静态方法，由于静态方法不依赖于任何对象就可以进行访问，因此对于静态方法来说，是没有`this`的，因为它不依附于任何对象，既然都没有对象，就谈不上`this`了，所以在静态方法中使用`this`会报错。并且由于这个特性，在静态方法中不能访问类的非静态成员变量和非静态成员方法，因为非静态成员方法/变量都是必须依赖具体的对象才能够被调用。

<span style="color:orange">注意：</span>虽然在静态方法中不能访问非静态成员方法和非静态成员变量，但是在非静态成员方法中是可以访问静态成员方法/变量的。

### &emsp;4.final关键字

final代表不可改变，只能赋值一次。

#### &emsp;&emsp;4.1final参数

如果声明一个final参数，就代表其参数不能被改变。也就是说传入的参数只能被读取，不能被修改。

#### &emsp;&emsp;4.2final方法

父类中的`final`方法可以被子类继承，但是不能被子类重写。同时`final`方法执行等级高于非`final`方法。

声明`final`方法的主要目的是防止该方法的内容被修改。

已知如果一个父类的一个方法被设置为`private`权限，那么子类无法访问该方法。既然无法访问，那就更无法修改，所以一个定义为`private`的方法隐式地指定为`final`权限。

如下所示，使用`final`修饰符声明方法。

```java
public class Test{
    public final void changeName(){
       // 方法体
    }
}
```

#### &emsp;&emsp;4.3final属性

这个变量就是之前说的常量，在声明的时候就必须被赋值，且不可被修改。这个属性可以被继承，但是不可以被子类重写。

#### &emsp;&emsp;4.4final类

final类不能被继承，没有类能够继承final类的任何特性，不可以有子类，只可以被使用。

```java
public final class Test {
   // 类体
}
```

final类的属性可以是final的也可以是其他的权限，也就是说对于非final权限的属性是可以被更改值的，而方法是默认final的。

&emsp;

## 对象

### &emsp;1. 创建对象

对象就是类构造的实例，通过`new`关键字构造。（Java中对象和实例可以互换通用）
  
<span style="color:aqua">格式：</span>`类名 实例名 = new 构造函数名(参数)`  

由于类名和构造函数名是要保持一致的，所以可以这样理解：`类名 实例名 = new 类名(参数)`

每个对象都是相互独立的。且每个对象都有自己的生命周期，当生命周期结束对象就变成垃圾被回收。

创建对象的同时会自动调用构造方法中的代码。如：`Person person1 = new Person();`会自动调用Person构造方法来创建实例对象。

### &emsp;2. 访问对象

当使用new创建一个对象后，可以使用 对象名.类成员名 来获取对象的属性或行为。

实际上创建对象时的类名 实例名的实例名是表示对类的引用，该实例名并不会保存一个对象，而是保存一个对象地址，虽然引用与对象是不同的，但是也可以忽略该不同，虽然person1是包含Person类的一个引用，但是可以说是person1是Person的对象。

### &emsp;3. 比较对象

比较对象可以使用两种方式：

1. ==：比较他们两个变量所指对象是不是同一个，实际比较的是对象的地址，仅有将已创建的对象（其实是地址）赋值给另一个变量，这两个变量使用==比较才相等。
2. `equals()`：是`String()`类的方法，用来比较两个对象的内容是否相同，而不是地址相同。

判断两个变量所指对象是否是同样一个对象，只需要查看是否创建新的对象。

### &emsp;4. 销毁对象

在许多方面，Java类似于C++。Java的语法非常类似于C++，Java有类、方法和数据成员；Java的类有构造函数；Java有异常处理。  

但是，如果你使用过C++会发现Java也丢掉一些可能是你熟悉的特性。这些特性之一就是析构函数。取代使用析构函数，Java支持`finalize()`方法。  

与Java不同，C++支持局部对象（基于栈）和全局对象（基于堆）。因为这一双重支持，C++ 也提供了自动构造和析构，这导致了对构造函数和析构函数的调用，（对于堆对象）就是内存的分配和释放。  

在Java中，所有对象都驻留在堆内存，因此局部对象就不存在。结果，Java的设计者觉得不需要析构函数。  

取而代之，Java定义了一个特殊的方法叫做`finalize()`，它提供了C++析构函数的一些功能。但是，`finalize()`并不完全与C++的析构函数一样，并可以假设它会导致一系列的问题。`finalize()`方法作用的一个关键元素是Java的垃圾回收器。

每个对象的生命周期结束后都会变成垃圾，然后被Java的垃圾回收机制回收，而不需要手动去回收（如Javascript中一定要将对象赋值为`null`）  

Java中什么样的对象会称为垃圾：

1. 对象引用超过其范围  
2. 对象被赋值为null

垃圾回收器只能回收那些`new`关键字创建的对象，如果对象不是通过`new`关键字创建的而是通过其他方式获得内存，那是不会被回收的。所以提供了`Object`类的方法`finalize()`方法，是`protected`权限的，可以在自己的类中定义这个方法，如果在类中定义了该方法，那垃圾回收会首先调用该方法，下一次垃圾回收发生时会被回收。  

然而垃圾回收或者`finalize()`方法不一定会发生，如果虚拟机内存不足不会执行。  

所以最后Java提供了强制启动垃圾回收器方法`System.gc()`方法强制垃圾回收器来释放这些对象的内存。

&emsp;

## 类包

在Java中定义一个类后通过编译就会产生一个.class字节码文件，如果不断扩大程序，很可能会出现命名冲突的问题。所以产生了类包。  

Java中每个接口或者类都来自不同的类包。

### &emsp;1. 类路径

如`Math`类完整的路径就是`java.lang.Math`。一个完整的类名应该是包名和类名的组合。  

同一个包中的类相互访问可以不用指定包名。  

同一个包名中的类可以不必放在同一个位置，只用将CLASSPATH分别指向其具体的位置就行了。

### &emsp;2. 创建包

<span style="color:aqua">格式：</span>`package 包名;`  

在类中指定包名时需要将`package`表达式放在程序第一行，且必须是非注释的。当使用`package`关键字为类指定包名后，包名会成为类名中的一部分，所以这个类必须指定全名。  

<span style="color:orange">注意：</span>Java包命名必须全部使用小写字母。  

在网络共享的包，往往使用创建者的Internet域名的反序作为包名，因为域名独一无二。

### &emsp;3. 导入包（import关键字）

<span style="color:aqua">格式：</span>`import 包名;`  

可以使用：`import 包名.*;`来导入包中所有的类。  

如`import java.utils.Date`，我们很奇怪这个Date为什么是大写的，不是路径都要小写吗？准确的更形象的来说对于文件夹名是应该小写的，包含类的文件是应该大写的，Date是文件名而非包名，要使用的类在这个文件中。

如果不同的类包中有相同名字的类，那么使用就必须带有全名。  

在程序中添加`import`关键字时，就开始在CLASSPATH指定的目录中查找符合的.class文件。当`import`导入一个包中所有类时，不会导入这个包子包的类，如果需要就要单独引用子包。

### &emsp;4. 导入静态成员（import static关键字）

<span style="color:aqua">格式：</span>`import static 静态成员/类包;`  

直接导入包中静态的方法，不需要实例化。静态导入可以使被导入类的所有静态变量和方法在当前类直接可见，使用这些静态成员无需再写出类名。

```java
import static java.lang.Math.*;
public class StaticMath{
    public static void main(String[] args) l
    //直接访问Math的random()方法
    int i = (int) (random()*10);
    System.out.println(i) ;
    //直接访问PI变量
    System.out.println(PI) ;
}
```

&emsp;

## 不定长参数

<span style="color:aqua">格式：</span>`参数数据类型...参数名称`  等价于 `参数数据类型 参数名称[]`  

如int...a等价于int a[];  

```java
public static int add(int...a){
    int s=0;
    for(int i=0,i<a.length；i++)
    s+=a[i];
    return s;
}
```

定义不定长参数仍然是可以作为方法的重载。且使用方法类似数组。
