---
title: Lua
author: Heart
date: 2023-04-03 15:55:14
categories: Lua
tags:
  - Lua
  - 热更新
  - Xlua
index_img: https://cdn.jsdelivr.net/gh/easilyid/Photo/photo/20230403155946.png
excerpt: Lua程序设计 Xlua 热更新 Lua和C#之间的通讯
---

## Lua

#### 类型与值

lua是一种动态类型的语言，并没有类型定义的语法，每个值都携带**类型**的信息

函数**type** 可根据传入的值返回其类型

```lua
                print(type("Hello wold"))        -- string
                print(type(10.4*3))              -- number
                print(type(print))               -- function
                print(type(type))                -- function
                print(type(true))                -- boolean
                print(type(nil))                 -- nil
                print(type(type(X)))             -- string


变量没有预定义的类型，任何类型值都可以包含进变量
				print(type(a))                -- nil （a尚未初始化）
                a = 10
                print(type(a))                -- number
                a = "a string!!"
                print(type(a))                -- string
                a = print                     -- 是的，这是合法的！
                a(type(a))                    -- function

```

- 其中 boolean不是一个条件值的唯一表现形式 false 和nil 都被视为nil
- Number类型值可以是32位的 0.3e12 ， 5e+20 都是合法值
- string 我们可以使用 字符# 来获取字符串长度

string常用API：

```lua
str="abcdefg"
print(string.upper(str)) --小转大
print(string.lower(str)) --大转小
print(string.reverse(str)) --翻转
print(string.find(str,"cd")) --查找 按索引查找 按1开始
print(string.sub(str,3,4)) --截取 按索引查找  索引按1开始
print(string.rep(str,2)) --重复
print(string.gsub(str,"ab","QW")) --修改 第二个参数是修改了多少次
--别的类型转字符串
a=123
print(tostring(a)=="123") --tostring是显示
-- 字符转ASCII码
a=string.byte("Lua",2) --第二个参数是指定位置
print(a)
-- ASCII码 转字符
print(string.char(a))
--字符串拼接使用 .. %d
```

是的，Lua包含类似于C语言的转义字符

| 转义符 | 说明     |
| :----- | :------- |
| `\a`   | 响铃     |
| `\b`   | 退格     |
| `\f`   | 提供表格 |
| `\n`   | 换行     |
| `\r`   | 回车     |
| `\t`   | 水平tab  |
| `\v`   | 垂直tab  |
| `\\`   | 反斜杠   |
| `\"`   | 双引号   |
| `\'`   | 单引号   |

##### table

 `table`类型实现了“关联数组”。“关联数组”是一种具有特殊索引方式的数组。不仅可以通过整数来索引它，还可以使用字符串或其他类型的值（除了`nil`）来索引它。

  `table`没有固定的大小，可以动态地添加任意数量的元素到一个`table`中。

  `table`是Lua中主要的（事实上也是仅有的）数据结构机制，具有强大的功能。

  在Lua中，table既不是“值”也不是“变量”，而是“对象”。

  `table`的创建是通过“构造表达式”完成的，最简单的构造表达式就是`{ }`。

```lua
                a = { }             -- 创建一个table，并将它的引用存储到a
                k = "x"
                a[k] = 10           -- 新条目，key = "x", value = 10
                a[20] = "great"     -- 新条目，key = 20, value = "great"
                print(a["x"])       -- 10
                k = 20
                print(a[k])         -- “great"
                a["x"] = a["x"] + 1 -- 递增条目"x"
                print(a["x"])       -- 11
```

  `table`永远是“匿名的”，一个持有`table`的变量与`table`自身之间没有固定的关联性。

```lua
                a = { }
                a["x"] = 10
                b = a               -- b与a引用了同一个table
                print(b["x"])       -- 10
                b["x"] = 20
                print(a["x"])       -- 20
                a = nil             -- 现在只有b还在引用table
                b = nil             -- 再也没有对table的引用了
                -- 当一个程序再也没有对一个table的引用时，Lua的垃圾收集器最终会删除该table，并复用它的内存。
```

  所有`table`都可以用不同类型的索引来访问`value`（值），当需要容纳新条目时，`table`会自动增长。

```lua
                a = { }

                -- 创建1000个新条目
                for i=1, 1000 do a[i] = i*2 end
                print(a[9])         -- 18
                a["x"] = 10
                print(a["x"])       -- 10
                print(a["y"])       -- nil 该元素没有初始化
                -- 可以将nil赋予table的某个元素来删除该元素。
```

  Lua对于诸如`a["name"]`的写法提供了一种更简便的“语法糖（syntactic sugar）”，可以直接输入`a.name`。

