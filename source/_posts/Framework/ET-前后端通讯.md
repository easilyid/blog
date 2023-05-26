---
title: ET-前后端通讯
author: Heart
date: 2022-01-05 16:24:48
categories: Framework
tags:
  - GameFramework
  - Unity
  - ET
  - Socket
index_img: https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/20220407115210.png
excerpt: 前后端通讯 Socket

---



UILoginComponentSystem

我们从Demo的**UILoginComponentSystem**类中开始看起 它继承自**AwakeSystem**的**UILoginComponent**组件的生命周期辅助类，

Awake方法注册了Demo Init的登录点击事件，实际上就是注册**UILoginComponent**的扩展方法OnLogin 而OnLogin调用了静态的辅助类**LoginHelper**里的Login方法，Login方法呢就是真正编写客户端向服务端发起登录请求的逻辑方法。



客户端是如何和服务端发起通讯的？

**LoginHelper**里的Login方法的第二个参数address，所传入的是ConstValue的常量LoginAddress所定义的IP地址和端口

使用Socket接口。



ET的Socket是如何调用的

**LoginHelper**里的Login方法里的zoneScene调用了NetKcpComponent组件的的Create方法，传入address参数，将IP地址进行转换，而Create方法的主要作用是创建了Session，Session是会话，而我们会拿到GetOrCreate的Get创建了一个**Channel**的类，而在TChannel的这个类中，Socket被正式创建出来了，而在创建Socket的时候使用的是一个session的ID，这个Id对Channel进行了对应和关联。这样就能实现一个映射表的关系，有这个映射表的关系，就能通过session来对Socket来写入数据，Socket就可以负责将网络消息数据进入到传输层，网络互联层 接口层，最终发到游戏服务器上去



C2R_LoginHandler类 消息处理类



消息传输流程 BY狐狸菌

## 结构模型

- NetworkComponent【NetOuterComponent、NetInnerComponent】
  - Session
  - AService【KService、TService、WService】
    - Socket
    - PacketParser
    - CircularBuffer
    - AChannel【KChannel、TChannel、WChannel】
  - IMessagePacker【MongoPacker、ProtobufPacker】
  - IMessageDispatcher【InnerMessageDispatcher、OuterMessageDispatcher（服务端）、OuterMessageDispatcher（客户端）】
- OpcodeTypeComponent
- MessageDispatcherComponent
  - IMHandler【AMHandler、AMRpcHandler】

服务器独占

- MailboxDispatcherComponent
  - IMailboxHandler【MailboxGateSessionHandler、MailboxMessageDispatcherHandler】
- ActorMessageDispatcherComponent
  - IMActorHandler【AMActorHandler、AMActorRpcHandler、AMActorLocationHandler、AMActorLocationRpcHandler】
- LocationProxyComponent
- LocationComponent
- ActorMessageSenderComponent
  - ActorMessageSender
- ActorLocationSenderComponent
  - ActorLocationSender

## 客户端建立Session

1. 添加`NetOuterComponent`

   因为`NetOuterComponent外网组件`继承自`NetworkComponent网络组件`，所以在`Init`类中为`Scene`添加`NetOuterComponent`也就等于添加了`NetworkComponent`，同时`NetOuterComponent`还指定了网络组件以什么协议沟通；

   - 考虑到服务端Hotfix与Model分离规则，所以`NetOuterComponent`的事件驱动与组件不在一个类中。

   - 在

     ```
     NetOuterComponent
     ```

     的

     ```
     Awake
     ```

     事件中创建了

     ```
     TService、ProtobufPacker、OuterMessageDispatcher
     ```

     ，并保存引用到自身

     - TService：TCPService，继承自`AService`
     - ProtobufPacker：Protobuf打包器，继承自`IMessagePacker`,主要用于调用`ProtobufHelper`对消息进行打包处理
     - OuterMessageDispatcher：外部消息分发组件，继承自`IMessageDispatcher`，主要用于调用`MessageDispatcherComponent`对外部消息进行分发处理

