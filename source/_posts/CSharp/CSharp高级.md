---
title: CSharp高级
date: 2021-12-23 20:09:14
author: Heart
categories: CSharp
tags:
  - 编程语言
  - CSharp
  - 面对对象编程
  - 数据结构
index_img: https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/20211223200531.png
excerpt: 数据结构 委托和事件 多线程
hide: true

---

## C#高级

### 一.简单的数据结构类

#### 1.Arrarylist

##### 1.1.ArrayList的本质

ArrayList是一个C#为我们封装好的类，它的本质是一个object类型的数组，ArrayList类帮助我们实现了很多方法，比如数组的增删改查。



##### 1.2.ArrayList的申明

~~~C#
// ArrayList在System.Collection命名空间里面，使用之前需要引用命名空间
ArrayList array = new ArrayList();
~~~



##### 1.3.ArrayList的增删改查

增：

~~~C#
ArrayList array = new ArrayList();
array.Add(1);
array.Add("12s1");
array.Add(true);
array.Add(new Object());
// 我们还可以使用两个ArrayList拼接，让一个ArrayList内部的所有值追加到另一个ArrayList的后面
ArrayList = array2 = new ArrayList("123");
// 这里我们原本的array里面会在它之前的尾部把新的“123”存进去
array.AddRange(array2);

// 如果我们想要从中间某一个位置插入元素
// 参数1 插入的位置, 参数2 插入的值 
array.Insert(1, "13331");
Console.WriteLine(array[1]);

~~~

首先，ArrayList的优势是可以存储不同类型的值，因为它的本质就是万物之父（Object）类型的数组，所以它的本质使得它可以存储所有类型的值。

删：

~~~C#
ArrayList array = new ArrayList(1);
// 移除指定元素，从头找，找到就删除，如果ArrayList里面存储了两个相同的值，会删除第一个
array.Remove(1);
// 移除指定位置的元素
array.RemoveAt(2);
// 清空
array.Clear();
~~~

改：

~~~C#
ArrayList array = new ArrayList(1);
Console.WriteLine(arrayp[0]);
array[0] = "999";
Console.WriteLine(array[0]);
~~~



查：

~~~C#
ArrayList array = new ArrayList(1);
// 得到指定位置的元素
Console.WriteLine(array[0]);
// 查看元素是否存在，如果存在返回true,不存在返回false
if(array.Contain("123")){
    Console.WriteLine("存在");
}
// 正向(从头开始找)查找元素位置
// 如果找到，那么返回值是位置，如果没有找到，返回值是-1
int index = array.IndexOf(true);
Console.WriteLine(index);
Console.WriteLine(array.IndexOf(1));
// 返向(从尾开始找)查找元素位置
// 如果找到，那么返回值是位置(从头开始找的计数)，如果没有找到，返回值是-1
Console.WriteLine(array.LastIndexOf(1));
~~~

##### 1.4.遍历

~~~C#
ArrayList array = new ArrayList();
array.Add(1);
array.Add("12s1");
array.Add(true);
array.Add(new Object());
// 长度
// ***注意：我们封装的数据结构类里面获取元素长度都是用Count,而不是普通Array.Length方法
// 并且我们也点.不出Length方法，因为在数据结构中容量的定义上就是Length的意思，属于被保护的属性,我们不因该使用Length或者Capacity去遍历我们的数据结构的元素。
Console.WriteLine(array.Count);
// 容量(相当于我们普通类里的Array.Length)
// ***为了避免产生过多的垃圾
Console.WriteLine(array.Capacity);
for(int i = 0;i<array.Count;i++){
    Console.WriteLine(array[i]);
}

// 迭代器遍历
foreach(var item in array){
    Console.WriteLine(item);
}
~~~

##### 1.5.装箱拆箱

因为ArrayList本质上是一个可以自动扩容的object数组，由于用万物之父来存储数据，自然存在装箱拆箱的问题。当往其中进行值类型存储时就是在装箱，当将值类型对象取出来转换使用时，就存在拆箱。所以ArrayList尽量少用，但是不是不用它，它是有着自己独特的特点的，因为它是Object类型的，所以所有类型的东西都可以装，这是其他数据结构所不存在的特点。



##### 1.6.ArrayList和数组的区别

1.ArrayList本质上是一个object类型的数组的封装

2.ArrayList可以不用一开始就定长，单独使用数组是定长的。

3.数组的增删我们需要自己取实现，ArrayList帮我们封装了方便的API来使用

4.ArrayList使用时可能存在装箱拆箱的问题，数组使用时只要不是object数组那就不存在这个问题 

5.数组长度为Length，ArrayList长度为Count(这里的长度只存储了元素位置的长度，而非数组的容量大小)

#### 2.Stack

##### 2.1.Stack的本质

Stack(栈)是一个C#为什么封装好的类，它的本质也是object[]数组，只是封装了特殊的存储规则。

Stack是栈存储容器，栈是一种先进后出的数据结构，先存入的数据后获取，后存入的数据先获取。

Stack(栈)的特点***：先进后出

##### 2.2.Stack的申明

~~~C#
// 使用Stack之前需要引入它的命名空间 System.Collections
Stack stack = new Stack();
~~~



##### 2.3.Stack的增取查清空

增（压栈）：

~~~C#
// 压栈
Stack stack = new Stack();
stack.Push(1);
stack.Push("123");
stack.Push(true);
stack.Push(new Object());
~~~



取（弹栈）：

~~~C#
// 栈中不存在删除的概念，只有取的概念
// 弹栈
var v = stack.Pop();
// 弹栈也是遵循先进后出，后进先出
Console.WriteLine(v);
~~~

查：

~~~C#
// ***注意：栈是无法查看指定位置的元素(因为栈是没有实现索引器的功能)，只能查看栈顶的内容
// 只是查看栈顶的内容，却不取出来
v = stack.Peek();

// 查看元素是否存在于栈中
if(stack.Contains(1.2f)){
    Console.WriteLine("存在1.2f");
}
~~~

清空：(栈是无法改变其中的元素的，因为它本身是没有实现对索引器的功能)

~~~C#
stack.Clear();
~~~

##### 2.4.Stack的遍历

~~~C#
// Stack也一样没有Length的功能，对于获取其元素长度的就只有Count字段
// 长度
Console.WriteLine(stack.Count);

// ***由于我们的栈是没有提供索引器，所以我们是无法通过中括号“[]”的方式获取我们的成员的
// 第一种方法：用foreach遍历，而且遍历出来的顺序也是从栈顶到栈底的
foreach(object item in stack){
    Console.WriteLine(item);
}

// 但是由于我们的栈提供一个转为数组的方法，所以如果我们实在是想用for循环遍历也是可以的。但是要配合下面的方式使用。
// 第二种方法：将栈转换为object数组，再用for循环遍历出来的顺序也从栈顶到栈底的
object[] array = stack.ToArray();
for(int i = 0; i < array.Length; i++){
    Console.WriteLine(array[i]);
}

// 第三种方法：循环弹栈
while(stack.Count>0){
    objetc o = stack.Pop();
    Console.WirteLine(o);
}
~~~



##### 2.5.装箱拆箱

由于是用万物之父来存储数据，自然存在装箱拆箱；当往其中进行值类型存储时候就是在装箱；当将值类型对象取出来转换使用时候，就存在拆箱。



#### 3.Queue

##### 3.1.Queue的本质 

Queue是一个C#为我们封装好的类，它的本质也是object[]数组，只是封装了特殊的存储规则。

Queue是队列存储容器，队列是一种先进先出的数据结构，先存入的数据先获取，后存入的数据后获取。

Queue(队列)的特点***：先进先出，后进后出

##### 3.2.Queue的申明 

~~~C#
// 使用Queue队列需要引用其命名空间 System.Collections
Queue queue = new Queue();
~~~

##### 3.3.Queue的增删查清空

增：

~~~C#
// 进队
Queue queue = new Queue();
queue.Enqueue(1);
queue.Enqueue("123");
quueue.Enqueue(1.4f);
queue.Enqueue(new Test());
~~~

取：

~~~C#
// 出队
// 队列中不存在删除的概念
// 队列中只存在取的概念，取出先加入的对象
object v =queue.Dequeue();
Console.WriteLine(v);
v = queue.Dequeue();
Console.WriteLine(v);
~~~

查：

~~~C#
// 1.查看队列头部元素但是不会移除
v = queue.Peek();
Console.WriteLine(v);

// 2.查看元素是否存在于队列当中,如果存在返回true,不存在返回false
if(queue.Contains(1.4f)){
    Console.WriteLine("存在");
}

~~~

清空：（队列和栈一样，因为没有实现索引器的功能，是无法删除指定元素和修改元素的，如果想要修改，只能先清空之后，再重新存入修改后的值）

~~~C#
queue.Clear();
~~~

##### 3.4.Queue的遍历 

~~~C#
// Queue(队列)和Stack(栈)很相似，而且因为Queue没有索引器，所以我们是无法通过for循环直接用中括号“[]”的方式遍历得到其元素的。
// 第一种遍历方式：用foreach遍历
foreach(object item in queue){
    Console.WriteLine(item);
}

// 第二种遍历方式：将队列转换为object数组
object[] array = queue.ToArray();
for(int i = 0;i<array.Length;i++){
    Console.WriteLine(array[i]);
}

// 循环出列
while(queue.Count>0){
    object o = queue.Dequeue();
    Console.WriteLine(o);
}
~~~

##### 3.5.Queue的装箱拆箱

由于是用万物之父来存储数据，自然存在装箱拆箱；当往其中进行值类型存储时候就是在装箱；当将值类型对象取出来转换使用时候，就存在拆箱。



#### 4.Hashtable

##### 4.1.Hashtable的本质

Hashtable（又称散列表）是基于键的哈希代码组织起来的键/值对，它的主要作用是提高数据查询的效率，使用键来访问集合中的元素

##### 4.2.Hashtable的申明

~~~C#
// 使用Hashtable需要引入其命名空间 Sysytem.Collections
Hashtable hashtable = new Hashtable();
~~~

##### 4.3.Hashtable的增删查改

增：

~~~C#
// 参数1 键, 参数2 值
// 下面的语句意思：整型 1 代表键, "123"代表值
hashtable.Add(1, "123");
// 下面的语句意思：“123”代表键, 整型 2 代表值
hashtable.Add("123", 2);
// 下面的语句意思：true代表键, false代表值
hashtable.Add(true, false);
// 下面的语句意思：false代表键, false代表值
hashtable.Add(false, false);
// ***注意：不能出现相同的键，但是可以是相同的值(也就是不同的键存储了相同的值)
~~~

删（散列表的删除只能通过键去删除，无法通过值去删除，即使传入了一个值也会被当成传入键去操作）：

~~~C#
// 1.只能通过键删除
// ***注意：通过键移除，不代表只移除键对应的值，而且一对键值对一起被移除了
// 把原本hashtable.Add(1, "123");这对键值对”一起“移除
hashtable.Remove(1);

// 2.删除不存在的键——没反应
// ***注意：我们删除不存在的键是不会报错的，也不会有任何的操作反馈
hashtable.Remove(123123);

// 3.清空
hashtable.Clear();
~~~

查：

~~~C#
// 1.通过键查看值，找不到会返回空(不会报错,返回值为空)
// ***注意：散列表里面是不存在下标概念的，”[]“里面写入的是代表键
Console.WriteLine(hashtable[1]);
Console.WriteLine(hashtable["123123"]);
Console.WriteLine(hashtable[true]);

// 2.查看是否存在
// 通过键去查找是否存在，存在返回值为true，不存在返回false,两个方法的作用都是一样的
if(hashtable.Contains("2")){
    Console.WriteLine("存在键位2的键值对");
}
if(hashtable.ContainsKey("2")){
    Console.WriteLine("存在键为2的键值对");
}
// 通过值去查找是否存在，存在返回true，不存在返回false
if(hashtable.ContainsValue(12)){
    Console.WriteLine("存在值为12的键值对");
}
~~~

改（改只能改键对应的值内容，无法修改键）：

~~~C#
hashtable[1] = 100.5f
Console.WriteLine(hashtable[1]);
~~~

##### 4.4.Hashtable的遍历

