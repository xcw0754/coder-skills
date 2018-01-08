# 智能指针shared_ptr、unique_ptr、weak_ptr


### 简介
三者均在头文件memory中，智能的地方在于它会在适当的时候释放指针所指向的内存，其实就是用了个引用计数，当计数为0时就会释放。线程安全。
而只有深入了解"智能"的原理，才能用好智能指针，否则下场有点不明不白。

-----
### shared_ptr类
`shared_prt`类(它是个模板类，这里简单处理)近似理解如下
```
class shared_ptr{
	...
private:
    element_type *px;	//指针
    shared_count pn; 	//引用计数
}
```
也就是带了引用计数和一个指针，封装了一下而已。使用智能指针有个好处，就是发生异常时都会自动释放内存，这在普通的指针是需要捕获异常的，所以方便了很多。而且，怕智能指针在清理内存时做得不够好，可以自己指定一个删除器来善后(想用智能数组就必须指定，单个变量可使用默认的)。
看看有哪些需要注意的地方。

`shared_ptr`类实现了一些成员函数(不包括非成员的)如下
```
bool	// 操作符
*		// 操作符
->		// 操作符
=		// 操作符，只是用来智能指针之间的赋值。其他初始化形式应用构造函数。
get()
owner_before()
reset()
swap()
unique()
use_count()
```

### get()的使用需谨慎

```
int* p = new int (10);
std::shared_ptr<int> a(p); //这样初始化可以认为p就不能再用了，否则易出问题。
```
a是个对象，上面的初始化方式相当于`a.px=p`，然后引用`a.pn=1`。问题来了。
```
delete p;
cout<<*a<<endl; 	// 运行时错误，因为a.px指向的内存已被释放，故越界错。
cout<<a.use_count()<<endl;	// 输出1
```
```
int *b = a.get();
delete b;
cout<<a.use_count()<<endl;	//输出1
cout<<*a<<endl;	// 运行时错误，因为又偷偷释放了a.px指向的内存，再访问就越界。
```
还有一个小坑
```
int* p = new int (10);
func(shared_ptr<int>(p));	//作为参数传给函数
cout<<*p<<endl;		// 运行时错误，p指向的内存已经被释放了！上面有提到别用p了。
```

综上，使用get()时应小心别去释放掉shared_ptr所指向的内存，这种粗活让它自己做就行了。要掌握好智能指针的释放时机，它在没有其他智能指针引用的时候自动释放内存，普通指针不是归它管的，它无能为力。


### use_count()
```
int* p = new int (10);
std::shared_ptr<int> a(p);
std::shared_ptr<int> c = a;	// 赋值符＝。
cout<<a.use_count()<<endl;	//输出２
cout<<c.use_count()<<endl;	//输出２，正常使用都是能正确维护pn计数的。
```




-----
`new`有个小细节很有趣。
```
int n = 0;
int *p = new int[n];
```
这里n=0，new一个空的数组，仍然会返回一个地址给p，但是这个地址里是没有元素的，这个地址的用途没多打。但是每次new一个空数组都能返回一个不同的地址，这点就很神奇了。

-----
### unique_ptr
提供独享所有权的智能指针，当unique_ptr被销毁时，它指向的对象就被释放，不能直接拷贝或赋值。

-----
### weak_ptr
是一种智能指针，指向由shared_ptr管理的对象，而shared_ptr的use_count()不会将weak_ptr所引用的次数算在内。










