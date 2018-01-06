# 构造函数可能造成的问题

### 构造函数在使用时需要小心
如下代码段有没有问题？
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

### 如何改正？
将第二个构造函数改成`A(A &other) {value = other.value;} `引用型的就可以了，没有产生拷贝的过程。

