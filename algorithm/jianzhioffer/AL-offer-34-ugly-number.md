# 丑数

### Q：一个数的质数因子只有2、3、5，则称为丑数，若第一个丑数为1，求第n个丑数。

此题略有难度，难在n的范围不知道，丑数的范围也不知道。因此只能用最佳的解法，空间时间代价均要最小。容易想到的是，若u为丑数，则u*2、u*3、u*5均是丑数，但是需要去重，因为若u=2，那么u*3=2*3=6，若u=3,那么u*2=3*2=6，这就重复了。而且，不能按{u*2，u*3，u*5}这样每次就增加3个丑数，处理起来很麻烦。

```
class Solution {
public:
    int GetUglyNumber_Solution(int index) {
        if(index<1) return 0;
        vector<int> vect(index, 1);
        int t2=0, t3=0, t5=0; // 作指针来用
        for(int i=1; i<index; i++) {
            vect[i] = min(vect[t2]*2, min(vect[t3]*3, vect[t5]*5));
            if(vect[i]==vect[t2]*2) ++t2;
            if(vect[i]==vect[t3]*3) ++t3; //这样可以去重，不要写成else if了
            if(vect[i]==vect[t5]*5) ++t5;
        }
        return vect[index-1];
    }
};
```


