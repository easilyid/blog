---
title: Interface，Abstract
author: Heart
date: 2023-03-03 15:39:36
categories: CSharp
tags:
  - 编程语言
  - CSharp
  - 面对对象编程
index_img: https://cdn.jsdelivr.net/gh/easilyid/Photo/photo/20230304214219.png
excerpt: 接口 抽象类
---

#### 多态和VOB

让继承同一父类的子类们在执行相同方法时候有不同的表现(状态)，主要目的是同一父类的对象执行相同行为(方法)有不同的表现，主要的解决的问题是让同一对象具有唯一行为的特征。

V- virtual (虚函数)  O - override (重写)  B -base (父类)

![VOB](Interface，Abstract/VOB.png)

#### Abstract

抽象类 被关键字 abstract 修饰的类

1. 具体类 -> 抽象类 ->接口 越来越抽象，内部所实现的东西越来越少
2. 抽象类是未完全实现逻辑的类,(可以有字段和非public成员，代表了具体逻辑)
3. 为复用而生 专门做基类使用，具有解耦功能 封装确定的，开放不确定的，推迟到合适的子类中实现

```c#
    public class Ball 
    {
        public void Stop()
        {
            Console.WriteLine("Stop");
        }
        //解决方法        
        public void Run(string type)
        {
            if(type=="Basketball")
            Console.WriteLine("Basket ball running");
             else if(type=="Football")
            Console.WriteLine("Basket ball running");
        }
        
    }
    class Basketball : Ball
    {
        
        public void Run()
        {
            Console.WriteLine("Basket ball running");
        }
    }

    class Football : Ball
    {
        public void Run()
        {
            Console.WriteLine("Football ball running");
        }
    }
```

在这种情况下，然后想用多态的话，只能调用到Stop方法，调用不到Run，因为在基类没有Run方法，可以用上面解决方法解决，但这种写法违反了开闭原则，让新功能的添加不方便。所以我们使用虚方法

```c#
       public virtual void Run()
        {//子类重写记得override
            Console.WriteLine("running");
        }
       同时也可以去掉方法体变纯虚方法
        public abstract void Run();
```

同时类也就变成了抽象类，在这种情况下，**新建一个类派生于抽象类的时候，就必须实现抽象方法**

抽象类，专为做基类而生，不能为基类创造实例，在这种情况下，我们就需要一个纯的抽象类

```c#
    abstract class ballBase
    {
        abstract public void Stop();
        abstract public void Run();
    }
```

这就是一个特别抽象到抽象到具体的一个情况，同时呢这个在实际上就是一个接口

```c#
    //具体类->抽象类->接口 内部实现的东西越来越少 抽象类是未完全实现逻辑的类 为复用而生
    //接口是完全未实现逻辑的类
    interface IBall
    {
        //接口成员都是隐式Public 纯虚类 只有成员
        //接口本身就包含了纯抽象的含义，在实现过程中override也要去掉
        void Stop();
        void Run();
    }
```

#### Interface

- 接口是未完全实现逻辑的类(纯虚类，只有函数成员，全部是隐式Public) 为解耦 
- 接口是行为的抽象规范；它也是一种自定义类型。
- 都不能实例化，只能用来声明变量，引用具体类的实例 类可以继承多个接口，必须实现所有接口成员
- 自底向上(重构)，自顶而下(设计)
- 接口可以被显示实现，主要用于实现不同接口中的同名函数的不同表现
- 实现的接口方法可以加virtual关键字之后子类再去重写

```c#
    public class InterfaceEngine
    {
       public static void Test()
        {
            var engine = new Engine();
            var car = new Car(engine);
            car.Run(3);
            Console.WriteLine(car.Speed);
        }
    }
    class Engine
    {
        public int RPM { get; private set; }
        public void Work(int gas)
        {
            this.RPM = 1000 * gas;
        }
    }
    class Car
    {
        private Engine _engine;

        public Car(Engine engine)
        {
            this._engine = engine;
        }

        public int Speed { get; private set; }
        public void Run(int gas)
        {
            _engine.Work(gas);
            this.Speed = _engine.RPM / 100;
        }
    }
```

