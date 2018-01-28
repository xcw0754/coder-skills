# Redis发现细节


### 数据的底层存储结构

在Redis中，同一种数据结构的低层存储结构可能是不同的，比如两个平常所用的List，地层可能用的是ziplist存储方式，也可能是linkedlist存储方式。甚至某个LIst一开始是ziplist存储的，后来自动变成了linkedlist存储的，这是Redis的自动管理，挑选合适的存储方式以节省空间或时间。(注：Redis正在高速发展中，实现方式可能随时变化，以官网为准。详见`object`命令。)

多种方式编码：
- `String`可以被编码为 raw 或 int 或 embstr。
- `List`可以被编码为 ziplist 或 linkedlist。
- `Set`可以被编码为 intset 或者 hashtable 。
- `Hash`可以编码为 zipmap 或者 hashtable 。
- `Sorted set`可以编码为 ziplist 或者 skiplist 。


### 关于 String

先看如下的例子

```
SET msg 127
OBJECT ENCODING msg   # 输出："int"

SET msg "abc"
OBJECT ENCODING msg   # 输出："embstr"

SET msg "abcaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaccc"
OBJECT ENCODING msg   # 输出："raw"
```

- 当存储的字符串表示的是较短的整数时，采用`int`存储方式。
- 当存储的字符串是个短字符串时，采用`embstr`存储方式。
- 当存储的字符串超过一定长度时（如39字节），采用`raw`存储方式。

其实`embstr`和`raw`的实现是很接近的，用的都是同样的数据结构管理的，仅有略微的差别。

### 关于 List

linkedlist存储方式就是普通的链表。而ziplist存储方式就是将元素存放在连续的一块内存中，省去了每个元素的后继指针所占用的内存，优点就在这。不过要注意一点，考虑到修改值时的扩展的方便，并不是真的将连续的裸字符串放在一块，而是用Entry来代表一个元素，里面带有指针指向元素的实际内容，而表示该元素内容的仍是string对象。

### 关于 Set

hashtable也就是dict，只不过元素均只有key，没有value而已。而intset很简单，就是一个有序的整型数组，每次插入都需要重新申请一块内存，插入时可能会引起多个元素调整位置。intset和ziplist不一样，它存的是直接数组，它的定义如下
```
struct intset {
    uint32_t encoding;
    uint32_t length;
    int8_t contents[];   // 纯数组
};
```

### 关于 Hash

hashtable在Set中见过了。zipmap是将key-value对直接存放在一块连续的内存中以节省内存的方案，查找的复杂度为O(n)，修改也可能带来复杂度较高的开销。

假设有个map为 "foo" => "bar", "hello" => "world"。那么zipmap的格式如 
`<zmlen><len>"foo"<len><free>"bar"<len>"hello"<len><free>"world"`
其中 zmlen是当前zipmap的大小。每个key只有一个len表示key长度，每个value有一个len和free域，len表示value长度，假如修改过value，且改短了，那么free就用于记录空闲的长度，这是为了避免重新申请内存的开销。



### 关于 Sorted Set

ziplist已经在List中见过了，用有序的链表来作Sorted Set的存储方式在数量级很小的时候，也许更快更省内存。而Skip List是一种有名的特殊数据结构，详细请看论文。



-----
### 对象共享

Redis中可以共享的只有整型字符串对象，其他的都不能共享，主要是验证是否一致比较难，复杂度高，而整型比较好验证。共享是通过引用计数实现的，计数为0则回收。不要认为共享对象很牛，并不是处处皆可共享的，比如执行如下命令
```
SET A 1025
SET B 100
SET C 1025
```
这样C就会共享A的value是吗？不是的，不信你用命令`OBJECT REFCOUNT C`试试。
在执行`SET C 1025`时要解决的问题是：1025是否已经存在？如何找到它？答案是找不到的。那么共享对象这么鸡肋有什么用？比如对象的赋值，传参等等这些就能用得到，因为可以很方便的找到被共享的对象。










