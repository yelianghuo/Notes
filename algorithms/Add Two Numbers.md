/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        if(l1 == NULL && l2 == NULL)
            return NULL;
        int upper = 0;
        ListNode *h = new ListNode(0);
        ListNode *p = h;
        while(upper != 0 || l1 != NULL || l2 !=NULL){
            int tmp = (upper + l1->val + l2-> val) % 10;
            upper = (upper + l1->val + l2-> val) / 10;
            p->next = new ListNode(tmp);
            p = p->next;
            
        }
        return h;
        
    }
};