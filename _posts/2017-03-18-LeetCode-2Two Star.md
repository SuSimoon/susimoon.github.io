---
layout: post
title:  "LeetCode-Two Star"
date:   2017-03-18
excerpt: ""
project: true
tag:

comments: false
---


***

### 242. Valid Anagram

Given two strings s and t, write a function to determine if t is an anagram of s.

For example,
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.

> 想到了自己的方法，但没有想到第一种哈希表方法

```java
public class Solution {
    public boolean isAnagram(String s, String t) {
        int[] alphabet = new int[26];
        for (int i = 0; i < s.length(); i++) alphabet[s.charAt(i) - 'a']++;
        for (int i = 0; i < t.length(); i++) alphabet[t.charAt(i) - 'a']--;
        for (int i : alphabet) if (i != 0) return false;
        return true;
    }
}
```

```java
//我的麻烦方法
public class Solution {
    public boolean isAnagram(String s, String t) {
        Map<Character,Integer> map = new HashMap<Character,Integer>();
       
        for(int i=0; i<s.length(); i++) {
            if(map.containsKey(s.charAt(i))) {
                map.put(s.charAt(i), map.get(s.charAt(i))+1);
            } else {
                map.put(s.charAt(i), 1);
            }
        }
        
         for(int i=0; i<t.length(); i++) {
             if(map.containsKey(t.charAt(i))) {
                map.put(t.charAt(i), map.get(t.charAt(i))-1);
             } else {
                return false;
             }
         }
         
         for(Character key : map.keySet()) {
             Integer value = map.get(key);
             if(value != 0)
                return false;
         }
         
       return true; 
    }
}
```

***

### 345. Reverse Vowels of a String

Write a function that takes a string as input and reverse only the vowels of a string.

Example 1:
Given s = "hello", return "holle".

Example 2:
Given s = "leetcode", return "leotcede".

思路：  
1.两个指针分别指向字符串的首和尾。  
2.左指针右移，直到遇到元音停止；右指针左移，直到遇到元音停止。  
3.将两个指针所指元素进行交换。  

> 没有把chars[start]转换成String，即+“”就可以转换成String
> 直接将首尾指针指向的字母进行单个判断了，没有持续判断是否有连续的非元音
> 内循环中没有start<end这个条件
> 没有在交换之后将两个指针移动到下一个元素

```java
public class Solution {
    public String reverseVowels(String s) {
        if(s == null || s.length()==0) return s;
        String vowels = "aeiouAEIOU";
        char[] chars = s.toCharArray();
        int start = 0;
        int end = s.length()-1;
        while(start<end){       
            
            while(start<end && !vowels.contains(chars[start]+"")){
                start++;
            }
            
            while(start<end && !vowels.contains(chars[end]+"")){
                end--;
            }
            
            char temp = chars[start];
            chars[start] = chars[end];
            chars[end] = temp;
            
            start++;
            end--;
        }
        return new String(chars);
    }
}
```

***


### 125. Valid Palindrome

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

For example,
"A man, a plan, a canal: Panama" is a palindrome.
"race a car" is not a palindrome.

内循环要注意：若是while则要有start<end条件，否则会无限循环。  
若是if则不需要写此条件。
{: .notice} 

> Character.isLetterOrDigit没有记住
> Character.toLowerCase没有考虑到
> 第二种方法没有想到

```java
//我的方法
public class Solution {
    public boolean isPalindrome(String s) {
        if(s.isEmpty())  return true;
        char[] chars = s.toCharArray();
        int start = 0;
        int end = s.length() - 1;
        
        while(start<end) {
            while(start<end && !Character.isLetterOrDigit(chars[start])) {
                start ++;
            }
            while(start<end && !Character.isLetterOrDigit(chars[end])) {
                end --;
            }
            
            if(Character.toLowerCase(chars[start]) != Character.toLowerCase(chars[end])) {
                return false;
            } else {
                start ++;
                end --;
            }
        }
        return true;
    }   
}
```