```lua
                a.x = 10            -- 等同于a["x"] = 10
                print(a.x)          -- 等同于print(a["x"])
                print(a.y)          -- 等同于print(a["y"])

                -- 对于Lua来说，这两种形式是等价的，可供自由选择使用。
                a.x                 -- 等同于a["x"]
                a[x]                -- 以变量x的值来索引table

                ----------
                a = { }
                x = "y"
                a[x] = 10           -- 将10放入字段“y”
                print(a[x])         -- 10 字段“y”的值
                print(a.x)          -- nil 字段“x”（未定义）的值
                print(a.y)          -- 10 字段“y”的值
```

  若要表示一个传统的数组或线性表，只需以整数作为`key`来使用`table`即可。这里不需要（也没有必要）声明一个大小值，直接初始化元素就可以了

```lua
                -- 读取10行内容，并存储到一个table中

                a = { }
                for i=1,10 do
                    a[i] = io.read()
                end
```

  虽然可以用任何值作为一个`table`的索引，也可以用任何数字作为数组索引的起始值。但**数组通常以1作为索引的起始值**。

 长度操作符“`#`”用于返回一个数组或线性表的最后一个索引值（或为其大小）。

```lua
                for i=1,#a do
                    print(a[i])
                end

                print(a[#a])            -- 打印列表a的最后一个值
                a[#a] = nil             -- 删除最后一个值
                a[#a+1] = v             -- 将v添加到列表末尾

                -- 读取一个文件的前10行
                a = { }
                for i=1,10 do
                    a[#a+1] = io.read()
                end
```

  当对索引的实际类型不是很确定时，可以明确地使用一个显式转换

```lua
                i = 10; j = "10"; k = "+10"
                a = { }
                a[i] = "one value"
                a[j] = "another value"
                a[k] = "yet another value"
                print(a[j])  --> another value
                print(a[k]) --> yet another value
                print(a[tonumber(j)]) --> one value
                print(a[tonumber(k)]) --> one value
```

```lua
 days = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"}
```

  会将`days[1]`初始化为字符串“`Sunday`”、`days[2]`初始化为“`Monday`”，以此类推。

##### 复杂数据结构

```lua
print("******复杂数据类型**********")
print("******字典**********")
--字典是键值对组成的
dic = { ["id1"] = "dove", ["age"] = 12, ["socre"] = 90,[2]= 100 }
print("访问方式1")
print(dic["id1"])
print(dic["age"])
print(dic["socre"])
print("访问方式2")
print(dic.id1)
print(dic.age)
print(dic.socre)
--print(dic.2)
print("不能通过数字")
print(dic[2])
--改 都是适用dic[键名]的
dic.age=20;
print(dic.age)
--增
dic.sex=false;
print(dic.sex)
print("******字典的遍历**********")

for i,v in pairs(dic) do
    --可以传N个参数
    print(i,v)
end

for i in pairs(dic) do
    print(dic[i])
end

print("***********类和结构体*************")
print("***********lua里没有类的概念，需要自己实现*************")
--lua没有面对对象的，需要我们自己去实现
--类也是一个表,利用表的概念去实现一个类
Student={
    --变量
    age=1,
    sex=true,
    up=function()
        --Lua中不能这样访问变量 这样写是一个全局的变量，值会为nil
        --print(age)
        --想要在表内部函数中，调用表本身的方法或者属性
        --一定要指定是谁的，所以 表名.属性
        print(Student.age)
        print("我成长了")
    end,
    --函数
    eat=function()
        print("我吃饭了")
    end
}
--声明表后，在表外添加变量和方法
Student.name="heart"
Student.Learn=function(t)
    print(t.sex)
    print("我学习了")
end
function Student.goSchool()
    print("去学校了")
end
--C#中使用类需要实例化new 或者静态直接点
--lua这中的表现形式更加像是很多个静态类
print(Student.age)
Student.up()
print(Student.name)
Student.Learn(Student) --第二种使用变量的方法
--Lua中点和冒号的区别
--冒号调用方法会默认把调用者作为第一个参数，传入方法中
Student:Learn()

function Student:Run()
    --Lua中有一个关键字self 表示默认传入的第一个参数
    print(self.name.."跑")
end
Student.goSchool()
Student:Run()

print("***********表的公共操作*************")
--table中提供了一些公共操作方法
t1={{age=1,name="heart"},{age=2,name="easily"}}
t2={name="heart",sex=true}
--插入
print(#t1)
table.insert(t1,t2);
print(#t1)
print(t1[3].name)

--删除指定元素
--会移除最后一个索引的内容
table.remove(t1)
print(#t1)
print(t1[1].name)
print(t1[2].name)
--移除内容的指定位置
table.remove(t1,1)

print("升序")
t3={5,3,4,2,7,8}
table.sort(t3)
for _, v in pairs(t3) do
    print(v)
end
--降序
print("降序")
t4={5,3,4,2,7,8}
--第二个参数是排序的规则
table.sort(t4,function(a, b)
    if a>b  then
        return true
    end end)
for _, v in pairs(t4) do
    print(v)
end
print("拼接")
tb={"123","456","789"}
--拼接函数 返回值为字符串
str=table.concat(tb,";")
print(str)
```

