---
title: AOI
date: 2021-12-23 11:08:09
author: Heart
categories: Engine
tags:
  - Unity
  - 花里胡哨
index_img: https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/image-20211206092201838.png
excerpt: AOI 地图消息同步

---

## AOI

#### 概念-广播的类型

广播范围

1. 全服广播 特点：每一个玩家都能接收，频率不会太高，15帧左右
2. 地图广播 特点：频率越高越好 ，玩家同步，地图同步类型 实时同步给身边的人
3. 社交关系 特点：频率不会特别高，根据社交定
4. 交互目标 特点：和别人进行的一对一的，点对点的，PVP的 频率低
5. 玩家自身 特点：严格不能算广播，客户端与服务器的广播。大部分行为只需要自身知道的



##### 地图消息同步

![image-20211206092201838](https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/image-20211206092201838.png)

将上面比作一个地图，点比作玩家，每一个点动了，应该把他的行为同步给身边的玩家（100X100 1s同步10次 1s=10000次消息处理 ~3.mb/s=24mb/s ），频率过高，性能越差

而不是所有玩家范围都需要接受到你的的同步，只针对一个兴趣范围

- 建立兴趣范围清单
- 只对兴趣兴趣范围内的目标广播
- 极大的降低消息处理压力和网络负载

##### AOI的设计-设计原则

1. 分析核心需求：减少压力消耗 降低带宽 提高负载
2. 明确设计目标 对象 数据 算法
3. 不为设计而设计 优化思想 忘记技术

##### 设计目标

1. 兴趣范围规划方案
2. 对象与数据结构
3. 高性能算法

##### 场景分析与方案设计 (一)

###### 

###### 级别设定

![image-20211206210243018](https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/image-20211206210243018.png)

```c#
void OnEntityMove(who){
    foreach (var entity in entities)
    {
        if (who==entity)continue;
        bool nowinAOI=who.Distance(entity)<who.AOIRange;
        bool alreadyInAOI=who.AOI.contains(entity);
        if (alreadyInAOI&&!nowinAOI)
        {
            who.OnLeaveAOI(entity);
            entity.OnLeaveAOI(who);
        }  
        if (!alreadyInAOI&&nowinAOI)
        {
            who.onEnterAOI(entity);
            entity.onEnterAOI(who);
        }
     }
}

```

优点

- 不需要特殊的数据结构
- 易于实现

缺点

- 计算成本较高（1+n）*n/2
- 1000人=500500次

改善方案

- 多线程 并行计算，提升计算效率
- 延迟计算 减少计算间隔
- 分批计算 100/Frame

##### 场景分析与方案设计 (二)

区域划分

![image-20211206212501192](https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/image-20211206212501192.png)

将区域划分为一个一个格子 如8X8



代码

```c#
void OnEntityMove(who){   
	int new_x=(int)(who.position.x/size);
    int new_y=(int)(who.position.y/size);
    if(new_x!=who.grid_X||new_y!=who.grid_y){
        who.LeaveGrid(who.grid_x,who.grid_y);
        who.EnterGrid(new_x,new_y);
        this.grid_x=new_x;
        this.grid_y=new_y;
    }
}
```

优点

- 计算速度快

缺点

- 需要额外数据结构存储格子信息
- 需要额外的格子管理逻辑
- 实现复杂度高
- **格子边界问题**

###### 场景分析与方案设计 (三)

基于方案二做优化

![image-20211206213228669](https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/image-20211206213228669.png)

九宫格，一个格子一个格子广播



进一步的优化思考

算法优化

- 优化数据结构
- 降低运算消耗

ECS架构

- 采用面向数据概念优化架构，提升运算性能

并行运算与GPU加速

- 采用并行计算提升性能
- 采用GPU加速减少CPU消耗

##### 优化性价比曲线

![image-20211206213733619](https://cdn.jsdelivr.net/gh/ShyHeart/Photo/photo/image-20211206213733619.png)

###### 优化方案参考

背景

由于正在开发的游戏涉及到10万个移动角色，如果单服10000玩家的话，采用双向循环查找，那就是10亿的量级，太恐怖了，不得不对算法做优化

场景：1000*1000的地图，10000客户端角色两个角色间的距离是10时，有效

优化前

随机生成10000角色的位置信息，然后计算那些角色的信息需要发给范围的客户端。原始：使用最简单的双向循环查找算法，两重for循环，每找到一个时数量+1结果如下，找到40030个有效值，耗时880ms

优化一

位置是双向的，也就是A在B的范围内，B在A的范围内，因此只需要循环N*（N-1）/2次，优化结果耗时**416ms**

优化二

由于大部分的角色位置相距较远，因此对地图进行分区，以100为单位，整个地图被分成100个区域，创建区域数组Player[100][]，然后计算每个客户端的更新范围所在的区域，并将客户端加入到区域中，注意客户端的四个顶点可能在不同的区域上，此时在几个区域就要加入几个区域。最后计算角色所在区域，并和区域内的Player计算距离。此算法得到的结果是**22.5ms**

优化三

区域大小用2AN来表示，从而在计算角色所在
区域时可以用移位来处理，使用64作为区域大
小，优化后平均耗时是：**15.5ms**

优化四

从算法的耗时来看，区域小一些，则区域数量
变多，但每个区域内的角色数量就少了，需要
计算的量也会变少，使用32作为区域大小后，
耗时为：**9.5ms**

优化五

如果地图变大一些，角色更加稀疏，则计算量
会更少，使用10000*10000的地图后， 同样64
大小的区域，耗时是：**1.8ms**

其他案例

http://cppblog.com/jaxe/archive/2011/06/20/148998.html