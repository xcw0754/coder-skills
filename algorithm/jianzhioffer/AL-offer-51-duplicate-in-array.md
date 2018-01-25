

### Q：在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

如果原数组可以修改的话，时间O(n)，空间O(1)。


```
class Solution {
public:
    bool duplicate(int numbers[], int length, int* duplication) {
        for(int i=0; i<length; i++) {
            int tmp = numbers[i];
            if(tmp==i) continue; //tmp已在其应处的位置上
            if((*duplication=tmp)==numbers[tmp]) return true; //重复了
            swap(numbers[i--], numbers[tmp]); //交换到tmp应处的位置上
        }
        *duplication = -1; // 找不到时的默认输出
        return false;
    }
};
```