~~~C#
// 得到散列表里面存储元素的数量
// ***注意：这个得到是键值对的对数
Console.WriteLine(hashtable.Count);

// 第一种遍历方法：遍历所有键
foreach(object item in hashtable.Keys){
    // 打印它的键
    Console.WriteLine(item);
    // 打印键对应的值
    Console.WriteLine(hashtable[item]);
}

// 第二种遍历方法：遍历所有的值
foreach(object item in hashtable.Values){
    Console.WriteLine(item);
}

// 第三种遍历方法：键值对一起遍历
// DictionaryEmtry按F12进去就会发现是一个结构体，结构体里面存了一个键，一个值
foreach(DictionaryEntry item in hashtable){
    Console.WriteLine("键：" + item.Key + " 值：" + item.Value);
}

// 第四种遍历方法：迭代器遍历法
IDictionaryEnumerator myEnumerator = hashtable.GetEnumerator();
bool flag = myEnumerator.MoveNext();
while(flag){
    Console.WriteLine("键：" + myEnumerator.Key + "值：" + myEnumerator.Value);
    flag = myEnumerator.MoveNext();
}
~~~



##### 4.5.装箱拆箱

由于是用万物之父来存储数据，自然存在装箱拆箱；当往其中进行值类型存储时候就是在装箱；当将值类型对象取出来转换使用时候，就存在拆箱。



### 二.泛型

#### 1.泛型的知识

##### 1.1.什么是泛型

泛型实现了类型参数化，达到代码重用的目的，通过类型参数化来实现同一份代码上的多种类型操作。

泛型相当于类型占位符，定义类或方法时候使用替代符代表变量类型，当真正使用类或者方法时候再具体指定类型。

##### 1.2.泛型的分类

~~~C#
// 泛型的分类就两种 泛型类和泛型接口
// 基本语法
class 类名<泛型占位字母>
// 举例：下面就是一个泛型类
class Test1<T>{
    public T value;
}
// 当我们要用这个类的时候，我们需要在申明跟初始化的时候去告诉我们的编译器它的泛型是什么类型
// 举例：下面就是一个泛型类的使用
// 我们把泛型指定为了int类型，这时候我们Test里面的成员value就是一个int类型的字段了
Test<int> test = new Test<int>();

// 我们把泛型指定为了string类型，这时候我们Test里面的成员value就是一个string类型的字段了
Test<string> test = new Test<string>();
// ***总结：我们可以很明显的看到了，我们声明的泛型类可以满足我们的不同需求，一份代码可以多次重用，这是泛型类最大的特点

interface 接口名<泛型占位字母>
// 举例：下面就是一个泛型接口的使用
interface TestInterface<T>{
    T value{
        get;
        set;
    }
}
// 接着我们再让我们的Test2类继承这个接口
// 我们可以看到，当我们继承了某个泛型接口的时候，这时候我们是需要指定这个泛型的具体类型的
class Test2:TestInterface<int>{
    
}
// *注意：泛型接口一样可以拥有多个泛型占位字母

// 泛型函数
// 基本语法：函数名<泛型占位字母>(参数列表)
// 举例：下面就是一个有一个参数的泛型函数
public void Func<T>(T t1){
    
}
// ***注意：泛型占位字母可以有多个，用逗号分开
// 举例：下面这个泛型方法
public void Func2<K, V>(K k1, V v1){
    
}
// 类和接口也是一样的
class Test3<T1, T2, K, M LL, Key Value>{
    public T1 value;
    public T2 value;
    public K value;
    public MM value;
    public LL value;
    public Key value;
    public Value value;
}

// *注意：泛型占位字母/泛型占位符不单单只是“T”，还可以是任意的字母，只要是写在尖括号“<>”里面的字母，都算是泛型占位符
~~~

##### 1.3.泛型方法

###### 1.普通类中的泛型方法

~~~C#
class Test2{
    // 下面是普通类中泛型方法的申明
    public void TestFun<T>(T value){
        Console.WriteLine(value);
    }
    
    // 你也可以使用我们的无参泛型方法
    // ***注意：泛型方法也是方法，所以它是支持函数重载的。
    public void TestFunc<T>(){
        // 泛型方法的内部去处理一些逻辑
        // 例如得到我们类型的默认值
        T t = default(T);
    }
    
    // 我们还可以利用泛型做一些特殊的返回值
    public T TestFunc<T>(string value){
        return default(T);
    }
    
    // 同理泛型方法也是可以有多个泛型占位符的
	public void Func3<T1, T2, T3>(T1 t1, T2 t2, T3 t3){
        
    }
}

// 接下来看如何使用我们申明的泛型方法
class Program{
    static void Main(string[] args){
        // 第一步，先申明跟实例化我们的类
        Test2 test2 = new Test2();
        // 第二部，使用我们的泛型方法，先把泛型占位符指定为具体的类型，然后再填写函数参数
        test2.TestFun<string>(“123”);
       
        // ***注意:当我们有多个泛型方法的时候，我们是可以省略尖括号“<>”的方式，以直接传参数的方式，让编辑器自己去根据我们的参数类型确认调用哪个泛型方法，例如我们的函数重载一样。
        // 举例子：我们的类中有一个是泛型方法但是参数是string类型的TestFunc<T>(string value)，还有一个是TestFunc<T>(T t1)
        // 我们直接调用方法，并且传入一个字符串，这时候编辑器会默认调用我们的TestFunc<T>(string value)方法
        // 我可以运行后，把鼠标放在下面这个语句上，然后按F12定位到它调用的函数，这样也可以找到我们调用了哪个泛型函数
        test2.TestFunc("123");
    }
}
~~~



###### 2.泛型类中的泛型方法

~~~C#
// ***注意：class Test{}和class Test<T>虽然看似是同名类，但是他们是不一样的类，你可以把<T>也看作类名的一部分来看。
// class Test{}是普通类，class Test<T>{}是泛型类，功能是也是不同的，所以是不一样的两个类，互不相关。
class Test<T>{
    public T value;
    
    // ***注意：这个不叫泛型方法，因为T是泛型类申明的时候就指定，在使用这个函数的时候，我们就不能再去动态的变化了
    // 那么什么是泛型方法？简单的说就是函数名后面带着一对尖括号“<>”的我们就可以当作是泛型方法
	public void TestFun(T t){
    
	}
}

// 使用
class Program{
    static void Main(stringp[] args){
        // 泛型类，我们再申明和实例化的时候就需要指定我们的具体类型了
        Test<int> test = new Test<int>();
        // 调用泛型类中的方法的时候就不需要去指定了，直接调用。
        test.TestFun();
    }
}

~~~

##### 1.4.总结

1.申明泛型时，它只是一个类型的占位符

2.泛型真正起作用的时候，是在使用它的时候

3.泛型占位字母可以有n个用逗号分开

4.泛型占位字母一般是大写字母

5.不确定泛型类型时获取默认值可以使用default(占位字母)

6.看到<>的字母那肯定是泛型



###### *泛型的选择和作用

1.让不同类型对象的相同逻辑处理就可以选择泛型

2.使用泛型可以一定程度上避免装箱拆箱

###### 举例：优化我们的ArrayList

~~~C#
// 我们知道，ArrayList是一个object类型的数组组成，所以它是存在装箱拆箱的问题了，但是如果我们自己利用泛型重新一个ArrayList就可以达到我们优化的效果
class ArrayList<T>{
    private T[] array;
    public void Add(T value){
        
    }
    public void Remove(T value){
        
    }
}
~~~



#### 2.泛型的约束

##### 2.1.什么是泛型约束

让泛型的类型有一定的限制

关键字：where

泛型约束一共有6种

1.值类型															where 泛型字母：struct

2.引用类型														where 泛型字母：class

3.存在无参公共构造函数								 where 泛型字母：new()

4.某个类本身或者其派生类							 where 泛型字母：类名

5.某个接口的派生类型								     where 泛型字母：接口名

6.另一个泛型类型本身或者派生类型			  where 泛型字母：另一个泛型字母

基本语法：where 泛型字母：约束的类型

##### 2.2.各种泛型的约束讲解

~~~C#
// 1.值类型约束
// 这样就代表了T一定是一个结构体，因为结构体是值类型(这里肯定会有疑问，那如果不是结构体呢？我传入int、float这类值类型还可以吗？下面的使用案例会有解释)
class Test<T> where T:struct{
    public T value;
    // 这样代表泛型方法的泛型字母K也是一个值类型类型
    public void TestFunc<K> where K:struct{
        
	}
}
// 使用
class Program{
    static void Main(string[] args){
        // 使用案例
        // 大家会发现只要是值类型的都可以，那为什么上面我会说是T一定是一个结构体呢？我们可以把光标点一下int或者float然后按F12进入看看int和float的实现源码
        // 当我们对int按下F12进去的时候，我们进入了
        // public readonly struct Int32 : IComparable, IComparable<Int32>, IConvertible, IEquatable<Int32>, IFormattable{.....}
        // 说明int真正的名字是int32，int只是int32的别名，它前面struct表示了int32是一个结构体，说明int类型本身就是一个结构体，同理的，也可以对float进行同样的操作，float的真正名字是Single,float是别名，它也是一个结构体类型。
        // 为了避免产生疑问，我们之前学的结构体，算是自定义结构体的统称，其实只要是是值类型的实现，它的本身就是一个结构体。
        Test<int> test = new Test<int>();
        Test<float> test2 = new Test<>(float);
        test.TestFunc<int>();
    }
}
~~~

~~~C#
// 2.引用类型约束
// 泛型必须是引用类型
class Test2<T> where T:class{
    public T value;
    public void TestFun<K>(K k) where K:class{
        
    }
}
class Program
    {
        static void Main(string[] args)
        {
            // 使用案例
            Test2<Random> test = new Test2<Random>();
            test.TestFun<object>();
        }
    }
~~~

~~~C#
// 3.公共无参构造约束
// 这个表示我们的泛型指定类型必须一个具有“公共”“无参”构造函数，两个条件缺一不可
// ***注意：结构体也满足此条件，如果有疑问可以接着往下看
class Test3<T> where T:new(){
    public T value;
    public void TestFun<K>() where K:new(){
        
	}
}
// 为了方便测试，我们再写一个用于测试的类
class Test4{
  // 当我们没有写任何构造函数的时候，会默认调用自带的公共无参构造函数
  // 一定不能是私有类型或者保护类型！必须是public类型
  // ***注意：当我们自己写了一个构造函数的时候，会顶掉我们的默认构造函数；如果我们写了一个无参的构造函数，其实意义不大就跟默认的公共构造函数的作用是一样的。但是一定记住，只要写了我们自己的构造函数之后，该类就会失去其原有的默认的公共无参构造函数。
}
// 使用案例
class Program
    {
        static void Main(string[] args)
        {
            // 因为我们Test4里面自带了默认的公共无参构造函数，所以下面这条语句没问题
            // ***注意：当是我们不可以传入一个抽象类，因为抽象类是无法被实例化创建的。
            Test3<Test4> test = new Test3<Test4>();
            
            
            // ***注意：我们学过结构体也明白，结构体也是可以写构造函数的
            // 我们是可以写入一个结构体类型的，因为结构体也具有无参构造函数
            Test3<int> test3 = new Test3<>(int);
            
            // ***注意：再提醒一下，结构体和类不一样，类中我们是可以写显式的无参构造函数的，但是在结构体中，我们是无法在其中写一个不带参数的无参公共构造函数的，这是因为编译器不允许的(为什么不被允许，可以自行Google，因为解释起来麻烦，具体的为什么在Unity中结构体无法作为等式的左值进行赋值修改，例如:this.transform.position.x = 10f，为什么不被允许，也是跟结构体和编辑器有关，可以自行Google。)，其次，注意一点，如果我们一定想在结构体中写构造函数的化，我们是必须实现其全部成员的带参构造函数，也就是说，我们的必须对其所有成员变量进行初始化。说到这里，再提醒一点，结构中声明的字段是不允许被初始化的(自行Google).
        }
    }
~~~

~~~C#
// 4.某个类本身或者其派生类
class Test1{
    
}

