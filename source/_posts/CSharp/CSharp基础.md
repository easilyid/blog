---
title: CSharp基础
date: 2021-12-23 20:02:18
author: Heart
categories: CSharp
tags:
  - 编程语言
  - CSharp
  - 面对对象编程
index_img: https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/20211223200531.png
excerpt: 面对对象编程 封装 继承 多态
hide: true
---

## C#基础

### 一.面向对象编程基础概念

面向对象编程：万物皆对象；用程序来抽象(形容)对象；用面向对象的思想来编程。

面向对象编程三大特性：封装、继承、多态

特性总结：

①封装：用程序语言来形容对象

②继承：复用封装对象的代码；儿子继承父亲，复用现成代码

③多态：同样行为的不同表现，儿子继承父亲染色体Y的基因，但是具有与父亲不一样的行为表现

面向对象编程七大原则：开闭原则、依赖倒转原则、里氏替换原则、单一职责原则、接口隔离原则、合成复用原则、迪米特法则



### 二.面向对象三大特性——封装

#### 2.1访问修饰符

Public 公开的

Protected 对继承的子类公开

Private 私有的



#### 2.2成员变量的封装

在类的内部，声明对象的属性

**如果声明的成员变量中，有与自身同名的(同类型)引用类型变量，切记不可初始化(new XXX())。**

例如：错误写法

Class Person{

​	Person p1 = new Person():

}

这样的声明同名的成员变量，在我们其他其他类中对它进行实例化的时候，会造成无限的循环实例化它，最后造成内存溢出，因为，我们在内的内部初始化了这个类，对于这个成员变量来说，它的内部结构也是：

Class Person{

​	Person p1 = new Person();

}

所以就会造成无限的实例化这个内部成员变量，最终堆溢出。



#### 2.3成员方法的封装

在类的内部，构造对象的行为。



#### 2.4构造函数与析构函数 

##### 2.4.1构造函数的基本概念

在实例化对象之前会调用的用于自身初始化的函数，如果不写，会调用系统默认的无参构造函数。



##### 2.4.2构造函数的写法

1.没有返回值

2.函数名与类名必须相同

3.没有特殊需求时，一般都是Public

4.构造函数可以被重载

5.this代表当前被调用该函数的对象自己

注意:如果自己不是实现无参构造函数而实现了有参构造函数，就会失去系统默认的无参构造函数。

6.构造函数的特殊用法

```c#
Class Person{
    string name;
    int age;
    Public Person(){
        name = "xiaoxiaoze";
        age = 18;
    }
    Public Person(int age){
        this.age = age;
    }
    public Person(string name){
        this.name = name;
    }
    // 构造函数的特殊用法
    public Person(string name, int age):this(){
        Console.WriteLine("111111");
    }
}
public Class static main(){
    // 这是一个生成的初始化我们自己写的无参构造函数
    // 因为我们自己写了带参或者不带参数的构造函数之后，系统就不会在提供默认的无参构造函数了。
    Person p = new Person();
    // 接下来是调用带this()的构造函数
    // 第一步它会先去找this();因为this代表函数对象自身，我们可以把this替换成Person更加方便理解；
    //那么第一步会是先去调用无参构造函数Person();
    //接着才是调用带参数的构造函数Person(String name, ing age);
    Person p = new Person("xiaoxiaoze", 18);
    
}
```



##### 结构体与类的区别：

**类中是允许自己申明无参构造函数的，而结构体不行，结构体的构造函数必须初始化自身所有的成员变量**



#### 2.5 析构函数

当引用类型的堆内存被回收时，会调用该函数；对于需要手动管理内存的语言(比如C++)，需要在析构函数中做一些内存回收处理；但是C#中存在自动垃圾回收机制GC;所以我们几乎不怎么使用，所以该知识点只是做了解即可。

**注意：在Unity开发中析构函数几乎不会使用，所以该知识点只做了解即可。**

##### 2.5.1.基本语法：

~类名（）{

}

~~~C#
class Test{
    ~Test(){
        
    }
}
~~~



##### 2.5.2什么是托管代码？什么是非托管代码？什么是托管资源？什么是非托管资源？什么是解释型语言？

**托管代码 (managed code)** 
由公共语言运行库环境（而不是直接由操作系统）执行的代码。托管代码应用程序可以获得公共语言运行库服务，例如自动垃圾回收、运行库类型检查和安全支持等。这些服务帮助提供独立于平台和语言的、统一的托管代码应用程序行为。
 如C#

**非托管代码 (unmanaged code)**
在公共语言运行库环境的外部，由操作系统直接执行的代码。非托管代码必须提供自己的垃圾回收、类型检查、安全支持等服务；它与托管代码不同，后者从公共语言运行库中获得这些服务。如C++,C

**托管资源 (managed Assets) **

托管资源指的是.NET可以自动进行回收的资源，主要是指托管堆上分配的内存资源。托管资源的回收工作是不需要人工干预的，由.NET运行时在适当的时候调用垃圾回收器进行回收。例如程序中分配的对象,作用域内的变量等。但如果我们需要立即进行垃圾回收，则可以使用`GC.Collect()`来触发。

**非托管资源 (unmanaged Assets)**

非托管资源指的是.NET不知道如何回收的资源，最常见的一类非托管资源如文件，窗口，网络连接，数据库连接，画刷，图标等。这类资源，垃圾回收器在清理的时候会调用`Object.Finalize()`方法。默认情况下，方法是空的，对于非托管对象，需要在此方法中编写回收非托管资源的代码，以便垃圾回收器正确回收资源。

**解释型语言**

简单的说，如果一门语言主流实现的通常使用方式中没有显式的把这个语言源代码转换成另一种相对来说更低级的语言(字节码，汇编，机器码等等)的过程，就能叫作解释型语言。



##### **补充：**