在这个例子我们会发现，代码之间紧耦合，car依赖的engine类如果出现修改的话，car也会跟着出现问题，这不是我们希望看到的。解决这个问题的办法就是引入接口，降低代码之间的耦合度。

```c#
    public class InterfacePhone
    {
        public static void Test()
        {
            var user = new PhoneUser(new NokiaPhone());
            user.UserPhone();
        }
    }
    class PhoneUser
    {
        private IPhone _phone;

        public PhoneUser(IPhone phone)
        {
            _phone = phone;
        }

        public void UserPhone()
        {
            _phone.Dail();
            _phone.PickUp();
            _phone.Send();
            _phone.Receive();
        }
    }
    interface IPhone
    {
        void Dail();//拨号
        void PickUp();
        void Send();
        void Receive();//接收
    }
    class NokiaPhone:IPhone
    {
        public void Dail()
        {
            Console.WriteLine("Nokia 拨号中");
        }
        public void PickUp()
        {
            Console.WriteLine("Nokia 诺基亚");
        }
        public void Send()
        {
            Console.WriteLine("Nokia 消息发送中");
        }
        public void Receive()
        {
            Console.WriteLine("Nokia 接收");
        }
    }
    
    class VivoPhone:IPhone
    {
        public void Dail()
        {
            Console.WriteLine("Vivo 拨号中");
        }
        public void PickUp()
        {
            Console.WriteLine("Vivo 诺基亚");
        }
        public void Send()
        {
            Console.WriteLine("Vivo 消息发送中");
        }
        public void Receive()
        {
            Console.WriteLine("Vivo 接收");
        }
    }
```

在接口的写法下，我们如果想更换使用类，只需要更换实例就可以依旧实现相同的功能。

**在代码中，如果有可以替换的存在，就一定会有接口的存在**

PS：当类实现一个接口的时候，类与接口之间的关系也是"紧耦合"

```c#
    public class InterfaceSupply
    {
        public static void Test()
        {
            var fan = new DeskFan(new PowerSupply());
            Console.WriteLine(fan.Work());
        }
    }
    //电源
    class PowerSupply
    {
        public int GetPower()
        {
            return 150;
        }
    }
    class DeskFan
    {
        private PowerSupply _powerSupply;

        public DeskFan(PowerSupply powerSupply)
        {
            this._powerSupply = powerSupply;
        }

        public string Work()
        {
            int power = _powerSupply.GetPower();
            if (power <= 0)
            {
                return "不能工作，没电";
            }
            else if (power < 100)
            {
                return "电不够";
            }
            else if (power < 200)
            {
                return "可以工作";
            }
            else
            {
                return "警告，电流太大";
            }
        }
    }
```

在这个例子中，电源的值是固定的，那么如果在另外的电器的电源需要的值更大，那么很可能就会影响到电扇的使用。代码耦合我们可以使用接口来解耦

```c#
 public class InterfaceSupply
    {
        public static void Test()
        {
            var fan = new DeskFan(new PowerSupply());
            var fan2 = new DeskFan(new PowerSupply());
            Console.WriteLine(fan.Work());
            Console.WriteLine(fan2.Work());
        }
    }
    interface IPowerSupply
    {
        int GetPower();
    }
    
    //电源1
    class PowerSupply: IPowerSupply
    {
        public int GetPower()
        {
            return 150;
        }
    }
    //电源2
    class PowerSupply2: IPowerSupply
    {
        public int GetPower()
        {
            return 260;
        }
    }
    class DeskFan
    {
        private IPowerSupply _powerSupply;
        public DeskFan(IPowerSupply powerSupply)
        {
            this._powerSupply = powerSupply;
        }
        public string Work()
        {
            int power = _powerSupply.GetPower();
            if (power <= 0)
            {
                return "不能工作，没电";
            }
            else if (power < 100)
            {
                return "电不够";
            }
            else if (power < 200)
            {
                return "可以工作";
            }
            else
            {
                return "警告，电流太大";
            }
        }
    }
```



##### 抽象类和接口的区别

抽象类和抽象方法：abstract修饰的类和方法，抽象类不能实例化，抽象方法只能在抽象类中申明，是个纯虚方法，必须在子类中实现

