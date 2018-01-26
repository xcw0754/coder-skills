# define和inline的区别


### 不同之处

> define

- 编译前的预处理
- 执行简单字符串替换工作


> inline

- 编译时处理，有普通函数的所有特点
- 建议编译器进行函数展开，只是建议，未必会展开，由编译器决定



-----
### 相同之处

- 两者是用于避免调用函数时带来的压栈消耗
- 若能展开，两者开销差别不会太大


-----
### define有什么坑？
以下两种定义方式
```
#define sqrt(A) A*A //第一种
#define sqrt(B) (B)*(B) //第二种
```
分别用这两种来计算看看效果
```
int i=7;
sqrt(i); // 7*7
sqrt(i+1); //7+1*7+1
sqrt(--i); //--i*--i
```
define只会在预处理时作简单替换，上面这些就是基本的坑。上面的注释为第一种写法的替换结果，式子写出来计算结果明显不如预期。

再来看看这种写法
```
#define INTP int*
INTP a, b, c;
```
三个变量它们并不都是int*类型的，只有a才是，其他两个都是int而已。这种功能应该使用typedef。


-----
### inline有什么坑？

- 关键字必须加在函数定义处，而不能仅加在函数明处。
- 编译器有时会展开有时不会展开，有时在不加inline时也会展开(和优化有关，如-O2选项)。
- 调试时你加在内联函数内的断点可能被忽略，即调试不了。



-----
### define的高级用法

除了执行简单的替换之外，define还能有一些稍微高级的功能。
1. `#`符号。
假如有如下代码
```
#define SUM(a,b) printf(#a" + "#b" = %d\n", (a) + (b))
void main () {
    SUM(2, 3);
    int k = 1, y = 5;
	SUM(k, y);
}
```
对其进行预处理，即gcc/g++的`-E`选项，处理结果如下
```
void main (){
    printf("2"" + ""3"" = %d\n", (2) + (3));
    int k = 1, y = 5;
    printf("k"" + ""y"" = %d\n", (k) + (y));
}
```
就是将形参a和b对应的实参的变量名(若有名)变成一个用双引号包起来的字符串。注意上面末行的printf的写法等同于`printf("k + y = %d\n", k + y);`。

2. `##`符号
假如有如下的代码
```
#define GETNUM(n) x##n //将x与n连起来
void main() {
    int GETNUM(9) = 2;
    printf("输出: %d", GETNUM(9));
    int y = 0;
    GETNUM(y);
}
```
对其进行预处理，即gcc/g++的`-E`选项，处理结果如下
```
void main() {
    int x9 = 2;
    printf("输出: %d", x9);
    int y = 0;
    int xy;
}
```
看起来和`#`很相似，但是`#n`处理后的结果是字符串`"y"`，而`##n`取到的是个变量名`y`。
下面这些写法都达不到效果。
- `#define GETNUM(n) xn`，因为替换的时候压根不知道`xn`中的n是跟着x还是要取参数n的什么。
- `#define GETNUM(n) x n`，因为会有个空格在x和n之间，空格会留下来。
- `#define GETNUM(n) x(n)`，因为括号也会留下来。

-----
### 条件编译

顺便扯一点条件编译相关的用法。

1. `#undef xxx	`可取消宏定义。
```
#ifdef abc
#undef abc
#define abcc
#endif
```
2. `#ifdef ... #else ... #endif`等条件编译
```
#ifdef TEN
int a = 10;
#elif TWO
int a = 2;
#else
int a = 0;
#endif
```
3. `#ifndef xxx`表示:如果没有定义xxx则编译下面内容。常搭配`#define xxx`在其后。
```
#ifndef yyy //如果没有定义yyy
#define yyy //表示定义yyy，没有替换
#endif
```
4. `#if defined(xxx) && (1!=2)` 这种写法也容易理解，还能并列写多个表达式，而defined跟函数的使用方式一样。





