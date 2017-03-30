---
layout: post
title:  "LeetCode-One Star"
date:   2017-03-18
excerpt: ""
project: true
tag:

comments: false
---



***

### 345. Reverse Vowels of a String

Write a function that takes a string as input and reverse only the vowels of a string.

Example 1:
Given s = "hello", return "holle".

Example 2:
Given s = "leetcode", return "leotcede".

思路：  
1.两个指针分别指向字符串的首和尾。  
两个指针分别向中间移动，遇到元音就进行交换，直到两个指针相遇。

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


### 125. Valid Palindrome

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

For example,
"A man, a plan, a canal: Panama" is a palindrome.
"race a car" is not a palindrome.

内循环要注意：若是while则要有start<end条件，否则会无限循环。  
若是if则不需要写此条件。
{: .notice} 


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

## 88. Merge Sorted Array

Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

Note:
You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2. The number of elements initialized in nums1 and nums2 are m and n respectively.

思路：  
i，j指针指向两个数组的最后一个元素，k指向合并后数组的尾部。  
比较i，j指向两个元素的大小，大的就放在k所指向的位置。  
若i先达到了-1，j元素还有剩余，则将nums2剩余的元素都移入nums1中；  
若j先达到了-1，i元素还有剩余，则不需要移动，因为nums1的元素本身就已经排好了。

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

## 1. Two Sum

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

思路：  
遍历数组，若target与元素的差不在map中，就把该元素加入map中。  
若存在，则将这两个元素的下标赋值到res数组中并返回。  
{: .notice} 

```java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] res = new int[2];
        Map<Integer,Integer> map = new HashMap<Integer,Integer>();
        for(int i=0; i<nums.length; i++) {
            if(map.containsKey(target-nums[i])) {
                res[0] = map.get(target-nums[i]);
                res[1] = i;
            } 
            map.put(nums[i], i);
        }
       return res;
    }
}
```

***

## 167. Two Sum II - Input array is sorted

Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution and you may not use the same element twice.

Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2


思路：  
sums[i] + sums [j] = target   
max + min = v  
如果v比target小，而此时min元素已经是最小值，所以指向目前最大值的指针应该左移；  
如果v比target大，而此时max元素已经是最大值，所以指向目前最小值的指针应该右移； 