接口：interface自定义类型，是行为的抽象，不包含成员变量，仅包含方法、属性、索引器、事件、成员都不能实现，建议不写访问修饰符，默认public。

**相同点**

- 都可以被继承
- 都不能被直接实例化
- 都可以包含方法申明
- 子类必须实现未实现的方法
- 都遵循里氏替换原则

**不同点**

- 抽象类中可以有构造函数；接口不行
- 抽象类只能被单一继承；接口可以被继承多个
- 抽象类中可以有成员变量；接口不能

- 抽象类中可以申明成员方法，虚方法，抽象方法，静态方法；接口中只能申明没有实现的抽象方法

- 抽象类方法可以使用访问修饰符；接口中建议不写，默认public

##### 如何选择抽象类和接口

表示对象的用抽象类，表示拓展的用接口，不同对象拥有的共同行为，我们往往可以使用接口来实现。

举个例子：

动物是一类对象，我们自然会选择抽象类；而飞翔是一个行为，我们自然会选择接口。



##### 密封方法

概念：用密封关键字sealed修饰的重写函数

作用：让虚方法或者抽象方法之后不能再被重写

特点：和override一起出现

```c#
  abstract class Animal
    {
        public string name;
        public abstract void Eat();

        public virtual void Speak()
        {
            Console.WriteLine("吃");
        }
    }

    class Person : Animal
    {
        public override void Eat()
        {
        }

        public override void Speak()
        {
        }
    }

    class WhitePerson : Peron
    {
        public sealed override void Eat()
        {
        }

        public override void Speak()
        {
        }
    }

    class Son : WhitePerson
    {
        // 我们会发现无法实现Eat()的方法了，因为父类把Eat()方法用sealed修饰了
    }
```

##### 接口隔离原则

我们都知道，接口是一种对供需方均做出了约束的协议。对于供方，“不会少给”很容易做到，因为接口要求供方必须实现其里面的所有方法，否则不能实例化。但对于需方来说，“不会多要”这一点则是软性的。

需求方应该**全部**用到**提供的接口**里的东西，接口中不能存在一些完全没被用过的方法，即接口不能太大。如果接口太大，可以将其拆分成多个小接口。

###### 第一种违反接口隔离原则

将过多的功能包含在接口里，传进来的接口太大，有些方法用不到，故应该分为小接口

 每个小接口都描述单一的功能，把本质不同的功能隔离开，用接口封装起来

```c#
public class Interface_isolationTank
    {
        public static void Test()
        {
            Driver d = new Driver(new Car());
            d.Drive();
        }
    }

    class Driver
    {
        private IVehicle _vehicle;
        private ITank _tank;

        public Driver(IVehicle vehicle)
        {
            this._vehicle = vehicle;
        }
        public Driver(ITank tank)
        {
            this._tank = tank;
        }

        public void Drive()
        {
            _vehicle.Run();
        }
    }

    interface IVehicle
    {
        void Run();
    }

    interface ITank
    {
        void Fire();
        void Run();
    }
    
    class Car : IVehicle
    {
        public void Run()
        {
            Console.WriteLine("Car is Running");
        }
    }


    class LightTank :ITank
    {
        public void Fire()
        {
            Console.WriteLine("小坦克开火");
        }

        public void Run()
        {
            Console.WriteLine("小坦克走");
        }
    }
   
   
```

在这个例子中，我们只想把坦克作为代步工具，因而，ITank这个接口里的Fire方法完全用不到。

而事实上，我们确实可以把坦克这个事物在这个场景的意义下拆分为两方面，一方面是用于行走的轮子，一方面是用于攻击的火炮。因而，我们可以选择把ITank这个接口拆分为两个小接口，让tank全部都接上那两个小接口；

```c#

    class Interface_isolationTank
    {
        static void Main()
        {
            IVehicle tank = new HeavyTank();
            tank.Run();
        }
    }
    
    interface IVehicle
    {
        void Run();
    }
 
 
    interface IWeapon
    {
        void Fire();
    }
 
    class HeavyTank : IWeapon,IVehicle
    {
        public void Fire()
        {
            Console.WriteLine("Boom!!!");
        }
 
        public void Run()
        {
            Console.WriteLine("A heavytank is running......"); 
        }
    }
 
```

