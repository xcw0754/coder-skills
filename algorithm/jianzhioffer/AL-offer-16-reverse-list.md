# 反转链表

### Q：给一个单链表，反转该链表并返回头指针。

用C++11来写。将旧链表的元素逐个头插到新链表，这样简单些。
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
    ListNode* ReverseList(ListNode* pHead) {
        decltype(pHead) p = nullptr;	//decltype可感知指针类型
        while(pHead) {
            auto *tmp = pHead->next;	//auto感知不到指针类型
            pHead->next = p;
            p = pHead;
            pHead = tmp;
        }
        return p;
    }
};
```