```java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] res = new int[2];
        int i = 0;
        int j = nums.length - 1;
       
        while (i<j) {
            int sum = nums[i] + nums[j];
            if (sum == target) {
                res[0] = i+1;
                res[1] = j+1;
                break;
            } else if (sum > target) {
                j--;
            } else
                i++;
        } 
        return res;
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

## Implement strStr()  

Implement strStr().

Returns the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

思路：  
1.i，j两个指针分别指向字符串和子串的首字母。  
2.当找到两个字符串首字母相同的位置时，对子串进行循环遍历。  
{: .notice} 

```java
public int strStr(String haystack, String needle) {
  for (int i = 0; ; i++) {
    for (int j = 0; ; j++) {
      if (j == needle.length()) return i; //子串遍历完
      if (i + j == haystack.length()) return -1; //字符串遍历完
      if (needle.charAt(j) != haystack.charAt(i + j)) break; //两个字符串比较的字母不相等则跳出循环
    }
  }
}
```

***

### 27. Remove Element

Given an array and a value, remove all instances of that value in place and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

Example:
Given input array nums = [3,2,2,3], val = 3

Your function should return length = 2, with the first two elements of nums being 2.


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

## 26. Remove Duplicates from Sorted Array  

Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

```java
public class Solution {
    public int removeDuplicates(int[] nums) {
        int k = 1;
        for (int i=1; i<nums.length; i++) {
            if (nums[i] != nums[i-1]) {
                nums[k++] = nums[i];
            } 
        }
        return k;
    }
}
```

```java
public int removeDuplicates(int[] nums) {
    int i = 0;
    for (int n : nums)
        if (i == 0 || n > nums[i-1])
            nums[i++] = n;
    return i;
}
```

***

## 141. Linked List Cycle

Given a linked list, determine if it has a cycle in it.

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

Given an array of integers and an integer k, you need to find the number of unique k-diff pairs in the array. Here a k-diff pair is defined as an integer pair (i, j), where i and j are both numbers in the array and their absolute difference is k.

Example 1:
Input: [3, 1, 4, 1, 5], k = 2
Output: 2
Explanation: There are two 2-diff pairs in the array, (1, 3) and (3, 5).
Although we have two 1s in the input, we should only return the number of unique pairs.

```java
//my solution
public class Solution {
    public int findPairs(int[] nums, int k) {
        Arrays.sort(nums);
        Set<Integer> set = new HashSet<Integer>();
        int pairs = 0;
        
        if (nums == null || nums.length == 0 || k < 0)   return 0;
        
        if (k == 0) {
            int temp;
            if (nums[0] == 0) 
                temp = nums[0] + 1;
            else  
                temp = nums[0] * (-1);
                
            for (int i=0; i< nums.length-1; i++) {
                if (nums[i] == nums[i+1] && nums[i] != temp) {
                    pairs ++;
                    temp = nums[i];
                }
            }
        } else {
            for (int i=0; i<nums.length; i++) {
                if (set.contains(nums[i]-k)) {
                    set.remove(nums[i]-k);
                    pairs++;
                } 
                set.add(nums[i]);
            }
        }
        
        return pairs;
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

Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.

Supposed the linked list is 1 -> 2 -> 3 -> 4 and you are given the third node with value 3, the linked list should become 1 -> 2 -> 4 after calling your function.

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

```java
//??
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


```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    //boundary check
    if(headA == null || headB == null) return null;
    
    ListNode a = headA;
    ListNode b = headB;
    
    //if a & b have different len, then we will stop the loop after second iteration
    while( a != b){
        //for the end of first iteration, we just reset the pointer to the head of another linkedlist
        a = a == null? headB : a.next;
        b = b == null? headA : b.next;    
    }
    
    return a;
}
```

***
***

## 21. Merge Two Sorted Lists

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

```java
class Solution {
public:
    ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
        if(l1 == NULL) return l2;
        if(l2 == NULL) return l1;
        
        if(l1->val < l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l2->next, l1);
            return l2;
        }
    }
};
```

```java
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode a = l1;
        ListNode b = l2;
        
        if (l1 == null)  return l2;
        if (l2 == null)  return l1;
        
        while (a!=null && b!=null) {
            if (a.val <= b.val) {
                ListNode t = a.next;
                a.next = b;
                a = t;
            } else {
                ListNode t = b.next;
                b.next = a;
                b = t;
            } 
            
        }
        return l1.val < l2.val ? l1 : l2;
    }
}
```

***

## 203. Remove Linked List Elements

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
    if (head == null)   return null;  //判断该结点是否为空
    head.next = removeElements(head.next, val); //得到下一个结点的指向
    return head.val == val ? head.next : head; //判断是否需要删除该结点
}
```

***

## 206. Reverse Linked List


```java
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
public ListNode reverseList(ListNode head) {
    /* recursive solution */
    return reverseListInt(head, null);
}

private ListNode reverseListInt(ListNode head, ListNode newHead) {
    if (head == null)
        return newHead;
    ListNode next = head.next;
    head.next = newHead;
    return reverseListInt(next, head);
}
```

***

## 136. Single Number

Given an array of integers, every element appears twice except for one. Find that single one.

```java
//my solution
public class Solution {
    public int singleNumber(int[] nums) {
        Arrays.sort(nums);
        if (nums.length == 1) 
            return nums[0];
        int i;
        for (i=1; i<nums.length; i=i+2) {
            if (nums[i] != nums[i-1])
                return nums[i-1];
        }
        
        return nums[nums.length-1];
    }
}
```

思路：  
亦或运算：两个操作数的位中，相同则结果为0，不同则结果为1。  
所以数和0亦或运算为这个数的本身。  
{: .notice}  


```java
int singleNumber(int A[], int n) {
    int result = 0;
    for (int i = 0; i<n; i++)
    {
        result ^= A[i];
    }
    return result;
}
```

***

## 1. Two Sum

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].


