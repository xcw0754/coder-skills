# 从1到n整数中1出现的次数

### Q：给定一个整数n，问闭区间[0, n]内这n个数字的十进制表示中共有多少个1？

很多种解法，暴力就别去想了，不太可取，考虑低一点复杂度的。
不太容易想明白的解法可参考 [最简短的解法](https://discuss.leetcode.com/topic/18054/4-lines-o-log-n-c-java-python)。

下面讲一种比较容易理解，容易写的解法，以21345为例子。

考虑21345这个数字，将其拆分成`[0~19999]`和`[20000~21345]`两个部分。先看第一部分，很直观可以计算出其中含1的个数，为 10000 + (2 \* 1000) + (2 \* 1000) + (2 \* 1000) + (2 \* 1000)，这只是个排列组合计算问题。再看第二部分，最高位为2是固定的，那就可以直接将1345递归处理。

有一个特殊情况要考虑，就是最高位为1怎么办？

比如 1345这个数字，会分为`[0~0999]`和`[1000~1345]`，第一部分是可以递归的，第二部分最高位必为1,要先计入346个，后将345递归处理。

综上，将当前数字拆成两个部分，争取递归处理，每次可以去掉一个最高位，只需要考虑最高为1和非1两种情况。当n的所有位全为1时复杂度最高，同深度遍历一棵满二叉树的复杂度一样，树高为n的位数，即二叉树结点个数最多为2048个。这个复杂度还是可以接受的，虽然比最简短的解法要大很多。

```
class Solution {
public:
    int Pow(int base, int exp) { return round(pow(base, exp)); }
    int countDigitOne(int n)
    {
        if(n<1) return 0;
        if(n<10) return 1;
        string sn = to_string(n);
        int siz = sn.size();
        int hbase = Pow(10, siz-1);    // 最高位改为1,其他位改为0
        int remain = countDigitOne(n%hbase); // 去掉n的最高位再递归

        if(sn[0]=='1') {
            return countDigitOne(hbase-1) + remain + n%hbase+ 1;
        } else {
            return hbase + (siz-1)*(sn[0]-'0')*Pow(10, siz-2) + remain;
        }
    }
};
```