class Test2:Test1{
    
}
// where T:类名约束的内容是该类型必须是该类名本身或者其派生类
// 下面约束的就是该类型必须是Test1本身这个类或者其子类Test2
class Test<T> where T:Test1{
    
}
class Program
    {
        static void Main(string[] args)
        {
            // 使用案例
			Test<Test1> test1 = new Test<Test1>();
        	Test<Test2> test2 = new Test<>(Test2);
        	// 注意传入的必须是这个类或者其子类，如果是这个类的父类是不允许的，会报错的，例如我们传入万物之父Object类，编辑器是会报错误的。
        }
    }
~~~

~~~C#
// 5.接口约束
interface IFly{
    void Fly();
}

interface IJump:IFly{
    
}

class Test2:IFly{
    // 这一行不需要管，因为我们继承了接口就需要实现接口的方法
    public void Fly()
    {
        // 这一行不需要管，这里是抛出没有逻辑内容错处
        throw new NotImplementedException();
    }
}
// 接口约束，是指定类型是某个接口的派生类型
class Test<T> where T:IFly{
    
}
// 使用案例            
class Program
    {
        static void Main(string[] args)
        {
            // 只要是接口本身或者其派生接口或者是继承至接口的普通类都可以传入
            Test<Test2> test = new test<Test2>();
            Test<IFly> test2 = new Test4<IFly>();
            Test<IJump> test3 = new Test4<IJump>();
        	
        }
    }
~~~

~~~C#
// 6.另一个泛型约束
class Test1{
    
}

class Test2 : Test1{
    
}

class Test<T, U> where T:U{
  
}

class Program
    {
        static void Main(string[] args)
        {
            // 这里有点绕，我们拆开来看
            // 我们把U先替换成Test1，这时候原本的class Test<T, Test1>where T : Test1{}是不是变成了，我们对类名的约束，也就是说，这时候剩下的泛型占位字母T要么是这个Test1类，要么是其子类
            Test<Test2, Test1> test = new Test<Test2, Test1>();
        }
    }
~~~

##### 2.3.约束的组合使用

举个例子：

~~~C#
// 这里相当于我们约束了泛型占位字母T的类型必须是引用类型且拥有公共无参的构造函数
// ***注意：这里的class和new()两个约束条件是能倒过来，先写new()再写class的，因为，我们是现先有了类，才能有构造函数的
class Test<T> where T:class, new(){
    
}
// 给个建议：泛型约束的组合使用根据排列组合可以拥有很多种组合方式，这里不一一的列举，我们可以在编辑器是自行尝试，只要是编辑器不报错误都算是一种约束的组合。
~~~

##### 2.4.多个泛型有约束

举个例子：

~~~C#
// 注意：多个泛型的约束是一个空格隔开，几个泛型字母就写几个where关键字，不要乱加逗号、冒号
class Test<T, K> where T:class, new() where K:struct{
    
}
~~~



##### 总结

泛型约束：让类型有一定限制

泛型约束的分类：class、struct、new()、类名、接口名、另一个泛型字母

注意：

1.可以组合使用

2.多个泛型约束用where连接即可



##### 实践

(以下内容，是我学习之余，收藏总结前人与网络上各大神分享再去结合我个人对单例模式的理解与思考做出来的结果，如果需要了解原文，附链接：https://www.cnblogs.com/zszh/p/10755256.html；https://blog.csdn.net/weixin_38531633/article/details/108975868，感谢各大大佬的分享才有开源的环境)

###### 1.*如何利用泛型写一个简单普通的单例基类？

~~~C#
//以下代码是 不支持私有的无参构造函数的单例泛型(总结的时候会说明)
using System;
/// <summary>
/// 简单的单例模式基类
/// </summary>
/// <typeparam name="T">待指定的泛型类型，该类型约束了必须是带有公共无参构造函数的</typeparam>
public class SingleBase<T> where T : new()
{
    // 把单例模式私有化，不允许外面修改
    private static T instance = new T();

    // 写一个私有的构造无参构造函数，防止外面实例化单例基类
    private SingleBase()
    { 
		
    }

    // 通过属性中的get方法得到单例
    public static T GetInstance
    {
        get
        {
            return instance;
        }
    }
}	
// 总结
// 注意该单例是有缺陷的，因为继承了这个单例基类的子类是必须带有公共无参构造函数的，因为是public的，所以可以在外部实例化这个单例，但是众所周知，单例是不因该被初始化的，所以这里还可以进行进一步的优化。
// 总结该单例基类特点：
// 1：where 约束：引用类型/值类型
// where T:class 是 引用类型约束，使用class关键字
// 引用类型约束将一个类型形参限定为引用类型。
// 引用类型一般是用户定义的类型,包含类/接口/委托/字符串和数组类型。
// 在这个where子句中,class关键字是指定T必须是引用类型。因此,尝试对T使用值类型,将会导致编译错误。
// where T:struct 是 值类型约束，使用struct关键字
// 值类型约束将一个类型形参限定为值类型。
// 值类型派生于System.ValueType类型。基元和结构都是值类型。
// 在该形式中,struct关键字指定T必须是值类型。因此,尝试对T使用引用类型,将导致编译错误。

// 2：where 约束：new()注意：*【new不支持私有的无参构造函数】(这里也是我们第一种该单例的缺陷，后面会讲如何优化)
// 使用new()约束时应当注意:
// 1.new()约束可以与其他约束一起使用,但必须位于约束列表的末端
// 2.new()约束仅允许开发人员使用无参数的构造函数构造一个对象,
//即使同时存在其他的构造函数也是如此。即不允许给类型形参的构造
// 函数传递实参。
// 3.不可以同时使用new()约束和值类型约束。因为值类型都隐式的提
// 供了一个无参公共构造函数。
// 就如同定义接口时指定访问类型为public一样,编译器会报错,因为接口一定是public的。
~~~

###### 2.*如何利用泛型写一个多线程的泛型单例基类(带锁线程可释放)？

~~~C#
// 以下代码是 不支持私有的无参构造函数的单例泛型(总结的时候会说明)
class Singleton<T> where T: class,new()  
{  
    // 定义一个静态变量来保存类的实例
    private static T _instance; 
    // 定义一个标识确保线程同步
    private static readonly object syslock=new object();  
  
    /// <summary>
    /// 定义公有方法提供一个全局访问点,
    /// 同时你也可以定义公有属性来提供全局访问点
    /// </summary>
    /// <returns></returns>
    public static T getInstance()   
    {  
        // 当第一个线程运行到这里时，此时会对locker对象 "加锁"，
        // 当第二个线程运行该方法时，
        /// 首先检测到locker对象为"加锁"状态，
        /// 该线程就会挂起等待第一个线程解锁
        /// lock语句运行完之后（即线程运行完之后）
        /// 会对该对象"解锁"
        // 双重锁定只需要一句判断就可以了
        if (_instance == null)  
        {  
            lock (syslock) {  
                // 如果类的实例不存在则创建，否则直接返回
                if (_instance == null)  
                {  
                	//T如果没有约束new() 这里是不允许new的
                    _instance = new T();  
                }  
            }  
        }  
       return _instance;  
    }  
} 
// 总结
// 该单例父类的具有第一种的所有特点，且双重线程锁，更加安全
~~~

###### 3.如何写出一个“完美的”单例基类(支持私有的无参构造函数的单例泛型)？

~~~C#
public class BaseInstance<T> where T : class//new()，new不支持非公共的无参构造函数 
    {
        /*
         * 单线程测试通过！
         * 多线程测试通过！
         * 根据需要在调用的时候才实例化单例类！
          */
        private static T _instance;
        private static readonly object SyncObject = new object();
        public static T Instance
        {
            get
            {
                if (_instance == null)//没有第一重 singleton == null 的话，每一次有线程进入 GetInstance()时，均会执行锁定操作来实现线程同步，
                //非常耗费性能 增加第一重singleton ==null 成立时的情况下执行一次锁定以实现线程同步
                {
                    lock (SyncObject)
                    {
                        if (_instance == null)//Double-Check Locking 双重检查锁定
                        {
                            //_instance = new T();
                            //需要非公共的无参构造函数，
                            //不能使用new T() ,new不支持非公共的无参构造函数 
                            _instance = (T)Activator.CreateInstance(typeof(T), true); 
                            //第二个参数防止异常：“没有为该对象定义无参数的构造函数。”
                        }
                    }
                }
                return _instance;
            }
        }
        public static void SetInstance(T value)
        {
            _instance = value;
        }
    }
~~~



###### 4.*C#在Unity中的写一个完善的单例基类

~~~C#
using System;
using UnityEngine;

public class SingletonProvider<T> where T : class ,new()
{
    private SingletonProvider()
    {
    }

    private static T _instance;
    // 用于lock块的对象
    private static readonly object _synclock = new object();

    public static T Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_synclock)
                {
                    if (_instance == null)
                    {
                        // 若T class具有私有构造函数,那么则无法使用SingletonProvider<T>来实例化new T();
                        _instance = new T();
                        //测试用，如果T类型创建了实例，则输出它的类型名称
                        Debug.Log("{0}：创建了单例对象" + typeof(T).Name);
                    }
                }
            }
            return _instance;
        }
        set { _instance = value; }
    }
}

~~~

### 三.常用泛型数据结构类

#### 数据结构的概念

数据结构是计算机存储、组织数据的方式(规则)，数据结构是指相互之间存在一种或者多种特定关系的数据元素的集合，比如自定义的一个类也可以称为一种数据结构，自己定义其数据组合规则

常用的数据结构：数组、栈、队列、链表、树、图、堆、散列表(哈希表)



#### 1.List



#### 2.Dictionary



#### 3.顺序存储和链式存储

##### 3.1线性表

线性表是一种数据结构，是由n个具有相同特性的数据元素以有限且有序的连接起来的序列，例如：数组、ArrayList、Stack、Queue、链表等等。

顺序存储和链式存储是数据结构中两种存储结构

##### 3.2顺序存储

常见的顺序存储：数组、Stack、Queue、List、ArrayList

顺序存储的特点：用一组地址连续的存储单元依次存储线性表的各个数据元素。（所以我们是无法动态扩容的，只能声明与初始化一个比原来更大的数据结构，然后改变原来的引用）

##### 3.3链式存储

常见的链式存储：单向链表、双向链表、循环链表

链式存储(连接存储)：用一组任意的存储单元存储线性表中的各个数据元素

##### 3.4顺序存储和链式存储的优缺点

增：链式存储计算上优于顺序存储（中间插入时链式不用像顺序一样去移动位置）

删：链式存储计算上优于顺序存储（中间删除时链式不用像顺序一样去移动位置）

查：顺序存储使用上优于链式存储（数组可以直接通过下标得到元素，链式需要遍历）

改：顺序存储使用上优于链式存储（数组可以直接通过下标得到元素，链式需要遍历）

##### 总结

顺序存储：内存中用一组连续的存储单元存储的线性表

链式存储：内存中用一组不连续的存储单元存储的线性表

#### 4.LinkedList

##### 4.1LinkedList概念

LinkedList是一个C#为我们封装好的类，它的本质是一个可变类型的泛型双向链表。

##### 4.2LinkedList的申明

~~~C#
// 需要引入命名空间 using System.Collections.Generic
LinkedList<int> linkedList = new LinkedList<int>();
// 掌握LinkedList类链表对象需要掌握两个类，一个是链表本身LinkedList，还有一个是链表节点类LinkedNode
~~~

##### 4.3LinkedList的增删查改

增：

~~~C#
// 1.在链表尾部添加元素
linkedList.AddLast(10);

// 2.在链表头部添加元素
linkedList.AddFirst(20);

// 3.在某一个节点之后添加一个节点
// 我们需要指定节点之前要先得到一个节点
// 举例：我们要在20的后面插入一个15，那么，我门要先找到我们存储了20的节点，然后再添加进去
LinkedListNode<int> n = linkedList.Find(20);
// 参数1 查找的指定节点位置， 参数2 要存储的节点的值(这里我们不需要自己new 一个新节点，我们直接传入我们需要的值就行)
linkedList.AddAfter(n, 15);

// 4.在某一个节点之前添加一个节点
// 我们需要在指定节点之前先得到一个节点
// 与3同理
LinkedListNode<int> n = linkedList.Find(20);
linkedList.AddAfter(n, 11);
~~~

删：

~~~C#
// 1.移除头节点
linkedList.RemoveFirst();

