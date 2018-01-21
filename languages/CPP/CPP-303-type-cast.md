# 类型转换cast

附上一个站点 [点我直达](http://www.cplusplus.com/doc/tutorial/typecasting/)

C++11提供了4种一元操作符，分别是const_cast、static_cast、dynamic_cast、reinpreter_cast。

为什么不用C的类型转换风格，而要用CPP的风格呢？C的类型转换有强转、隐转两种。强转是指显式转换，隐转是指表达式引起的隐式转换，它们都没有安全性检查，更容易出错，比如C的指针类型可以任意地转。而CPP提供的cast则会执行更加严格的检查。



### const_cast

`const_cast`用于将const型的指针转成非const的指针，这种功能C中是没有提供的。。看看如下的例子
```
void print(int *p)
{
    cout<<*p<<endl;
    *p = 2; // 正确，会改变num的值。是否不安全？
}

int main()
{
    int num = 1235;
    const int *a = &num;
    print(a); //错误。(const int *) => (int *) 会报错
    print(const_cast<int*>(a)); //正确
    *a = 1; //错误。const限制
    return 0;
}
```
为了不让指针a修改num，所以加上了const限制a，但是有了const_cast就可以破坏这个规则了，将a转成`int*`后就可以修改num的值。


### static_cast

`static_cast`的用法应该挺多的，等着去探索，比如

- 空指针转普通指针，如(void*) => (int*)
- 亲缘类的指针转换。比如父类指针转成派生类指针，或者相反。安全性由用户保证，比如派生类指针指向一个父类对象，就会不完整，解引用时会运行报错。
- 普通类型的转换。
- 非const转const。
- ...


例子1
```
int num = 1235;
cout<<static_cast<char>(num)<<endl; //错误
num = 65;
cout<<static_cast<char>(num)<<endl; //正确 
 
```
这代码能编译能运行，但是第一个输出没有意义，这需要用户来保证。平时int转char都是直接转的，如`int a = 'b'`。

例子2
```
class Base {};
class Derived: public Base {};
Base * a = new Base;
Derived * b = static_cast<Derived*>(a);  // 经典用法。以前用的是强转。
```
这种用法一般不反过来用，除非你知道你在干什么，

例子3
```
int j = 41;
int v = 4;
float m = j/v;
float d = static_cast<float>(j)/v;
cout << "m = " << m << endl;
cout << "d = " << d << endl;
```
在C中，如果要得到d这样的效果，一般可以写成表达式`float d = 1.0 * j / v;`。


### dynamic_cast

`dynamic_cast`的用途比较少，用于类指针、类引用、空指针等的转换。是运行时才处理的。

```
class Base { virtual void dummy() {} };
class Derived: public Base { int a; };

int main () {
    try {
        Base * a = new Derived;
        Base * b = new Base;
        Derived * pd = dynamic_cast<Derived*>(a); //派生类指针指向派生类
        if (pd==0) cout << "Null pointer on first type-cast.\n";

        pd = dynamic_cast<Derived*>(b); //派生类指针指向基类
        if (pd==0) cout << "Null pointer on second type-cast.\n";

    } catch (exception& e) {cout << "Exception: " << e.what();}
    return 0;
}
```
输出结果为`Null pointer on second type-cast.`，因为转换失败，故代码`pd = dynamic_cast<Derived*>(b);`相当于`pd = NULL;`，这比运行时错误要好得多了。但是如果是类引用，则会抛出异常。

static_cast和dynamic_cast的区别主要是：static_cast在转换时没有安全性检查，而dynamic有安全性检查，失败时会转成NULL。static_cast主要用于值的转换，dynamic_cast能用于基类向派生类的转换。



### reinpreter_cast

`reinpreter_cast`可以将任何指针类型转成其他指针类型，非亲缘类指针都能转，自由度很高。其实就是简单的二进制值的拷贝，将地址给拷过来了。所以指针与整型互转也可以。也因为几乎什么指针类型都能转，出错的风险也增加了。

```
A * a = new A;
B * b = reinterpret_cast<B*>(a);
```