```java
public class Solution {
    public boolean isPalindrome(String s) {
        if (s.isEmpty()) {
            return true;
        }
        int head = 0, tail = s.length() - 1;
        char cHead, cTail;

        while(head <= tail) {
            cHead = s.charAt(head);
            cTail = s.charAt(tail);
            if (!Character.isLetterOrDigit(cHead)) {
                head++;
            } else if(!Character.isLetterOrDigit(cTail)) {
                tail--;
            } else {
                if (Character.toLowerCase(cHead) != Character.toLowerCase(cTail)) {
                    return false;
                }
                head++;
                tail--;
            }
        }       
        return true;
    }
}
```

***

## 283. Move Zeroes

Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.

For example, given nums = [0, 1, 0, 3, 12], after calling your function, nums should be [1, 3, 12, 0, 0].

Note:
You must do this in-place without making a copy of the array.
Minimize the total number of operations.

思路：  
从0开始，遇到不为0的元素就依次填入数组。  
剩余的元素补零。  
{: .notice} 

> 当返回值是void时，判断边界条件时用return。  
> 没有想到简洁for和while结构

```java
public void moveZeroes(int[] nums) {
    if (nums == null || nums.length == 0) return;        

    int insertPos = 0;
    for (int num: nums) {
        if (num != 0) nums[insertPos++] = num;
    }        

    while (insertPos < nums.length) {
        nums[insertPos++] = 0;
    }
}
```

***

## 234. Palindrome Linked List

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

***

## 35. Search Insert Position  

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

Here are few examples.
[1,3,5,6], 5 → 2
[1,3,5,6], 2 → 1
[1,3,5,6], 7 → 4
[1,3,5,6], 0 → 0

```java
public class Solution {
    public int searchInsert(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        int mid;
        
        while (low <= high) {
            mid = (low + high) >>> 1;
            if (nums[mid] == target)  return mid;
            else if (nums[mid] < target) {  // 小于target时就写low
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return low; //不是low-1
    }
}
```

***

## 107. Binary Tree Level Order Traversal II

http://blog.csdn.net/ustcjackylau/article/details/42454779

> poll写成了peek造成超过内存限制

```java
//BFS
public class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
        
        if(root == null) return wrapList;
        
        queue.offer(root);
        while(!queue.isEmpty()){
            int levelNum = queue.size();
            List<Integer> subList = new LinkedList<Integer>();
            for(int i=0; i<levelNum; i++) {
                if(queue.peek().left != null) queue.offer(queue.peek().left);
                if(queue.peek().right != null) queue.offer(queue.peek().right);
                subList.add(queue.poll().val);
            }
            wrapList.add(0, subList);
        }
        return wrapList;
    }
}
```


```java
//BFS ？？？？
public class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
        levelMaker(wrapList, root, 0);
        return wrapList;
    }
    
    public void levelMaker(List<List<Integer>> list, TreeNode root, int level) {
        if(root == null) return;
        if(level >= list.size()) {
            list.add(0, new LinkedList<Integer>());
        }
        levelMaker(list, root.left, level+1);
        levelMaker(list, root.right, level+1);
        list.get(list.size()-level-1).add(root.val);
    }
}
```

***

## 404. Sum of Left Leaves

思路：  
检测左子树是否是个叶子，是就求和，不是就计算左子树叶子和。    
计算右子树的左叶子和。  
{: .notice} 

```java
public class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null)  return 0;
        int sum = 0;
        if (root.left != null) {
            if (root.left.left==null && root.left.right==null)
               sum += root.left.val;
            else 
               sum += sumOfLeftLeaves(root.left);
        }
        sum += sumOfLeftLeaves(root.right);
        
        return sum;
    }
}
```

思路：  
检测左子树是否是个叶子，是就求和，不是就让左子树入栈。 
检测右子树是否是个叶子，不是就入栈。  

