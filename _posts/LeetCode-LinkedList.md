
```
关于while循环中的条件：
1.如果后续代码中有node.next.val，while要排除node.next==null的情况。
2.如果后续代码有node.next.next，while要排除node.next==null的情况。
```

#### 141. Linked List Cycle

Given a linked list, determine if it has a cycle in it.


```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) return false; //无head.next==null return false 节点可以指向自己
        ListNode fast = head;
        ListNode slow = head;
        
        while (fast.next != null && fast.next.next != null) { //注意条件和fast相关
            slow = slow.next;
            fast = fast.next.next;
           
            if( fast == slow)  return true;
        }
        return false;
    }
}
```

***

#### 83. Remove Duplicates from Sorted List *

Given a sorted linked list, delete all duplicates such that each element appear only once.

For example,
Given 1->1->2, return 1->2.
Given 1->1->2->3->3, return 1->2->3.

没有把node = node.next;放在else中

```java
//while
public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;
        while (node!=null && node.next!=null) { //注意条件
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next; //必须放在else里而不是没有else，排除全是同样的元素的情况
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

> 没有判断head.next == null的情况
> 如果不写这个条件，最后一行head.next.val就会出错。 

```java
//Recrusive
public ListNode deleteDuplicates(ListNode head) {
    if (head == null || head.next == null)  return head;
    head.next = deleteDuplicates(head.next);
    return head.val == head.next.val ? head.next : head;
}
```

***

#### 160. Intersection of Two Linked Lists

Write a program to find the node at which the intersection of two singly linked lists begins.  
For example, the following two linked lists:

A:          a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3

> while循环有两种停止情况：1.相遇在同一个结点；2.都指向了null
> 注意while条件

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if(headA == null || headB == null)  return null;
    
    ListNode a = headA;
    ListNode b = headB;
    
    while( a != b) {
        a = (a==null ? headB : a.next);
        b = (b==null ? headA : b.next);   
    }   
    return a;
}
```

***

#### 21. Merge Two Sorted Lists

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

思路：  
1.设置一个合并的头结点。  
2.头结点指向那个值小的结点，并将余下的进行合并。  
{: .notice} 

```java
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        
        ListNode mergeHead = null; //ListNode head = new ListNode();错误
        
        if (l1.val < l2.val) {
            mergeHead = l1;
            mergeHead.next = mergeTwoLists(l1.next, l2);
        } else {
            mergeHead = l2;
            mergeHead.next = mergeTwoLists(l1, l2.next);
        }
        return mergeHead;
    }
}
```


***

#### 203. Remove Linked List Elements *(while方法)

Remove all elements from a linked list of integers that have value val.

Example
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
1.先排除头结点为val的情况。  
2.再检测下一个结点是否为val。  
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

***

#### 206. Reverse Linked List *（recursive方法）

> while条件写错

```java
//while
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

思路：
1.获取下一个结点
2.反转以下一个结点为头结点的链表
3.反转的链表最后加上头结点

> 注意递归截止条件  
> 没有写head.next = null

```java
//recursive
public class Solution {
    public ListNode reverseList(ListNode head) {
        if (head==null || head.next==null)  return head;
        ListNode newHead = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return newHead;
    }
}
```

***

## 234. Palindrome Linked List *

Given a singly linked list, determine if it is a palindrome.

思路：  
1.设置快慢两个指针指向头结点。  
2.快指针每次移动两个节点，慢指针每次移动一个节点。  
3.将以慢指针所指向的节点为头结点的链表反转。  
4.比较链表的每个节点是否相同。  
{: .notice} 

> 判断节点奇偶：节点为奇数个时，fast最后指向null，若为偶数，最后指向的不是null。  
> while条件为：fast != null && fast.next != null 因为fast最后指向的是null(奇)或者下一个是null(偶)
> 最后折半比较时，以slow为head的链表更短，所以slow != null而不是fast != null

```java
public boolean isPalindrome(ListNode head) {
    ListNode fast = head, slow = head;
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        slow = slow.next;
    }
    if (fast != null) { //奇数个节点时慢指针后移一个节点，为了使右边部分更小
        slow = slow.next;
    }
    slow = reverse(slow);
    fast = head;
    
    while (slow != null) {
        if (fast.val != slow.val) {
            return false;
        }
        fast = fast.next;
        slow = slow.next;
    }
    return true;
}

public ListNode reverse(ListNode head) {
    ListNode prev = null; 
    while (head != null) {
        ListNode next = head.next;
        head.next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```
