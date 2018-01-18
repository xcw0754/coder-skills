# 把数组排成最小的数

### Q：给定n个数字，将其组合成最小的数字。

此题的解法略难想，又很难证。其实就是排序，如果数字a和数字b分别组成ab和ba，通过比较这两个组成的数字来排序。可能的情况有3种：
- ab < ba 则a应在前。
- ab > ba 则b应在前。
- ab = ba 这种很头疼，不太明白谁应在前，但事实好像是无所谓的？

```
class Solution {
public:
    static int cmp(const string &a, const string &b) {
        return a+b < b+a;
    }
    string PrintMinNumber(vector<int> numbers) {
        vector<string> vect;
        for(int i=0; i<numbers.size(); i++) {
            vect.push_back(to_string(numbers[i]));
        }
        sort(vect.begin(), vect.end(), Solution::cmp);
		string ans = "";
        for(int i=0; i<vect.size(); i++) {
            ans += vect[i];
        }
        return ans;
    }
};
```