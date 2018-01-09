# 工厂模式与抽象工厂模式

### 何谓工厂模式？
工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一，分为三种，提供了一种创建对象的最佳方式。
在工厂模式中，我们在创建对象时不会对客户端(广义)暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。


-----
### 一般可以用于什么地方？

> 举例：游戏服务端经常出现这样的需求，某个玩法需要统计玩家的得分、耗时、错题数等等。怎么构造数据以方便管理玩家数据(仅针对此玩法)？

可以用结构体来记录每个玩家的玩法数据以方便管理，在客户端传来数据之后记录玩家的各种数据，注意一下，数据并不是一次性由客户端传来的，而是陆陆续续，每次得1分就得记一次，每次错一题就记一次。得分接口和错题接口已经有两个接口了，我需要对它们进行实现，那么每个接口中写`new struct...`，然后往里填数据。并不是每次需要new，大部分都是在修改已有数据而已。

> 这样会有什么问题呢？

我写得不错，而且玩法顺利完成了。策划突然说需要改一下玩法规则，我们不需要错题了，我们将错题做成扣分项，于是，我对每个接口涉及到结构体初始化和错题等等的重新封装(也许有五六个接口)。但是我每次都得这样做么？策划老是改需求，于是工厂方法可以派上用场。将创建、修改玩法数据等逻辑抽出来，作为一个接口，这就方便多了，通常只需要改一个接口就完事。
此时抽出来的接口可以想象成工厂，每个需要调用此接口的接口可以想象成客户，所用结构体可以想象成产品。(工厂模式)



-----
### 工厂模式的分类及原理

工厂模式的细分可以是
- 简单工厂
- 工厂方法
- 抽象工厂

其实GOF提出的工厂模式就两类，即上面前两种归为一类统称称为工厂模式，第3种为一类称为抽象工厂模式。那就以两种来归类。

> 简单工厂

由3个要素组成：产品基类(1个)，产品具体类(n个)，工厂类(1个)。客户(不是我们要关心的点)
原理： 工厂类可以生产n种不同的产品。
优点： 角色非常明确，各干各的事。
缺点： 一个工厂生产各类产品有点繁琐啊，牙刷要产生，洗衣机也要生产，还得生产水果。不太靠谱。

> 工厂方法

由4个要素组成：产品基类(1个)，产品具体类(n个)，工厂基类(1个)，具体工厂类(n个)。
原理：工厂分类，每厂负责一个产品。
ps：此时多态就可以派上用场了。

> 抽象工厂

由4个要素组成：产品基类(k个)，产品具体类(n个)，工厂基类(1个)，具体工厂类(m个)。
原理：一个工厂可以产生1至多种产品。
具体：由于产品的分类关系比较复杂才产生这种模式，一个汽车配件厂生产CD机、空调、引擎等，而这三种东西明显是不同分类的，它们必然会继承不同的产品基类。但是这三种东西又可以定义为`汽车配件类`，由同一个工厂来产生比较合适。

> 无论什么猫，只要...就是好猫。我们只需要考虑如何建模最佳，管它什么厂！

来总结一下三种模式：
- 简单工厂只有1个工厂类。
- 工厂方法增加多个工厂类，一个工厂仅生产一种产品。
- 抽象工厂又增加多个产品类，每个工厂可生产多种产品。

-----
### 怎么实现？

不用过度考虑实现，重在建模，抽象。


------
### 参考文章