// 2.移除尾节点
linkedList.RemoveLast();

// 3.移除指定节点(但是我们是无法通过下标位置直接移除的)
linkedList.Remove(20);

// 4.清空
linkedList.Clear();
~~~

查：

~~~C#
// 1.头节点
LinkedListNode<int> first = linkedList.First;

// 2.尾节点
LinkedListNode<int> last = linkedList.Last;

// 3.找到指定值的节点(无法直接通过下标位置获取中间元素，只有遍历查找指定位置元素)
LinkedListNode<int> node = linkedList.Find(3);
node = linkedList.Find(5);

// 4.判断是否存在
if(linkedList.Contains(1)){
    Console.WriteLine("链表中存在1");
}
~~~

改：

~~~C#
// 需要先得到要改的节点，再去改变其中的值
// 举例：我们要改头节点的值
linkedList.First.Value = 10;
~~~

##### 4.4LinkedList的遍历

~~~C#
// 1.foreach遍历
// 这里做一些解释，为什么我们不需要声明节点而是直接声明int就可以得到链表的值。因为在底层编辑器用迭代器的方式帮我处理了，所以我们可以直接访问到其中的值。(迭代器具体后面有迭代器讲解)
foreach(int item in linkedList){
    Console.WriteLine(item);
}

// 2.通过节点遍历
// 从头到尾遍历
LinkedListNode<int> headNode = linkedList.First;
while(headNode != null){
   Console.WriteLine(headNode.Value);
   headNode = headNode.Next;
}
// 从尾到头遍历
LinkedListNode<int> headNode = linkedList.Last;
while(headNode != null){
   Console.WriteLine(headNode.Value);
   headNode = headNode.Previous;
}
~~~

#### 5.泛型栈和队列

##### 5.1回顾数据容器

###### 变量：

无符号

byte ushort uint ulong

有符号

sbyte short int long

浮点数

float double decimal

特殊

