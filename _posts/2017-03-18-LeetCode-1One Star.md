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
        if(l1 == null){
            return l2;
        }
        if(l2 == null){
            return l1;
        }
        
        ListNode mergeHead;
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
    if (head == null)   return null;  //判断该结点是否为空(递归结束标志)
    head.next = removeElements(head.next, val); //得到下一个结点的指向
    return head.val == val ? head.next : head; //判断是否需要删除该结点
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

```java
//recursive solution
public ListNode reverseList(ListNode head) {
    return reverseListInt(head, null);
}

private ListNode reverseListInt(ListNode head, ListNode newHead) {
    if (head == null) return newHead; //当余下未反转的链为空时，返回反转后的链的头结点
    ListNode next = head.next; //保存下一个结点位置
    head.next = newHead; //head指向新建的revers链
    return reverseListInt(next, head); //余下的进行reverse，并传把余下链的头结点和反转后链的头结点位置传过去
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
***

## 438. Find All Anagrams in a String

Given a string s and a non-empty string p, find all the start indices of p's anagrams in s.

Strings consists of lowercase English letters only and the length of both strings s and p will not be larger than 20,100.

The order of output does not matter.

Example 1:

```
Input:
s: "cbaebabacd" p: "abc"

Output:
[0, 6]

Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

思路：  
1.将p子串的字母放入Hash表中，设count值为p子串长度。  
2.设置左右两个指针，表示滑窗的左右边界。每一次循环left固定而right右移。  
3.如果Hash表包含right指向的元素，就将Hash表内对应的字母数量-1，然后right右移。 
4.若滑窗里的所有字母都和Hash表吻合，即count=0时，将left指针位置记录在ArrayList中。  
5.如果滑窗的宽度达到了p的长度，判断left指向的元素是否在Hash表中，若在将Hash表中该元素数量恢复。  
{: .notice} 


```java
public class Solution {
    public List<Integer> findAnagrams(String s, String p) {
    
    if (s == null || s.length() == 0 || p == null || p.length() == 0) return list;
 
    List<Integer> list = new ArrayList<>();
    int[] hash = new int[256]; //ASCII码前128个字符包含小写字母
    
    // 将p字符放入hash表中
    for (char c : p.toCharArray()) { 
        hash[c]++;
    }

    int left = 0, right = 0, count = p.length();
    
    while (right < s.length()) {
      
        if (hash[s.charAt(right)] >= 1) {
            count--;
        }
        hash[s.charAt(right)]--; //这里不可以挪到上边的if里，因为下边要进行第5步的判断，left是hash中元素就不会为负数
        right++;
        
        if (count == 0) {
            list.add(left);
        }

        if (right - left == p.length() ) {
            if (hash[s.charAt(left)] >= 0) {
                count++;
            }
            hash[s.charAt(left)]++; //这里不可以挪到上边的if里，因为若窗口里有重复的属于p的元素，就会被多减一次
            left++;
        }
    }
    return list;
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

> 没有思路

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

***

## 167. Two Sum II - Input array is sorted

Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution and you may not use the same element twice.

Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2


思路：(注意数组已经排好序了)  
1.i，j指针分别指向数组的首尾两个元素。  
2.计算i和j指向的元素的和。  
3.若和sum比target小，则j左移；若大则i右移。  
{: .notice} 

> 没有想到第一种方法
> 容易分不清sum > target时究竟i、j哪个要移动

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

```java
//可用和1.Two Sum一样的方法
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        int[] res = new int[2];
        
        for (int i=0; i<nums.length; i++) {
            if (map.containsKey(target-nums[i])) {
                res[0] = map.get(target-nums[i])+1;
                res[1] = i+1;
                return res;
            } 
            map.put(nums[i], i);
        }
        return res;
    }
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

> 没有思路
> 3个if的顺序不能变

