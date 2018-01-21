# 两个链表的第一个公共结点

### Q：输入两个链表，找出它们的第一个公共结点。要考虑非法输入。

经典问题，两个链表求首个公共点，要考虑输入空链，和无公共结点的情况。开始时，A指针在链表1上，B指针在链表2上，每次各自往后移动一步，在到达链尾时切换到另一个链表上，走到最后若有公共点则必会相遇，首个相遇的结点即是答案。其实就是利用两个指针走过的路程长度相等，跟马路上的车一样。

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
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        if(!pHead1 || !pHead2) return NULL;
        ListNode *A = pHead1, *B = pHead2;
        int flag = 0; // 用于判断A是否在pHead2上
        while(A!=B) {
            if(flag && !A->next) return NULL;
            if(A->next) A = A->next;
            else {A = pHead2; flag = 1;}
            if(B->next) B = B->next;
            else B = pHead1;
        }
        return A;
    }
};
```