char(***注意：C#中char类型是占两个字节，C与C++中是占一个字节) bool string

###### 复杂数据容器：

枚举 enum，结构体 struct， 数组(一维[]，二维[,]， 交错"[] []"[][])，类

###### 数据集合：

using System.Collections下的数据结构：

ArrayList(object数据列表)，Stack(栈，先进后出)，Queue(先进先出)，Hash table(哈希表，键值对)

这类特殊数据结构的优点：它们都是Object基类的数组，说明它们任何引用类型都可以存储(除了自定义类且不继承object或者其派生类)，相当于一个大杂烩的容器，我们不需要指定特定的类型。

这类特殊数据结构的缺点：可能存在装箱拆箱的问题(核心有做解释)。

###### 泛型数据集合：

using System.Collections.Generic下的泛型数据集合：

List(列表，泛型队列)，Dictionary(字典，泛型哈希表)，LinkedList(双向链表)，Stack(泛型栈)，Queue(泛型队列)。

***注意：Object的数据集合(数据结构)我们会很少去使用，一般常用都是泛型数据集合，根据需求选择合适的泛型数据结构。

##### 5.2泛型栈和队列

~~~C#
// 命名空间 using System.Collections.Generic;
// 使用上和之前学习过的栈和队列是一模一样的，所以这里就只做基本语法
Stack<int> stack  = new Stack<int>();
// 增(压栈)
stack.Push();
// 删(弹栈)
stack.Pop();
// 查
// ***注意：栈是无法查看指定位置的元素(因为栈是没有实现索引器的功能)，只能查看栈顶的内容
// 只是查看栈顶的内容，却不取出来
// 第一种：查看栈顶的值，无法取出
stack.Peek();
// 第二种：查看元素是否存在于队列当中,如果存在返回true,不存在返回false
stack.Contains();
// 清空
stack.Clear();
Queue<int> queue = new Queue<int>();
// 增(进队)
queue.Enqueue();
// 删(出队)
// 队列中不存在删除的概念
// 队列中只存在取的概念，取出先加入的对象
queue.Dequeue();
// 查
// 第一种：查看头部元素是否存在其值
queue.Peek();
// 第二种：查看元素是否存在于队列当中,如果存在返回true,不存在返回false
queue.Contains();
// 清空
queue.Clear();
~~~

#### 总结

简单的总结一下我们常见存储容器如何选择使用：

###### 普通线性表：

数组，List，LinkedList;

数组：固定的不变的一组数据

List：经常改变，经常通过下标查找

LinkedList：不确定长度的，经常临时插入改变，查找不多

###### 先进后出：

Stack：对于一些可以利用先进后出存储特定的逻辑

比如：UI面板显隐规则

###### 先进先出：

Queue：对于一些可以利用先进先出存储特点的逻辑

比如：消息队列

###### 键值对：

Dictionary：需要频繁查找的，有对应关系的数据

比如：一些数据存储，id对应数据内容

道具ID -----> 道具信息

怪物ID -----> 怪物对象

***注意：我们的Dictionary还常常用于对象池子的使用。





### 四.委托和事件

#### 1.委托

##### 1.1委托是什么？

委托是函数(方法)的容器，可以理解为表示函数(方法)的变量类型，用来存储、传递函数(方法)。委托的本质是一个类，用来定义函数(方法)的类型(返回值和参数的类型)，不同的函数(方法)必须对应和各自"格式"一致的委托。

***注意：我们时刻要记住委托变量是函数的容器！！！！

##### 1.2基本语法

关键字：delegate

语法：访问修饰符 delefate 返回值 委托名(参数列表)；(我们可以抽象记忆，委托就是函数的命名方法再加上一个delegate的关键字)

写在哪里？

一般可以什么在namespace和class语句块中，但是更多的是写在namespace中。

##### 1.3如何定义自定义委托?

~~~C#
// 访问修饰默认不写，则为public，中别的命名空间中也可以使用
// private 其他命名空间就不能用了，一般使用public
//这里申明了一个可以用来存储无参无返回值函数的容器
// 现在这里只是定义了规则，并没有使用
delegate void Fun();
// ***注意：(在同一语句块中)委托不存在同名的两个委托，编辑器会报错误的，它不是函数不能重载
delegate int Fun2(int a);// 表示用来装载或者传递返回值为int 有一个int参数的函数的委托容器规则
~~~

##### 1.4如何使用定义好的委托？

~~~C#
// ***注意：委托变量是函数的容器
// 首先先说明一点，委托的本质是一个类，下面的示例会是完整的可以运行代码
namespace Csharp进阶笔记
{
    
	// 委托的本质是一个类，直接声明在namespace与class之间，当然也可以在namespace之外或者class之内声明委托
    delegate void Func();
    // 申明了一个带int类型参数且返回值类型为int的委托
    delegate int void Func2(int i);
    class Program
    {
        static void Main(string[] args)
        {
            // 委托的存储函数
            // 第一种：委托的实例化(***注意：委托不具有0个参数的构造函数，所以我们的实例化必须传入参数)
            // 专门用来装载函数的容器，这时候我们只是装载了还没有调用函数
            // ***注意：我们存储的函数不需要加"()"，加(表示取调用它了)，我们这里只是需要存起来，不是需要取调用它的
            Func fun = new Func(Fun);
            
            // 第二种
            // 我们可以直接用赋值的方式给委托赋值
            // 这里我们也是只需要函数名，不需要"()"，加了"()"就表示调用这个函数了。
            Func fun2 = Fun;
            // ***注意：只有符合规则的函数才可以存进我们申明好了的委托中，例如我们上面申明了一个无参无返回值的委托，那么存储的函数就必须也是无参无返回值的函数 
            
            // 委托的调用(相当于函数的调用)
            // 第一种
            fun.Invoke();
            // 第二种
            fun();
            
            // 演示带参数带返回值的委托怎么存储和调用
            Func2 func3 = Func3;
            func3(10);
            func3.Invoke(20);
        }
        static void Fun()
        {
			Console.WriteLine("Fun");
        }
        static void Fun2(){
            Console.WriteLine("Fun2");
        }
        static int Fun3(int value){
            return value;
        }
    }
    
    // 委托以经常用在：
    // 1.作为类的成员
    // 2.作为函数的参数
    class Test{
        public Func fun;
        public Func2 fun2;
        
        public void TestFun(Func func, Func2 func2){
            // 我们会在调用委托之前处理许多的游戏有逻辑
            // ......（处理的逻辑）
            // 然后调用我们的委托
            fun();
            fun2();
            
            // 当然我们也可以存进来，然后自己选择执行
            // this.fun = func
            // this.fun2 = func2
        }
    }
}
// 注意：我们在使用的时候是传入我们的函数名字，不要!不要！不要!加括号。
// 举例：
// Test t = new Test();
// t.TestFun(函数名，函数名);一定不要加括号！！！！
~~~

##### 1.5委托变量可以存储多个函数(多播委托)

~~~C#
// 记住：委托是函数的容器
// 我们的委托是可以存储多个符合委托规则的函数
// 增加
Func fun = new fun(Fun);
fun += Fun2;
// 这时候两个函数都会被调用
fun.Invoke();
public void Fun(){
    
}
public void Fun2(){
    
}
// 我们也可以先让委托容器为空，再有+=方式添加
// Func fun = null;
// fun += Fun;
// fun += Fun2;

// 删除
// 删除的方式很简单，原理跟加是相似的，只是区别是把“+”换成“-”
// ***注意：多减是不会报错的，无非是找不到然后不做任何处理
fun -= Fun;

// 清空委托容器
fun = null;
// ***注意：委托为空的时候，我们是不能去调用的，如果运行了，是控制台会闪退，然后编辑器报错。所以我们委托调用之前最好的方式是进行判空。
if(fun != null){
    fun.Invoke();
}
// 这里介绍一种语法糖 fun?.Invoke();（自行Google）
~~~

##### 1.6系统定义好的委托

~~~C#
// 需要引入命名空间 using System;
// 1. Action-----无参无返回值的委托
Action action = null;

// 2.Func<T>-----泛型委托(一个可以根据泛型指定返回值类型的泛型无参委托)
Fction func = null;
// 我们也可以定义我们自己的泛型委托
delegate T MyFunc<T, K>(T t, K k);

// 3.Action<>-----这是一个可以传最少1个最多16个不同类型参数的无返回值委托
Action<int, string, bool> action2 = null;

// 4.Func<>-----可以传n(1-15)个参数的并且有返回值的委托，系统提供了16种重载
// 这个委托就表示了带有一个int类型参数的返回值为int类型的委托容器
Func<int, int> func2 = null;

// 系统也会提供其他的委托，但是这里这四类足够我们日常的使用了
~~~

#### 2.事件

##### 2.1事件是什么?

事件是基于委托的存在，事件是委托的安全包裹，让委托的使用更具有安全性

事件是一种特殊的变量类型;

##### 2.2事件的使用

申明语法：

访问修饰符 event 委托类型 事件名；

事件的使用:

1.事件是作为成员变量存在于类中

2.委托是怎么用,事件就怎么用

事件相对于委托的区别：

1.不能在类的外部赋值

2.不能在类外部调用

~~~C#
class Test{
    // 委托成员变量用于存储函数的
    public Action myFun;
    // 事件成员变量用于存储函数的
    public event Action myEvent;
    
    public Test(){
        // 事件的使用和委托一模一样只是有些细微的区别
        myFun = TestFun;
        myFun += TestFun;
        myFun -= TestFun;
        myFun = null;
        myFun();
        MyFun.Invoke();
        
        myEvent = TestFun;
        myEvent += TestFun;
        myEvent -= TestFun;
        myEvent();
        myEvent.Invoke();
     	meEvent = null;
 		
    }
    public void TestFun(){
        
    }
}

// 那么我们为什么还要学习事件Event呢?
// 首先我们要记住:
// 事件相对于委托的区别：
// 1.不能在类的外部赋值
// 2.不能在类外部调用

// 举例:
class Test2{
    static void Main(){
        Test t = new Test();
        // 我们会发现,我们的委托是可以在类的外部被点(.)出来使用的
        t.myFun = null;
        // 但是我们的Event不行,尽管它是Public的访问修饰符
        // Event能做的只能是在类的外部执行增(+=)和删(-=),不允许赋值操作
    
        
		// 我们的委托是可以在类的外部被调用的
        t.myFun();
        t.myFun.Invoke();
        
        // 但是我们的事件Event是做不到的,如果我们需要调用Event是需要在类的内部进行封装方法的
		// ***注意:事件是不能作为临时变量在函数中使用的,也就是我们在函数中是无法申明事件的,它只能作为成员存在于类\接口\结构体中,但是委托是可以的        
    }
}
~~~

##### 2.3为什么有事件

1.防止外部随意置空委托

2.防止外部随意调用委托

3.事件相当于对委托进行了一次封装让其更安全

#### 3.匿名函数

##### 3.1什么是匿名函数

顾名思义,匿名函数就是没有名字的函数;匿名函数的使用主要是配合委托和事件进行使用的;脱离委托和事件是不会使用匿名函数的.

##### 3.2基本语法

~~~C#
delegate (参数列表){
    // 函数逻辑
};
// 何时使用?
// 1.函数中传递委托参数时
// 2.委托或者事件赋值时
~~~

##### 3.3匿名函数的使用

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

##### 3.4匿名函数的缺点

添加到委托或者事件容器后,不记录下来的话,我们无法单独移除,因为我们的匿名函数没有函数名,所以唯一的方法是把委托或者事件容器全部清空才行.

#### 4.Lambda表达式

##### 4.1什么是Lambda表达式

Lambda表达式可以理解为匿名函数的简写形式，它除了写法上的不同外，使用上和功能上完全与匿名函数一模一样，优缺点也是一模一样的，同理，它也是需要配合委托或者事件使用的。

##### 4.2lambda表达式语法

~~~C#
(参数列表)=>{
    // 函数体
};
~~~

##### 4.3lambda表达式的使用

~~~C#
// 1.无参无返回值
// ***注意：Lambda表达式是无法单独使用的，是需要配合委托或者事件使用的
Action ac = ()=>{
    
};

// 2.有参数无返回值
Action ac = (int value)=>{
    Console.WriteLine(value);
};

// 3.甚至参数类型都可以省略，只要参数类型和委托或事件容器一致的就行
// 因为我们通过泛型占位字母指定了我们的类型，所以我们可以省略参数类型
Action<int> ac = (value)=>{
    Console.WriteLine(value);
};

// 4.有返回值的
Func<string, int> ac = (value)=>{
  return "123";  
};
~~~

##### 4.4 *闭包

内层的函数可以引用包含在它外层的函数变量，即使外层函数的执行已经终止。

注意：该变量提供的值并非变量创建时的值，而是在父类函数范围内的最终值。

~~~C#
class Test{
    public event Action action;
    public Test(){
        int value = 10;
        // 这里我们就形成了一个闭包
        // 解释：因为value按道理它的生命周期就是构造函数被调用时候存在，一旦构造函数执行完，作为栈上的value会立马被回收，但是因为它现在是存储在了事件里面，所以这时候value的生命周期已经被改变了，它永远都不会被回收其内存(除非，当我们清空我们的事件容器的时候，也就是把event = null，否则它永远存在于内存中)。
        action = ()=>{
          Console.WriteLine(value);  
        };
    }
    
    // 接下来我们需要理解这句话：“注意：该变量提供的值并非变量创建时的值，而是在父类函数范围内的最终值。”
    // 我们看一下以下代码：
    for(int i = 0; i<10 ;i++){
        action += ()=>{
          Console.WriteLine(i);  
        };
    }
    // 这时候，如果我们去action()，执行我们的事件，我们的控制台会出现哪种情况？是打印0-9还是打印10次9？
    // 打印会是打印10次9，为什么呢？理解一下我们的这句话“注意：该变量提供的值并非变量创建时的值，而是在父类函数范围内的最终值。”；
    // 解释：因为我们存储的是变量i,i在for循环中其值是改变的，也就是说我们事件中存储的i的值就是在变量的，那么它最终下来的值就是循环最后一次的i的值。
    // 接着问：如果我们想要打印0-9怎么办呢？
    // 下面代码：
    for(int i = 0; i<10 ;i++){
        // int index = i;很关键！这是很关键的一句代码，因为我们在每次循环的时候，都在申明一个新的index,它于上一个for语句块的逻辑不一样，这里的index每一次循环都是新的index，每个index之间互无影响，没有联系，所以，我们事件中存储的index就是相当于申明了10个index变量存储每次循环过后i的值。这时候打印的结果就会是0-9。
        int index = i;
        action += ()=>{
          Console.WriteLine(index);  
        };
    }
}
~~~

### 五.List排序

#### 1.List排序

##### 1.1List自带排序方法

~~~c#
// 需要引用命名空间 using System.Collections.Generic;
List<int> list = new List<int>();
// 如果我们不调用List自带的排序API那么就会按添加的顺序
list.Add(3);
list.Add(1);
list.Add(5);
for(int i = 0; i < list.Count; i++){
    Console.WriteLine(list[i]);
}

// 默认是升序排序
list.Sort();
~~~



##### 1.2自定义类的排序

~~~C#
class Item{
    pirvate int money;
    public Item(int money){
        this.money = money;
    }
}

class Program{
    static void Main(){
        List<Item> itemList = new List<Item>();
    	itemList.Add(new Item(45));
        itemList.Add(new Item(55));
        itemList.Add(new Item(10));
        itemList.Add(new Item(459));
		// 首先，可以把下面这行代码的注释去了，大家运行一下，然后猜测一下，我会的List会帮我们排好顺序还是不会呢？
        // 答案是不会的，甚至我们一运行就报错了，对于自定义类，我们是无法通过List自带的API进行排序的。那为什么我们在上一个知识点上存储单独的int类型，我们却可以对int类型进行排序呢?我们可以把鼠标的光标点击一下int,再按下F12进去，我们发现，int类型是继承了IComparable<Int32>的泛型接口和IComparable非泛型接口，之所以int类型可以进行排序，就是它继承了这两个接口，实现了接口中的排序方法---int CompareTo();我们的Lits.Sort()就是通过调用这个方法进行的排序。
        // itemList.Sort();
         for(int i = 0; i< itemList.Count; i++){
		 	Console.WriteLine(itemList[i].money);
         }
    }
}

// 实例：实现我们自定义类的排序
class Item : IComparable<int>{
    public int money;
    public Item(int money){
        this.money = money;
    }
    public int CompareTo(Item other){
        // 返回值的含义：
        // 1.果小于0：放在传入对象的前面
        // 2.等于0：保持当前位置不变
        // 3.大于0：放在传入对象后面
        
        // 我们先把传入对象的位置理解为就是零，如果返回 为负数就放在它左边，也就是前面；如果返回正数，就放在它的右边，也就是后面
        // 代码：
        if(this.money > other.monet(
         return 1;
        )
        else{
            return -1;
        }
    }
}

class Program{
    static void Main(){
         List<Item> itemList = new List<Item>();
    	itemList.Add(new Item(45));
        itemList.Add(new Item(55));
        itemList.Add(new Item(10));
        itemList.Add(new Item(459));
		// 因为我们的Item类继承了比较的接口，我们也实现了接口的方法，这时候我们再执行排序的方法就不会报错了，而且会出现升序排列。
        // *注意：这里提醒一下，我们的Sort()排序不是说只要有CompareTo()方法然后不继承IComparabe就可以调用的，注意！我们的Sort()调用的基准是根据是否继承了IComparable来判断的，而非只是ComparareTo()方法。
        // 提醒一下：我们常用的接口都是用泛型接口，就像这个例子有两个接口一个是泛型的IComparable<>一个是IComparable，前者是泛型我们可以指定自己类型可以解决装箱拆箱的问题，后者的参数是一个object，我们如果需要调用参数中的某个成员就需要用里氏替换原则进行转换as成我们的类型再点(.)出我们的成员，很不方便，而且存在装箱拆箱的问题。
        itemList.Sort();
         for(int i = 0; i< itemList.Count; i++){
		 	Console.WriteLine(itemList[i].money);
         }
    }
    }
}
~~~



##### 1.3通过委托函数进行排序

如果我有一个自定义的类，但是我不希望我的类都需要去继承IComparable<>泛型接口实现CompareTo方法怎么办？

List.Sort();自身是提供了三种重载方法的，我们把鼠标的光标点击List然后按下F12，我们可以看见以下三种重载方法：

  		1. public void Sort(Comparison<T> comparison);
  		2. public void Sort(int index, int count, IComparer<T>? comparer);
  		3. public void Sort();

我们对第一种进行重载，我们可以把鼠标光标定位到Comparison<T>上按下F12进去看看，我们会发现其实它的本质是一个委托，那么我们接下来就用我们自己的委托函数来重载我们的Sort()方法达到升序排列的目的。

~~~C#
// 实例：
class ItemShop{
    public int money;
    
    public ItemShop(int money){
        this.money = money;
    }
}

class Program{
    void static Main(){
        List<ItemShop> list = new List<ItemShop>();
    	list.Add(new ItemShop(10));
        list.Add(new ItemShop(20));
        list.Add(new ItemShop(1));
        list.Add(new ItemShop(100));
		// 这里我们如果不传入我们的委托函数是会报错的，运行直接奔溃        
        list.Sort(SortShopItem);
        // 我们也可以使用我们学过的匿名函数的方式
        // list.Sort(delegate SortShopItem(ItemShop a, ItemShop b){
        //  	if(a.money > b.money){
        //      		return -1; 
        //  	}
		//  	else{
        //     		return 1;
        //  	}        
   		//  });
        
        // 同理，我们也可以使用Lambda表达式
        // list.Sort((a, b)=>{
        //  	return a.money > b.money ? -1 : 1;
   		//  });
        
    }
    
    public staic int SortShopItem(ItemShop a, ItemShop b){
        // 传入的两个对象为列表的中的两个对象
        // 进行两两的比较，用左边的和右边的条件比较
        // 返回值规则和上一个例子一样，0做标准，负数在左(前)正数在右(后)，最后结果成升序排序。
        if(a.money > b.money){
           return -1; 
        }
		else{
            return 1;
        }        
    } 
}
~~~



### 六.协变逆变

#### 1.协变逆变

##### 1.1什么是协变逆变？

协变：

简单的总结：协变就是和谐的变化，自然的变化。因为里氏替换原则父类可以装子类，所以子类变父类比如string变object是合理的和谐的。

逆变：

逆常规的变化，不正常的变化。因为里氏替换原则父类可以装子类，但是子类不能装父类的，所以父类变子类比如object 变成 string 感受是不和谐的。



协变和逆变是用来修饰泛型的

协变：out

逆变：in

用于在泛型中修饰泛型字母的，只有泛型接口和泛型委托能使用。

##### 1.2使用

~~~C#
// 1.返回值和参数
// 用out修饰的泛型只能作为返回值
delegate T TestOut<out T>();

// 用in修饰的泛型只能作为参数
delegate T TestIn<in T>(T t);
// ***注意：协变out和逆变是只能在泛型接口和泛型委托中使用的

//  2.结合里氏替换原则理解
class Father{
    
}
class Son : Father{
    
}
class Program{
    static void Main(){
        // 协变
        TestOut<Son> os = ()=>{
          return new Son();  
        };
        // ***注意：如果我们把TestOut类中的out修饰符去掉，会报错误的，说明当我们加上了out之后，编译器会帮我们判断，这里是否符合里氏替换原则，符合就进行里氏替换原则进行了转换。
        TestOut<Father> of = os;// 这里我们相当于是用一个父类TestOut<Father>装载了一个子类TestOut<Son>，符合里氏替换原则的，是协变。
        // 这一行代码就证实了我们的os确实是转换为了Father，发生了里氏替换原则
        Father f = of();// 实际上返回的是os里面装的函数，返回的是Son
        
		// 逆变
        TestIn<Father> iF = (value)=>{
            
        };
        TestIn<Son> iS = IF;// 这里我们相当于是用一个子类TestOut<Son>装载了一个父类TestOut<父类>，是不符合里氏替换原则的，是逆变。
        // 我们TestIn<Son>指定了泛型类型是一个Son类型，所以这里我们必须传入的是一个new Son()，如果我们传入的是一个Father()是会报错误的。
        iS(new Son());//实际上调用的是iF,而iF里面是一个TestIn<Father>，是个Father类型的，也就是说我们因该是要传入一个Father类型的。
    }
}
~~~

##### 总结

协变：out

逆变：in

协变out和逆变in是用来修饰泛型替代符的，只能修饰接口和委托中的泛型

作用：

1.out修饰的泛型类型只能作为返回值类型，in修饰的泛型修饰符只能作为参数类型

2.遵循里氏替换原则，用out和in修饰的泛型委托可以相互装载（有父子关系的泛型）

协变：父类泛型委托装子类泛型委托

逆变：子类泛型委托装父类泛型委托

### 七.多线程

#### 1.多线程

##### 1.1了解线程前先了解进程

进程(Process)是计算机中程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基本。

说人话：打开一个应用程序就是在操作系统上开启了一个进程，进程之间可以相互独立运行，互不干扰，进程之间也可以相互访问、操作。

##### 1.2什么是线程？

操作系统能够进行运算调度的最小单位。

它被包含在进程之中，是进程中的实际运作单位。

一条线程指的是一个进程中一个单一顺序的控制流，一个进程中可以并发多个线程，我们目前写的程序都在主线程中。

简单理解线程：就是代码从上到下运行的一条“管道”。

##### 1.3什么是多线程？

我们可以通过代码开启新的线程，可以同时运行代码的多条“管道”就叫多线程。

##### 1.4语法相关

C#专门为我们提供了线程类 Thread

~~~C#
// 我们需要引用命名空间 using System.Threading;
// 1.首先申明一个新的线程
// 注意：线程执行的代码需要封装到一个函数中
// 新线程将要执行的代码逻辑被封装到了一个函数语句块中
Thread t = new Thread(NewThreadLogic);
static void NewThreadLogic(){
    // 新开线程执行的代码逻辑在该函数语句块内
    Console.WriteLine("新开的线程代码逻辑");
}

// 2.启动线程
// 如果我们不启动线程，是不会执行我们封装好的函数内部的逻辑的
// 线程的开启
t.Start();

// 3.设置为后台线程
// 当前台线程都结束了的时候，整个程序也就结束了，即使还有后台线程正在运行。
// 后台线程不会防止应用程序的进程被终止掉，如果不设置为后台线程，可能导致进程无法正常关闭。
// ***解释一下：我们的主线程是我们的Main()函数，当我们Main()函数内部逻辑执行完了之后，我们的程序也因该就结束了，但是我们新开了一个新的线程它默认是前台线程，那么它是不会受我们的主线程控制的。
// 举例子：我们把我们新线程的封装函数改一下，改成下面这个
static void NewThreadLogic(){
    while(true){
    	// 新开线程执行的代码逻辑在该函数语句块内
    	Console.WriteLine("新开的线程代码逻辑");
    }
}
// 这时候如果我们运行程序，我们会发现控制台会一直打印。看似是不是没有问题，毕竟我们写了一个死循环，但是请细细的思考，我们的主线程(main()函数)就两句代码语句，一句是新开一个新线程new Thread();另一句是启动线程t.Start();,到这里程序的生命周期就结束了，程序因该要结束了并且关闭了，但是我们却一直还在执行我们新线程的逻辑，导致阻塞了我们主线程的正常关闭。

// 那么如何设置成后台线程呢?
t.IsBackground = true;
// 这时候我们在运行一下，会发现控制台不在无限循环打印了，当主线程结束，新开的后台线程也结束了。
// 注意：两个线程是同时运行的，是互不干扰的，所以主线程运行的时候，我们的新线程也会运行。

// 4.关闭释放一个线程
// 如果开启的线程中不是死循环，是能够结束的逻辑，那么不用刻意的去关闭它。
// 如果开启的线程是死循环，想要中止这个线程，有两种方式
// 4.1.死循环中bool标识
我们可以在主线程中声明一个bool类型标识符，在执行的第一句为false,执行完了主线程的逻辑后，为true，作为新开线程处理死循环中的判断的条件。

// 4.2.通过线程提供的方法(注意在.Net core版本中无法中止，会报错误)
// 中止线程
t.Abort();
t = null;

// 5.线程休眠
// 让线程休眠多少毫秒
// 注意：在哪个线程里执行就休眠哪个线程
Thread.Sleep();// 里面传入的是毫秒
~~~



##### 1.5线程之间共享数据

多个线程使用的内存是共享的，都属于该程序(进程)，所以要注意，当多线程同时操作同一片内存区域时候可能会出现问题，可以通过加锁的方式避免问题。



lock

当我们在多个线程当中想要访问同样的东西进行逻辑处理时候，为了避免不必要的逻辑顺序执行的差错。

lock(引用类型对象);

~~~C#
// 例如我们在主线程中执行这样的代码：
While(true){
    Console.SetCursorPosition(0,0);
    Console.ForegroundColor = ConsoleColor.Red;
    Console.Write("a");
}
// 然后我们在附属线程中执行这样的代码
While(true){
    Console.SetCursorPosition(15,15);
    Console.ForegroundColor = ConsoleColor.Red;
    Console.Write("c");
}

// 如果我们不加锁的运行的话，那那么就可能造成当主线程运行到了第二条的语句的时候，附属线程执行了第三条语句，然后就在本该打印“a”的地方打印了“c”，在本该打印“c”的地方打印了“a”。因为线程是共享一片内存空间的，对于Console也是可以一起同时访问的。

//那么如何加锁呢?
我们的lock需要一个引用类型的参数，我们可以随便传入一个
Object o = new Object();
// 主线程中加锁：
While(true){
    lock(o){
    	Console.SetCursorPosition(0,0);
    	Console.ForegroundColor = ConsoleColor.Red;
    	Console.Write("a");
	}    
}
    
// 附属线程中加锁
While(true){
    lock(o){
    	Console.SetCursorPosition(15,15);
    	Console.ForegroundColor = ConsoleColor.Red;
    	Console.Write("c");
    }
    
 // 解释一下：我们相当于对主线程和附属线程同时锁住了同一个引用类型object，那么当我们主线程先执行到lock()的时候，就会给object加锁，这时候附属线程执行到lock()的时候发现object已经上锁了，就会等到主线程的lock()内部逻辑执行完后，把object的锁释放了，才会再去执行附属线程的lock()内部的逻辑。
    
 // 加锁的缺点：影响线程的效率，当我们的线程执行很快的时候，如果存在了锁，那要等到另一个线程把锁释放了，才能继续执行。
 // 加锁的优点：解决了不同线程之间的共享一片内存，并对同一个区域操作时候会出现的问题。
}
~~~



##### 1.6多线程对于我们的意义

可以用多线程专门处理一些复杂耗时的逻辑，例如：寻路、网络通信等等。



### 八.预处理器指令

#### 1.预处理器指令

##### 1.1什么是编译器

编译器是一种翻译程序，它用于将源语言程序翻译为目标语言程序。

源语言程序：某种程序设计语言写成的，比如C#、C、C++、Java等语言写的程序。

目标语言程序：二进制数表示的伪机器代码写的程序。

##### 1.2什么是预处理器指令

预处理器指令制导编译器在实际编译开始之前对信息进行预处理；

预处理器指令都是以#开始；

预处理器指令不是语句，所以它们不以分号；结束；

##### 1.3常见的预处理器指令

一.

#define：定义一个符号，类似一个没有值的变量。

#undef：取消define定义的符号，让其失效。

~~~C#
// 预编译指令是写在我们using的上面
// 定义一个符号
#define Unity4
#define Unity5
#define Unity2017
#define Unity2018
#define Unity2019
// 取消定义一个符号
#undef Unity4
~~~



二.

#if、#elif、#else、#endif和if语句规则一样，一般配合#define定义的符号进行使用，用于告诉编译器进行编译代码的流程控制。我们还可以通过逻辑“或”和逻辑“与”进行多种符号的组合判断。

~~~C#
// 这里的意思是，如果发现我们定义了Unity4这个符号的话，那么其中包含的代码就会被编译器翻译
// 注意：如果我先定义了一个符号，然后又在下面用#undef取消定义的话也不会执行下面语句的
#if Unity4
 Console.WriteLine("Unity4");
#endif

// 语法上基本是与if-else if- else是一样的
#if Unity4
    Console.WriteLine("Unity4");
#elif Unity2019
    Console.WriteLine("Unity2019");
#else
    Console.WriteLine("没有Unity");
#endif

// 配合逻辑“或”或者“与”的组合判断
#if Unity2018 && IOS
    Console.WriteLine("符合要求的开发环境");
#else
    Console.WriteLine("不符合要求的开发环境");
~~~

三.

#warning、#error：用于告诉编译器是报警告还是报错误，一般还是配合if使用。

~~~C#
#if Unity4
    Console.WriteLine("Unity4");
	// 如果我们代码通过了我们的if逻辑判断的话，我们就会执行下面这个警告(注意：我们不需要按F11进入调式模式下就会报警告)	
	#warning 这个版本不合法
#elif Unity2019
    Console.WriteLine("Unity2019");
#else
    Console.WriteLine("没有Unity");
	// 如果我们代码执行了最后的else的话，我们就会执行下面这个错误(注意：我们不需要按F11进入调式模式下就会报错误，这个时候的错误不是我们的语法错误也不是代码逻辑错误，就是我们的预处理指令报了一个错误，错误的内容就是我们写的内容)	
	#error 没有安装Unity！
#endif
~~~



##### 总结

预处理是让编译器在编译之前预先处理我们的预处理判断，我们常常用于Unity的版本，或者不同移动端的代码，例如IOS、Andorid、PC。

### 九.反射和特性

#### 1.反射

##### 1.1什么是程序集

程序集是经由编译器编译得到的，供进一步编译执行的之间产物。在WINDOWS系统中，它一般表现为后缀为.dll(库文件)或者.exe(可执行文件)的格式。

简单理解：程序集就是我们写的一个代码集合，我们现在写的所有代码最终都会被编译器翻译为一个程序集供给他人使用，比如一个代码库文件(dll)或者一个可执行文件(exe)。

##### 1.2元数据

元数据就是用来描述数据的数据。这个概念不仅仅用于程序，在别的领域也有元数据。

简单理解：程序中的类，类中的函数，变量等等信息就是程序的元数据，有关程序以及类型的数据被称为元数据，它们保存在程序集中。

##### 1.3反射的概念

程序正在运行时，可以查看其他程序集或者自身的元数据。一个正在运行的程序查看本身或者其他程序的元数据的行为就叫反射。

简单理解：在程序运行时候，通过反射可以得到其他程序集或者自己程序集代码的各种信息，例如：类、函数、变量、对象等等，实例化它们，执行它们，操作它们。

##### 1.4反射的作用

因为反射可以在程序编译后获得，所以它提高了程序的拓展性和灵活性。

1.程序运行时得到所有元数据，包括元数据的特性

2.程序运行时，实例化对象，操作对象

3.程序运行时创建新对象，用这些对象执行任务

##### 1.5语法相关

~~~C#
class Test{
    private int i = 1;
    public int j = 0;
    public string str = "123";
    public Test(){
        
    }
    public Test(int i){
        this.i = i;
    }
    public Test(int i, string str):this(i){
        this.str = str;
    }
    public void Speak(){
        Console.WriteLine(i);
    }
}

class Program{
    static void Main(){
        // 1.Type
        // Type(类的信息类)，它是反射功能的基础!它是访问元数据的主要方式。使用Type的成员获取有关类型声明的信息，有关类型的成员(如构造函数、方法、字段、属性和类的事件)
        
        // 2.获取Type
        //   2.1万物之父object中的GetType()可以获取对象的Type(对象的类型)
        	int a = 42;
        	Type type = a.GetType();
        	Console.WriteLine(type);// 会打印System.Int32
        
        //   2.2通过typeof关键字传入类名也可以得到对象的Type
        	Type type2 = typeof(int);
        	Console.WriteLine(type2);// 会打印System.Int32
        //   2.3通过类或结构体的名字也可以获取类型
        //   注意：类名或者结构体名字必须包含命名空间，不然找不到
        //   如果这里我们不加命名空间的话：Type type3 = Type.GetType("Int32");只会打印空。
        Type type3 = Type.GetType("System.Int32");
        Console.WriteLine(type3);
        //   注意：虽然我们上面申明了三个Type类型的变量，但是它们的地址都是一样的，都指向了同一片内存空间，我们可以理解为我们的每个类都是唯一的，地址也是唯一的，所以每个Type指向的地址都是同一个地址。
        
        // 3.得到类的程序集信息
        Console.WriteLine(type.Assembly);
        
        // 4.获取类中的所有公共成员
        // 第一步：首先得到Type
        Type t = typeof(Test);
        // 第二步：得到所有公共公开的成员
        // 补充：需要引入命名空间 using System.Reflection;
        MemberInfo[] infos = t.GetMembers();
        for(int i = 0; i < infos.Length; i++){
        	// 最后：打印出反射内存储的公共公开的信息(包括我们的万物之父object中的基类的公开公共方法)
            Console.WriteLine(infos[i]);
        }
        
        // 5.获取类的公共构造函数并调用
        // 5.1获取所有构造函数
        ConstructorInfo-= ctors = t.GetConstructors();
        for(int i = 0; i < ctors.Length; i++){
            Console.WriteLine(ctor[i]);
        }
        // 5.2获取其中一个构造函数并执行
        // 得到构造函数传入Type数组，数组中内容按顺序是参数类型
        // 执行构造函数传入object数组表示按顺序传入的参数
        // 5.2.1得到无参构造函数
		ConstructorInfo info = t.GetConstructor(new Type[0]); // 这相当于得到了一个无参数的构造函数        
        info.Invoke(null); // 执行构造函数
        // 我们也可以通过里氏替换原则转换为Test
        Test obj = info.Invoke(null) as Test;
        // 5.2.2得到有参构造函数
		ConstructorInfo info2 = t.GetConstructor(new Type[]{typeof(int)});// 这里相当于得到了一个有一个参数，参数类型为int的构造函数
        obj = info2.Invoke(new object[]{2}) as Test;// 执行我们的构造函数；
        ConstructorInfo info3 = t.GetConstructor(new Type[]{typeof(int), typeof(string)});// 这里相当于我们得到了一个带有两个参数，参数类型分别为int类型和string类型的构造函数
        obj = info3.Invoke(new object[]{4, "444444"}) as Test;
        
        // 6.获取类的公共成员变量
        // 6.1得到所有成员变量
        FieldInfo[] fieldInfos = t.GetFields();
        for(int i = 0; i < fieldInfos.Length; i++){
            Console.WriteLine(fieldInfos[i]);
        }
        // 6.2得到指定名称的公开公共成员变量
        FieldInfo infoJ = t.GetField("j");// 这里传入的字符串是代表我们的变量名字
        // 6.3 通过反射获取和设置对象的值
  		Test test = new Test();// 这里我们新创建一个Test用于测试
        test.j = 99;
        test.str = "1111";
        // 6.3.1通过反射获取对象的某个变量的值
		Console.WriteLine(infoJ.GetValue(test));// 传入的参数是我们的类名
        // 6.3.2通过反射设置指定对象的某个变量的值
        infoJ.SetValue(test, 100);
        Console.WriteLine(infoJ.GetValue(test));
       
        
        
        // 7.获取类的公共成员方法
        // 通过Type类中的GetMethod方法得到类中的方法，MethodInfo是方法的反射信息
        Type strType = typeof(string);// 我们用string类型做实例
   		// 1.如果存在方法重载，用Type数组表示参数类型
        MethodInfo[] methods = strType.GetMethods();
        for(int i = 0; i < methods.Length; i++){
            Console.WriteLine(methods[i]);
        }
        MethodInfo subStr = strType.GetMethod("Substring", new Type[]{typeof(int), typeof(int)});
        // 2.调用该方法
        // 注意：如果是静态方法，Invoke中的第一个参数传null即可
        string str = "Hello,World";
        // 第一个参数相当于是哪个对象要执行这个成员方法
        // 因为截取的方法是不会改变原有字符串的，所以这里我们必须新创建一个来接收新的字符串
        object result = subStr.Invoke(str, new object[]{7,5});
        Console.WriteLine(result);
        
        // 8.其它
    	// Type:
        // 得枚举：GetEnumName,GetEnumNames
        // 得事件：GetEvent,GetEvents
        // 得接口：GetInterface,GetInterfaces
        // 得属性：GetProperty,GetPropertys
        // 等等
    }
}
~~~

#### 

~~~C#
// 首先，我们先写一个用于测试的类
class Test{
    private int i = 1;
    public int j = 0;
    public string str = "123";
    public Test(){
        
    }
    public Test(int i){
        this.i = i;
    }
    public Test(int i, string str):this(i){
        this.str = str;
    }
    public void Speak(){
        Console.WriteLine(i);
    }
}
~~~

#### 2.Assembly

程序集类，主要用于加载其他程序集，加载后才能用Type来使用其他程序集中的信息，如果想要使用不是自己程序集中的内容，需要先加载程序集，比如：dll文件（库文件）。简单的把库文件看成一种代码仓库，它提供给使用者一些可以直接拿来用的变量、函数或者类。

三种加载程序集的函数：

1.一般用来加载在同一文件下的其他程序集：Assembly asembly2 = Assembly.LoadFrom(“包含程序集清单的文件的名称或者路径”);



2.一般用来加载不在同一文件下的其他程序集：

Assembly asembly = Assembly.LoadFrom("包含程序集清单的文件的名称或者路径");

Assembly asembly3 = Assembly.LoadFile("要加载的文件的完全限定路径");

~~~C#
// 1.先加载一个指定程序集
// 需要引用命名空间 System.Reflection;
Assembly asembly = Assembly.LoadFrom(@"xxx\xxx\xxx\xxx\xxx.dll");
Type[] types = asembly.GetTypes();// 获取我们程序集中的所有类型
for(int i = 0; i < types.Length; i++){
    Console.WriteLine(types[i]); // 打印我们反射中存储的所有的类型
}

// 2.再加载程序集中的一个类对象，之后才能使用反射
Type icon = asembly.GetType("XXXX.类对象名字");
MemberInfo[] members = icon.GetMembers();// 得到我们icon中存储的所有成员
for(int i = 0; i< members.Length; i++){
    Console.WriteLine(members[i]);
}

// 3.类库(dll库文件)工程创建
解决方案资源管理器->随便一个项目右键->添加->新建项目->选择类库(C# .Net FrameWork)

作用：我们的类库文件一般是运行不了的，一般是用于给我们纯写逻辑代码和算法代码供给他人所用的，它内部不需要Main()方法的，我们一般写好之后，在解决方案资源管理器->点击项目名->右键->生成，然后我们就可以在我们的项目的文件夹下面找到我们的项目文件夹->bin->Debug就可以看到外面的两个文件一个是.dll，还有一个是.pdb。然后我们就可以用我们反射的知识去引用我们的dll文件。

~~~



用于快速实例化对象的类，用于将Type对象快捷实例化为对象，先得到Type，然后快速实例化出一个对象。

~~~C#
Type testType = typeof(Test);
// 1.无参构造
Test testObj = Activator.CreateInstance(testType) as Test;
Console.WriteLine(testObj.str);
// 2.有参数构造
// 得到一个参数的构造函数的实例
// 参数1 反射， 参数2 我们的构造函数参数列表
testObj = Activator.CreateInstance(testType, 99) as Test;
Console.WriteLine(testObj.j);
// 我们再试试获取两个参数构造函数的实例
testObj = Activator.CreateInstance(testType, 55, "111222") as Test;
Console.WriteLine(testObj.j);
// 注意：我们不能传入我们没有实现的构造函数，如果我们传入了我们没有实现的构造函数，运行后会直接报错。
~~~

#### 反射总结

反射：在程序运行时，通过反射可以得到其他程序集或者自己的程序集代码的各种信息，类、函数、变量、对象等等，实例化它们，执行它们，操作它们。

关键类：Type、Assembly、Activator。

为什么学习反射？Unity引擎的基本工作机制就是建立在反射的基础上的，学习反射是为了之后学习Unity引擎的基本工作原理做铺垫。

#### 3.特性

##### 3.1特性是什么？

特性是一种允许我们向程序的程序集添加元数据的语言结构，它是用于保存程序结构信息的某种特殊类型的类。

特性提供功能强大的方法以将声明信息与C#代码（类型、方法、属性等）相关联。

特性与程序实体关联后，即可在运行时使用反射查询特性信息。

特性的目的是告诉编译器把程序结构的某组元数据嵌入程序集中，它可以放置在几乎所有的声明中（类、变量、函数等等申明）。

简单理解：特性本质是一个类，我们可以利用特性类为元数据添加额外信息，比如一个类、成员变量、成员方法等等为他们添加更多的额外信息，之后可以通过反射来获取得到这些额外信息。

##### 3.2自定义特性

在上面我们讲解了特性的本质就是一个类，那么我们只需要先去申明一个类，然后让这个类基础至特性基类Attribute就可以了。

~~~C#
class MyCustomAttribute : Attribute{
    // 这个类的内容其实就是让你最后变成一个元数据的，方便外部利用反射引用的。
	// 特性中的成员
    public string info;
    public MyCustomAttribute(string info){
        this.info = info;
    }
    public void TestFun(){
        Console.WriteLine("特性的方法");
    }
}
~~~

##### 3.3特性的使用

基本语法：[特性名（参数列表）]，本质上就是在调用特性类的构造函数。

写在哪里?类、函数、变量上一行，表示他们具有该特性信息。

~~~C#
// 我们使用我们上一个知识点申明的特性
// 注意：系统会默认帮我们省略了类名后面的Attribute
// 参数列表里面根据我们的构造函数的参数列表传入参数
[MyCustom("这是我自己写的一个用于计算的类")]
class MyClass{
    [MyCustom("这是一个成员变量")]
    public int value;
    [MyCustom("这是一个用于计算加法的函数")]
    public void TestFun([MyCustom("函数参数"int a)]){
        
    }
}

// 特性的作用就是添加额外的信息，当我们通过反射得到这些成员的时候，我们可以获取到特性的信息来知道成员的用法。
// 举例：
class Program{
    static void Main(){
        // 特性的使用
        MyClass mc = new MyClass();
        // 得到Type的三种方法
        // 1.Type t = mc.GetType();
        // 2.t = typeof(MyClass);
        // 3.t = Type.GetType(命名空间名字.MyClass)
        Type t = mc.GetType();
        
        // 判断是否使用了某个特性
        // 参数一：特性的类型
        // 参数二：代表是否搜索继承链（属性和事件忽略此参数）
        // 注意这个方法只是判断类是否使用了特性，类里面的成员是该方法无法判断
        if(t.IsDefined(typeof(MyCustomAtrribute)), false){
            Console.WriteLine("该类型应用了MyCustom特性");
        }
        
        // 获取Type元数据中的所有特性
        object[] array = t.GetCustomAttribute(true);
        for(int i = 0; i < array.Length; i++){
			if(array[i] is MyCustomAttribute){
				Console.WriteLine((array[i] as MyCustomAttribute).info);
                // 因为特性的本质就是一个类，所以我们甚至可以调用其中的方法
                (array[i] as MyCustomAttribute).TestFun();
            }
        }
    }
}
~~~

##### 3.4限制自定义特性的使用范围

~~~C#
// 通过为特性类加特性限制其使用范围
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct, AllowMultiple = true, Inherited = true)]
// 参数一：AttributeTargets-----特性能够用在哪些地方
// 参数二：AllowMultiple-----是否允许多个特性实例用在同一个目标上
// 参数三：Inherited-----特性是否能被派生类和重写成员继承
public class MyCustom2Attribute : Attribute{
    
}
~~~

