# 二叉搜索树的后续遍历序列

### Q：给定一个序列，问该序列是否为某棵二叉搜索树的后续遍历序列？保证序列无重复元素。

一开始就想怎样不用新接口就能写出来，确实不行，因为如果想用递归，每个子问题都得建新的seq，最坏情况下空间是O(n*n)，这还没算栈的开销。用另外的接口就方便了，题不难，要小心别写死递归了。

```
class Solution {
public:
    bool DFS(vector<int>& seq, const int s, const int e) {
		if(s>=e) return true;
        int root=seq[e-1], i=s-1, j=e-1;
        while(seq[++i]<root);	// 找前部分
        while(--j>=i && seq[j]>root);	//验证后部分
        if(j>=i) return false;
        return DFS(seq, s, i) && DFS(seq, i, e-1); //子问题递归
    }
    bool VerifySquenceOfBST(vector<int> &seq) {
        return seq.empty()?false:DFS(seq, 0, seq.size());
    }
};
```