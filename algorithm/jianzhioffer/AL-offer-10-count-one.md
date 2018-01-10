# 二进制中1的个数

### Q：输入一个正整数n，问n转成二进制后其中包含1的个数是多少？

不考虑非法输入情况下，可这样写
```
int CountOne(int n)
{
    int cnt = 0;
    while(n) 
    {
        cnt++;
        n &= n - 1;	//关键
    }
    return cnt;
}
```

如果n可能为负数，那么可以逐位来检查n，32次即可检查完毕。注意不要用`n>>1`，这样达不到目的。