但是请注意，这个也不能使用得太过火了，因为它可能会让只有一个方法的小接口越来越多，颗粒度增加。因而，**应该把类的和接口大小都控制在一个范围内**。

###### 第二种违反接口隔离原则的

是一个大接口由两个设计很好的小接口合并，传的时候却传了大接口，这就导致本来会用到的被隔绝在了门外。

例如平常用到的IEnumerable接口，来源于一个更大的接口ICollection

我们需要创建一个只接上IEnumerable的接口，而没有接上ICollection接口的类。

```c#
public class InterfaceIsolation
    {
        public static void Test()
        {
            int[] nums = {1, 2, 3, 4, 5};
            ArrayList nums2 = new ArrayList {1, 2, 3, 4, 5};
            Console.WriteLine(Sum(nums));
            Console.WriteLine(Sum(nums2));
            var nums3 = new ReadOnlyCollection(nums);
            Console.WriteLine(Sum(nums3));
        }

        static int Sum(IEnumerable nums) 
        {
            int sum = 0;
            foreach (var v in nums)
            {
                sum += (int) v;
            }

            return sum;
        }
    }

    //只实现了IEnumerable
    class ReadOnlyCollection : IEnumerable
    {
        private int[] _array;

        public ReadOnlyCollection(int[] array)
        {
            this._array = array;
        }
        
        public IEnumerator GetEnumerator()
        {
            return new Enumerator(this);
        }

        //为了不污染名称空间，成员类
        class Enumerator : IEnumerator
        {
            private ReadOnlyCollection _collection;

            private int _head;
            public Enumerator(ReadOnlyCollection collection)
            {
                this._collection = collection;
                _head = -1;
            }
            public bool MoveNext()
            {
                if (++_head < _collection._array.Length)
                {
                    return true;
                }
                else
                {
                    return false;
                }
            }

            public void Reset()
            {
                _head = -1;
            }

            public object Current
            {
                get
                {//装箱
                    object o = _collection._array[_head];
                    return o;
                }
            }
        }
    }
```

因为传入的是ICollection，把Sum需求者的接口改为ICollection，立马报错，此时你已经把这个IEnumerable拒之门外了。

我们知道，这个功能只需要可迭代就行了。传IEnumerable明显比传ICollection合适。

![image-20230305164940871](Interface，Abstract/image-20230305164940871.png)

这就更加符合我们的接口隔离原则，调用者绝不多要，传进来的接口不应该有用不着的功能

###### 第三个接口隔离的例子

我们将展现C#语言特有的功能，即**显式接口实现**。

一个大接口可被拆分成若干个小接口。有没有一个办法，可以让只需要使用这个小接口时，接口的方法才能被看到呢？这个办法就是显式接口实现。

```c#
public class InterfaceIsolation3
    {
        public static void Test()
        {
            var wk = new WarmKiller();
        }
    }

    interface IGentleman
    {
        void Love();
    }

    interface IKiller
    {
        void Kill();
    }

    class WarmKiller:IGentleman,IKiller
    {
        public void Love()
        {
            Console.WriteLine("I Love You");
        }

        public void Kill()
        {
            Console.WriteLine("I will kill the enemy.");
        }
    }
```

在这个例子中，我们可以看见，在接口的影响下，我们可以看到Kill的方法，对于一个杀手来说，是不应该把Kill的功能暴露出来的

![image-20230305165740447](Interface，Abstract/image-20230305165740447.png)

在这种情况下，就应该用到接口的显示实现

![image-20230305171621173](Interface，Abstract/image-20230305171621173.png)

![image-20230305171722005](Interface，Abstract/image-20230305171722005.png)

这个情况下，Kill就被隐藏起来了，只有在用IKiller情况下才能看到Kill

![image-20230305171843123](Interface，Abstract/image-20230305171843123.png)

接口隔离原则为接口单一原则提供了判断标准。如果调用者只是用了接口的部分功能(a,b,c 三个方法。系统只用到了a,b),那么说明这个接口不够单一

接口功能也要单一(如果a方法除了负责加还负责减乘除取余等等,功能不够单一)

调用者依赖其所需要的，而不依赖其所不需要的
