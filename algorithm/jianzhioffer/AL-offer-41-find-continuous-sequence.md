# 和为s的连续正数序列

### Q：小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

其实就是找多个连续的非0自然数序列，且要求每个序列的和均为s。根据两个指针的思路，左指针每次右移一步，右指针每次右移多步，若左指针追上了右指针，则程序结束。

```
class Solution {
public:
    int Sum(int small, int big) {
        return (big+small)*(big-small+1)/2; //求和
    }
    vector<int> GenNum(int small, int big) {
        vector<int> ret;
        for(int i=small; i<=big; i++) ret.push_back(i);
        return ret;
    }
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int> > ans;
        if(sum<3) return ans;
        int i = 1, j = 2;
        while(i<j) {
            while(Sum(i, j)<sum) j++; // 多步
            if(Sum(i, j)==sum) ans.push_back(GenNum(i, j)); 
            i++; //一步
        }
        return ans;
    }
};
```