# 重建二叉树

### Q：给定某个二叉树的前序遍历和中序遍历，请建出这棵二叉树。

> 这题思路比较清晰，关键在找准位置，递归处理即可。
在前序pre数组中的首个元素pre[0]就是当前子树的根，它将中序vin数组分成两边，分别是左右子树。接下来将vin的左右两棵子树的分别切开成两个数组A1和A2(在中间根记得丢掉)，同理pre按照A1的大小就能切成两个数组(根也丢掉)。递归处理。

参考代码?
```
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    int find(vector<int> &vect, int num){
        for(int i=0; i<vect.size(); i++){
            if(vect[i]==num)	return i;
        }
        return -1;	//不可能走这的！
    }
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
		if(pre.empty() || vin.empty())	return 0;
        TreeNode *p = new TreeNode(pre[0]);
        int pivot = find(vin, pre[0]);
        p->left = reConstructBinaryTree(
                vector<int>(pre.begin()+1, pre.begin()+1+pivot), 
                vector<int>(vin.begin(),   vin.begin()+1+pivot));	
                // 这里用到了匿名构造函数，省得还要起名字
        p->right = reConstructBinaryTree(
                vector<int>(pre.begin()+1+pivot, pre.end()),
                vector<int>(vin.begin()+1+pivot, vin.end()));
        return p;
    }
};
```