**结构体的构造函数需要实现自身所有的成员变量，也就是说我们的有参构造函数需要对自身所有的成员变量进行初始化，而我们的类就明显没有这个特点。(类和结构体的具体区别会放在后面讲解)**



*需要注意的是，析构函数不需要访问修饰符。结构体和类虽然相似都有构造函数，但是**结构体是没用析构函数**。



#### 2.6垃圾回收机制

垃圾回收机制，简称：GC(Garbage Collector)

垃圾回收的过程是在遍历堆(Heap)上动态分配的所有对象，通过识别它们是否被引用来确定哪些对象是垃圾，哪些对象仍要被使用，所谓的垃圾就是没有被任何变量，对象引用的内存，垃圾就需要被回收释放。

垃圾回收有很多种算法，比如：引用计数(Reference Counting)、标记清除(Mark Sweep)、标记整理(Mark Compact)、复制集合(Copy Collection).

注意：GC只负责堆(Heap)内存的垃圾回收，引用类型都是存在堆(Heap)种的，所以它的分配和释放都是通过垃圾回收机制来管理。

栈(Stack)上的内存是有系统自动管理的，值类型在栈(Stack)中分配内存的，他们有自己的生命周期，不用对他们进行管理，会自动分配和释放。



##### C#内存回收机制原理：

内存回收机制存在三代内存：0代内存、1代内存、2代内存；

代的概念：代是垃圾回收机制中使用的一种算法(分代算法)，每次新分配的对象都会被配置在第0代内存中，每次新分配都可能会进行垃圾回收以释放内存(0代内存满时)；在第一次内存回收过程开始时候，垃圾回收器会认为堆中全是内存，会进行以下两步：

1.标记对象：从根（静态字段、方法参数）开始检查引用对象，标记后为可达对象，未标记为不可达对象，不可达对象就被认为是垃圾。

2.搬迁对象压缩堆（挂起执行托管代码线程）释放未标记的对象，搬迁可达对象，修改引用地址。

**大对象总被认为是第二代内存，目的是减少性能损耗，提升性能；不会对大对象进行搬迁，默认85000字节(83kb)以上的对象为大对象。**



#### 2.7成员属性

##### 2.7.1基本概念：

1.用于保护成员变量

2.为成员属性的获取和赋值添加逻辑处理

3.解决访问权限的局限性

​    Public——内外访问

​	Private——内部访问

​	Protected——内部和子类访问

**属性可以让成员变量在外部只能获取不能修改或者只能修改不能获取。**



##### 2.7.2成员属性的基本写法

访问修饰符    属性类型    属性名

{

​		get{}

​		set{}

}

```C#
Class Person{
    private string name
    public string Name{
        get{
            return name;
        }
        set{
            name = value;
        }
    }
}

// get与set方法也可以在前面加访问修饰符，不加时候默认为属性的访问修饰符
lass Person{
    private string name
    public string Name{
    private get{
            return name;
        }
        set{
            name = value;
        }
    }
}

```

**注意：**

1.默认不加会使用属性声明时候的访问权限

2.加的访问修饰符要低于属性的访问权限

3.不能让get和set的访问权限都低于属性的权限

4.get和set可以只有一个，但是不能全部都没有



#### 2.8索引器

##### 2.8.1基本概念

让对象可以像数组一样通过索引访问其中元素，使程序看起来更加直观，更加容易编写。



##### 2.8.2索引器语法

访问修饰符    返回值    this[参数类型    参数名， 参数类型    参数名··········]

{

​		内部的写法和规则和属性相同

​		set{}

​		get{}

}

```c#
Class Person{
    private string name;
    private int age;
    private Person[] friends;
    
    // 索引器的写法
    public Person this[int index]{
        
        set{
        	friends[index] = value;    
        }
        
        get{
            return friends[index];
        }
    }
}
Class Programe{
    static void main(){
        // 索引器的使用
        Person p = new Person();
        p[0] = new Person();
    }
}
```

##### 2.8.3索引器里面写逻辑

```C#
// 索引器跟属性差不多，所以其内部也是可以写逻辑的
Class Person{
    private Person[] friends;
    Public Person this[int index]{
        get{
            if(friends == null || friends <= index){
                return null;
            }
        }
        set{
            if(friends == null){
				friends = new Person(){value};
            }
            if(friends.Length <= index){
                return
            }
            else{
                friends[index] = value;
            }
        }
    }
}
```



##### 2.8.4索引器的重载

```C#
Class Person{
    private String name;
    private int age;
    private Person[] friends;
    private Person[,] persons;
    
    Public Person this[index]{
        get{
            return friends[index];
        }
        set{
            friends[index] = value;
        }
    }
    // 索引器的重载
    Public Person this[int i, int j]{
 		set{
            if(person == null)
                person = new Person[i,j];
        }   
        get{
            return person[i,j];
        }
    }
    
    // 索引器的重载
    Public int this[int num]{
        set{
            age = num;
        }
        get{
            return age;
        }
    }
    
    // 索引器的重载
    Public String this[string str]{
        set{
            name = str;
        }
        get{
            switch(str){
                case "name":
                    return name;
                default:return "";
            }
        }
    }
}

Class Programe{
    static void main(){
		Person p = new Person();
        p[1,3] = new Person;
    }
}
```

注意：我们通过以上案例可以知道索引器是方便我们像数组一样访问我们对象的成员变量，索引器的参数类型可以是任意的，索引器可以重载。*索引器的用法同样是适用于结构体的。



#### 2.9静态成员

##### 2.9.1基本概念

静态关键字：static

概念：用static修饰的成员变量、方法、属性等等称为静态成员

特点：可以直接用类名点(.)出来使用



##### 2.9.2自定义静态成员

