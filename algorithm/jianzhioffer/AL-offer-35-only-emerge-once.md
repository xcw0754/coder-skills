# 第一个只出现一次的字符

### Q：在一个字符串(1<=字符串长度<=10000，全部由大小写敏感的字母组成)中找到第一个只出现一次的字符,并返回它的下标。其他情况则输出-1。

要写简洁一点也是挺麻烦的。

```
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        vector<int> vect[128];
        for(int i=0; i<str.size(); i++) 
            vect[str[i]-'A'].push_back(i);

		int ans = str.size();
        for(int i=0; i<128; i++)
            if(vect[i].size()==1)
                ans = min(ans, vect[i].front());
        return ans==str.size()? -1: ans;
    }
};
```