```java
public int strStr(String haystack, String needle) {
  for (int i = 0; ; i++) {
    for (int j = 0; ; j++) {
        if (j == needle.length()) 
            return i; //子串遍历完
        if (i + j == haystack.length()) //不能放在第一行，若匹配在最后，则直接返回了-1
            return -1; //字符串遍历完
        if (needle.charAt(j) != haystack.charAt(i + j)) //不能放在第一行，因为要先判断结束条件
            break; //两个字符串比较的首字母不相等则跳出循环
    }
  }
}
```

***

## 409. Longest Palindrome

Given a string which consists of lowercase or uppercase letters, find the length of the longest palindromes that can be built with those letters.

This is case sensitive, for example "Aa" is not considered a palindrome here.

```java
//my solution
public class Solution {
    public int longestPalindrome(String s) {
        if (s == null || s.length()==0)  return 0;
        
        int[] hash = new int[128];
        for (char c : s.toCharArray()) {
            hash[c] ++;
        }
        int count = 0; 
        int odd = 0;
        
        for (int i=65; i<123; i++) {
            if (hash[i] > 0) {
                if(hash[i]%2 == 1)
                    odd ++;
                count += hash[i]/2*2;
            }  
        }
        
        if (odd>0) {
            return count+1;
        }
        return count;
    }
}
```

```java
public int longestPalindrome(String s) {
        if(s==null || s.length()==0) return 0;
        HashSet<Character> hs = new HashSet<Character>();
        int count = 0;
        for(int i=0; i<s.length(); i++){
            if(hs.contains(s.charAt(i))){
                hs.remove(s.charAt(i));
                count++;
            }else{
                hs.add(s.charAt(i));
            }
        }
        if(!hs.isEmpty()) return count*2+1;
        return count*2;
}
```

```java
public int longestPalindrome(String s) {
    int[] lowercase = new int[26];
    int[] uppercase = new int[26];
    int res = 0;
    for (int i = 0; i < s.length(); i++){
        char temp = s.charAt(i);
        if (temp >= 97) lowercase[temp-'a']++;
        else uppercase[temp-'A']++;
    }
    for (int i = 0; i < 26; i++){
        res+=(lowercase[i]/2)*2;
        res+=(uppercase[i]/2)*2;
    }
    return res == s.length() ? res : res+1;
        
}
```

***

## 389. Find the Difference

Given two strings s and t which consist of only lowercase letters.

String t is generated by random shuffling string s and then add one more letter at a random position.

Find the letter that was added in t.

```java
public char findTheDifference(String s, String t) {
    char c = 0; // char初试化为0
    for (int i = 0; i < s.length(); ++i) {
        c ^= s.charAt(i); // 0和数亦或为那个数
    }
    for (int i = 0; i < t.length(); ++i) {
        c ^= t.charAt(i);
    }
    return c;
}
```

```java
public char findTheDifference(String s, String t) {
    int n = t.length();
    char c = t.charAt(n - 1);
    for (int i = 0; i < n - 1; i++) {
        c ^= s.charAt(i);
        c ^= t.charAt(i);
    }
    return c;
}
```

```java
public char findTheDifference(String s, String t) {
    int charCode = t.charAt(s.length());
    for (int i = 0; i < s.length(); ++i) {
          charCode -= (int)s.charAt(i);
          charCode += (int)t.charAt(i); 
    }
    return (char)charCode;
}
```
***