#### 表达式

- 算术运算符 +（加法）、-（减法）、*（乘法）、／（除法）、^（指数）、%（取模）、-（负号）
- 关系操作符  <  >  <=  >=  ==   ~=(不等于) 这些结果返回的都是true或者false
- 逻辑操作符 and(逻辑与)  or(逻辑或)  not  

```lua
   --与条件控制语句一样，所有的逻辑操作符将false和nil视为假，而将其他的任何东西视为真。

   --对于操作符and来说，如果它的第一个操作数为假，就返回第一个操作数；不然返回第二个操作数。

   --对于操作符or来说，如果它的第一个操作数为真，就返回第一个操作数；不然返回第二个操作数。
    print(4 and 5)        --> 5
    print(nil and 13)     --> nil
    print(false and 13)   --> false
    print(4 or 5)         --> 4
    print(false or 5)     --> 5
    
  --and和or都是用“短路求值”，也就是说，它们只会在需要时才去评估第二个操作数。
    --短路求值可以确保像（type(v)=="table" and v.tag == "h1"）这样的表达式不会导致运行时错误。

   -- 有一种常用的Lua习惯写法“x = x or v”，它等价于：
    if not x then x = v end

   -- 另外，还有一种习惯写法是“（a and b）or c”，这等价于表达式a?b:c，但前提是b不为假。
   --三目运算符
   --例如，为了选出数字x和y中的较大者，可以使用以下语句：
    max = (x > y) and x or y
   -- 操作符not永远只返回true或false：
        print(not nil)        --> true
        print(not false)      --> true
        print(not 0)          --> false
        print(not not nil)    --> false
```



#### 语句

Lua的常规语句和C语言差不多 同时也有一些不太常见的语句 如 多重赋值，和局部变量声明

```lua
    a = "hello" .. "world"
    t = { n = 0 }
    t.n = t.n + 1
```

  Lua允许“多重赋值”，也就是一下子将多个值赋予多个变量。每个值或每个变量之间以逗号分隔。

```lua
    a, b = 10, 2*x    -- a为10，b为2*x
```

  在多重赋值中，Lua先对等号右边的所有元素求值，然后才执行赋值。这样便可以用一句多重赋值来交互两个变量了。

```lua
    x, y = y, x                    -- 交换x与x
    a[i], a[j] = a[j], a[i]        -- 交换a[i]与a[j]
```

相对于全局变量 lua的局部变量用 `local`语句来创建

```lua
do
        local a = 1
        local b = 2
    end            -- a和b的作用域至此结束 do end
```

Lua提供了一组传统的、小巧的控制结构，包括用于条件执行的`if`，用于迭代的`while`、`repeat`和`for`。所有的控制结构都有一个显式的终止符：`if`、`for`和`while`以`end`作为结尾，`repeat`以`until`作为结尾。控制结构中的条件表达式可以是任何值，Lua将所有不是`false`和`nil`的值视为“真”

```lua
print("******循环语句*****")
print("******while循环语句*****")
x = 0
while x < 10 do
    print(x)
    x = x + 1
end
print("******do while循环 也就是repeat*****")  --
y = 0
repeat
    print(y)
    y = y + 1
until y > 5   --满足条件退出
print("lua中的do whlie 和C# 不一样 用repeat until 同时条件是退出条件不是进入条件")
print("******for循环语句*****")
for i = 1, 5 do
    print(i)
end
print("lua i 会默认自增")

for i = 1, 5,2 do  --另外一个值是指定自增多少  自减的话 改成-1
    print(i)
end
print("自减")
for i = 10, 1,-2 do
    print(i)
end

print("******增强for循环语句*****")
arr={1,2,3,4,5,6}
for i, v in ipairs(arr) do
    print(v)
end

```

`for`语句有两种形式：数字型`for`和泛型`for`。

##### 数字型 for

  `for`语句有两种形式：数字型`for`和泛型`for`。

  数字型`for`的语法如下：

