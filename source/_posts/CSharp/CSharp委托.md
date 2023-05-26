---
title: CSharp委托
author: Heart
date: 2023-03-07 20:32:06
ategories: CSharp
tags:
  - 计算机网络
  - 花里胡哨
  - CSharp
index_img: https://cdn.jsdelivr.net/gh/easilyid/Photo/photo/20230304214219.png
excerpt: 委托 Lambda
---

### 委托

委托： 函数指针的“升级版”；（C/C++中的函数指针）
委托分类： Action委托、Function委托、自定义委托(delegate)；
"一切皆地址"： 程序（本质） = 数据 + 算法；（变量（数据） 是以某个地址为起点的一段内存中所存储的值；函数（算法） 是以某个地址为起点的一段内存中所存储的一组机器语言指令）
Java 中没有与委托相对应的功能实体

**委托是函数(方法)的容器**，可以理解为表示函数(方法)的变量类型，用来存储、传递函数(方法)。委托的本质是一个类，用来定义函数(方法)的类型(返回值和参数的类型)，不同的函数(方法)必须对应和各自"格式"一致的委托

##### 值类型与引用类型

相同点 

- 引用类型可以实现接口，值类型当中的结构体也可以实现接口；
- 引用类型和值类型都继承自System.Object类。

区别

范围方面
C#的值类型包括：结构体（数值类型、bool型、用户定义的结构体），枚举，可空类型。
C#的引用类型包括：数组，用户定义的类、接口、委托，object，字符串。
内存分配方面：
数组的元素不管是引用类型还是值类型，都存储在托管堆上。
引用类型在栈中存储一个引用，其实际的存储位置位于托管堆。简称引类型部署在托管推上；
而值类型总是分配在它声明的地方：作为字段时，跟随其所属的变量（实 例）存储；作为局部变量时，存储在栈上。（栈的内存是自动释放的，堆内存是.NET中会由GC来自动释放）
适用场合
值类型在内存管理方面具有更好的效率，并且不支持多态，适合用做存储数据的载体；引用类型支持多态，适合用于定义应用程序的行为。
引用类型可以派生出新的类型，而值类型不能，因为所有的值类型都是密封（seal）的；
引用类型可以包含null值，值类型不能（可空类型功能允许将 null 赋给值类型，如 int? a = null; ）；
引用类型变量的赋值只复制对对象的引用，而不复制对象本身。而将一个值类型变量赋给另一个值类型变量时，将复制包含的值。

##### 三种不同的委托类型

###### **Action 委托：** 指向无返回值成员

```c#
// 需要引入命名空间 using System;
//Action<>-----这是一个可以传最少1个最多16个不同类型参数的无返回值委托
public class Combine
    {
        public static void Test()
        {
            //无返回值委托
            Action<string> action = new Action<string>(sayHello);
            action.Invoke("heart");
            var action2 = new Action<string, int>(sayHello);
            action2("Heart", 3);
        }

        static void sayHello(string name)
        {
            Console.WriteLine($"Hello,{name}");
        }
        static void sayHello(string name,int rount)
        {
            for (int i = 0; i < rount; i++)
            {
                Console.WriteLine($"Hello,{name}");
            }
        }
    }
```

###### Function委托：指向有返回值成员

（Func<参数1类型，参数2类型，返回值类型>）

```c#
 //Func<T>-----泛型委托(一个可以根据泛型指定返回值类型的泛型无参委托)
 //可以传n(1-15)个参数的并且有返回值的委托，系统提供了16种重载
 public class Combine
    {
        public static void Test()
        {
            //有返回值委托
            Func<int, int, int> func = new Func<int, int, int>(Add);
            int res= func(100, 200);
            Console.WriteLine(res);
        }
         static int Add(int x, int y)
        {
            return x + y;
        }

        static double Mul(double x, double y)
        {
            return x * y;
        }
    }
```

###### delegate 自定义委托

【修饰符】 delegate 【返回类型】【名称】（【参数列表】） （例如：public delegate int Add(int x,int y)）

```c#
// 访问修饰默认不写，则为public，中别的命名空间中也可以使用
// private 其他命名空间就不能用了，一般使用public
//这里申明了一个可以用来存储无参无返回值函数的容器
// 现在这里只是定义了规则，并没有使用
delegate void Fun();
// ***注意：(在同一语句块中)委托不存在同名的两个委托，编辑器会报错误的，它不是函数不能重载
delegate int Fun2(int a);// 表示用来装载或者传递返回值为int 有一个int参数的函数的委托容器规则
```

