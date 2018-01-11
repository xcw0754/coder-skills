# 类型说明符auto和decltype


### auto
`auto`关键字是C++11特性，用于自动推断类型的定义，这一工作由编译器完成。简单理解就是编译器自动帮你推断出适当的类型，并用该类型替换掉auto。常用法如下
```
int b, &e = b;
double c;
auto a = b + c;
auto d = 137 / 2;
auto r = e;		// 小心r就是个int，不是int&
const auto &j = 42;	//正确的！
auto &q = 41;		// 错误的！
```
- 如果多个变量需要同时定义，那么应保证类型是一致的。
- auto的推断也没有那么强大，引用&、指针*、常量const等这些它感知不到，需要显式指明，如`const int a = e`

-----
### decltype
`decltype`关键字同auto一样也是用于类型的声明，格式为`decltype(x)`，这样就能自动推出x的类型。这里的x可以是个表达式，变量名，常量。常用法如下
```
const int i = 0, &j = i;
int *p = &i, a=11;
decltype(i) x = 0;	// 类型是const int
decltype(j) y = x;	// 类型是const int&
decltype(i+5) z = 0;
decltype(*p) w;		// 类型是int&，特别小心这里。
decltype((a)) bb;	// bb是int&，须初始化
decltype(a) cc;		// cc是int
```


与auto不同的是：
- decltype可以感知`const`和`&`
- x还可以是个函数的返回值，如`decltype(func())`，编译器只做类型推断，而不会真的去执行func
- 如果x是普通的变量(非引用型)，那么类型就是引用，如上面末尾的两个变量的定义