> stack弹出是pop  
> 若右子树是个叶子，什么也不用操作

```java
public int sumOfLeftLeaves(TreeNode root) {
    if(root == null) return 0;
    int ans = 0;
    Stack<TreeNode> stack = new Stack<TreeNode>();
    stack.push(root);
    
    while(!stack.empty()) {
        TreeNode node = stack.pop();
        if(node.left != null) {
            if (node.left.left == null && node.left.right == null)
                ans += node.left.val;
            else
                stack.push(node.left);
        }
        if(node.right != null) {
            if (node.right.left != null || node.right.right != null)
                stack.push(node.right);
        }
    }
    return ans;
}
```


***

## 111. Minimum Depth of Binary Tree  

思路：  
计算一个节点的左子树最小深度和右子树最小深度分别有多少个。  
若没有左子树或者右子树，就让存在的那个子树深度+1；  
若左右子数都存在，就选择最小的那棵+1。  
{: .notice} 

```java
public class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) return 0;
        int left = minDepth(root.left);
        int right = minDepth(root.right);
        return (left == 0 || right == 0) ? left + right + 1: Math.min(left,right) + 1;  
    }
}
```

```java
//错误方式：若没有右子树，则右子树没有深度，所以不能算上右子树的值。这和求最大深度是不同的。  
public class Solution {
    public int minDepth(TreeNode root) {
        if (root == null)  return 0;
        return Math.min(minDepth(root.left), minDepth(root.right))+1;
    }
}
```
***

## 257. Binary Tree Paths  

思路：  
若一个节点有左结点，就将自己的路径和值传给左结点。  
若一个节点有右结点，就将自己的路径和值传给右结点。  
若没有左右节点，说明已经遍历到了叶子，就把完整的路径加入到answer中。  
{: .notice} 

> 没写出优化方法

```java
public class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> answer = new ArrayList<>();
        if (root != null)  SearchBT(root, "", answer);
        return answer;
    }
    public void SearchBT(TreeNode node, String path, List<String> answer) {
        if (node.left == null && node.right == null)  answer.add(path + node.val);
        if (node.left != null)  SearchBT(node.left, path+node.val+"->", answer);
        if (node.right != null)  SearchBT(node.right, path+node.val+"->", answer);
    }
}
```

思路：  
第一种方法中在path中使用了String，耗费空间。更好的方式是使用StringBuffer。

```
String是不可变的字符序列。当s1 += s2时，不是将s1序列后面添加了s2序列，而是重新生成一块内存，将两个序列复制进去。
StringBuilder是可变的字符序列，可以避免这样的问题。
```


```java
//上述方法的优化
public List<String> binaryTreePaths(TreeNode root) {
    List<String> res = new ArrayList<>();
    StringBuilder sb = new StringBuilder();
    helper(res, root, sb);
    return res;
}

private void helper(List<String> res, TreeNode root, StringBuilder sb) {
    if(root == null)  return;
    
    int len = sb.length();
    sb.append(root.val);
    if(root.left == null && root.right == null) {
        res.add(sb.toString());
    } else {
        sb.append("->");
        helper(res, root.left, sb);
        helper(res, root.right, sb);
    }
    sb.setLength(len); //恢复上一层的路径
}
```

```java
public List<String> binaryTreePaths(TreeNode root) {
        
    List<String> paths = new LinkedList<>();

    if(root == null)  return paths;
    
    if(root.left == null && root.right == null){
        paths.add(root.val+"");
        return paths;
    }

     for (String path : binaryTreePaths(root.left)) {
         paths.add(root.val + "->" + path);
     }

     for (String path : binaryTreePaths(root.right)) {
         paths.add(root.val + "->" + path);
     }

     return paths;
    
}
```
***

## 101. Symmetric Tree

