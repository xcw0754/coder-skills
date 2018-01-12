# 栈的压入、弹出序列

### Q：给一个进栈序列和一个出栈序列，判断该出栈序列是否合法？

这题最优的方式是模拟栈的出入操作，复杂度O(n)。其他方式不止这个复杂度。

```
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if(pushV.size()!=popV.size())	return false;
        if(pushV.empty()||popV.empty())	return false;
        stack<int> stac;
        while(!pushV.empty()) {
            stac.push(pushV.front());
            pushV.erase(pushV.begin());	//这种删除方式有效率问题
            while(!stac.empty() && stac.top()==popV.front()) {
                stac.pop();
                popV.erase(popV.begin());
            }
        }
        if(popV.empty()) return true;
        else return false;
    }
};
```