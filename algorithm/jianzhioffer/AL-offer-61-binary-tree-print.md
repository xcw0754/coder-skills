# 按之字形顺序打印二叉树

### Q：请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

这个题要讲明白可能不那么容易，但是想一下还是能想明白的。第一层从左往右，先遍历左孩子后右孩子，收集到的第二层结点也是从左往右的。根据收集到的结果，第二层得反向遍历，并且先访问右孩子，收集到的第三层是从右往左的。根据收集到的结果，第三层又得反向遍历，并且先访问左孩子。似乎看到规律了，就是奇数层先左后右地收集孩子结点，偶数层相反。且每一层都得根据收集到的结果反向遍历。也就是说，只需要在收集左右孩子顺序方面作点文章就行了，其他每层的操作都是一样的，就是反向遍历。



```
class Solution {
public:
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int> > vect;
        if(!pRoot) return vect;
        deque<TreeNode*> que(1, pRoot);
        for(int i=0; !que.empty(); i++) {
            int size = que.size();
            vector<int> layer;
            while(size--) {
                TreeNode *tmp = que.front();que.pop_front();
                layer.push_back(tmp->val);
                if(i&1) swap(tmp->left, tmp->right); //区分奇偶层
                if(tmp->left) que.push_back(tmp->left);
                if(tmp->right) que.push_back(tmp->right);
            }
            vect.push_back(layer);
            reverse(que.begin(), que.end()); //反向
        }
        return vect;
    }
};
```