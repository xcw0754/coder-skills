# 二叉树的下一个节点

### Q：给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

分两种情况考虑：
- 指定结点有右子树。要求的结点就在该右子树的中序遍历结果的首个结点。
- 指定结点无右子树。沿着父结点向上，找一个满足"本结点是家父的左孩子"的结点，若无，则返回NULL。

```
/*
struct TreeLinkNode {
    int val;
    struct TreeLinkNode *left;
    struct TreeLinkNode *right;
    struct TreeLinkNode *next; //父指针
    TreeLinkNode(int x) :val(x), left(NULL), right(NULL), next(NULL) {}
};
*/
class Solution {
public:
    TreeLinkNode* GetNext(TreeLinkNode* pNode)
    {
        if(!pNode) return NULL;
        if(pNode->right) { //有右孩子
            pNode = pNode->right;
            while(pNode->left) pNode = pNode->left;
        } else {
            while(pNode->next && pNode->next->right==pNode) 
                pNode = pNode->next;
            pNode = pNode->next;
        }
        return pNode;
    }
};
```