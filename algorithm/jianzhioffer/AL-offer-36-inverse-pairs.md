# 数组中的逆序对

### Q：在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。输出P%1000000007。

经典的逆序对问题，分而治之。假设将一个数组arr分成左右两个部分，且各自有序，那么arr的逆序对数由3个部分构成

- arr1的逆序对数 
- arr2的逆序对数
- (a,b)的对数。其中a在arr1中，b在arr2中，a>b。

前两个部分是可以分治处理的(就是递归)，而第三部分就是需要算的部分。如果arr1和arr2已经各自有序了，那就好办了，各自用一个指针就可以统计，复杂度约 arr1+arr2。

```
class Solution {
public:
    int InversePairs(vector<int> &data, int L=0, int R=-1) {
        R = R<0? data.size()-1: R;
        if(R-L<1) return 0; //少于2个元素
        int mid = (L+R)/2;
        int ans = (InversePairs(data, L, mid)+InversePairs(data, mid+1, R))%1000000007;
        int i = L, j = mid+1;
        while(i<mid+1) {
            while(j<=R && data[i]>data[j]) j++;
            ans = (ans+j-mid-1)%1000000007;
            i++;
        }
        inplace_merge(data.begin()+L, data.begin()+mid+1, data.begin()+R+1);
        return ans;
    }
};
```