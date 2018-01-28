# 循环新写法 for-range loop

C++11支持了for-range loop，类似于使用python的`for a in range(9)`，就是写法上的控制起来比较简单，有一定的用处，比如我要遍历一个vector，就可以写`for(int x : vector<int>(10, 1))`，当然这个例子没有什么实际意义。

如果标准一点的写法，应该是使用迭代器：`for(std::vector<int>::iterator it=vec.begin(); it!=vec.end(); it++)`，这种写法巨长。

### 各种写法


例子1：
```
// 没有修改容器
std::vector<int> vec {1,2,3,4};
for(auto n :vec) n++;
for(auto n :vec) cout<<n<<endl;  //输出仍为: 1 2 3 4
```
例子2：map的循环，`C++17`写法，普通编译器就别忙活了
```
// 会修改容器
map<int,int> mymap {{1,2}, {3,4}};
for (auto & [first,second] : mymap) {
	cout<<first <<":" <<second <<endl;
}
```
例子3：map的循环，`C++11`写法
```
// 不修改容器
map<int,int> mapp {{1,2}, {3,4}};
for (auto kv : mapp) {
    cout<<kv.first <<kv.second <<endl;  //kv是pair类型
}
```
例子4：
```
// 函数GetMyVector()只会调用一次
vector GetMyVector();  //函数声明
for(auto n: GetMyVector()) cout<<n<<endl;
```

有些容器如map、set，它们本身的特性决定了是否可以修改容器元素，它们的低层实现是红黑树，如果能这样简单修改，内部就乱了。比如这样写`for(auto &kv: mymap) kv.first = 5;`，kv是引用，想修改原来的key，这会报错；而`for(auto &kv: mapp) kv.second = 5;`就能修改到value，是正确的写法。

**小心坑！**`vector<bool>`是个很特殊的东西，如果用例子1的写法则会改变容器中元素的值，可以参考[[Brief Talk] auto, auto&, const auto&以及其它形式的auto变种在for-range loop的选择](https://zhuanlan.zhihu.com/p/25148592)

