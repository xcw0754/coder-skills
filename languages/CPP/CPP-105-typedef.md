#类型别名

### typedef
通常typedef可以将很长的变量名声明为一个较短的别名。
如`typedef long double ld `,这样ld就跟写long double一样了。
如`typedef BB_Customer_HU_YY BCHY`，用BCHY代替前面那一长串(可能是个类名)，解放双手。

-----
### 如果这么写就不易理解了

> 先学会区分以下3种写法表示什么意思。

- ` const char * p  `	常量指针。
- ` char const * p  `	同上。
- ` char * const p = 0 `	指针常量

常量指针是指向常量的指针，它不是常量，所以可以改变指向，如`p=1`再`p=2`。
指针常量是指针类型的常量，它是常量，声明时须初始化，即初始化后就不能改变其值，`p=2`会报错。
tips： 记忆`const`修饰在它之后最近的一个类型，如指针常量，就修饰了p，所以p是常量。如常量指针，修饰了`char *`，即修饰了p所指向的对象。

> 再来看别名定义后表示什么意思。

- ` typedef char * pc `		pc是char型指针的别名
- ` const pc p = 0 `	p是指针
- ` const pc * pp `	pp是指向指针的指针

#### Q：` const pc p = 0 `和` const char * p  `所定义的p是否一样？
> A：不一样。
> 前者是个指针常量，是常量。后者是个常量指针，非常量。根据常量必须初始化的原则，试试定义` const pc p`会不会报错就知道了。


#### Q： 使用预定义define和typedef一样吗？
> A：不一样。
> define在预编译期间就被展开了，只是简单替换。而typedef是在编译期间才处理的。
> 两者各有长处，如`#define PI 3.14`，换作typedef就做不到了，typedef只是用来定义变量的别名。而将define用于复杂的环境时需要谨慎，可适当加几层括号来保证其正确性。


#### Q：typedef和引用是一样的吗？
> A：不一样。
> typedef定义类型的别名，而引用是变量的别名。两者完全不同。PS：定义常量(`const`修饰)的引用会报错。

#### 看起来很奇怪的写法
> `typedef void(*p)(char *, int, double);`这就是定义了个函数指针类型的别名p，函数的参数有3个。用p定义函数指针类型就短得多了，只是看到的时候可能没有第一眼反应过来。