```java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
       int[] res = new int[2];
       Map<Integer, Integer> map = new HashMap<Integer, Integer>();
       
       for (int i=0; i<nums.length; i++) {
           if (map.containsKey(target-nums[i])) {
               res[0] = map.get(target-nums[i]);
               res[1] = i;
           } 
           map.put(nums[i], i);
       }
       return res;
    }
}
```

***

## 463. Island Perimeter


```java
//my solution 浪费空间，舍弃
public class Solution {
    public int islandPerimeter(int[][] grid) {
        int count = 0;
        int col = grid[0].length;
        int row = grid.length;
        int[][] temp = new int[row+1][col+1];
        
        for (int i=0; i<row; i++) {
            for (int j=0; j<col; j++) {
                temp[i][j] = grid[i][j];
            }
        }
        
        for (int i=0; i<row; i++) {
            for (int j=0; j<col; j++) {
                if (temp[i][j] == 1) {
                    count += 4;
                    if (temp[i+1][j] == 1) {
                        count -= 2;
                    }
                    if (temp[i][j+1] == 1) {
                        count -= 2;
                    }
                }
            }
        }
        
        return count;
    }
}
```

思路：  
把所有有1的格子都赋值为4。(一个格子有4条边)  
检测每个格子左边和上边的格子是否为1，若是则-2(有一个相邻的格子就减少2个边)。  
长方形最上边的行和最左边的列不进行检测  
i=0则不进行上检测，j=0则不进行左检测  
{: .notice} 

```java
//和我的思路相同，但不浪费空间
public static int islandPerimeter(int[][] grid) {
    if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
    int result = 0;
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[0].length; j++) {
            if (grid[i][j] == 1) {
                result += 4;
                if (i > 0 && grid[i-1][j] == 1) result -= 2; //i=0则不进行上检测
                if (j > 0 && grid[i][j-1] == 1) result -= 2; //j=0则不进行左检测
            }
        }
    }
    return result;
}
```

```java
//另一种类似思路
public class Solution {
    public int islandPerimeter(int[][] grid) {
        int islands = 0, neighbours = 0;

        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[i].length; j++) {
                if (grid[i][j] == 1) {
                    islands++; // count islands
                    if (i < grid.length - 1 && grid[i + 1][j] == 1) neighbours++; // count down neighbours
                    if (j < grid[i].length - 1 && grid[i][j + 1] == 1) neighbours++; // count right neighbours
                }
            }
        }

        return islands * 4 - neighbours * 2;
    }
}
```

***

## 447. Number of Boomerangs

Given n points in the plane that are all pairwise distinct, a "boomerang" is a tuple of points (i, j, k) such that the distance between i and j equals the distance between i and k (the order of the tuple matters).

Find the number of boomerangs. You may assume that n will be at most 500 and coordinates of points are all in the range [-10000, 10000](inclusive).

Example:
Input:
[[0,0],[1,0],[2,0]]

Output:
2

Explanation:
The two boomerangs are [[1,0],[0,0],[2,0]] and [[1,0],[2,0],[0,0]]

思路：  
每一个点都和其它点进行比较，计算距离放入map中。  
根据map的value值对数量进行计算。n*(n-1)即是角个数公式。  
{: .notice} 

```java
public class Solution {
    public int numberOfBoomerangs(int[][] points) {
        Map<Integer, Integer> map = new HashMap<>();
        int count = 0;
        for (int i=0; i<points.length; i++) {
            for (int j=0; j<points.length; j++) {
                if (i == j)    continue;
                int d = getDistance(points[i], points[j]);
                map.put(d, map.getOrDefault(d, 0)+1);
            }
            for (int n : map.values()) {
                count += n*(n-1);
            }
            map.clear();
        }
        return count;
    }
    public int getDistance(int[] a, int[] b) {
        int dx = a[0] - b[0];
        int dy = a[1] - b[1];
        return dx*dx + dy*dy;
    }
}
```

```java
if (map.containsKey(d)) {
    map.put(d, map.get(d)+1);
} else 
    map.put(d, 1);
//可以简化成：
map.put(d, map.getOrDefault(d, 0) + 1);
```


***

## 



```java

```

***

## 


```java

```

***

