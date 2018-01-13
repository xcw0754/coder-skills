# 构造函数可能造成的问题


### 先看看如下代码段有没有问题？
```
class A
{
    private:
        int value;
    public:
        A(int n){value = n;}	// 关注这两个构造函数
        A(A other) {value = other.value;} 
}

int main()
{
    A a = 10;
    B b = a;	// 重点关注这里
    return 0;
}
```

咋一看好像没有问题，其实有大问题。
> 先看b是如何初始化的：用a初始化b，此时走的是第二个构造函数，将a传进去参数other，这里！出问题了。a变成形参other，是需要调用第二个构造函数来赋值的，然后循环反复...就死循环了。

如何改正？

将第二个构造函数改成`A(A &other) {value = other.value;} `引用型的就可以了，没有产生拷贝的过程。



### 再来看看如下的类设计得怎样
```
class A
{
public:
    A() {
        b = new B;
        ....	//代码块1
    }
    ~A() {
        ....	//代码块2
        delete b;
    }
private:
    B *b;
}
```
> Q：如果代码块1发生异常了，会怎样？

A的析构函数不会被调用，导致b不能正确释放，从而内存泄漏。可以考虑在代码块1进行catch，但是可能有些麻烦，可以考虑C++11的智能指针，退出作用域时自动释放内存，将体力活交给编译器去做。

> 如果代码块2发生异常呢？

A的析构函数末行不会被执行，导致b又不能正确释放，从而内存泄漏。还会产生的一个问题是，如果抛异常了，可能会引发无限循环，后面举个例子说明(★)。


PS：C++默认析构函数是noexcept(true)的，也就是异常不会向该函数外抛，如果在函数体内未得到处理，则会调用std::terminate()。但是这个可以通过在析构函数末尾加上noexcept(false)来去掉这个默认操作，这样异常就能走出函数外。具体看例子：

```
class A {
public:
    ~A() noexcept(true) {throw 1;}	// 不会抛异常到函数之外
}
void test() { A a;}
void main() {
    test();	//触发terminate()，异常在析构函数内未得到处理
}
```
将main改一下：
```
class A {
public:
    ~A() noexcept(false) {throw 1;}	// 正常throw
}
void test() { A a;}
void main() {
    try{
        test();
    } catch(int e) {} //这样才能catch到
}
```

★上面要举的例子在这：
```
class A {
public:
    ~A() noexcept(false) {throw 1;}
}
void test() { A a, b;}  //这次用了两个变量
void main() {
    try{
        test();
    } catch(int e) {}
}
```
这个程序是崩溃的，提示是`terminate called after throwing an instance of 'int'`。可是明明写了catch的不是吗，而且上个例子就捕获得到，这里怎么就捕获不到了？在抛出一个异常后，栈需要回退，此时需要析构函数里的其他对象，而就在析构其他对象时又抛异常，这样的话一个异常没处理完成又抛出一个异常。(TODO 具体什么原因导致catch不到异常，待了解)

综上，类的构造函数和析构函数在发生异常时不应抛出异常，不过即使明显写throw了，编译器也不会报错。但还是建议构造函数中不抛异常或少抛，而析构函数中不应该抛异常！


-----
### 构造函数的过程

在有继承关系时，派生类对象的初始化总是先调用基类的构造函数，再依次调用每个派生类的构造函数。析构的时候是个逆过程，即先调用派生类的析构函数，再调用基类的析构函数。




