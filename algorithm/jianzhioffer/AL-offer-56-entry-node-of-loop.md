# 链表中环的入口结点

### Q：一个链表中包含环，请找出该链表的环的入口结点。

首先，从头结点开始，一个指针走两步，一个指针走一步，若有环，两者必会在环上相遇于某结点。其次，在这环上一点，与头结点安排两个指针齐步走，相遇的点即为环入口结点。其实画个图，量一下距离就能知道为何会这样。有一个小坑需要注意，在第一步中，两个指针都从头结点开始走的，所以在计算距离时，头结点不能算进去。

另一种解法，如果可以破坏所给链表的话，你可以逐个将链表拆了，每个结点next指针逐个置空，拆完也就知道答案了。

```
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        ListNode *one = pHead, *two = pHead;
        do { //找环上的一个点
            if(!two || !two->next) return NULL;
            two = two->next->next;
            one = one->next;
        } while(one!=two);
        //two = two->next;  //小坑在这！
        while(pHead!=two) { // 同时走
            pHead = pHead->next;
            two = two->next;
        }
        return two;
    }
};
```