```java
public class Solution {
    public boolean isSymmetric(TreeNode root) {
        return root == null ||  isSymmetricHelp(root.left, root.right);
        
    }
    public boolean isSymmetricHelp(TreeNode left, TreeNode right) {
        if (left==null || right==null)  return left==right;
        if (left.val != right.val)  return false;
        return isSymmetricHelp(left.left, right.right) && isSymmetricHelp(left.right, right.left);
    }
}
```

***

## 100. Same Tree  

思路：  
若两个节点的值相等，就去比较它们的左子树和右子树是否也相等。  
若两个节点值不等，return false。  
{: .notice} 

> 没有考虑左右子树值是否相等的条件

```java
public class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p==null || q==null)  return p==q;
        if (p.val == q.val) 
            return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
        return false;
    }
}
```

***

## 110. Balanced Binary Tree  

思路：  
需要满足**三个条件**：  
1.左右子树深度差值<=1；  
2.左子树是平衡二叉树；  
3.右子树是平衡二叉树。  
{: .notice} 

> return处的顺序不能写错

```java
//比较深度
public class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null)  return true;
        int left = depth(root.left);
        int right = depth(root.right);
        return Math.abs(left-right) <= 1 && isBalanced(root.left) && isBalanced(root.right);
        //Math.abs(left-right) <= 1不能方法最后，因为此时root指向已经变了
    }
    public int depth(TreeNode root) {
        if (root == null)  return 0;
        return Math.max(depth(root.left), depth(root.right))+1;
    }
}
```

```java
//比较高度
public class Solution {
    public boolean isBalanced(TreeNode root) {
        return dfsHeight(root) != -1;
    }

    public int dfsHeight(TreeNode root) {
        if (root == null)  return 0;
        
        int leftHight = dfsHeight(root.left);
        if (leftHight == -1)  return -1;
        
        int rightHight = dfsHeight(root.right);
        if (rightHight == -1)  return -1;
        
        if (Math.abs(leftHight-rightHight)>1)  return -1;
        
        return Math.max(leftHight, rightHight)+1;
    }
}
```

***

### 27. Remove Element

Example:
Given input array nums = [3,2,2,3], val = 3

Your function should return length = 2, with the first two elements of nums being 2.

思路：  
使用两个指针，都从第一个元素开始移动。  
一个用来检测该值是否为val，另一个用来放入不是val的元素。  
{: .notice} 

> 在for循环里重复写i++
> 最后return m+1，注意要求返回的是长度不是索引

```java
public class Solution {
    public int removeElement(int[] nums, int val) {
        int m = 0;
        for (int i=0; i<nums.length; i++) {
            if (nums[i] != val) {
                nums[m++] = nums[i];
            }
        }
        return m;
    }
}
```

***

## 141. Linked List Cycle

Given a linked list, determine if it has a cycle in it.

> while的条件

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

***

## 532. K-diff Pairs in an Array  

> 对于自己的方法，没有考虑到当k=0的情况。  
> 没有想到其它方法。  

思路：  
当k!=0时，用set来解决。  
当k=0时，验证该元素是否和下一个元素相等但不和前一个相等。  
{: .notice} 

```java
//my solution
public class Solution {
    public int findPairs(int[] nums, int k) {
        Set<Integer> set = new HashSet<>();
        int count = 0;
        Arrays.sort(nums);
        if (k != 0) {
           for (int i=0; i<nums.length; i++) {
                if (set.contains(nums[i]-k)) {
                    count ++;
                    set.remove(nums[i]-k);
                } 
                set.add(nums[i]);
            } 
        } else {
            for (int i=0; i<nums.length-1; i++) {
                if (i==0 && nums[1]==nums[0]) count ++;
                if (i!=0 && nums[i]==nums[i+1] && nums[i]!=nums[i-1])  count++;
            }
        }
        
        return count;
    }
}
```