```lua
    for var=exp1, exp2,exp3 do
        <执行体>
    end
```

  `var`从`exp1`变化到`exp2`，每次变化都以`exp3`作为步长（`step`）递增`var`，并执行一次“执行体”。第三个表达式是可选的，若不指定的话，Lua会将步长默认为1。

```lua
    for i=1, f(x) do print(i) end
    for i=10, 1, -1 do print(i) end
```

  如果不想给循环设置上限的话，可以使用常量`math.huge`：

```lua
    for i=1, math.huge do
        if(0.3*i^3 - 20*i^2 - 500 >= 0) then
            print(i)
            break
        end
    end
```

  首先，`for`的3个表达式是在循环开始前一次性求值的。例如，上例中的`f(x)`只会执行一次。其次，控制变量会被自动地声明为`for`语句的局部变量，并且仅在循环体内可见。

#####   泛型for

  泛型`for`循环通过一个迭代器函数来遍历所有值：

```lua
    -- 打印数组a的所有值
    for i,v in ipairs(a) do print(v) end
```

  Lua的基础库提供了`ipairs`，这是一个用于遍历数组的迭代器函数。在每次循环中，`i`会被赋予一个索引值，同时`v`被赋予一个对应于该索引的数组元素值。

```lua
    -- 打印table t中所有的key
    for k in pairs(t) do print(k) end
```

>   迭代table元素的（pairs）、迭代数组元素的（ipairs）、迭代字符串中单词的（string.gmatch）等。

  

  **泛型for循环与数字型for循环有两个相同点：**

```lua
    循环变量是循环体的局部变量；
    绝不应该对循环变量作任何赋值。
    -- 例：

    days = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"}

    -- 需要创建如下talbe
    revDays = {["Sunday"] = 1, ["Monday"] = 2, ["Tuesday"] = 3, ["Wednesday"] = 4, ["Thursday"] = 5, ["Friday"] = 6, ["Saturday"] = 7}

    -- 则可以按如下实现
    revDays = {}
    for k, v in pairs(day) do
        revDays[v]
```

#### 函数

> Lua为面向对象式的调用也提供了一种特殊的语法---冒号操作符。
>
> ```lua
>   o.foo(o, x)      -- 另一种写法是 o::foo(x)
> ```
>
>   冒号操作符使调用`o.foo`时将`o`隐含地作为函数第一个参数。
>
> Lua中函数不支持重载，默认调用最后一个函数 

##### 多返回值

调用函数时提供的实参数量可以与形參数量不同。Lua会自动调整实参的数量，以匹配参数表的要求。

```lua
    -- 假设一个函数如下：
    function f(a, b) return a or b end

    -- 在以下几种调用中，实参与形參的对应关系为：
    -- 调用        形參
    f(3)          a=3, b=nil
    f(3, 4)       a=3, b=4
    f(3, 4, 5)    a=3, b=4 (5被丢弃了)
    -- 默认实参的应用

    function incCount(n)
        n = n or 1
        count = count + n
    end
```

 Lua允许函数返回多个结果。只需在`return`关键字后列出所有的返回值即可。

```lua
 -- 查找数组中的最大元素，并返回该元素的位置：

    function maximum(a)
        local index = 1         -- 最大值的索引
        local max = a[index]    -- 最大值
        for i,val in ipairs(a) do
            if val > max then
                max = val; index = i
            end
        end
        return max, index
    end

    print(maximum{8, 10, 23, 12, 5})    --> 23   3
```

 Lua会调整一个函数的返回值数量以适应不同的调用情况。若将函数调用作为一条单独语句时，Lua会丢弃函数的所有返回值。**如果你传入的参数，和函数个数不匹配，不会报错，但会补nil或者丢弃**，若将函数作为表达式的一部分来调用时，Lua只保留函数的第一个返回值。**只有当一个函数调用是一系列表达式中的最后一个元素（或仅有一个元素）时，才能获得它的所有返回值。** 

##### 变长参数

Lua中的函数可以接受不同数量的实参。

```lua
 -- 这个函数返回了所有参数的总和：

    function add( ... )
        local s = 0
        for i, v in ipairs( ... ) do  -- 表达式{...}表示一个由所有变长参数构成的数组。
            s = s + v
        end
        return s
    end

    print(add(3, 4, 10, 25, 12))        --> 54
```

 参数中的3个点（`...`）表示该函数可接受不同数量的实参。    

##### 闭包

若将一个函数写在另一个函数之内，那么这个位于内部的函数便可以访问外部函数中的局部变量，这项特征称之为“词法域”。

