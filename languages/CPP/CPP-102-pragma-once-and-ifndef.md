#pragma once 和 #ifndef

-----
### 用途
> 这两者都是用于解决重复包含的问题，经常出现在大量的头文件互相包含，结果造成在foo.cpp中随手include一个头文件就可能会多次包含某个头文件而报错。

-----
### 区别
- `#pragma one`比`#ifdef`出现得晚，故较为少见，且部分编译器会不支持，根据维基百科来看已经有很多编译器支持了，包括GCC，微软的编译器等等。因此现在基本可以用`#pragma one`来替代以前的`#ifdef`。
- 如果写过`#ifdef`就知道它的麻烦之处，要自己定义宏，还要避免命名冲突。我们只是要防止重复包含，为什么要做这么多重复的体力活？不如交给编译器去完成。
- 当然，你像两者都用上，没问题的，只要你了解这样其实和只用`#ifdef`没啥区别。