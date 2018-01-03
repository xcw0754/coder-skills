# 文件操作相关命令

文件查找和比较
======

- strings

strings可以输出文件中可打印的字符序列(即部分ASCII字符)，默认至少4个字符组成的字符串。常同grep配合使用。
eg.在二进制文件中搜索一些关键字时就能用。` strings -a test | grep -i "gcc" # 查看可执行文件test的GCC版本 `




