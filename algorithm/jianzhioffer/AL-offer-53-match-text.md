# 正则表达式匹配

### Q：请实现一个函数用来匹配包括`.`和`*`的正则表达式。模式中的字符`.`表示任意一个字符，而`*`表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配。

这题非常考验逻辑，先要想到一些可能的输入，如`**`、`.*`、`*abc`。其中`.*`最恶心了，可以匹配任意长度的任意字符串。


```
class Solution {
public:
    bool match(char* str, char* pattern)
    {
        if(!str || !pattern || *pattern=='*') return false; //非法
        if(*pattern=='\0') return *str=='\0'; //递归出口
        if(*(pattern+1)=='*') {
            if(*pattern=='*') return false; //非法
            if(*str=='\0')  return match(str, pattern+2);
            if(*pattern=='.' || *pattern==*str) 
                return match(str+1, pattern) || match(str, pattern+2);
            return match(str, pattern+2);
        }
        if(*str!='\0' && *pattern=='.') return match(str+1, pattern+1);
        if(*pattern==*str) return match(str+1, pattern+1); //普通匹配
        return false;
    }
};
```