```lua
function pack1(x)
    return function(y)
        return x+y
    end
end

--闭包机制改变了传入参数的生命周期
p=pack1(10)
P= p(5) 
print(P)  --> 15  x的值为10 y为5
```

##### 函数的嵌套

```lua
function F8()
    F9=function()
        print(123)
    end
    return F9
end

function F10()
    return function()
        print(456)
    end
end

f9=F8()
f9() -->123
f10= F10()
f10() --456
```

#### 协程

> Lua将所有关于协同程序的函数放置在一个名为“`coroutine`”的`table`中。

函数`create`用于创建新的协同程序，它只有一个参数，就是一个函数。该函数的代码就是协同程序所需执行的内容。`create`会返回一个`thread`类型的值，用以表示新的协同程序。通常`create`的参数是一个匿名函数。

```lua
    co = coroutine.create( function () print( "hi" ) end )
    print( co )    -- thread: 0x7fe2f1506218
--一个协程有四种状态
--coroutine.status(协程对象)
--dead 结束
--suspended 暂停
--running 进行中
--通过 print( coroutine.status( co ) )来检查状态
```

函数`coroutine.resume`用于启动或再次启动一个协同程序的执行，并将其状态由挂起改为运行：

```lua
 coroutine.resume( co )             -- hi
```

在运行后协程变终止了 处于`dead`状态

协程的本质是一个线程对象

到目前为止，协同程序看上去还只是像一种复杂的函数调用方法。其实协同程序的真正强大之处在于函数`yield`的使用上，该函数可以让一个运行中的协同程序挂起，而之后可以再恢复它的运行。

```lua
    co = coroutine.create( function ()
        for i = 1, 10 do
            print( "co", i )
            coroutine.yield()
        end
    end )
```

  现在当唤醒这个协同程序时，它就会开始执行，直到第一个`yield`：

```lua
    coroutine.resume( co )    -- co 1
```

  如果此时检查其状态，会发现协同程序处于挂起状态，因此可以再次恢复其运行：

```lua
    print( coroutine.status( co ) )    -- suspended
```

  从协同程序的角度看，所有在它挂起时发生的活动都发生在`yield`调用中。当恢复协同程序的执行时，对于`yield`的调用才最终返回。然后协同程序继续它的执行，直到下一个`yield`调用或执行结束：

```lua
    coroutine.resume( co )    -- co 2
    coroutine.resume( co )    -- co 3
    ...
    coroutine.resume( co )    -- co 10
    coroutine.resume( co )    -- 什么都不打印
```

  在最后一次调用`resume`时，协同程序的内容已经执行完毕，并已经返回。因此，这时协同程序处于死亡状态。如果试图再次恢复它的执行，`resume`将返回`false`及一条错误消息：

```lua
    print(coroutine.resume( co ))    -- false cannot resume dead coroutine
```

> 请注意，`resume`是在保护模式中运行的。因此，如果在一个协同程序的执行中发生任何错误，Lua是不会显示错误消息的，而是将执行权返回给`resume`调用。

**Lua的协同程序还具有一项有用的机制，就是可以通过一对`resume-yield`来交换数据。**在第一次调用`resume`时，并没有对应的`yield`在等待它，因此所有传递给`resume`的额外参数都将视为协同程序主函数的参数：

```lua
    co = coroutine.create( function ( a, b, c )
        print( "co", a, b, c )
    end )
    coroutine.resume( co, 1, 2, 3 )    -- co 1 2 3
```

  在`resume`调用返回的内容中，第一个值为`true`则表示没有错误，而后面所有的值都是对应`yield`传入的参数：

```lua
    co = coroutine.create( function ( a, b )
        coroutine.yield( a + b, a - b )
    end )

    print( coroutine.resume( co, 20, 10 ) )    -- true 30 10
```

  与此对应的是，`yield`返回的额外值就是对应`resume`传入的参数：

```lua
    co = coroutine.create( function ()
        print( "co", coroutine.yield() )
    end )

    print(coroutine.resume( co, "a" ))      -- true
    print(coroutine.resume( co, 4, 5, 6 ))  -- co 4 5 6  -- true
    print(coroutine.resume( co, 4, 5))      -- false cannot resume dead coroutine
```

  最后，当一个协同程序结束时，它的主函数所返回的值都将作为对应`resume`的返回值：

```lua
    co = coroutine.create( function ()
        return 6, 7
    end )
    print( coroutine.resume( co ) )        -- true 6 7
```



#### 元表

任何表变量都可以作为另一个表变量的元素，任何表变量都可以有自己的元表，当我们子表进行一些特定操作时，会执行元表中的内容