2. 创建`Model.Session`

   通过外网组件间接调用网络组件的`Create`方法，先通过`TService`的`ConnectChannel`方法传入远端IP地址创建了一个`TChannel`,然后将`TChannel`作物初始化参数创建了一个`Model.Session`，并调用`Session`的`Start`方法启动Session。

   - TChannel：TCP通道，继承自：`AChannel`，在构造函数中创建了`Socket、PacketParser`，并设置了各种流处理相关属性

     - Socket：套接字
     - PacketParser：包解析器
     - CircularBuffer：缓冲区

   - Session

     在其Awake方法中，将`NetworkComponent.Remove`自身方法作为委托传入了`TChannel`的`ErrorCallback`中，还将自身的`OnRead`方法作为委托传入了`TChannel`的`ReadCallback`中

     Session的`Start`方法调用了`TChannel`的`Start`方法

     1. 建立远程链接

     2. 开始循环异步接收消息

        注意，到这里Model.Session已经创建完成，并且开始接收消息，以下是收到消息后的处理

        - 当一条消息读取完毕后，先调用`PacketParser`包解析器，进行解包
        - 再通过委托调用`Session`的`OnRead`方法对消息进行分发处理

3. 创建`Hotfix.Session`

   将上一步生成的`Model.Session`作为初始化参数，创建一个`Hotfix.Session`，该类的`Dispose`方法会自动调用`Model.Session`的`Dispose`

   在该类的`Awake`事件中，会给`Model.Session`添加`SessionCallbackComponent热更层Session回调组件`；该组件持有两个委托

   - `MessageCallback`用于`Model.Session`通过委托调用`Hotfix.Session`的`Run`方法
   - `DisposeCallback`用于`Model.Session`通过委托调用`Hotfix.Session`的`Dispose`方法

## 发送消息

1. Hotfix.Session
   - 发送消息
     - 发送普通消息，调用`Hotfix.Session`的`Send`方法
     - 发送请求消息，调用`Hotfix.Session`的`Call`方法，创建一个异步完成的委托回调，用于消息答复时触发ETTask返回
   - 通过`OpcodeTypeComponent`对应的消息码
   - 调用`Model.Session`的`Send`方法
2. Model.Session
   - 通过`NetOuterComponent`类的`ProtobufPacker`序列化消息
   - 写入操作码
   - 调用`TChannel`的`Send`方法
3. Tchannel
   - 将消息写入缓冲区`CircularBuffer`
   - 通过`TService`的`MarkNeedStartSend`方法将自生标记为待发送
   - `TService`的`Update`事件将调用`Tchannel`的`StartSend`方法发送消息
   - `StartSend`调用`SendAsync`向远端发送一条消息

## 接收消息

`TChannel`的`Start`方法将开启循环接收消息。下面演示接收到一条消息后的处理流程

1. TChannel

   - 当一条消息读取完毕后，先调用`PacketParser`包解析器，进行解包
   - 通过委托调用`Session`的`OnRead`方法对消息进行分发处理

2. Session

   - 获取操作码

   - 通过`OpcodeHelper.IsClientHotfixMessage`检测是否是热更层消息

     如果是热更层消息，则通过`SessionCallbackComponent`委托调用热更层Session处理消息，后续hotfix和Model处理流程是一样的

   - 通过`OpcodeTypeComponent操作码-类型组件`获取操作码对应的消息类实例

   - 通过`NetOuterComponent`类的`ProtobufPacker`反序列化数据

   - 检测消息是否是响应消息

     - 不是响应消息，直接通过`NetOuterComponent`类的`OuterMessageDispatcher`调用`MessageDispatcherComponent`处理消息
     - 是响应消息，通过消息的RpcId查找请求队列中对应的请求，并激活完成事件

## 服务端Actor模型建立

