# 二叉搜索树与双向链表

### Q：将一棵二叉搜索树转成一个双向链表，要求不能创建新的结点。

实现的方法大同小异，可以递归处理。本结点与左子树的最右节点、右子树的左节点有关，需要得到这两个信息，都得用额外的变量来记录，3种记录方式：成员变量、形参、返回值。因为需要两个信息，3种方式混搭也行。用成员变量会稍简单一些。我用返回值处理。

```
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<TreeNode*> Help(TreeNode* T)
    {
        vector<TreeNode*> ret(2, T), tmp;
        if(T && T->left)
        {
            tmp = Help(T->left);
            ret[0] = tmp[0]; // 左子树的最左结点指针
            tmp[1]->right = T;
            T->left = tmp[1];
        }
        if(T && T->right)
        {
            tmp = Help(T->right);
            ret[1] = tmp[1]; // 右子树的最右结点指针
            tmp[0]->left = T;
            T->right = tmp[0];
        }
        return ret;
    }
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        if(!pRootOfTree) return NULL;
        vector<TreeNode*> ret = Help(pRootOfTree);
        return ret[0];
    }
};
```