可以通过元表来修改一个值的行为，使其在面对一个非预定义的操作时执行一个指定的操作。例如，假设a和b都是table，通过元表可以定义如何计算表达式a+b。当Lua试图将两个table相加时，它会先检查两者之一是否有元表，然后检查该元表中是否有一个叫__add的字段。如果Lua找到了该字段，就调用该字段对应的值。这个值也就是所谓的“元方法”

```lua
meta = {}
myTable = {}
--设置元表函数 第一个是字表 第二个是元表
setmetatable(myTable, meta)
```

tostring操作

```lua
meta2 = {
    --当字表要被当做字符串使用时，会默认调用元表中的tostring方法
    __tostring = function(t)
        return t.name
    end
}
myTable2 = {
    name = "Heart"
}
setmetatable(myTable2, meta2)
print(myTable2) --> Heart
```

```lua
print("***********特定操作_ Call************")
meta3 = {
    --当字表要被当做字符串使用时，会默认调用元表中的tostring方法
    __tostring = function(t)
        return t.name
    end,

    --当字表当做一个函数使用时，会默认调用__call里的内容
    --当希望传入参数时，默认第一个参数是调用者自己
    __call = function(t, b)
        print(b)
        print(t.name .. " 在call你")
    end
}
myTable3 = {
    name = "Heart"
}

setmetatable(myTable3, meta3)
--第一个参数是本身，第二个参数才是传入的值
myTable3(1)
```

```lua
print("***********特定操作_ 运算符重载************")
meta4 = {
    --相当于运算符重载，当字表调用+运算符时，会调用该方法
    --运算符+
    __add = function(t1, t2)
        return t1.age + t2.age
    end,

    --运算符-
    __sub = function(t1, t2)
        return t1.age - t2.age
    end,
    --运算符*
    __mul = function(t1, t2)
        return t1.age * t2.age
    end,
    --运算符/
    __div = function(t1, t2)
        return t1.age / t2.age
    end,
    --运算符%
    __mod = function()
    end,
    --运算符^
    __pow = function()
    end,
    --运算符==
    __eq = function()
    end,
    --运算符<
    __lt = function()
    end,
    --运算符<=
    __le = function(t1, t2)
        return t1.age <= t2.age
    end,
    --运算符 .. 拼接
    __concat = function(t1, t2)
        return t1.age .. t2.age
    end
}
myTable4 = {
    age = 0
}
setmetatable(myTable4, meta4)
myTable5 = {
    age = 2
}
setmetatable(myTable5, meta4)

--如果要用条件运算符，来比较两个对象
--这两个对象的元表一定要一致 才能准确调用
print(myTable4 + myTable5) --2
print(myTable4 .. myTable5) -- 02
print(myTable4 <= myTable5) --true
```

##### table访问的元方法

  算术类和关系类元算符的元方法都为各种错误情况定义了行为，它们不会改变语言的常规行为。但是Lua还提供了一种可以改变table行为的方法。有两种可以改变的table行为：查询table及修改table中不存在的字段。

######  __index元方法

当访问一个table中不存在的字段时，得到的结果为nil。这是对的，但并非完全正确。实际上，这些访问会促使解释器去查找一个叫__index的元方法。如果没有这个元方法，那么访问结果如前述的为nil。否则，就由这个元方法来提供最终结果。

  下面将介绍一个有关继承的典型示例。假设要创建一些描述窗口的table，每个table中必须描述一些窗口参数，例如位置、大小及主题颜色等。所有这些参数都有默认值，因此希望在创建窗口对象时可以仅指定那些不同于默认值的参数。第一种方法是使用一个构造式，在其中填写那些不存在的字段。第二种方法是让新窗口从一个原型窗口处继承所有不存在的字段。首先，声明一个原型和一个构造函数，构造函数创建新的窗口，并使它们共享同一个元表：

```lua
    Window = {}         -- 创建一个名字空间
    -- 使用默认值来创建一个原型
    Window.prototype = {x=0, y=0, width=100, height=100}
    Window.mt = {}         -- 创建元表
    -- 声明构造函数
    function Window.new(o)
        setmetatable(o, Window.mt)
        return o
    end
```

  现在，来定义`__index`元方法：

```lua
    Window.mt.__index = function(table, key)
        return Window.prototype[key]
    end
```

  在这段代码之后，创建一个新窗口，并查询一个它没有的字段：