##### 3.5系统自带特性——过时特性

~~~C#
// 过时特性-----Obsolete，用于提示用户使用的方法等成员已经过时，建议使用新方法，一般加在函数前的特性
Class TestClass{
    // 参数一：调用过时方法时提升的内容
    // 参数二：true-使用该方法会报错(编译之前就会报错)，false-使用该方法自己报警告
    [Obsolete("OldSpeak方法已经过时了，请使用Speak方法"), false]
    public void OldSpeak(string str){
        
    }
    public void Speak(){
        
    }
}
~~~

##### 3.6系统自带特性——调用者信息特性

~~~C#
// 哪个文件调用?
CallerFilePath特性
// 哪一行调用？
CallerLineNumber特性
// 哪一个函数调用？
CallerMemberName特性

// 需要引用命名空间 using System.Runtime.CompilerServices;
// 一般作为函数参数的特性
// 举例：
class Test{
    // 注意：我们的调用者信息特性后面是需要有一个默认值的，不然会报错误
    // 特性的默认值也可以让用户在调用函数的时候，指定传入，一般如果没有在调用函数的时候传值，系统会帮我们传入默认值：该文件的路径、该语句的行数、哪个函数在调用该函数。
    public void TestFun(string str, [CallerFilePath]string fileName = "", [CallerLineNumber]int line = 0, [CallerMemberName]string target = ""){
        
    }
}
~~~

