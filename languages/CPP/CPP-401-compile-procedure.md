# 编译流程

从源文件(*.cpp)到可执行文件(机器码)的中间环节一般有
- 预编译
- 编译
- 汇编
- 链接

### 预编译CPP
主要处理以`#`开头的预编译指令（除了`#pragma`)，删除注释，加行号。处理指令如下
```
gcc -E file.c -o file.i	// 方法一
cpp file.c > file.i		// 方法二。二选一即可。
```

### 编译
最关键的一个过程，将预处理后的文件加工成汇编代码文件。
```
gcc -S file.i -o file.s		// 情况一：仅接上一步
gcc -S file.c -o file.s		// 情况二：跳过预编译，直接编译
cc1plus file.c		// 情况三：用编译器cc1plus直接编译（我的机子没有cc1plus）
```

### 汇编
汇编成机器码，生成`.o`文件。
```
gcc -c file.s -o file.o	//情况一
as file.s -o file.o		//情况二，效果同上
gcc -c file.c -o file.o	//情况三：一步到位
```

### 链接(静态)
链接过程主要有地址和空间分配、符号决议、重定位等。
静态链接完毕之后啥代码都集于一个文件，这个可执行文件不依赖于任何库了，即使你的电脑没有库文件，也可以直接执行，只是不能换平台执行而已。但是代码冗长了很多，printf使用一次就多一份printf代码在里面，因为推荐用动态链接，代码无需在可执行文件中，但需要依赖链接库。

```
ld -static ...	// 链接一个简单的文件都要很长！省了。
```


### 装入
其实还有个装入的过程，即装入内存。想运行可执行文件就得装入。