```lua
    w = Window.new{x=10, y=20}
    print(w.width)            --> 100
```

  若Lua检测到`w`中没有某字段，但在其元表中却有一个`__index`字段，那么Lua就会以`w(table)`和“`width`”（不存在的key）来调用这个`__index`元方法。随后元方法用这个key来索引原型table，并返回结果。

  在Lua中，将`__index`元方法用于继承是很普通的方法，因此Lua还提供了一种更便捷的方式来实现此功能。`__index`元方法不必一定是一个函数，它还可以是一个table。当它是一个函数时，Lua以table和不存在的key作为参数来调用该函数，这就如同上述内容。而当它是一个table时，Lua就以相同的方式来重新访问这个table。因此，前例中`__index`的声明可以简单地写为：

```lua
    Window.mt.__index = Window.prototype
```

  现在，当Lua查找到元表的`__index`字段时，发现`__index`字段的值是一个table，那么Lua就会在Window.prototype中继续查找。也就是说，Lua会在这个table中重复这个访问过程，类似于执行这样的代码：

```lua
    Window.prototype["width"]
```

  然后由这次访问给出想要的结果。

  将一个table作为`__index`元方法是一种快捷的、实现单一继承的方式。虽然将函数作为`__index`来实现相同功能的开销较大，但函数更加灵活。可以通过函数来实现多重继承、缓存及其他一些功能。

  如果不想在访问一个table时涉及到它的`__index`元方法，可以使用函数`rawget`。调用`rawget(t, i)`就是对table t进行了一个“原始的(raw)”访问，也就是一次不考虑元表的简单访问。一次原始访问并不会加速代码执行，但有时会用到它。

######  __newindex元方法

  **`__newindex`元方法与`__index`类似，不同之处在于前者用于table的更新，而后者用于table的查询**。当对一个table中不存在的索引赋值时，解释器就会查找`__newindex`元方法。如果有这个元方法，解释器就调用它，而不是执行赋值。如果这个元方法是一个table，解释器就在此table中执行赋值，而不是对原来的table。此外，还有一个原始函数允许绕过元方法：调用`rawset(t,k,v)`就可以不涉及任何元方法而直接设置table t中与key k相关联的value v。

  组合使用`__index`和`__newindex`元方法就可以实现出Lua中的一些强大功能，例如，只读的table、具有默认值的table和面向对象编程中的继承。



#### 面对对象编程

Lua的面对对象编程需要我们自己去实现

 Lua中的`table`就是一种对象，这句话可以从3个方面来证实。首先，`table`与对象一样可以拥有状态。其次，`table`也与对象一样拥有一个独立于其值的标识（一个`self`）。例如，两个具有相同值的对象（`table`）是两个不同的对象。最后，`table`与对象一样具有独立于创建者和创建地的生命周期。

```lua
 Account = {balance = 0}
    function Account.withdraw(v)
        Account.balance = Account.balance - v
    end
```

  上面的代码创建了一个新函数，并将该函数存入`Account`对象的`withdraw`字段中。则可进行如下调用：

```lua
    Account.withdraw(100.00)
```

但这个函数只能针对特定对对象工作，这个特定对象还必须存储在特定的全局变量中。如果改变了对象的名称，`withdraw`就再也不能工作了：

```lua
    a = Account;Account = nil
    a.withdraw(100.00)        -- 错误！❌
```

这种行为违法了对象的特性，为此Lua提供了一个额外的参数来表示接受者，这个参数是 `self`或`this`

```lua
 function Account.withdraw(self, v)
        self.balance = self.balance - v
    end

--此时当调用该方法时，必须指定其作用的对象：

    a1 = Account; Account = nil
    ...
    a1.withdraw(a1, 100.00)        -- OK

```

通过对`self`参数的使用还可以针对多个对象使用同样的方法

```lua
 a2 = {balance=0, withdraw=Account.withdraw}
    ...
    a2.withdraw(a2, 260.00)
--使用self参数是所有面向对象语言的一个核心。大多数面向对象语言都能对程序员隐藏部分self参数，从而使得程序员不必显式地声明这个参数。Lua只需使用冒号，则能隐藏该参数。即可将上例重写为：

    function Account:withdraw(v)
        self.balance = self.balance - v
    end
 --调用时可写为：

    a:withdraw(100.00)
```

> 冒号的作用是在一个方法定义中添加一个额外的隐藏参数，以及在一个方法调用中添加一个额外的实参。冒号只是一种语法便利，并没有引入任何新的东西

##### 类

要在Lua中模拟类并不困难，例实现一个Object类

```lua
Object={}
--实例化
function Object:new(obj)
    local obj= obj or {} --如果用户满意提供table，则创建一个
    self.__index=self
    --给对象设置元表 以及index
    setmetatable(obj,self)
    return obj
end
```

