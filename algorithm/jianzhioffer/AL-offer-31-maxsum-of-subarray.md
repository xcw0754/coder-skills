# 连续子数组的最大和

### Q：给定一个int型数组，求其连续子数组的最大和。

这道题挺经典，似乎很复杂，代码却很短。为了解决快速求连续子数组的和，可以用前缀和的方式，如prefixSum[j]-prefixSum[i]就可以求出从A[i+1~j]的和。现在考虑如何得到连续子数组的最大和，prefixSum[j]应该减去`min{prefixSum[0～j-1]}`就能得到以A[j]为子数组尾元素的所有连续子数组的最大和，遍历j的所有可能取值就能得到答案了。
```
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> &array) {
        if(array.empty())	return 0;
        int ans = array[0], small = 0, sum = 0;
        for(int i=0; i<array.size(); i++) {
            sum += array[i];	//前缀和，A[0~i]
            ans = max(ans, sum-small);
            small = min(small, sum);
        }
        return ans;
    }
};
```

现在考虑另一种解法，DP思想。对于A[j]，有两种选择：1)A[i~j]  2)A[j]。遍历一遍就能统计到连续子数组的最大和。
```
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> &array) {
        if(array.empty())	return 0;
        int ans = array[0], sum = 0xffffffff;	//8个f是伪负无穷
        for(int i=0; i<array.size(); i++) {
            sum = max(sum+array[i], array[i]);
            ans = max(ans, sum);
        }
        return ans;
    }
};
```