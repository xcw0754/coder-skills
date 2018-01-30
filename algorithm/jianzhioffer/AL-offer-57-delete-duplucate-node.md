# 删除链表中重复的节点

### Q：在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

在原链表的头部插入一个临时头节点，以方便统一操作。先判断是否需要删除，若不需要，则直接考虑下一个结点。需要删除必定是删除两个以上的结点，将要删除的结点值存起来，逐个比对，再删除节点。

```
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead)
    {
        ListNode *tmpNode = new ListNode(0), *p1 = tmpNode;
        tmpNode->next = pHead;
        while(p1->next) {
            int curVal = p1->next->val; //当前考虑的值
            if(!p1->next->next) break; //p1->next为尾结点
            if(curVal!=p1->next->next->val) { //不用删
                p1 = p1->next; 
            } else {
                while(p1->next && curVal==p1->next->val) { //要删
                    ListNode *y = p1->next; //要删除的节点
                    p1->next = y->next;
                    delete y;
                }
            }
        }
        pHead = tmpNode->next; //临时头节点是要删的
        delete tmpNode;
        return pHead;
    }
};
```