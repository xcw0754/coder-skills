# 数组中只出现一次的数字

### Q：一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

如果数组里只有一个数字是出现1次的，那就简单多了，求异或和就是了。而本题也这样求异或和的话，两个要求的数字会缠在一块，得想办法分出来。利用异或的特点，不同的位异或结果为1，那就可以根据这一点将数组分成两个子数组，两个要求的数必在不同的子数组中。

```
class Solution {
public:
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        if(data.size()<2) return ;
        int x = 0, b = 1; // x是异或和，b取x低位的1
        for(int i=0; i<data.size(); i++) x ^= data[i];
        while(!(b&x)) b <<= 1;

        for(int i=*num1=0; i<data.size(); i++)
            if(b&data[i]) *num1 ^= data[i];
        *num2 = x ^ *num1;
    }
};
```