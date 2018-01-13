# 复杂链表的复制

### Q：给一个复杂链表，复制一个备份并返回。

下面的思路和别人不太一样，利用申请到的内存的连续性，将原始链表的label暂时用来存其他信息，后面再恢复回来。
```
/*
struct RandomListNode {
    int label;
    struct RandomListNode *next, *random;
    RandomListNode(int x) :
            label(x), next(NULL), random(NULL) {
    }
};
*/
class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead) {
        if(!pHead) return NULL;
        int n = 0;
        RandomListNode *p = pHead, cHead = NULL;
        while(p && ++n) p = p->next;	//统计长度n
        cHead = (RandomListNode *)malloc(sizeof(RandomListNode)*n); //连续空间
        p = pHead;
        for(int i=0; i<n; i++) {
            memcpy(cHead+i, p, sizeof(RandomListNode));
            (cHead+i)->next = (i+1==n)? NULL: cHead+i+1;
            p->label = i;	// 改了它
            p = p->next;
        }
        for(int i=0; i<n; i++) { //修正random
            if(!(cHead+i)->random)	continue;
            (cHead+i)->random = cHead + (cHead+i)->random->label;
        }
        for(int i=0; i<n; i++) {
            pHead->label = (cHead+i)->label; //恢复label
            pHead = pHead->next;
        }
        return cHead;
    }
};
```