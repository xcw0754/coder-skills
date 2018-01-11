# Linux内存布局

### 初识

以32位系统为例，经典版的Linux的虚拟内存布局划分为两部分：
- 0~3GB，用户区，也就是用户进程的大小。
- 3~4GB，系统区，也就是不让你用的部分。


具体来看这张图是怎么划分的

![image](https://github.com/xcw0754/coder-skills/blob/master/pics/OS_01_001_linux_memory.png)

- kernel space
- stack
- mapping segment
- heap
- BSS segment
- DATA segment
- TEXT segment(ELF)。

用户区简单来看就是从下往上分别是`代码段、数据段、堆、栈`，这4个段，堆从下往上，栈从上往下。代码段和数据段再继续细分下去还有很多小部分。堆和栈中间还夹着一个映射段mapping segment，动态链接库啥的就在这。为了安全，各个段之间可能会有一些间隔，这些是不用的，防止有心人轻易推测出内存布局？