```c#
delegate void Mydele();//无返回

    delegate int Mydele2(int a, int b);
    public class HelloDelegate
    {

        public static void Test()
        {
            //dele1，这个变量引用着一个Mydele的实例，实例里包裹这M1的方法
            Mydele dele1 = new Mydele(M1);
            dele1 += M1;//又包裹了一层M1进去
            dele1.Invoke();

            Student stu = new Student();
            dele1 += stu.SayHello;
            //dele1 += new Student().SayHello; 另外一种方法
            dele1.Invoke();//因为又Invoke了一次，所以M1又会执行两次然后是SayHello
            Console.WriteLine("*******************");
            
            Mydele2 mydele2 = new Mydele2(M2Add);
            Console.WriteLine(mydele2.Invoke(2, 5));
            
         
        }
        static void M1()
        {
            Console.WriteLine("M1 执行了");
        }

        static int M2Add(int x, int y)
        {
            return x + y;
        }
    }

    class Student
    {
        public void SayHello()
        {
            Console.WriteLine("Hello 我是学生");
        }
    }
```



##### 委托的一般使用

###### **模板方法**

######  借用指定的外部方法产生结果（可最大限度重用代码）



```c#

    public delegate double Cal(double x, double y);//委托声明
    class Program
    {
        static void Main(string[] args)
        {
            ProductFactory productFactory = new ProductFactory(); //声明产品方法
            WrapFactory wrapFactory = new WrapFactory();  //声明产品封装方法

            Func<Product> func1 = new Func<Product>(productFactory.MakePizza);//声明委托方法，获取产品名字信息，并作为产品封装方法的传入参数
            Func<Product> func2 = new Func<Product>(productFactory.MakeToCar);

            Box box1 = wrapFactory.WrapProduct(func1); //调用产品封装方法，输出含产品名字信息的box类
            Box box2 = wrapFactory.WrapProduct(func2);

            System.Console.WriteLine(box1.Product.Name); //输出结果
            System.Console.WriteLine(box2.Product.Name);
        }
    }
    class Product
    {
        public string Name { get; set; }
    }
    class Box
    {
        public Product Product { get; set; }
    }
    class WrapFactory
    {
        //传入委托参数，内部使用委托方法
        public Box WrapProduct(Func<Product> getProduct)
        {
            Box box = new Box();
            Product product = getProduct.Invoke();//调用委托方法，即所传入委托参数的成员类型
            box.Product = product;
            return box;
        }
    }
    class ProductFactory
    {
        public Product MakePizza()
        {
            Product product = new Product();
            product.Name = "Pizza";
            return product;
        }

        public Product MakeToCar()
        {
            Product product = new Product();
            product.Name = "MakeToCar";
            return product;
        }
    }
```

###### **回调（callback）方法**

调用指定的外部方法

```
 public delegate double Cal(double x, double y);//委托声明
    class Program
    {
        static void Main(string[] args)
        {
            ProductFactory productFactory = new ProductFactory(); //声明产品方法
            WrapFactory wrapFactory = new WrapFactory();  //声明产品封装方法

            Func<Product> func1 = new Func<Product>(productFactory.MakePizza);//声明委托方法，获取产品名字信息，并作为产品封装方法的传入参数
            Func<Product> func2 = new Func<Product>(productFactory.MakeToCar);

            Logger logger = new Logger(); //声明产品记录方法
            Action<Product> log = new Action<Product>(logger.Log);

            Box box1 = wrapFactory.WrapProduct(func1, log); //调用产品封装方法，输出含产品名字信息的box类
            Box box2 = wrapFactory.WrapProduct(func2, log);

            System.Console.WriteLine(box1.Product.Name); //输出结果
            System.Console.WriteLine(box2.Product.Name);
        }
    }

    //定义记录产品信息类
    class Logger
    {
        public void Log(Product product)
        {
            System.Console.WriteLine("Product'{0}' create at {1} Price is {2}.", product.Name, DateTime.UtcNow, product.Price); //utcNow为不带时区的时间形式
        }
    }
    class Product
    {
        public string Name { get; set; }
        public double Price { get; set; }
    }
    class Box
    {
        public Product Product { get; set; }
    }
    class WrapFactory
    {
        //传入委托参数，内部使用委托方法
        public Box WrapProduct(Func<Product> getProduct, Action<Product> logCallBack)
        {
            Box box = new Box();
            Product product = getProduct.Invoke();//调用委托方法，即所传入委托参数的成员类型
            if (product.Price > 50)
            {
                logCallBack.Invoke(product);
            }
            box.Product = product;
            return box;
        }
    }
    class ProductFactory
    {
        public Product MakePizza()
        {
            Product product = new Product();
            product.Name = "Pizza";
            product.Price = 12;
            return product;
        }

        public Product MakeToCar()
        {
            Product product = new Product();
            product.Name = "MakeToCar";
            product.Price = 100;
            return product;
        }
    }
```



