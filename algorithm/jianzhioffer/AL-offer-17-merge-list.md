# 合并两个排序的链表

### Q：给出两个单调递增的链表，合并为单调不减的链表并返回头指针。

善用递归，善用swap函数。先将规模减小，考虑每次将当前两个链表中最小的一个弹出来，弹出来一个之后剩下的又是一个子问题，递归下去就可以了。

```
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if(!pHead1 || !pHead2)	return pHead1?pHead1:pHead2;	//出口
        if(pHead1->val > pHead2->val)	swap(pHead1,pHead2);
        pHead1->next = Merge(pHead1->next, pHead2);
        return pHead1;
    }
};
```