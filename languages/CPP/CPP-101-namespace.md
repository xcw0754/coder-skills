# C++命名空间namespace

-----
### 普通命名空间
自定义一个命名空间通常是这样的
```
namespace myspace {
	Class A {...};	// 一般是类的定义和声明
    Class B {...};
    ...
}  // <--- 没有分号
```

- 命名空间可以嵌套。
- 每个命名空间为一个作用域，每个命名空间中的名字在该空间内唯一，在不同命名空间可以相同。
- 同个命名空间可以不连续，即A文件中定义myspace，B文件中也可以定义myspace，它们会被合并起来。

-----
### 内联命名空间

通常可以这样定义
```
>>> 文件 my_ns1.h
inline namespace ns1{
	...
}

>>> 文件 my_ns2.h
namespace ns2{
	...
}

>>> 文件 my_ns.h
namespace ns{
	#include "my_ns1.h"		// 将两个命名空间嵌在里边
    #include "my_ns2.h"
}
```

- 使用ns1中的名字只需要`ns::xxx`即可。
- 使用ns2中的名字需要`ns::ns2:xxx`，因为它不是内联的。



-----
### 未命名的命名空间（待续）

```
namespace {		// <--- 没有指定名字
	...
}
```
- 可以取代static，将所有的static变量丢里面，不同再声明为static了，效果一样。
- 可以关注一下如果在头文件中定义static实体会怎样。(每个引用此头文件的文件都会有各自的副本)



-----
### using声明和using指示(待续)

