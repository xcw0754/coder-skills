# 二叉搜索树与双向链表

### Q：将一棵二叉搜索树转成一个双向链表，要求不能创建新的结点。

实现的方法大同小异，一般是递归处理。
这里提供一种比较直观，面试时手写不容易出错的方式。本结点与左子树的最右节点、右子树的左节点有关，需要得到这两个信息，都得用额外的变量来记录，3种记录方式：成员变量、形参、返回值。因为需要两个信息，3种方式混搭也行。用成员变量会稍简单一些。我用返回值处理。

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
如果想要一种让别人觉得你功底很不错，那么可以考虑另外的方法。用一个指针记录信息，该指针指向当前结点的前继。其实就是利用中序遍历，当遍历到节点A时，A的前继就都已经有序，且左右指针都调整完毕了，此时只需要将前继的right指针指向自己，自己的left指向它即可。然后把指针指向A，再右子树传递下去。这种递归的设计比较巧，如果发挥不好，就是搬石头砸自己的脚。