```java
public class Solution {
    public int findPairs(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k < 0)   return 0;
        
        Map<Integer, Integer> map = new HashMap<>();
        int count = 0;
        for (int i : nums) {
            map.put(i, map.getOrDefault(i, 0) + 1);
        }
        
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (k == 0) {
                //count how many elements in the array that appear more than twice.
                if (entry.getValue() >= 2) {
                    count++;
                } 
            } else {
                if (map.containsKey(entry.getKey() + k)) {
                    count++;
                }
            }
        }
        
        return count;
    }
}
```

```java
public int findPairs(int[] nums, int k) {
    int ans = 0;
    Arrays.sort(nums);
    for (int i = 0, j = 0; i < nums.length; i++) {
        for (j = Math.max(j, i + 1); j < nums.length && (long) nums[j] - nums[i] < k; j++) ;
        if (j < nums.length && (long) nums[j] - nums[i] == k) 
            ans++;
        while (i + 1 < nums.length && nums[i] == nums[i + 1]) 
            i++;
    }
    return ans;
}
```

***

## 237. Delete Node in a Linked List

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
***

## 83. Remove Duplicates from Sorted List

Given a sorted linked list, delete all duplicates such that each element appear only once.

For example,
Given 1->1->2, return 1->2.
Given 1->1->2->3->3, return 1->2->3.

> 无法确认while条件

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


***


## 160. Intersection of Two Linked Lists

Write a program to find the node at which the intersection of two singly linked lists begins.  
For example, the following two linked lists:

A:          a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3

the two iterations will both run for listA.length + listB.length and will reach the intersection point at the same time after switching the pointer.

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

***

## 21. Merge Two Sorted Lists

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

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

***

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

> 递归没有写出

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


## 88. Merge Sorted Array

Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

Note:
You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2. The number of elements initialized in nums1 and nums2 are m and n respectively.

思路：  
i，j指针指向两个数组的最后一个元素，k指向合并后数组的尾部。  
比较i，j指向两个元素的大小，大的就放在k所指向的位置。  
若i先达到了-1，j元素还有剩余，则将nums2剩余的元素都移入nums1中；  
若j先达到了-1，i元素还有剩余，则不需要移动，因为nums1的元素本身就已经排好了。

> while的条件是>=0而不是>0  
> 判断j时直接用while，不需要使用if。  

```java
public class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m-1;
        int j = n-1;
        int k = m+n-1;
        
        while(i>=0 && j>=0) {
            if(nums1[i] > nums2[j])  
                nums1[k--] = nums1[i--];
            else                     
                nums1[k--] = nums2[j--];
        }
        while(j>=0) {
            nums1[k--] = nums2[j--];
        }
    }
}
```
***

## 28.Implement strStr()  

Implement strStr().

Returns the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.


> 没有思路
> 3个if的顺序不能变

> 要有两层循环

```java
public int strStr(String haystack, String needle) {
  for (int i = 0; ; i++) {
    for (int j = 0; ; j++) {
        if (j == needle.length()) 
            return i; //子串遍历完
        if (i + j == haystack.length()) //不能放在第一行，若匹配在最后，则直接返回了-1
            return -1; //字符串遍历完
        if (needle.charAt(j) != haystack.charAt(i + j)) //不能放在第一行，因为要先判断结束条件
            break; 
    }
  }
}
```

***

## 409. Longest Palindrome

```
A-Z: 65-90
a-z: 97-122
大小写字母: 128
```

思路：  
哈希表存储所有字母个数，计算>0的字母个数和奇数个字母的个数。  
{: .notice} 

```java
//my solution
public class Solution {
    public int longestPalindrome(String s) {
        if (s == null || s.length()==0)  return 0;
        
        int[] hash = new int[128];
        for (char c : s.toCharArray()) {
            hash[c] ++;
        }
        int count = 0, odd = 0; 
        
        for (int i=65; i<123; i++) {
            if (hash[i] > 0) {
                if(hash[i]%2 == 1)
                    odd ++;
                count += hash[i]/2*2;
            }  
        }
        
        if (odd>0)  return count+1;
        
        return count;
    }
}
```