当调用new时，self就等于Object  当创建新对象时，obj会将Object作为元表

ps ：注意`self`的作用

##### 封装

```lua
Object = { }
Object.id=1
Object.name="Heart"
function Object:Test()
    print(self.name.."封装测试") --Heart封装测试
end
--冒号 会自动将调用这个函数的对象 作为第一个参数传入的写法
function Object:new()
    --self 代表传入的默认第一个参数
    --对象就是新的变量 返回一个新的变量
    --返回出去的内容 本质上就是表对象
    local obj = {}
    --结合元表知识 index  当找自己的变量 找不到时 就会去元表中找 index指向的内容
    self.__index = self
    setmetatable(obj, self)
    return obj
end
--local myobj=Object:new()
--print(myobj.id)--这样是无法new出来的 我们要结合元表的知识
local myobj = Object:new()
print(myobj)
print(myobj.id) --1
myobj:Test()--注意调用形式
--ps 注意
myobj.id=2
print(Object.id)--还会是1 因为myobj是空表 他的声明一个新的属性为id
```

##### 继承

由于类也是对象，他们可以从其他类获得方法，这种行为就是一种继承。

假设有一个基类`Account`：

```lua
    Account = {}

    function Account:new(o)
        o = o or {}
        setmetatable(o, self)
        self.__index = self
        return o
    end

    function Account:deposit(v)
        self.balance = self.balance + v
    end

    function Account:withdraw(v)
        if v > self.balance then error "insufficient funds" end
        self.balance = self.balance - v
    end
```

  若想从这个类派生出一个子类`SpecialAccount`，以使客户能够透支。则先需要创建一个空的类，从基类继承所有的操作：

```lua
    SpecialAccount = Account:new()
```

  直到现在，`SpecialAccount`还只是`Account`的一个实例

```lua
    s = SpecialAccount:new{limit=1000.00}
```

  `SpecialAccount`从`Account`继承了`new`，就像继承其他方法一样。不过这次`new`在执行时，它的`self`参数表示为`SpecialAccount`。因此，s的元表为`SpecialAccount`，`SpecialAccount`中字段`__index`的值也是`SpecialAccount`。`s`继承自`SpecialAccount`，而`SpecialAccount`又继承自`Account`。当执行：

```lua
    s:deposit(100.00)
```

  Lua在`s`中找不到`deposit`字段时，就会查找`SpecialAccount`。如果仍找不到`deposit`字段，就查找`Account`。最终会在那里找到`deposit`的原始实现。

  `SpecialAccount`之所以特殊是因为可以重定义那些从基类继承的方法。编写一个方法的新实现只需：

```lua
    function SpecialAccount:withdraw(v)
        if v - self.balance >= self:getLimit() then
            error "insufficient funds"
        end
        self.balane = self.balance - v
    end

    function SpecialAccount:getLimit()
        return self.limit or 0
    end
```

  现在，当调用`s:withdraw(200.00)`时，Lua就不会在`Account`中查找了。因为Lua会在`SpecialAccount`中先找到`withdraw`方法。由于`s.limit`为1000.00，程序会执行取款，并使`s`变成一个负的余额。

  Lua中的对象有一个特殊现象，就是无须为指定一种新行为而创建一个新类。如果只有一个对象需要某种特殊的行为，那么可以直接在该对象中实现这个行为。例如，账户`s`表示一个特殊的客户，这个客户的透支额度总是其余额的10%。那么可以只修改这个对象：

```lua
    function s:getLimit()
        return self.balance * 0.10
    end
```

  在这段代码后，调用`s:withdraw(200.00)`还是会执行`SpecialAccount`的`withdraw`。但`withdraw`所调用的`self:getLimit`则是上面这个定义。

##### 多态

```lua
--继承
function Object:SubClass(ClassName)
     _G[ClassName]={}
    local obj=_G[ClassName]
    obj.base=self
    --给子类设置元表
    self.__index=self
    setmetatable(obj,self)
end

Object:SubClass("GameObject")
GameObject.posx=0;
GameObject.posy=0;
function GameObject:Move()
    self.posx=self.posy+1
    self.posy=self.posy+1
end

local obj=GameObject:new()
print(obj.posx)
obj:Move()
print(obj.posx)

local obj2=GameObject:new()
print(obj2.posx)
obj2:Move()
print(obj2.posx)

print("********")
--多态
GameObject:SubClass("Player")
--重写方法
function Player:Move()
    self.base.Move(self)
end
local p1=Player:new()
print(p1.posx)
p1:Move()
print(p1.posx)
```