##### 3.7系统自带特性——条件编译特性

~~~C#
// 条件编译特性-----Conditional 
// 它会和预处理指令 #define配合使用，使用之前需要引用命名空间 using System.Diagnostics;
// 主要可以用在一些调式代码上，有时想执行有时不想执行的代码
class Program{
    // 注意：一旦我们加了条件编译特性，那么下面这个静态方法Fun()就需要我们预先定义了#define Fun才可以使用，或者说Main()里面的Fun();才会被执行
    [Conditional("Fun")]
    void Static Fun(){
        Console.WriteLine("Fun");
    }
    void static Main(){
        Fun();
    }
}
~~~

##### 3.8系统自带特性——外部Dll包函数特性

~~~C#
// DllImport
// 用来标记非.Net(C#)的函数，表明该函数在一个外部的DLL中定义。
// 一般用来调用C或者C++的Dll包写好的方法，需要引用命名空间 using System.Runtime.InteropServices;
class Program{
    void static Main(){
        [DllImport("Test.dll")]
        // 这句代码是把我们Test.dll包中与下面这个函数一定是存在着一模一样的函数，把C\C++中这个一模一样的函数映射到这个函数中
        public static extern int Add(int a, int b);
    }
}
~~~

##### 特性总结

特性是用于为元数据再添加更多的额外信息（变量、方法等等），我们可以通过反射获取这些额外的数据来进行一些特殊的处理。