- 登录服务器

  1. 添加`NetInnerComponent、NetOuterComponent`内网组件和外网组件来为当前服务器建立基本网络收发配置
  2. 分配服务器网关地址，在登录验证通过后，通过`RealmGateAddressComponent`随机网关地址组件获取`内网`网关地址
  3. 通过向`NetInnerComponent.Get`方法传入获取到的网关地址，创建一个与网关服务器链接的内网Session
  4. 通过Session在`网关服务器`上创建一个登录Key，然后将Key和客户端地址保存到`GateSessionKeyComponent`网关登录Key组件中，注意：该Key会在20秒后自动销毁

- 网关服务器

  1. 客户端通过从登录服务器获取的`外网`网关地址，建立一个链接网关的Session

  2. 通过`GateSessionKeyComponent`验证网关登录Key和Value是否匹配，不匹配返回登录失败

  3. 将Value作为初始参数创建一个`Plyaer`

  4. 为当前链接客户端的`Session`添加`SessionPlayerComponent`Session绑定Player组件，并绑定Player

     该类在Session销毁时，自身的Destroy事件会被调用。这里的Destroy还没写完

  5. 同时为当前`Session`添加邮箱组件，并设置类型为网关邮箱（网关邮箱收到的信息会直接转发给链接当前Session的客户端）

     挂上这个组件表示该Entity是一个Actor,接收的消息将会队列处理

     > 到这一步，算是为对象建立Actor模型完成

  6. 进入Map服务器

     通过`StartConfigComponent`初始配置管理组件获取一个map服务器`内网`地址，并根据地址使用`NetInnerComponent.Get`方法创建一个链接Map服务器的内网Session

     通过Session发送在Map服务器上创建Unit的请求，并传入当前网关服务链接客户端的Session的唯一Id

- Map服务器

  1. 创建Unit

  2. 为Unit添加`UnitGateComponent`Unit网关组件，并将传入的网关Session的唯一Id绑定到组件中

  3. 为Unit添加`MailBoxComponent`邮箱组件

  4. 调用`MailBoxComponent`邮箱组件的`AddLocation`方法

     调用`LocationProxyComponent`位置代理组件的`Add`方法

     创建一获取地址服务器的`内网`地址创建一个链接地址服务器的内网Session

     通过内网Session将Unit的Id和唯一Id发动到Location服务器

- Location服务器

  1. 接收到Map服务器发送的`ObjectAddRequest`请求后，在`LocationComponent`位置组件中保存传送过来的Unit的Id与InstanceId

     > 到这一步，算是为对象建立了LocationActor模型完成，其实Actor模型和LocationActor模型代码上步骤分支主要在于添加邮箱组件时分配的邮箱类型和有没有调用`AddLocation`方法

## 发送Actor消息

参考服务端`MessageHelper`（客户端ActorLocation消息参考`OperaComponent`，服务端参考`SessionPlayerComponentSystem`）

1. 获取Unit的`UnitGateComponent`Unit网关组件，并得到组件中网关Session对象的唯一Id

2. 通过网关Session的唯一Id提取网关服务器设备Id

3. 通过`ActorMessageSenderComponent`Actor消息发送器管理组件创建一个`ActorMessageSender`Actor消息发送器，并通过上一步获取的网关服务器设备Id查找到对应的网关服务器的`内网地址`，赋值到Actor消息发送器中。

4. 调用Actor消息发送器的`Send`方法

   使用`NetInnerComponent`内网组件和网关服务器地址，创建一个链接网关服务器的内网Session，然后使用该Session发送消息。

   > 由于创建Session时使用的时内网地址，所以会有网关服务器的内网组件接收Session传来的消息，由于内网组件使用的消息分发器和外网组件不同，所以在解析消息的时候Actor消息和普通消息相比会有更多步骤

## 接收Actor消息

和普通消息前期收取规则是一样的，产生差异在于Session调用消息分发器分发消息时。