```C#
class Test{
    // 声明静态成员变量
    static Public float PI = 3.14;
    public int testInt = 100;
    // 定义静态成员方法
    public static float CalcCircle(float r){
        return PI * r * r;
    }
}

class Program{
    void main(){
        Test.CalcCircle(10f);
    }
}
```



##### 2.9.3为什么静态成员和静态成员方法不需要实例化就可以通过类名.出来使用？

***首先，我们知道程序中是不可能无中生有的，在一个普通的类对象中，我们需要对这个对象进行实例化操作之后，让它在堆内存中开辟一块空间，在栈上存储对堆的引用，完成这一步之后，我们才可以通过我们实例化出来的变量通过点(.)使用其中的成员变量或者成员方法。所以，我们明白我们要使用的对象、变量、函数都是要在内存中分配内存空间的，之所要要实例化对象，目的就是分配内存空间，在程序中产生一个抽象的对象。那么回过头来，静态的成员变量或者函数之所以能不需要实例化就可以使用，说明程序是已经为它们分配好了内存空间的。在程序开始运行的时候，就会帮它们分配内存空间，所以我们就能直接使用。静态成员和程序是同生共死的，只要使用了它，直到程序结束时内存空间才会被释放。所以一个静态成员就会有自己的唯一的一个"内存小房间"，这让静态成员就有了唯一性，在任何地方使用都是用的"小房间"里面的内容，改变了它也是改变小房间里的内容。



##### 2.9.4静态函数中不能使用非静态成员

```C#
class Test{
    static public float PI = 3.14;
    public int testInt = 100;
    public static float CalcCircle(float r){
        // 先看错误例子
        // 会报错误，因为静态成员变量和静态函数的生命周期跟普通类成员变量和成员方法的声明周期是不一样的，程序运行了之后，静态的成员变量和静态的方法会被分配内存空间，但是普通的类是需要我实例化，手动分配内存空间才能使用，在静态方法中我们是无法调用普通的非静态成员或者方法的因为它们还没被分配内存空间(或实例化)
        Console.WriteLine(testInt);
        // 正确的方式
        // 实例化在使用
        Test t = new Test();
        Console.WriteLine(t.testInt);
    }
}
```

##### 2.9.5非静态成员函数中可以直接使用静态成员变量

```C#
class Test{    static int testInt = 100;    public void Te(){        Console.WriteLine(testInt);    }}
```

同理还是因为生命周期不同的原因，当普通类的成员方法使用到了静态成员变量或者静态成员方法的时候，说明已经完成了实例化的操作，在内存空间得到了分配。



##### 2.9.6静态成员对我们的作用

静态变量：

1.常用唯一的变量的申明，例如PI = 3.14f;

2.方便别人获取的对象申明

静态方法：

常用唯一的方法申明，例如相同规则的数学计算相关函数



##### 2.9.7常量和静态变量

const(常量)可以理解为特殊的static(静态)。



###### Ⅰ.const的特点

const必须写在访问修饰符的后面，变量声明的前面；

const必须初始化且初始化之后无法被修改

const只能修饰变量

const可以跟static一样，被类名点出来



###### Ⅱ.**总结**

概念：用static修饰的成员变量、成员方法、成员属性等就称为静态成员

特点：直接用类名点出来使用（全局性）

生命周期：和程序同生共死，程序运行后就会一直存在内存中，直到程序结束后才会释放因此静态成员具有唯一性。

注意：

1.静态函数中不能直接使用非静态成员

2.非静态函数中可以使用静态成员



###### Ⅲ.常量和静态变量的比较

1.相同点：

它们都可以通过类名点出来使用

2.不同点：

2.1const必须初始化且不能被修改，static没有这个规则

2.2const只能修饰变量，static可以修饰很多

2.3const不能写在访问修饰符前面一定是写在变量声明前面，static没有这个规则。



#### 2.10静态类和静态构造函数

##### 2.10.1静态类

概念：用statci修饰的类

特点：只能包含静态成员，不能被实例化

作用：

1.将常用的静态成员写在静态类中，方便使用

2.静态类不能被实例化，更能体现工具类的唯一性，例如Console类就是一个静态类。

```C#
static class Tools{    static int testInt = 100;    public void static Fun(){            }        public int TestInt{        set;        get;    }}
```



##### 2.10.2静态类中的静态构造函数

概念：在构造函数加上static修饰

特点：

1.静态类和普通类都可以有

2.不能使用访问修饰符

3.不能有参数

4.只会自动调用一次

作用：在静态构造函数中初始化静态变量

使用：静态类中的静态构造函数

```C#
static class StaticClass{    public static int testInt = 100;    public static int testInt2 = 100;    static StaticClass(){        Console.WriteLine("静态构造函数");    }}class Programe{    void main(){        console.WriteLine(StaticClass.testInt);        console.WriteLine(StaticClass.testInt2);        console.WriteLine(StaticClass.testInt);            }}
```

结果：运行了上面的代码，我们的构造函数自动调用了一次，先显示了构造函数的内容："静态构造函数"，接着再打印了三次100；

结论/总结：静态构造函数只会调用一次，它的作用相当于对我们的静态类中的成员进行初始化的意义。



##### 2.10.3普通类中的静态构造函数