## 

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
                if (i==word.length()) {
                   list.add(word);
                   break;
                }
                char c = Character.toLowerCase(word.charAt(i));
                if( hash[c-'a'] != hash[first-'a'])
                    break;
            }
            
        }
        String[] res = new String[list.size()];
        for (int i=0; i<list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```

***

## 202. Happy Number

Write an algorithm to determine if a number is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.

思路：  
检测链表是否有环  
{: .notice}   

```java
public class Solution {
    public boolean isHappy(int n) {
        int fast=n, slow=n;
        do {
            slow = squareSum(slow);
            fast = squareSum(fast);
            fast = squareSum(fast);
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

思路：  
Set不能放重复的元素  
{: .notice} 

```java
public boolean isHappy(int n) {
    Set<Integer> inLoop = new HashSet<Integer>();
    int squareSum,remain;
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


```java
//???
public boolean containsNearbyDuplicate(int[] nums, int k) {
    Set<Integer> set = new HashSet<Integer>();
    for(int i = 0; i < nums.length; i++) {
        if(i > k) set.remove(nums[i-k-1]);
        if(!set.add(nums[i])) return true;
    }
    return false;
}
```

```java
//my solution
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
```

***

## 290. Word Pattern

Given a pattern and a string str, find if str follows the same pattern.

思路：  
map.put返回的是上一次put的value。若是第一次put则返回null。  
{: .notice} 

```java
public boolean wordPattern(String pattern, String str) {
    String[] words = str.split(" ");
    if (words.length != pattern.length())
        return false;
    Map index = new HashMap();
   
    for (Integer i=0; i<words.length; ++i)
        if (index.put(pattern.charAt(i), i) != index.put(words[i], i))
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
                if(map.containsValue(strs[i])) // <-----
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
不能用第一种方法了，因为会出现 a a的情况

***

## 496. Next Greater Element I

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
        while (!stack.isEmpty() && stack.peek() < num)
            map.put(stack.pop(), num);
        stack.push(num);
    }   
    for (int i = 0; i < findNums.length; i++)
        findNums[i] = map.getOrDefault(findNums[i], -1);
    return findNums;
}
```

***

## 58. Length of Last Word

Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word in the string.

If the last word does not exist, return 0.

Note: A word is defined as a character sequence consists of non-space characters only.

For example, 
Given s = "Hello World",
return 5.


```java
public class Solution {
    public int lengthOfLastWord(String s) {      
        String[] strs = s.split(" ");
        if (strs.length == 0) return 0;  // strs若都是由“ ”组成，则split后length为0
        String end = strs[strs.length-1];
        return end == " " ? 0 : end.length();
    }
}
```

```java
public int lengthOfLastWord(String s) {
    s = s.trim();
    int lastIndex = s.lastIndexOf(' ') + 1;
    return s.length() - lastIndex;        
}
```

***

## 20. Valid Parentheses

```java
public class Solution {
    public boolean isValid(String s) {
       
       Stack<Character> stack = new Stack<>();
       
       for (char c : s.toCharArray()) {
            if (c == '[')  stack.push(']');
            else if (c == '(')  
                stack.push(')');
            else if (c == '{')  
                stack.push('}');
            else if (stack.isEmpty() || stack.pop()!=c)  
                return false;
       }
        return stack.isEmpty();
    }
}
```

***

## 155. Min Stack  

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

push(x) -- Push element x onto stack.
pop() -- Removes the element on top of the stack.
top() -- Get the top element.
getMin() -- Retrieve the minimum element in the stack.

思路：  
min记录当前栈中最小值。  
入栈的是与下面最小元素的差值。  
注：不要把top和x弄混。  
{: .notice} 

```java
public class MinStack {
    long min;
    Stack<Long> stack;

    public MinStack(){
        stack=new Stack<>();
    }
    
    public void push(int x) {
        if (stack.isEmpty()){
            stack.push(0L);
            min=x;
        }else{
            stack.push(x-min);//Could be negative if min value needs to change
            if (x<min) min=x;
        }
    }

    public void pop() {
        if (stack.isEmpty()) return;
        
        long pop=stack.pop();
        
        if (pop<0)  min=min-pop;//If negative, increase the min value
        
    }

    public int top() {
        long top=stack.peek();
        if (top>0){
            return (int)(top+min);
        }else{
           return (int)(min);
        }
    }

    public int getMin() {
        return (int)min;
    }
}
```

***

## 

```java

```

***

## 

```java

```









