# 虚继承和棱形继承

### 棱形继承
看如下继承关系
```
class A
{
public:	// 如果没有写public，编译器默认private!
    int a;
    void print(){cout<<a<<endl;}
};

class BB:public A	// 第二层
{
};

class CC:public A	// 第二层
{
};

class DDD:public BB,public CC	//第三层
{
};

int main()
{
    DDD a;
    a.BB::print();	//正确
    a.AA::print();	//正确
    a.print();		//报错，语义模糊。
    return 0;
}
```
这样的继承就是棱形继承方式，现在a里面有两份类A的拷贝，所以在调用`a.print()`时就模糊了，编译器不知道你想使用其中哪一个，你只需要明确知名就可以了。
但是存两份拷贝，真的必要吗？使用虚继承可以使得只保留一份拷贝，就是在第二层的继承时用virtual关键字，如下
```
class A
{
public:
    int a;
    void print(){cout<<a<<endl;}
};

class BB:virtual public A	// 第二层：virtual
{
};

class CC:virtual public A	// 第二层：virtual
{
};

class DDD:public BB,public CC	//第三层
{
};

int main()
{
    DDD a;
    a.BB::print();	//正确
    a.AA::print();	//正确
    a.print();		//正确
    return 0;
}
```
三者的输出结果是一样的，毕竟是同一份拷贝。






