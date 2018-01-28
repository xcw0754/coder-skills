# POD、Trival、Standard Layout

先大致来了解它们：
- **POD**: Plain old data
- **trivial**: 平凡的、
- **standard layout**: 标准布局的

POD是`C++11`标准之前的概念，在`C++11`已几乎弃用这个概念了，发展成相关的概念：trivial和standard layout。其实`trivial`+`standard layout`=`POD`，同时满足这两个新概念也就是POD了。既然现在已经没有POD概念了，那就了解两个新的概念就好了。可参考[C++ concepts: TrivialType](http://en.cppreference.com/w/cpp/concept/TrivialType) 和 [C++ concepts: StandardLayoutType](http://en.cppreference.com/w/cpp/concept/StandardLayoutType)。

在`C++11`中有相应的函数可以判断是否为pod、trivial、standard layout类型，如下：

```
std::is_pod()
std:：is_trivial()
std:：is_standard_layout()
```
3个函数的用法一样的，举个例子，is_pod的用法如下
```
struct A { int i; };            // C-struct (POD)
class B : public A {};          // still POD (no data members added)
struct C : B { void fn(){} };   // still POD (member function)
struct D : C { D(){} };         // no POD (custom default constructor)

int main() {
  cout << "int: " << std::is_pod<int>::value << std::endl;
  cout << "A: " << std::is_pod<A>::value << std::endl;
  cout << "B: " << std::is_pod<B>::value << std::endl;
  cout << "C: " << std::is_pod<C>::value << std::endl;
  cout << "D: " << std::is_pod<D>::value << std::endl;
  return 0;
}
```


-----
### trivial 和 standard layout 有什么用？

1) **trivial** 的作用有：
- **支持静态初始化**。
```
class MyClass { //定义一个类
public:
    int i;
    int j;
}
MyClass good{1, 2};  //静态初始化，这种写法比较在C中比较常见。
```
- **支持安全拷贝**。可以将对象内存直接拷到字符数组(字符流亦可)中，再拷到另一个同类型的对象内存上也能实现安全复制。举个例子，某缓存系统中有些对象需要可持久化到磁盘上，然后关机，过一段时间再恢复出来，`trivial`类型能保证安全恢复。由于有这样的特性，用memcpy()等函数来操作对象的内存就是安全的。如果类中定义了虚函数，那就不能这样持久化。


2) **standard layout** 的作用有：
- **在C和CPP中有相同的内存布局**。如果一个对象在C和CPP中有相同的内存布局，它就能兼容两者，C就能方便得使用CPP中定义的对象了，这在多语言程序中有用。




-----
### trivial 和 standard layout 类型需要满足什么要求？

**trivial** 的要求([C++11的标准](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2011/n3242.pdf))有：

- has no non-trivial copy constructors (12.8),
即：拷贝构造函数为trivial型。
- has no non-trivial move constructors (12.8),
即：移动构造函数为trivial型。
- has no non-trivial copy assignment operators (13.5.3, 12.8),
即：拷贝赋值符为trivial型。
- has no non-trivial move assignment operators (13.5.3, 12.8), and
即：移动赋值符为trivial型。
- has a trivial destructor (12.4).
即：析构函数为trivial型。

```
// 以下这些类都是非trivial型
class A { A(){} }; //构造函数
class B { B(B&){} }; //拷贝构造函数
class C { C(C&&){} }; //移动构造函数
class D { D operator=(D&){} }; //赋值运算符
class E { E operator=(E&&){} }; //移动赋值符
class F { ~F(){} }; //析构函数
class G { virtual void foo() = 0; }; //有纯虚函数
class H : G {}; //其基类有虚函数
// 以下这些是trivial型
class I {};
class J {J()=default;};
```
看完这些例子，感觉只要全是编译器生成的构造赋值类型的函数，简单的内部结构就是trivial的。


**standard layout** 的要求有：

- has no non-static data members of type non-standard-layout class (or array of such types) or reference,
无静态数据成员。
- has no virtual functions (10.3) and no virtual base classes (10.1),
无虚函数和虚基类。
- has the same access control (Clause 11) for all non-static data members,
非静态数据成员的访问控制是一致的，比如数据全是public。
- has no non-standard-layout base classes,
若有基类则基类必须是标准布局的。
- either has no non-static data members in the most derived class and at most one base class with non-static data members, or has no base classes with non-static data members, and
(巨难翻译，看英文就好，either...or...结构，就是说只要满足其一即可)
- has no base classes of the same type as the first non-static data member
首个数据成员不能是基类对象。


------
### 参考

POD的相关概念可参考 [What are Aggregates and PODs and how/why are they special?](https://stackoverflow.com/questions/4178175/what-are-aggregates-and-pods-and-how-why-are-they-special)，里面还有另一个概念`Aggregate`。


