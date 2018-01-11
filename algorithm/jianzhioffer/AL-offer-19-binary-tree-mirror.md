# 二叉树的镜像

### Q：给一棵二叉树，将其变成它的镜像。(就是交换任一节点的左右孩子)

这递归起来简单多了，四行即可。

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
    void Mirror(TreeNode *pRoot) {
		if(!pRoot)	return;
        Mirror(pRoot->left);
        Mirror(pRoot->right);
        swap(pRoot->left, pRoot->right);
    }
};
```