~~~C#
class Test{    public static int testInt = 200;    // 这里先解释一个误区，虽然我们声明了两个Test()构造函数，它们不因该会报错吗？（函数的重载），在这里static修饰的Test()和普通的Test()已经不再遵循函数重载的规则了，所以这里不能称为函数的重载，算是两个不同的函数    static Test(){        Console.WriteLine("静态构造");    }    public Test(){        Console.WriteLine("普通构造");    }}class Programe{    void main(){        Console.WriteLine(Test.testInt);        Test t = new Test();        Test t2  = new Test();    }}
~~~

结果：运行上面的代码会先显示:"静态构造"，然后打印200，然后再打印两次："普通构造"。

结论：说明跟静态类中的静态构造函数相同，当对静态类或者非静态类的内容进行使用的时候，会自动的先调用静态构造函数的内容，再做行为。

**总结：**

静态类：用static修饰的类；

特点：只能包含静态成员，不能被实例化；

作用：工具类，拓展方法；

静态构造函数：用static修饰的构造函数

特点：静态类和普通类都可以有静态构造函数；不能使用访问修饰符；不能有参数；只会自动调用一次；

作用：初始化静态成员；



#### 2.11拓展方法

##### 2.11.1概念：为现有非静态变量类型添加新方法

作用：

1.提升程序的拓展性

2.不需要再对象中重新写方法

3.不需要继承来添加方法

4.为别人封装的类型写额外的方法

特点：

1.一定是写在静态类中

2.一定是个静态函数

3.第一个参数为拓展目标

4.第一个参数用this修饰



##### 2.11.2基本语法

访问修饰符 static 返回值 函数名(this 拓展类名 参数名，参数类型 参数名，参数类型 参数名......)

~~~C#
// 拓展方法一般写在静态的工具类中static class Tools{    // int类型的拓展的无参方法    public static void SpeakInt(this int value){        console.WriteLine("这个值为"+value);    }    // string类型的拓展的有两个参数的方法    public static void SpeakStringInfo(this string str, string str2, string str3){        console.WriteLine("两个参数："+str2+str3);    }}class Program{    static void main(){        // 拓展方法的使用        int i = 10;        i.SpeakInt();    }}
~~~



##### 2.11.2自定义自己的拓展方法

~~~C#
class test{    public void Fun1(){            }    public void Fun2(){            }}static class Tools{    // 为test这个类拓展了一个Fuc3的方法    public static void Fuc3(this test t){            }    // ***重点    // 如果拓展方法与原类中的方法有同名，则在使用的时候，会调用原类中的方法，不再调用拓展方法    public static void Fun2(){            }}
~~~

##### **总结**

概念：为现有的非静态变量类型添加方法

作用：

1.提升程序拓展性

2.不需要再在对象中重新写方法

3.不需要继承来添加方法

4.为别人封装的类型写额外的方法

特点：

1.静态类中的静态方法

2.第一个参数代表拓展的目标类

3.第一次参数前面必须要加this

注意：

1.拓展方法可以有返回值和n个参数

2.根据需求而定



#### 2.12运算符重载

概念：让自定义类和结构体可以使用运算符

关键字：operator

特点：

1.一定是一个公开的静态方法

2.返回值写在operator前

3.逻辑处理自定义

作用：

1.条件运算符需要成对实现

2.一个符合可以多个重载

3.不能使用ref和out

*基本语法：public static 返回值类型 operator 运算符(参数列表)

~~~C#
class Point{    public int x;    public int y;    // 写一个点与点之间的相加    public static Point operator +(Point p1, Point p2){        Point p = new Point();        p.x = p1.x + p2.x;        p.y = p1.y + p2.y;    }}class Program{    void Main(){        // 运算符重载的使用        Point p = new Point();        p.x = 1;        p.y = 1;        Point p1 = new Point();    	p1.x = 2;        p1.y = 2;        Point p2 = p + p1;     }}
~~~

**注意**

~~~C#
class test{    // ***必须要有两个以上的参数且参数类型中必须存在一个或者多个与该类同类型的参数    // 例如以下就符合，存在一个与类同名的参数，且参数列表必须等于2个    public static void operator +(test t, int i){            }    // 运算符重载符合函数的重载    // 以下就是对一个加法进行重载    public static void operator +(test t, test t1){            }}
~~~

**注意**

1.可重载的运算符：

算数运算符：+、-、*、/、++、--；

逻辑运算符：!

位运算符：|、&、^、~、>>、<<

条件运算符：<、>、>=、<=、==、！=

***注意：逻辑运算符中"与""或"是不允许重载的；条件运算符中必须成对出现，有">"的运算符重载就必须有"<"的运算符重载，有"=="的运算符重载就必须要有"!="的运算符重载。

2.不可重载的运算符：

逻辑与(&&)逻辑或(||)、索引器[]、强转运算符()、特殊运算符（点.、三目运算符？：、赋值符合=）

总结：

关键字：operator

固定语法：public static 返回值 operator 运算符（参数列表）

作用：让自己定义类或者结构体对象进行运算

注意：

1.参数的数量

2.条件运算符的配对实现

3.一个符合可以多个重载

4.不能用ref和out



#### 2.13内部类和分部类

##### 2.13.1内部类

概念：在一个类的内部再申明一个类

特点：使用时候要包裹者点出自己

作用：表现亲密关系

注意：访问修饰符的作用很大

~~~C#
class Person{    public int age;    public string name;    public Body body;    public class Body{        Arm leftArm;        Arm rightArm;        class Arm{                    }    }}
~~~

##### 2.13.2分部类

概念：把一个类分成几部分申明

关键字：partial

作用：分部描述一个类，增加程序的拓展性

注意：分布类可以写在多个脚本文件中；分布类的访问修饰符要一致；分布类中不能有重复成员；

~~~C#
partial class Student{    public bool sex;    public string name;}partial class Student{    public int number;    public void Speak(){            }}
~~~



##### 2.13.分部方法

特点：我们可以一个分部类中申明这个分部方法，再在另一个分部类中去实现这个分部方法。

~~~C#
partial class Test{    // 声明的方法    partial void Speak();}partial class Test{    partial void Speak(){        // 实现的逻辑    }}
~~~



### 三.面向对象三大特性——继承

#### 3.1继承的基本概念

概念：一个类A继承一个类B，类A将会继承类B的所有成员，A类将拥有B类的所有特征和行为，

被继承的类称呼为父类、基类、超类，继承的类称为子类、派生类；

*子类也可以有自己的成员、特征和行为

特点：

单根性：子类只能有一个父类；

传递性：子类可以间接继承父类的父类；

基本语法：

~~~C#
class A : B{    }
~~~

***注意：子类和父类运行存在同名成员，如果存在同名成员，一般使用其自身的成员，就是如果声明的是子类就使用子类的同名成员，如果是父类则是父类的同名成员。强烈不建议子类与父类之间存在同名成员，如果真的需要可以通过多态解决；



#### 3.2is和as:

基本概念：is是判断一个对象是否是指定类对象，返回值：bool,是为真，不是为假；

as是将一个对象转化为指定对象，成功返回执行类型对象，失败返回null。

基本语法：

类对象 is 类名 该语句块会有一个bool返回值；

类对象 as 类名 该语句块会有一个对象返回值；



#### 3.3继承中的构造函数

##### 3.3.1基础概念

当申明一个子类对象时候，先执行父类的构造函数，再执行子类的构造函数。

注意：1.父类的无参构造函数很重要；2.子类可以通过base关键字代表父类，调用父类构造函数。

##### 3.3.2继承中构造函数的执行顺序

父类的父类的构造函数->父类的构造函数->子类的构造函数

~~~C#
class GameObject{    Console.WriteLine("父类的父类构造函数");}class Person:GameObject{    Console.WriteLine("父类构造函数");}class Son:Person{    Console.WriteLine("子类构造函数");}class Program{    Son s = new Son();}================================结果：    父类的父类构造函数    父类构造函数    子类构造函数// 从结果上可以看出来，我们声明了一个子类，但是都是先从祖宗类开始向下执行其构造函数，最后再调用子类的构造函数
~~~

##### *3.3.3父类的构造函数很重要

~~~C#
// 先给结论：当子类继承于父类时候，父类中必须有自身的构造函数，且必须存在无参构造函数，除非出现子类的构造函数指定了调用父类中的有参构造函数// 第一种写法// 这样，当我们new Son()的时候不会出现报错，因为当没有写入自定义的构造函数的时候，会调用系统默认的构造函数class Father{    }class Son:Father{    }// 第二种写法// 与第一种一样，不过因为我们自己写了构造函数，所以会顶替掉系统自带的构造函数class Father{    public Father(){	    }}class Son:Father{    }// 第三种写法class Father{// 我们声明了一个有参构造函数，这时候我们的父类已经不存在无参构造函数了，如果子类的构造函数种不指定调用父类的有参构造函数，那么我们的IDE是会报错的。    public Father(int i){            }}class Son:Father{    // 指定调用父类的有参构造函数，base(i)表示调用父类的带参构造函数    public Son(int i):base(i){            }    // 调用自身！自身！自身！的构造函数，this表示当前类对象，这句代码块表示this的代码重用。    public Son(int i, string str):this(i){            }}
~~~

##### **总结**

继承中的构造函数的调用遵循的规则是自顶向下，从祖宗类开始执行构造函数，但是，当我们自己在父类中写了自己定义的构造函数且不是无参构造函数的时候，我们就会失去了系统默认的无参构造函数，这时候，我们需要让继承至父类的子类，指定使用其定义的构造函数。



#### 3.4万物之父与装箱拆箱

##### 3.4.1万物之父

关键字：object

概念：object是所有类型的基类，它是一个类(引用类型)

作用：

1.可以利用里氏替换原则，用object容器装所有对象

2.可以用来表示不确定类型，作为函数参数类型

3.4.2万物之父的使用

~~~C#
class Father{    }class Son{    public void Speak(){            }}class Program{	Father f = new Son();    if(f is Son){        (f as Son).Speak();    }	// 用万物之父实现装箱    // 引用类型装箱    object o = new Son();    if(o is Son){       (o as Son).Speak();     }    // 值类型装箱    object o2 = 1f;        float f1 = (float)o2;    // 特殊的string类型    object str = "123123";    // 第一种转法    string str2 = str.ToString();    // 第二种转法    string str3 = str as string;    // 特殊的数组    object arr  = new int[10];    // 第一种转法    int[] ar = (int[])arr;    // 第二种转法    int[] ar2 = arr as int[];}===============    总结：    装箱拆箱的条件：用object存值类型(装箱)，再把object转为值类型(拆箱)    装箱：    把值类型用引用类型存储，栈内存会迁移到堆内存中。    拆箱：    把引用类型存储的值类型取出来，堆内存会迁移到栈内存中    好处：不确定类型时候可以方便方便参数的存储和传递    坏处：存在内存迁移，增加性能消耗
~~~

#### 3.5密封类

基本概念：密封类是使用sealed密封关键字修饰的类

作用：让类无法再被继承

~~~C#
class Father{    }// 这时候这个Son这个类是无法被其他类继承的sealed class Son{    }
~~~

总结：

在面向对象程序的设计中，密封类的主要作用就是不允许底层子类被继承，可以保证程序的规范性、安全性，以便制作复杂系统和框架的时候更方便；



### 四.面向对象三大特性——多态

#### 4.1多态VOB的知识点

##### 4.1.1基本概念

多态按字面的意思就是“多种状态”，让继承同一父类的子类们在执行相同方法时候有不同的表现(状态)，主要目的是同一父类的对象执行相同行为(方法)有不同的表现，主要的解决的问题是让同一对象具有唯一行为的特征。



##### 4.1.2多态的基本实现

我们之前学过的函数重载其实就算是一张多态，只不过是编译时候的多态，是开始就写好的。我们现在要学习的多态是运行时多态(vob、抽象函数、接口)。

v——virtual（虚函数）、o——override（重写）、base（父类）



#### 4.2抽象类和抽象方法

##### 4.2.1抽象类

抽象类概念：被抽象的关键字abstract修饰的类

特点：

1.不能被实例化的类

2.可以包含抽象方法

3.继承抽象类必须重写其所有的抽象方法

~~~C#
abstract class T{    // 抽象类中 之前学过的封装的所有的知识点都可以在其中书写    public string name;    // 可以在抽象类中写抽象函数}class A:T{}class Program{    static void Main(string[] args){        // 抽象类不能被实例化        // T t = t new T();这是不被允许的        // 但是可以遵循里氏替换原则 用父类装子类        T t = new A();    }}==============================    总结：抽象类就相当于普通类+抽象方法，也就是它具备普通类的所有东西(不能被实例化)，并且还能写抽象函数(这是普通类不具备的特点)
~~~

##### 总结

抽象类更像是一种模板类，让子类继承父类，重写父类的抽象方法

##### 4.2.2抽象方法

抽象方法：又叫纯虚方法，用abstract关键字修饰的方法

特点：

1.只能在抽象类中申明

2.没有方法体

3.不能是私有的

4.继承后必须实现用override重写



#### 4.3接口

##### 4.3.1概念

***基本概念：接口是行为的抽象规范；它也是一种自定义类型。

关键字：interface

*接口申明的规范：

1.不包含成员变量

2.只包含方法、属性、索引器、事件

3.成员不能被实现

4.成员可以不用写访问修饰符，但是不能是私有的

5.接口不能继承类，但是可以继承另一个接口

*接口的使用规范：

1.类可以继承多个接口

2.类继承接口后，必须实现接口所有成员

特点：

1.它和类申明类似

2.接口是用来继承的

3.接口不能被实例化



##### 4.3.2接口的申明

接口关键字：interface

语法：

~~~C#
interface 接口名{}一句话记忆：接口是抽象行为的“基类”接口命名规范 帕斯卡前面加I
~~~

~~~C#
interface IFly{    // 不需要方法体(成员不能被实现)，跟抽象类一样，类继承接口后需要实现接口的所有成员    void Fly();    // 属性    string Name{        // 成员不能被实现，所以get、set方法也不能被实现(不能有方法体)        get;        set;    }    // 索引器    int this[int index]{        get;        set;    }    // 事件    event Action doSomething;}
~~~

##### 4.3.3接口的使用

~~~C#
interface IFly{    }class Animal{    }class Person:Animal, IFly{    }
~~~

##### 4.3.4接口可以继承接口

接口继承接口时，不需要去实现父类的成员，当有普通类去继承它时候，就必须实现所有成员。

##### 4.3.5显示实现接口

当一个类继承两个接口，但是接口中存在同名方法时，注意：显示实现接口时，不能写访问修饰符。

~~~C#
interface IAtk{	void Atk();    }interface ISuperAtk{    void Atk();}class Player:IAtk, ISuperAtk{    // 当我们继承两个或者多个接口时候，如果出现同名方法，我们需要使用显示实现接口    void IAtk.Atk(){            }    void ISuperAtk.Atk(){            }}// *******但是用显示实现接口会存在缺点class Program{    IAtk ia = new Player();    ISuperAtk isa = new Player();    ia.Atk();    isa.Atk();    // 缺点：    // 当我们实例化一个Player类的时候，我们无法点.出Atk()方法，因为Player方法继承的两个接口有同名方法，所有我们必须先把它转成指定的接口，再去使用其接口的方法    Player p = new Player();    (p as IAtk).Atk();    }
~~~

##### 总结

继承类：是对象间的继承，包括特征行为等等

继承接口：是行为间的继承，继承接口的行为规范，按照规范去实现内容

由于接口也是遵循里氏替换原则，所有可以用接口容器装对象，那么就可以实现装载各种毫无关系但是却有相同行为的对象

注意：

1.接口只包含 成员方法、属性、索引器、事件、并且都不实现，都没有访问修饰符

2.可以继承多个接口，但是只能继承一个类

3.接口可以继承接口，但是只能继承一个类

4.接口可以被显示实现，主要用于实现不同接口中的同名函数的不同表现

5.实现的接口方法可以加virtual关键字之后子类再去重写



#### 4.4密封方法

概念：用密封关键字sealed修饰的重写函数

作用：让虚方法或者抽象方法之后不能再被重写

特点：和override一起出现

~~~C#
abstract class Animal{    public string name;    public abstract void Eat();    public virtual void Speak(){        Console.WriteLine("吃");    }}class Person:Animal{    public override void Eat(){            }    public override void Speak(){            }}class WhitePerson:Peron{    public sealed override void Eat(){            }    public override void Speak(){            }}class Son:WhitePerson{    // 我们会发现无法实现Eat()的方法了，因为父类把Eat()方法用sealed修饰了    }
~~~



### 五.面向对象面向对象的相关知识点

#### 5.1命名空间

基本概念：命名空间是用来组织和重用代码的。

作用：就像是一个工具包，类就像是一件一件的工具，都是申明在命名空间中的。

命名空间的使用：

~~~C#
namespace 命名空间名{    类    类}// ***注意命名空间也遵循我们的分布类的规则，也就是我们可以使用同名的命名空间去写，它们是属于同一个命名空间的。// 同一命名空间下不允许存在不同类，但是不同命名空间下面允许同名类的存在// 如果不同命名空间中出现同名类的话，我们需要使用命名空间.出的方式指明使用哪个命名空间下的类namespace MyGame{	class T1{            }}namespace MyGame1{    class T1{            }}// 命名空间的引用// 第一种：using + 命名空间名// 第二种：命名空间 + 类名// 不同命名空间中允许有同名类namespace MyGame{    namespace UI{            }    namespace Game{            }}=======================================
~~~

##### 拓展：

1.命名空间中的类默认为Public；

2.Internal只能在该程序集中使用；



#### 5.2万物之父中的方法

##### 5.2.1object中的静态方法

1.object.Equals()判断两个对象是否相等，最终的判断权，交给左侧对象的Equals方法,不管是值类型还是引用类型都会按照左侧对象Equals方法的规则来进行比较。

***注意：值类型判断是否相等是比较它们的数值，引用类型判断是否相等是指它们的地址是否相等，而不是都是同一个类型实例化。

~~~C#
class Programe{    Test t = new Test();    Test t2 = t;    Console.WriteLine(object.Equals(t, t2));    =========================    结果：True    这里比较的是地址是否相等，而非比较是否都是Test类型的实例化。       }
~~~

2.object.ReferenceEquals()，这个是专门用于比较引用类型的，如果传入值类型，结果会永远是False,而且两个引用类型比较的也是地址的比较。

##### 5.2.2object中的成员方法

1.object.GetType()该方法在反射相关知识点中是非常重要的方法，之后我们会具体的讲解这里返回的Type类型，该方法的主要作用就是获取对象运行时的类型Type,通过Type结合反射相关知识点可以做很多关于对象的操作。



2.object.MemberwiseClone()该方法用于获取对象的浅拷贝对象，口语化讲解的意思就是会返回一个新的对象，但是新对象中的引用类型变量会和老对象保持一致，而值类型的不会。

~~~C#
class Test{    public int i = 1;    public Test t2 = new Test();        public Test Clone(){        return MemberwiseClone() as Test;    }}class Test2{    public int i = 2;}class Programe{    Test t = new Test();    Test t2 = t.Clone();    Console.WriteLine("克隆对象后");    Console.WriteLine("t.i = " + t.i);    Console.WriteLine("t.t2.i = " + t.t2.i);    Console.WriteLine("t2.i = " + t2.i);    Console.WriteLine("t2.t2.i = " + t2.t2.i);        t2.i = 20;    t2.t2.i = 21;        Console.WriteLine("改变克隆体信息后");    Console.WriteLine("t.i = " + t.i);    Console.WriteLine("t.t2.i = " + t.t2.i);    Console.WriteLine("t2.i = " + t2.i);    Console.WriteLine("t2.t2.i = " + t2.t2.i);    ============================================    结果：        克隆对象后        t.i = 1        t.t2.i = 2        t2.i = 1        t2.t2.i = 2        改变克隆体信息后        t.i = 1        t.t2.i = 21        t2.i = 20        t2.t2.i = 21}
~~~

##### 5.2.3object中的虚方法

1.虚方法Equals():默认实现还是比较两者是否同为一个引用，即相当于ReferenceEquals()。但是微软在所有值类型的基类System.ValueType中重写了该方法，用来比较值相等。我们也可以重写该方法，定义自己的比较相等的规则。



2.虚方法GetHashCode():该方法是获取对象的哈希码(一种通过算法算出的，表示对象的唯一编码，不同对象的哈希码有可能一样，具体值根据哈希算法决定)，我们可以通过重写该函数来定义自己对象的哈希码算法，正常情况下，我们使用的极少，基本不用。



3.虚方法ToString():该方法用于返回当前对象代表的字符串，我们可以重写它定义我们自己的对象转字符串规则，该方法非常常用，当我们调用打印方法时候，默认使用的就是对象的ToString方法后打印出来的内容。

~~~C#
namespace XXZ{    class Test{            }    class Programe{        void Main(){            Test t = new Test();        	Console.WriteLine(t);            =======================            结果：XXZ.Test            我们发现，我们的Test中并没有ToString方法，说明，我们调用了所有的类之父Object中的ToString方法，并且从结果我们可以看出，我们的打印出来的是所属的命名空间下的类。                          Random r = new Random();                Console.WriteLine(r);                ==========================             结果：System.Random        }    }}
~~~

#### 5.3string知识点

##### 5.3.1字符串指定位置获取

~~~C#
// ***C#中一个char类型占2个字节大小，C和C++中一个char类型占一个字节大小// 字符串的本质是char类型数组string str = “小小泽”;Console.WriteLine(str[0]);结果：小char[] chars = str.ToCharArray();Console.WriteLine(char[2]);结果：泽for(int i = 0;i<str.Length;i++){    Console.WriteLine(str[i]);}结果：小小泽
~~~

##### 5.3.2字符串拼接

~~~C#
string str = string.Format("{0}{1}",1 ,333);Console.WriteLine(str);
~~~

##### 5.3.3正向查找字符位置

~~~C#
string str = "小小泽";int index = str.IndexOf("小");Console.WriteLine(index);结果：0index = str.IndexOf("泽");Console.WriteLine(index);结果：2    index = str.IndexOf("屌");Console.WriteLine(index);结果：-1==============================结论：str.IndexOf(string str)会返回从头遍历到的第一个字符的索引下标返回出去,找不到就返回-1；
~~~



##### 5.3.4反向查找指定字符串位置

~~~C#
string str = "小小泽小小泽";int index = str.LastIndexOf("小小泽");Console.WriteLine(inde);结果：3index = str.LastIndexOf("泽泽");Console.WriteLine(index);结果：-1===============================结论：str.LastIndexOf(string str)是从字符串的尾部开始遍历，找到就返回该对象在次数组中的下标，没有就返回-1；
~~~



##### 5.3.5移除指定位置后的字符

~~~C#
string str = "我是小小泽小小泽";// 会把第四个位置(包括第四个)后面的字符全部删除str = str.Remove(4);Console.WriteLine(str);结果：我是小小    // 执行两个参数进行移除// 参数1 开始位置， 参数2 字符个数str  = str.Remove(1,1);Console.WriteLine(str);结果：我小小泽小小泽
~~~

##### 5.3.6替换指定字符串

~~~C#
string str = "我是小小泽小小泽";str = str.Replace("小小泽","大哥大");Console.WriteLine(str);结果：我是大哥大大哥大
~~~

##### 5.3.7大小写转换

~~~C#
string str = "asdawqdasdqwdasdqw";str = str.ToUpper();Console.WriteLine(str);// 结果就是全部变成大写的，原来的大写不变str = str.ToLower();Console.WriteLine(str);// 结果就是全部变成小写的，原来的小写不变
~~~

##### 5.3.8字符串截取

~~~C#
string str = "小小泽小小泽";// 截取从指定位置开始之后的字符串str = str.Substring(2);Console.WriteLine(str);结果：泽小小泽    // 参数1 开始的位置， 参数2 指定个数str = str.Substring(2, 2);
~~~

##### 5.3.9***字符串切割

~~~C#
string str = "1, 2, 3, 4, 5, 6, 7, 8";// 以逗号切割字符串string[] strs = str.Split(',');
~~~



#### 5.4StringBuilder知识点

StringBuilder是C#提供的一个用于处理字符串的公共类。

主要解决的问题是：修改字符串而不创建新的对象，需要频繁修改和拼接的字符串可以使用它，可以提升性能。使用前，需要引用命名空间。

初始化：

~~~C#
// StringBuilder自身是有许多构造方法的，这里举一个例子：我们直接传值StringBuilder str = new StringBuilder("123123123");// 默认的StringBuilder的大小是16，每次超过容量大小就自动扩容两倍// 获取容量Conosle.WriteLine(str.Capacity);// 我们也可以在初始化的时候直接给予一个初始容量大小StringBuilder str1 = new StringBuilder("123123",100);// ***注意，我们得到的长度是它自身存储的字符串长度Console.WriteLine(str1.Length);结果：6    // 增删改查// 增// 这个是直接从尾部插入str.Append("44444");// 这个是在尾部插入str.AppendFormat("{0}{1}", 100, 999);// 这个是在指定位置是插入// 参数1 指定位置， 参数2 插入的字符串str.Insert(0, "1231233");// 删// 参数1 起始位置(从哪里开始删除), 参数2 删除的个数(删多少个)str.Remove(0, 10);// 表示从最开始位置开始删除，删除10个长度大小// 清空str.Clear();// 查Console.WriteLine(str[0]);// 改// 在string当中，我们这种中括号的形式是无法修改的，因为string默认是只读的，但是在StringBuilder中，我们是可以修改的str[0] = 'A';// 替换// 参数1 要被替换的字符, 参数2 替换后的字符str.Replace("1", "2");
~~~

结论：StringBuilder也会产生垃圾——当我们要存储的内容超过了它的容量的时候，为了扩容，StringBuilder会重新创建一个比之前大两倍的容量的内存，而之前的那个则作为垃圾被遗弃了，但是它会因为不断的扩容造成的垃圾是越来越少，而且，StringBuilder比String最大的优势是，它的增删改查替换都是基于它自身的索引器改变自身的值的，不像String，修改之后还要作为右值返回给原来的字符串，然后每次操作之后就会产生垃圾，而StringBuilder的每次操作则不会产生垃圾。



#### 5.5结构体和类的区别

区别概述：结构体和类最大的区别是存储空间上的区别，因为结构体是值类型，类是引用类型，因此它们在存储位置是不同的，一个在栈上，一个在堆上；结构体和类在使用上很类似，结构体甚至可以用面向对象的思想来形容一类对象。结构体具备着面向对象思想中的封装特性，但是它不具备继承和多态的特性，因此大大减少了它的使用频录。由于结构体不具备继承的特性，所以它不能够使用protected保护访问修饰符。

###### 细节区别

1.结构体是值类型，类是引用类型

2.结构体存在栈中，类存在堆中

3.结构体成员不能使用protected访问修饰符，而类可以

4.结构体成员变量申明不能指定初始值，而类可以

5.结构体不能申明无参的构造函数，而类可以

6.结构体申明有参构造函数后，无参构造函数不会被顶替

7.结构体不能申明析构函数，而类可以

8.结构体不能被继承，而类可以

9.结构体需要在构造函数中初始化所有成员变量，而类随意

10.结构体不能被静态static修饰(不存在静态结构体)，而类可以

11.结构体不能在自己内部申明和自己一样的结构体变量，而类可以



###### 结构体的特别之处

**结构体可以继承接口，因为接口是行为的抽象**



###### 如何选择结构体和类

1.想要用继承和多态时候，直接淘汰结构体，比如玩家、怪物等等

2.对象数据集合时，优先考虑结构体，比如位置、坐标等等

3.从值类型和引用类型赋值时的区别上去考虑，比如经常被赋值传递的对象，并且改变赋值对象，原对象不想跟着变化时，就用结构体。比如坐标、向量、旋转等等。



#### 5.6抽象类和接口的区别

###### 知识回顾

抽象类和抽象方法：abstract修饰的类和方法，抽象类不能实例化，抽象方法只能在抽象类中申明，是个纯虚方法，必须在子类中实现

接口：interface自定义类型，是行为的抽象，不包含成员变量，仅包含方法、属性、索引器、事件、成员都不能实现，建议不写访问修饰符，默认public。

###### 相同点

1.都可以被继承

2.都不能被直接实例化

3.都可以包含方法申明

4.子类必须实现未实现的方法

5.都遵循里氏替换原则

###### 不同点

1.抽象类中可以有构造函数；接口不行

2.抽象类只能被单一继承；接口可以被继承多个

3.抽象类中可以有成员变量；接口不能

4.抽象类中可以申明成员方法，虚方法，抽象方法，静态方法；接口中只能申明没有实现的抽象方法

5.抽象类方法可以使用访问修饰符；接口中建议不写，默认public

如何选择抽象类和接口：

表示对象的用抽象类，表示拓展的用接口，不同对象拥有的共同行为，我们往往可以使用接口来实现。

举个例子：

动物是一类对象，我们自然会选择抽象类；而飞翔是一个行为，我们自然会选择接口。



























