# 对称的二叉树

### Q：请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

一般情况下需要用两个函数来实现才能简单一些，否则单个函数还是很麻烦的。


```
// 单函数版本，可以判断两棵树是否对称，又能判断一棵树是否对称。
class Solution {
public:
    bool isSymmetrical(TreeNode* pRoot1, TreeNode* pRoot2=NULL, bool isRoot=true)
    {
        if(isRoot && !pRoot1) return true;
        if(isRoot) return isSymmetrical(pRoot1->left, pRoot1->right, false);
        if(pRoot1 && pRoot2)
            return pRoot1->val==pRoot2->val
            && isSymmetrical(pRoot1->left, pRoot2->right, false)
            && isSymmetrical(pRoot1->right, pRoot2->left, false);
        return !pRoot1 && !pRoot2;
    }

};
```

```
// 两个函数的版本，逻辑就清晰多了。
class Solution {
public:
    bool __isSymmetrical(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        if(pRoot1 && pRoot2)
            return pRoot1->val==pRoot2->val
            && __isSymmetrical(pRoot1->left, pRoot2->right)
            && __isSymmetrical(pRoot1->right, pRoot2->left);
        return !pRoot1 && !pRoot2;
    }
    bool isSymmetrical(TreeNode* pRoot)
    {
        if(!pRoot) return true;
        return __isSymmetrical(pRoot->left, pRoot->right);
    }
};
```