###### **模板方法和回调方法的本** 

用委托类型的参数，封装一个外部方法，然后将此方法传递到方法内部，进行间接调用。

区别

模板方法：
相当于“填空题”；
常位于代码中部；
委托有返回值

回调方法：
相当于“流水线”；
常位于代码末尾；
委托无返回值

### 匿名函数

##### 什么是匿名函数

顾名思义,匿名函数就是没有名字的函数;匿名函数的使用主要是配合委托和事件进行使用的;脱离委托和事件是不会使用匿名函数的.

##### 基本语法

~~~C#
delegate (参数列表){
    // 函数逻辑
};
// 何时使用?
// 1.函数中传递委托参数时
// 2.委托或者事件赋值时
~~~

##### 匿名函数的使用

~~~C#
// 1.无参无返回
// ***注意:我们是无法申明匿名函数的,匿名函数是无法脱离了委托和事件存在的,所以匿名函数申明必须作为委托或者事件的右值赋值给委托或者事件,而无法单独存在.
Action ac = delegate (){
	Console.WriteLine("123");
};
// 执行匿名函数
ac();

//2.有参数
Action<int ,string> b = delegate (int a, string b){
    Console.WriteLine(a);
    Console.WriteLine(b);
};

//3.有返回值
Func<string> func = delegate (){
  return "123123";  
};
// 执行
Console.WriteLine(func());

//4.一般情况会作为函数参数传递或者作为函数返回值
// 作为参数传递时
class Test{
    public Action action;
    public void DoSomething(int a, Action fun){
       Console.WriteLine(a);
        fun();
    }
    
    public Action GetFun(){
        return delegate (){};
    }
}

class Test2{
    static void Main(){
        // 参数传递
        Test t = new Test();
        t.DoSomething(100, delegate (){
           Console.WriteLine("随着参数传入的匿名函数"); 
        });
        
        // 返回值
        Action ac = t.GetFun();
		ac();
		// 我们也可以直接一步到位
        // 我们先看前一半ac()返回了一个函数名,函数名＋后一半"()"相当于调用这个 函数
        ac()();
    }
}

~~~

##### 匿名函数的缺点

添加到委托或者事件容器后,不记录下来的话,我们无法单独移除,因为我们的匿名函数没有函数名,所以唯一的方法是把委托或者事件容器全部清空才行.

#### Lambda表达式

##### 什么是Lambda表达式

Lambda表达式可以理解为匿名函数的简写形式，它除了写法上的不同外，使用上和功能上完全与匿名函数一模一样，优缺点也是一模一样的，同理，它也是需要配合委托或者事件使用的。

##### lambda表达式语法

~~~C#
(参数列表)=>{
    // 函数体
};
~~~

##### lambda表达式的使用

~~~C#
    Func<int, int, int> func = new Func<int, int, int>((int a, int b) => { return a + b;});
            int res = func(100, 200);
            Console.WriteLine(res);
            func = new Func<int, int, int>((x, y) => { return x * y; }); //不带类型也OK
            res = func(3, 5);
            Console.WriteLine(res);
            func = ((x, y) => { return x / y; }); //最简洁写法
            res = func(15, 5);
            Console.WriteLine(res);
~~~

```c#
public static void Test()
        {
            //类型可以被推断出来，可以省略
            DoSomeCals(( a, b) => { return a * b;},100,200);
        }

        static void DoSomeCals<T>(Func<T, T, T> func, T x, T y)
        {
          T res=  func(x, y);
          Console.WriteLine(res);
        }
```

