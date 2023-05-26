---
title: CSahrp Socket连接
date: 2021-12-23 11:13:12
author: Heart
categories: CSharp
tags:
  - 计算机网络
  - 花里胡哨
  - Socket
  - CSharp
index_img: https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/20211223112332.png
excerpt: OSI TCP UDP

---

# C#实现Socket连接

### 服务端

1. 建立Socket

   AddressFamily.InterNetwork内网，SocketType.Stream以流来通信，Tcp为协议

2. 电脑主机和端口和ip地址进行绑定

3. 监听客户端连接

4. 阻塞方法  生成Socket 接受客户端连接

5. 声明数组，存放聊天的缓冲区。

6. 使用while方法 , 接收数据并存放在数组中 实现多次接消息

7. 还原接收信息为明文

8. 实现客服给客户发消息

### 代码如下

```c#
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;

namespace SocketDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            //创建Socket 相当于是通信的主机
            //监听打进来的电话，并转接给客服
            Socket serverSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

            //绑定ip和端口
            EndPoint iped = IPEndPoint.Parse("192.168.43.227:1234");
            serverSocket.Bind(iped);

            //监听有没有电话连接，顺便规划客服人数,如果是0就是无限个客服
            serverSocket.Listen(100);

            //接电话
            //分配客服和客户进行一对一通信
            //阻塞方法 没有电话打进来，那么代码就执行到这里 Accept
            Socket kefuScoket = serverSocket.Accept();
            System.Console.WriteLine("客户的本地端口是：" + kefuScoket.LocalEndPoint.ToString());
            System.Console.WriteLine("打电话进来的客户端是：" + kefuScoket.RemoteEndPoint.ToString());

            //接消息，需要一个消息的缓冲区
            byte[] readBuff = new byte[1024];

            while (true)
            {
                //消息拿到，并存放在缓冲区，并记住消息的长度
                int receiveCount = kefuScoket.Receive(readBuff);
                string receiveMessage = Encoding.UTF8.GetString(readBuff, 0, receiveCount);
                System.Console.WriteLine("客户端发过来的消息：" + receiveMessage);

                //string backMessage = "我是你的专属客服，有什么可以帮助你的";
                kefuScoket.Send(Encoding.UTF8.GetBytes("服务器已经接到你发来的消息：" + receiveMessage));
            }
        }
    }
}

```

### 客户端

1. 建立Socket
2. Connect连接服务端
3. try catch语句 捕捉异常

```c#
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;

namespace clientSocket
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            Socket clientSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
            //接收服务器的回馈
            byte[] readBuff = new byte[1024];

            //连接服务器
            clientSocket.Connect("192.168.43.227", 1234);

            //连接上服务器，就能发消息
            string sendMessage;

            //计数器
            int num = 0;

            while (true)
            {
                if (num == 0)
                {
                    sendMessage = "你好，我是192用户";
                    num++;
                }
                else
                {
                    sendMessage = Console.ReadLine();
                }

                //转码，转成0101010的格式
                byte[] sendBytes = Encoding.UTF8.GetBytes(sendMessage);

                //把二进制的消息发出去
                clientSocket.Send(sendBytes);
                int count = clientSocket.Receive(readBuff);
                System.Console.WriteLine("服务器发送的消息是：" + Encoding.UTF8.GetString(readBuff, 0, count));

            }

        }
    }
}

```

#### C#Socket各种类

一、IP地址操作类

1、IPAddress类

在该类中有一个Parse()方法，可以把点分的十进制转换成IPAddress类，方法1如下：IPAddress address=IPAddress.Parse("192.168.0.1");

IPAddress 提供4个只读字段

1. Any 用于代表本地系统可用的任何IP地址 常用来表示本机所有的IP地址 方便于对Socket服务进行监听，
2. Broadcase用于代表本地网络的IP广播地址 UDP的IP广播
3. Loopback用于代表系统的回送地址
4. None用于代表系统是没有网络接口

2、IPEndPoint类

我们可以通过二种构造方法创建IPEndPoint类   就是一个IP地址端口的绑定 可以代表一个服务，用来Socket通讯

a、IPEndPoint(long address,int port)

b、IPEndPoint(IPAddress address,int port)

四种属性：Address  AddressFamily Port MaxPort  MinPort

二、DNS相关类

DNS类有四个静态方法，来获取主机DNS相关信息

1. GetHostName（） 通过DNS.GetHostName()可以获得本地计算机的主机名
2. GetHostByName() 根据主机名称，返回一个IPHostEntry对象：IPHostEntry GetHostByName(string hostName) 其中IPHostEntry 把一个DNS主机名与一个别名和IP地址的数组相关联，包括三个属性：AddressList：一个IPAddress对象的数组   Aliases：一个字符串对象数组 HostName一个用于主机名的字符串对象
3. GetHostByAddress() 类似于GetHostByName(),只不过这里的参数是IP地址，而不是主机名，返回一个IPHostEntry对象。  IPHostEntry GetHostByAddress(IPAddress address)   IPHostEntry GetHostByAddress(string address)
4. Resolve()   当我们不知道输入的主机的地址是哪种格式时，用以上两种方法来实现，我们可能还要通过判断客户输入的格式，才能正确使用，但DNS类提供更简单的方法Resolve（），该方法可以接收或者是主机名格式或者是IP地址格式的任何一种地址，并返回IPHostEntry对象