思路：  
用HashSet，若包含字母，就将count+1，并将字母移除，以此来记录有多少对相同字母。  
若Set中没有奇数个字母，该Set为空，所以不为空即代表含有奇数个字母，因此数量要+1。  
{: .notice} 

```java
public int longestPalindrome(String s) {
    if(s==null || s.length()==0) return 0;
    HashSet<Character> hs = new HashSet<Character>();
    int count = 0;
    for (int i=0; i<s.length(); i++) {
        if (hs.contains(s.charAt(i))) {
            hs.remove(s.charAt(i));
            count++;
        } else {
            hs.add(s.charAt(i));
        }
    }
    if (!hs.isEmpty()) return count*2+1;
    return count*2;
}
```

***

## 500. Keyboard Row

> 没有思路

```java
//my solution
public class Solution {
    public String[] findWords(String[] words) {
        List<String> list = new ArrayList<>();
        int[] hash = new int[26];
        String[] rows = {"qwertyuiop","asdfghjkl","zxcvbnm"};
        
        for (int i=0; i<rows.length; i++) {
            for (char c : rows[i].toCharArray()) {
                hash[c-'a'] += i;
            }
        }
        
        for (String word : words) {
            char first = Character.toLowerCase(word.charAt(0));
            for (int i=1; i<=word.length(); i++) {
                if (i == word.length()) {
                   list.add(word);
                   break;
                }
                char c = Character.toLowerCase(word.charAt(i));
                if( hash[c-'a'] != hash[first-'a'])
                    break;
            }
            
        }
        // String[] res = new String[list.size()];
        // for (int i=0; i<list.size(); i++) {
        //     res[i] = list.get(i);
        // }
        // return res;
        return list.toArray(new String[0]);
    }
}
```***

## 202. Happy Number

> 没有思路
> 当回归其本身时，要考虑到环的检测方式
> 不会写程序
> while后面要加;
> 若要使用Math.pow()，要注意返回的是double类型

思路：  
检测链表是否有环  
{: .notice}   

```java
public class Solution {
    public boolean isHappy(int n) {
        int fast=n, slow=n;
        do {
            slow = squareSum(slow);
            fast = squareSum(squareSum(fast));
        } while (slow != fast);
        
        if (slow == 1)  return true;
        return false;
    }

    public int squareSum(int n) {
        int sum =0, temp;
        while (n > 0) {
            temp = n % 10;
            sum += temp * temp;
            n /= 10;
        }
        return sum;
    }
}
```

```java
// 改进：若是happy number，fast会先达到1，这样不需要继续重复计算等到low达到1
public class Solution {
    public boolean isHappy(int n) {
        int fast=n, slow=n;
        do {
            slow = squareSum(slow);
            fast = squareSum(squareSum(fast));
            if (fast == 1)  return 1;
        } while (slow != fast);
        return false;
    }

    public int squareSum(int n) {
        int sum =0, temp;
        while (n > 0) {
            temp = n % 10;
            sum += temp * temp;
            n /= 10;
        }
        return sum;
    }
}
```

> 注意每次计算一个数的squareSum时要将其清零。  

思路：  
Set不能放重复的元素，所以可以作为while的条件  
{: .notice} 

```java
public boolean isHappy(int n) {
    Set<Integer> inLoop = new HashSet<Integer>();
    int squareSum, remain;
    while (inLoop.add(n)) {
        squareSum = 0;
        while (n > 0) {
            remain = n%10;
            squareSum += remain*remain;
            n /= 10;
        }
        if (squareSum == 1)
            return true;
        else
            n = squareSum;

    }
    return false;

}
```

***

## 219. Contains Duplicate II

Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that nums[i] = nums[j] and the absolute difference between i and j is at most k.