自定义特性—继承Attribute类

系统自带特性：过时特性

反射和特性的学习是为了之后Unity引擎学习的，因为Unity引擎中很多地方都用到了特性来进行一些特殊处理。



### 十.迭代器

#### 1.迭代器是什么？

迭代器（iterator）有时候又被称为光标（cursor），是程序设计的软件设计模式，迭代器模式提供了一个方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部的标识。在表现效果上看，是可以在容器对象（例如：链表或者数组）上遍历访问的接口，设计人员无需关心容器对象的内存分配的实现细节，可以用foreach遍历的类，都是实现了迭代器的。

#### 2.标准迭代器的实现方法

~~~C#
// 关键接口：IEnumerator,IEnumerable;
// 命名空间：using System.Collections;
// 可以通过同时继承IEnumerable和IEnumerator实现其中的方法

// 首先写个测试类
class CustomList : IEnumerable, IEnumerator{
    private int[] list;
    // 这里是指我们的光标位置，默认为-1(可以简单的理解为我们数组的下标)
    private int position = -1;
    public CustomList(){
        list = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
    }
    
    // 实现IEnumerable接口的成员
    public IEnumerator GetEnumerator(){
        Reset();
    	return this;
    }
    
    // 实现IEnumerator接口的成员
    public object Current{
        get{
            return list[position];
        }
    }
    
    public bool MoveNext(){
        ++position;
        // 是否溢出，溢出就不合法
        return position < lits.Length;
    }
    
    // reset是重置光标位置，一般写在获取IEnumerator对象这个函数中，用于第一次重置光标位置，这个函数十分重要!!!
    public void Reset(){
        position = -1;
    }
}

class Program{
    static void Main(){
        CustomList list = new CustomList();
    	// foreach本质
        // 1.先获取in后面这个对象的IEnumerator,然后会调用对象其中的GetEnumerator方法来获取。
        // 2.执行得到这个IEnumerator对象中的MoveNext方法
        // 3.只要MoveNext方法的返回值为true时候，就会去得到Current,然后复制给item。
        // 注意：我们可以不用一定要继承IEnumerable这个接口，只要我们的类中实现了public IEnumerator GetEnumerator()；也是可以使用foreach语句的。
        foreach(int item in list)
            Console.WriteLine(item);
        }
    }
}
~~~

#### 3.用yield return语法糖实现迭代器

yield return 是C#提供给我们的语法糖，所谓语法糖，也称为糖衣语法。

主要作用就是将复杂逻辑简单化，可以增加程序的可读性，从而减少程序代码出错的机会。

~~~C#
// 关键接口：IEnumerable
// 命名空间：using System.Collections;
// 让想要通过foreach遍历的自定义类实现接口中的方法GetEnumerator即可。
class CustomList2 : IEnumerable{
    private int[] list;
    
    public CustomList2(){
        list = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
    }
    
    public IEnumerator GetEnumerator(){
        for(int i = 0; i < list,Length; i++){
            // yield关键字,配合迭代器使用
            // yield return其实就是实现IEnumerator接口中的成员一种简单写法，我们可以不必再去继承IEnumertor并且实现Current(),MoveNext(),Reset()这三个方法还有object属性。当编译器执行到这一句的时候，会默认的帮我实现以上的成员的，让我们自己省了自己去书写。
            yield return list[i];
            
            // 还有另一种复杂的方法
            /*
            yield return list[0];
            yield return list[1];
            yield return list[2];
            yield return list[3];
            yield return list[4];
            yield return list[5];
            yield return list[6];
            yield return list[7];
            */
        }
    }
}

class Program{
    static void Main(){
        CustomList list2 = new CustomList2();
        foreach(int item in list2){
            Console.WriteLine(item);
        }
    }
}
~~~



#### 4.用yield return语法糖为泛型类实现迭代器

~~~C#
class CustomList<T> : IEnumerableP{
    private T[] array;
    
    public CustomList(params T[] array){
        this.array = array;
    }
    public IEnumerator GetEnumerator(){
        for(int i = 0; i < array.Length; i++){
            yield return array[i];
        }
    }
}

class Program{
    staic void Main(){
        CustomList<string> list = new CustomList<string>("123", "123123", "123123123");
        foreach(string item in list){
            Console.WriteLine(item);
        }
    }
}
~~~

#### 总结：

迭代器本身就是让我们在外部直接通过foreach遍历对象中元素而不需要了解其结构。

主要的两种实现迭代器的方式：

1.传统方式：继承两个接口，实现里面的方法

2.用语法糖 yield return 去返回内容，只需要继承一个接口即可



### 十一.特殊语法

#### 1.var隐式类型

var是一种特殊的变量类型，它可以用来表示任意类型的变量。

注意：

1.var不能作为类的成员，只能用于临时变量申明时，也就是一般写在函数语句块中。

2.var必须初始化。

~~~C#
var i = 5;
var s = "123";
var array = new intp[]{1, 2, 3, 4};
var list = new List<int>();

// 温馨提示：一般在协同开发中很少使用var这类隐私类型，因为当别人在阅读自己写的代码的时候，如果遇到了var，我们在没有阅读到后面的初始化值的时候，我们是不知道这个变量的类型的。如果代码中大量的使用了var是很影响阅读效率的。
~~~

#### 2.设置对象初始值

申明对象时候，可以通过直接写大括号的形式初始化公共成员变量和属性。

注意：该方法是设置”对象“的初始值。

~~~C#
class Person{
    private int money;
    public bool sex;
    public string Name{
        get;
        set;
    }
    public int Age{
        get;
        set;
    }
}

class Program{
    static void Main(){
        //Person p = new Person();这是我们传统的调用构造函数实例化的方法
        // 下面是我们通过大括号的形式可以直接给我们对象中"公开"的变量进行初始化
        Person p = new Person{sex = true, Age = 19, Name = "小小泽"};
        // 我们也可以单独的为某一个"公开"变量进行初始化
        Person p2 = new Person{Age = 18};
        // 完整的写法
        // Person p3 = new Person(){};
    }
}
~~~

#### 3.设置集合初始值

~~~C#
// 申明集合对象时，也可以通过大括号直接初始化内部属性
int[] array = new int[]{1, 2, 3, 4, 5};
List<int> listInt = new List<int>(){1, 2, 3, 4, 5, 6};

List<Person> listPerson = new List<Person>(){
    new Person{},
    new Person{Age = 10},
    new Person{sex = true}
};

Dictionary<int, string> dic = new Dictionary<int, string>(){
    {1, "123"},
    {2, "222"}
};
~~~

#### 4.匿名类型

~~~C#
// var变量可以申明为自定义的匿名类型
var v = new {age = 10, money = 11, name = "小小泽"};// 这句代码可以直接复制到Main()语句中不会报错。
// 我们甚至可以打印出里面的内容
Console.WriteLine(v.age);
Console.WriteLine(v.money);
Console.WriteLine(v.name);
// 匿名类型更像是一种数据集合，它的大括号"{}"中除了成员属性之外的，例如：函数、委托、事件等等是无法写入大括号里面的。
~~~

#### *5.可空类型

~~~C#
// 1.值类型是不能赋值为空的
//int c = null;会报错

// 2.申明时在值类型后面加?可以赋值为空
int? c = null;

// 3.判断是否为空
if(c.HasValue){
    Console.WriteLine(c);
    Console.WriteLine(c.value);
}

// 4.安全获取可空类型值
int? value = null;
//   4-1.如果为空，默认返回值类型的默认值
Console.WriteLine(value.GetValueOrDefault());
//   4-2.也可以指定一个默认值
Console.WriteLine(value.GetValueOrDefault(100));// 这个方法需要特别注意一下，它并不是去给value赋值(下面一条语句就是为了验证value是否被改变了)，它的作用是判断value是否为空，如果是就打印100，如果不是就打印value的值；
Console.WriteLine(value);
~~~

***注意：我们的十四种普通类型里面除了引用类型（数组，类，string外）基本都是值类型都可以使用这种变量类型后面加”？“的可空类型。

~~~C#
// "?"其实就是一种语法糖，我们除了对值类型的声明上使用，也还可以对引用类型的方法执行进行判断
// 举例：
object 0 = null;
// 如果我们去执行下面这句代码的话，编译器是会报错的
// o.ToString();
// 所以我们一般会进行安全性判断
if(o != null)
    o.ToString();
// 现在有了"?"可空类型，我们可以下面这种渐变写法
// 相当于是一种语法糖，能够帮助我们自动取判断o是否为空，如果是null就不会执行tostring也不会报错。
o?.ToString();

int[] arrayInt = null;
Console.WriteLine(arrayInt?[0]);

Action action = null;
action?.Invoke();
~~~

#### 6.空合并操作符

~~~C#
// 空合并操作符 ？？
// 左边值 ？？ 右边值
// 如果左边值为null就返回右边值，否则返回左边值
// ***注意：只要是可以为null的类型都能使用(也就是说我们的值类型配合可空类型符合"?"也是可以使用的)
int? intValue = null;
// 下面这句语法就相当于三目运算符->int intValue2 = intValue == null ? 100 :intValue.Value;
int intValue2 = intV ?? 100;
Console.WriteLine(intValue2);

str = str ?? "hahahaha";
Console.WriteLine(str);
~~~

#### 7.内插字符串

~~~C#
// 关键字符：$
// 用$来构造字符串，让字符串中可以拼接变量
string name = "小小泽";
Console.WriteLine($"{name},好好学习!");
~~~

#### 8.单句逻辑简略写法

~~~C#
// 如果我们的语句块内只有一句代码的话是可以省略大括号的
if(true)
    Console.WriteLine("小小泽,好好学习！");
for(int i = 0; i < 10; i++)
    Console.WriteLine(i);
while(true)
    Console.WriteLine("小小泽,好好学习！");

// 注意：我们在类里面声明函数和属性也是同样可以的
class Person{
    public string name;
    Public String Name{
        // 我们的get方法本来是要return，用"=>"写法会直接帮我们把return都省略了。
        get => "小小泽";
        set => name = value;
    }
    
    // 我们该方法作用是计算加法，本来是要return一个int类型的，用"=>"的写法会自动的帮我们省略了return。
    public int Add(int x, int y) => x + y;
}
~~~
