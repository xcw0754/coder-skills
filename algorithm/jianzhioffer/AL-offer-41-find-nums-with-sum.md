# 和为s的两个数字

### Q：输入一个递增排序的数组和一个数字S，在数组中查找两个数，是的他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。对应每个测试案例，输出两个数，小的先输出。

经典问题，用两个指针来找到两个符合条件的数，`i`从arr[0]开始，`j`从arr[-1]开始，规定i每次可以右移个位置，j每次可以左移动多个位置，且始终保持i<j。表示的意思是为每个arr[i]找一个恰当的arr[j]使得arr[i]+arr[j]=s。仔细观察发现，i在右移过程中，j不可能右移，故可考虑如下代码


```
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        vector<int> ans(2, 40000); //随便两个足够大的数
        for(int i=0, j=array.size()-1; i<j; i++) {
            while(i<j && array[i]+array[j]>sum) j--; //左移调整j
            if(i<j && array[i]+array[j]==sum) { //找到？
                if(ans[0]*ans[1]>array[i]*array[j]){
                    ans[0] = array[i];
                    ans[1] = array[j];
                }
            }
        }
        return ans[0]==40000? vector<int>(): ans; //无符合的数返回空
    }
};
```