思路：  
将元素加入到set中，并不断剔除前面与该元素的距离大于k的元素。  
若遇到重复元素加入到set中，返回true。  
{: .notice} 

```java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    Set<Integer> set = new HashSet<Integer>();
    for (int i = 0; i < nums.length; i++) {
        if(i > k) 
            set.remove(nums[i-k-1]);
        if(!set.add(nums[i])) 
            return true;
    }
    return false;
}
```

```java
//my solution map存元素和索引
public class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i=0; i<nums.length; i++) {
            if (map.containsKey(nums[i])) {
                int d = i - map.get(nums[i]);
                if (d <= k)  return true;
            }
            map.put(nums[i], i);
        }
        return false;
    }
}
```***

## 290. Word Pattern(一对一)

> 还是写出了错误的自己的方法，没有想到其它方法。  

思路：  
map.put返回的是上一次put的value。若是第一次put则返回null。  
HashMap不使用泛型。  
{: .notice} 

注：  
split方法中，若“ ”在最前面，则含有长度为1的字符串；  
若“ ”在最后面，则可忽略。  
{: .notice} 


```java
public boolean wordPattern(String pattern, String str) {
    String[] words = str.split(" ");
    if (words.length != pattern.length()) return false;
   
    Map map = new HashMap();
   
    for (Integer i=0; i<words.length; ++i)
        if (map.put(pattern.charAt(i), i) != map.put(words[i], i))
            return false;
    return true;
}
```

```java
//my wrong slolution
public class Solution {
    public boolean wordPattern(String pattern, String str) {
        Map<Character, String> map = new HashMap<>();
        String[] strs = str.split(" ");
        
        if (strs.length != pattern.length())  return false;
        
        for (int i=0; i<strs.length; i++) {
            char c = pattern.charAt(i);
            if (!map.containsKey(c)) {
                map.put(c, strs[i]);
            } else {
                if (!map.get(c).equals(strs[i]))
                    return false;
            }
        }
        return true;
    }
}
```

```
"abba"
"dog dog dog dog"
会出错
```

```java
//correct my solution
public class Solution {
    public boolean wordPattern(String pattern, String str) {
        Map<Character, String> map = new HashMap<>();
        String[] strs = str.split(" ");
        
        if (strs.length != pattern.length())  return false;
        
        for (int i=0; i<strs.length; i++) {
            char c = pattern.charAt(i);
            if (!map.containsKey(c)) {
                if(map.containsValue(strs[i])) // <----- 保证一对一方式
                    return false;
                map.put(c, strs[i]);
            } else {
                if (!map.get(c).equals(strs[i])) //equals而不是==
                    return false;
            }
        }
        return true;
    }
}
```

## 205. Isomorphic Strings

若用第一种方法，出现 abc abc的情况时也会判决为false，但可以把另一个键值的类型改变，这样就不会发生冲突。  

```java
if (map.put(pattern.charAt(i), i) != map.put(String.valueOf(str.charAt(i)), i))
```

***

## 496. Next Greater Element I

> 没思路

> 没思路

思路：  
将nums遍历加入栈中。若栈为空或者栈顶元素小于nums[i]，就将nums[i]入栈。  
若栈顶元素大于nums[i]，就把所有小于它的元素都出栈并记录在map中。  
map中记录的就是比元素大的下个元素。  
{: .notice} 

```java
public int[] nextGreaterElement(int[] findNums, int[] nums) {
    Map<Integer, Integer> map = new HashMap<>(); // map from x to next greater element of x
    Stack<Integer> stack = new Stack<>();
    
    for (int num : nums) {
        while (!stack.isEmpty() && stack.peek()<num)
            map.put(stack.pop(), num);
        
        stack.push(num);
    }   
    
    for (int i = 0; i < findNums.length; i++)
        findNums[i] = map.getOrDefault(findNums[i], -1);
   
    return findNums;
}
```