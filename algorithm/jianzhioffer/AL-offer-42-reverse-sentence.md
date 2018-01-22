# 翻转单词顺序

### Q：比如将“I am a student.”翻转成“student. a am I”，标点跟最近的那个词即可。

用点额外的空间会比较好处理，不过不用额外空间也挺好算，就是整句翻转，再逐个词翻转。

```
class Solution {
public:
    string ReverseSentence(string str) {
        for(int i=0,j=0; i<str.size(); i++) {
            if(str[i]==' ' || i+1==str.size()) {
                i = i+1==str.size()? i+1: i;
                reverse(str.begin()+j, str.begin()+i);
                j = i + 1;
            }
        }
        reverse(str.begin(), str.end());
        return str;
    }
};
```