- `InnerMessageDispatcher`内网消息分发

  1. 解析消息是否是Actor消息，非Actor消息调用`MessageDispatcherComponent`消息分发组件处理消息

  2. Actor消息会通过`EventSystem`查找发送过来的ActorID（也就是对象的InstanceId）对应对象是否存在，不存在则直接返回`获取Actor失败异常`

  3. 获取对象身上的`MailBoxComponent`邮箱组件，（如果获取不到则返回`获取邮箱失败异常`）将Session和Actor消息封装成`ActorMessageInfo`存入邮箱组件中

  4. 邮箱组件会在循环中读取消息并调用`MailboxDispatcherComponent`邮箱分发组件的`Handle`方法进行二次分发消息

  5. `Handle`方法根据前面的邮箱组件类型分发给`MailboxGateSessionHandler或MailboxMessageDispatcherHandler`的`Handle`方法做处理

     - `MailboxGateSessionHandler`直接将第三步获取的对象转换成`Session`向客户端发送消息

     - `MailboxMessageDispatcherHandler`通过`ActorMessageDispatcherComponent`Actor消息分发组件的`Handle`方法，将消息进行最终分发处理

       `ActorMessageDispatcherComponent`Handle方法会查找注册在组件中消息操作码对应的消息处理实例，有该实例完成消息最终的处理

- `OuterMessageDispatcher`外网消息分发

  1. 解析消息是否是ActorLocation消息，非ActorLocation消息调用`MessageDispatcherComponent`消息分发组件处理消息

  2. ActorLocation消息通过Session绑定的`SessionPlayerCompoennt`组件获取Player，再通过Player获取UnitId；

  3. 通过`ActorLocationSenderComponent`ActorLocation消息发送器管理组件创建`ActorLocationSender`ActorLocation消息发送器，指定该对象的Id为UnitId，并将UnitId作为Key和ActorLocation消息发送器一起保存到字典中

     > 由于可能产生Bug或者进程挂掉而导致ActorLocationSender没有返回，所以在ActorLocationSenderComponent的Start事件中开启了一个携程，用于清理超时1分钟没有返回的ActorLocation消息发送器

  > 由于对象可能进行跨服转移，所以消息的接收不能直接使用对象身上挂在组件，需要生成一个代理对象来收发消息、对消息进行阻塞，也就是`ActorLocationSender`

  - 在`ActorLocationSender`的Start事件中，通过向`LocationProxyComponent`位置代理组件传入UnitId调用`Get`方法获取唯一ID
  - 在`Get`方法中建立一个链接位置服务器的内网Session，并通过该Session发送`ObjectGetResponse`请求，传入UnitID
  - 在位置服务器中，通过接收到的UnitId向`LocationComponent`位置组件`GetAsync`异步获取方法获取Unit的唯一Id
  - 在`ActorLocationSender`的Start事件中，还开启了一个携程循环，用于读取`ActorTask`队列

  1. 调用`ActorLocationSender`ActorLocation消息发送器的`Call或Send`方法，创建一个`ActorTask`Actor消息任务，并保存到队列中

  2. 在`ActorLocationSender`ActorLocation消息发送器的`UpdateAsync`方法中，会循环读取队列中的`ActoTask`Actor消息任务，并调用`RunTask`方法发送消息

     `RunTask`方法流程

     1. 先通过`ActorMessageSenderComponent`Actor消息发送器管理组件的`Get`方法获取一个`ActorMessageSender`Actor消息发送器

     - ```
       Get
       ```

       方法会使用传入的Unit的唯一ID提取对应的服务器设备ID，再通过设备ID获取对应服务器内网地址

       - 将Unit唯一ID和服务器地址作为初始参数创建一个`ActorMessageSender`Actor消息发送器

     1. 使用`ActorMessageSender.CallWithoutException`不抛出异常的方式发送消息，这里后续逻辑就接入了前面所说的`InnerMessageDispatcher`内网消息分发
     2. 检测上一步消息是否发送成功
        - 如果发送成功，则完成ActorLocation消息的收发流程
        - 如果是获取Actor失败异常，则等待半秒，然后重新从上面第五步开始`UpdateAsync`方法开始执行，注意该流程会重复5次，超过五次会将`ActorLocationSender`自身销毁，并抛出异常
        - 如果是获取邮箱失败异常，则直接将自身销毁，并抛出异常

