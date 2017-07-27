## LinkedList

### 141. Linked List Cycle

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null) return false;
        ListNode fast = head;
        ListNode slow = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if(fast == slow)    return true;
        }
        return false;
    }
}
```

### 237. Delete Node in a Linked List

思路：  
把结节点的下一个结点赋给该结点。  
{: .notice} 

```java
public class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

### 83. Remove Duplicates from Sorted List

```java
//my solution
public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;
        while (node!=null && node.next!=null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }
        return head;
    }
}
```

思路：  
求出此时结点后面的无重复序列。  
判断该结点是否与子序列的首结点相同，若相同则删掉子序列首结点。  
{: .notice} 

```java
public ListNode deleteDuplicates(ListNode head) {
    if(head == null || head.next == null)   return head;
    head.next = deleteDuplicates(head.next);
    return head.val == head.next.val ? head.next : head;
}
```

### 160. Intersection of Two Linked Lists

A:          a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3


> while循环有两种停止情况：1.相遇在同一个结点；2.都指向了null

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    //boundary check
    if(headA == null || headB == null) return null;
    
    ListNode a = headA;
    ListNode b = headB;
    
    //if a & b have different len, then we will stop the loop after second iteration
    while( a != b) {
        //for the end of first iteration, we just reset the pointer to the head of another linkedlist
        a = (a==null ? headB : a.next);
        b = (b==null ? headA : b.next);   
    }
    
    return a;
}
```


## 203. Remove Linked List Elements

Given: 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6
Return: 1 --> 2 --> 3 --> 4 --> 5

思路：递归  
可以用1->2->null来演示递归过程。  
1.判断此时所指向的结点是否为空。  
2.若此时指向的节点不为空，则得到下一个结点的指向。  
3.判断是否需要删除该结点，删除则返回下一个结点，不删除就返回此节点。  
{: .notice} 

```java
public ListNode removeElements(ListNode head, int val) {
    if (head == null)   return null;  //判断该结点是否为空(递归结束标志)
    head.next = removeElements(head.next, val); //得到下一个结点的指向
    return head.val == val ? head.next : head; //判断是否需要删除该结点
}
```

思路：  
先排除头结点为val的情况(用while而不是if)。  
再检测下一个结点是否为val。  
{: .notice} 

```java
//my solution 
public class Solution {
    public ListNode removeElements(ListNode head, int val) {
        
        while (head != null && head.val == val) { 
            head = head.next;
        }

        if (head == null)  return null;
        ListNode node = head;
        
        while (node.next != null) {
            if (node.next.val == val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            } 
        }
        return head;
    }
}
```

## 206. Reverse Linked List

```java
//my solution
public class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode pre = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
}
```


```java
//recursive solution
public ListNode reverseList(ListNode head) {
    return reverseListInt(head, null);
}

private ListNode reverseListInt(ListNode head, ListNode newHead) {
    if (head == null) return newHead; 
    ListNode next = head.next; 
    head.next = newHead;
    return reverseListInt(next, head); 
}
```

### 21. Merge Two Sorted Lists

思路：  
设置一个合并的头结点。  
头结点指向那个值小的结点，并将余下的进行合并。  
{: .notice} 

```java
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == NULL) return l2;
        if(l2 == NULL) return l1;
        
        ListNode mergeHead = null;
        if(l1.val < l2.val){
            mergeHead = l1;
            mergeHead.next = mergeTwoLists(l1.next, l2);
        }
        else{
            mergeHead = l2;
            mergeHead.next = mergeTwoLists(l1, l2.next);
        }
        return mergeHead;
    }
}
```
