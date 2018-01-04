# C++ 运算符重载

## Q：如下为类型CMyString的声明，请为该类添加赋值运算符函数。

```
class MyString
{ 
public:
    MyString(char *p = NULL);
    MyString(const MyString &str);
    ~MyString(void);
private:
    char *cp;
};
```

## 考察点

- 返回值
- 传入参数的类型
- 内存释放
- 传入参数的判断

## 解答

先声明该函数，再考虑实现。
```
/* 声明后的类 */
class MyString
{ 
public:
    MyString(char *p = NULL);
    MyString(const MyString &str);
    ~MyString(void);
    MyString& operator=(const MyString &str);  // 声明在这
private:
    char *cp;
};
```

-----
实现方法一：
```
MyString& MyString::operator=(const MyString &str)
{
    if(&str==this)   return *this;  // 先判断是否相同
    delete []cp;
    cp = NULL;	// 有可能申请不到内存，这里最好置NULL
    cp = new char[strlen(str.cp)+1];
    strcpy(tmp, str.cp);
    return *this;
}
```
> 
- this是当前对象的指针，所以*this才是当前对象。
- delete一个NULL的指针没事的。

-----
实现方法二：
```
MyString& MyString::operator=(const MyString &str)
{
    if(&str==this)   return *this;
    MyString tmp(str);	// 充分利用构造函数
    swap(cp, tmp.cp);	// 把两个指针交换
    return *this;
}


```
> 
- 在方法一中，若内存不足，当前的cp所指内存又被释放掉了，那就赋值不成还丢了自己的数据，有点尴尬。法二刚好避免了这一点，把问题推给构造函数去完成。
- 释放当前cp指向的内存块的工作也交给了临时对象tmp去析构掉。


