## 对象跨服跳转

参考`Actor_TransferHandler`

- Map服务器A
  1. 调用`LocationProxyComponent`位置代理组件的`Lock`方法（传入上锁对象ID和上锁时长），建立与位置服务器链接的内网Session，并发送上锁消息`ObjectLockRequest`
- Location服务器
  1. 调用`LocationComponent`的`Lock`方法，在Lock方法中会将上锁对象ID和唯一ID保存到容器中，建立一个计时器，超时后从容器中去除对象
- Map服务器A
  1. 从`EventSystem`中删除对象，保存对象原有唯一ID
  2. 通过转移的目标服务器设备ID获取目标服务器地址，并建立内网Session
  3. 通过内网Session，向目标服务器发送转移对象。
- Map服务器B
  1. 将收到的对象反序列化（注意这里反序列化会重新生成对象的唯一ID）
  2. 将对象加入`EventSystem`事件系统中
  3. 重新添加`MailBoxComponent`邮箱组件，来构建Actor模型
  4. 返回给服务器A新的唯一ID
- Map服务器A
  1. 将对象销毁
  2. 调用`LocationProxyComponent`位置代理组件的`UnLock`方法（传入上锁对象ID和新的唯一ID），建立与位置服务器链接的内网Session，并发送上锁消息`ObjectUnLockRequest`
- Location服务器
  1. 调用`LocationComponent`的`UnLockAndUpdate`方法
  2. 在UnLockAndUpdate方法中会将上锁对象的唯一ID进行更新
  3. 调用`UnLock`方法，从上锁容器中删除对象，然后将上锁期间收到堵塞的Location消息重新进行发送

## 总结

消息分类：

- S2C:

  - IMessage
  - IRequest

- C2S:

  - IMessage

  - IRequest

  - IActorLocationMessage

    前提是服务端对链接客户端的Session绑定的对象进行了ActorLocation绑定处理（挂载了邮箱组件，并注册到位置服务器中），使用Session按正常方式发送消息即可

  - IActorLocationRequest

- S2S:

  - IMessage

  - IRequest

  - IActorMessage

    前提是知道接收对象的唯一ID，且接收对象挂在了邮箱组件

    通过`ActorMessageSenderComponent`组件传入对象的唯一ID，建立`ActorMessageSender`，通过`ActorMessageSender`发送消息

    由于Actor是走内网组件所以只能在服务器内部传输使用，如果希望外网也能调用，需要自己手动扩展`OuterMessageDispatcher`

  - IActorRequest

  - IActorLocationMessage

    前提是接收对象有绑定邮箱组件，并在地址服务中进行了注册。

    使用`ActorLocationSenderComponent`组件传入对象Id，获取`ActorLocationSender`，通过`ActorLocationSender`发送消息

  - IActorLocationRequest

登录流程：

1. Client建立一个链接Realm的Session
2. Client向Realm发送登录请求，验证登录账成功后
3. Realm向Gate获取登录Gate的key和Gate的地址
4. Realm返回给Client登录Gate的key和Gate的地址
5. Client通过Gate地址建立一个新的Session，并销毁之前的Session
6. Client向Gate发送登录Gate请求，验证之前返回Key成功后在Gate创建Player
   1. 将Client地址保存到Player中，方便后续对Client建立Session
   2. 给当前Gate链接Client的Session添加邮箱组件，将Session注册成Actor
7. Gate返回给Client Player的ID，在客户端建立Player
8. Client向Gate发送登录Map请求
9. Gate向Map发送创建Unit请求
10. Map创建Unit，并返回UnitId
    1. 给Unit添加邮箱组件，注册成Actor
    2. 再调用邮箱组件的AddLocation方法，将Unit注册到Location服务器中
11. Gate返回给Client在Map返回的UnitId
12. Client通过UnitId建立Unit