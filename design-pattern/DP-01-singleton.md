# 单例模式

### 何谓单例模式？
“保证一个类仅有一个实例，并提供一个访问它的全局访问点。”  ---- 百度百科
有时我们的程序需要有这样一个实例，它全局唯一，一旦创建后就会一直存在，通常直至程序结束时灭亡，过程中不允许重新创建或者销毁。至于什么时候创建，视需求而定，创建过程可以推迟到首次使用时，也可以在程序启动时就创建。
换句话说，单例模式强调的是：
- 该类只能有一个实例；
- 它必须自行创建这个实例；
- 它必须自行向整个系统提供这个实例

-----
### 一般可以用于什么地方？

资源管理，统筹分配等。如
- 日志。日志模块可以设计为单例模式，方便管理各种log文件。
- 线程池。分配空闲线程用单例模式也是很适合的。
- 资源管理器。


-----
### 用C++怎么实现？

```
/* 懒汉式 Lazy Initialization */
class S 
{ 
public:
    static S& getInstance()
    {       
        static S instance;
        return instance;
    }
private:
    S() {} 	//构造函数
    S(const S &);	//拷贝构造函数
    void operator=(S const &);	//重载赋值运算符。没有写错！
};
```
注意点：
1. 构造函数，拷贝构造函数都声明为私有，这样就创建多余的S实例。
2. getInstance()接口被声明为static，因此无需实例化即可调用它，如` S &a = S::getInstance() `。
3. 通过getInstance()得到的S实例均是同一个，因为instance被声明为static了。
4. 拷贝构造函数和重载赋值运算符这两个函数仅声明，不要实现！
5. 这种写法在C++11标准中是线程安全的(GCC4.3支持)，在之前的标准中就不一定了,自己加锁去！


------
### 参考文章

- [C++ Singleton design pattern](https://stackoverflow.com/questions/1008019)
- [Is Meyers' implementation of the Singleton pattern thread safe?](https://stackoverflow.com/questions/1661529)
- [Can any one provide me a sample of Singleton in c++?](https://stackoverflow.com/questions/270947/can-any-one-provide-me-a-sample-of-singleton-in-c)
- 这几篇文章中提及的其他链接都可以作为参考。

