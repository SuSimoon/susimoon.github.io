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

注：  
split方法中，若“ ”在最前面，则含有长度为1的字符串；  
若“ ”在最后面，则可忽略。  
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

## 225. Implement Stack using Queues

Implement the following operations of a stack using queues.

思路：  
在入队时，将队列的顺序倒转，这样就可以实现后进先出。  
{: .notice} 

```java
class MyStack 
{
    Queue<Integer> queue;
    
    public MyStack()
    {
        this.queue=new LinkedList<Integer>();
    }
    
    // Push element x onto stack.
    public void push(int x) 
    {
       queue.add(x);
       for(int i=0;i<queue.size()-1;i++)
       {
           queue.add(queue.poll());
       }
    }

    public void pop() 
    {
        queue.poll();
    }

    public int top() 
    {
        return queue.peek();
    }

    public boolean empty() 
    {
        return queue.isEmpty();
    }
}
```

***

## 232. Implement Queue using Stacks

input栈是栈顺序，output栈是队列顺序。  
所以放元素时就放到input中，取元素就查找top时就在output中操作。  
{: .notice} 

```java
class MyQueue {

    Stack<Integer> input = new Stack();
    Stack<Integer> output = new Stack();
    
    public void push(int x) {
        input.push(x);
    }

    public int pop() {
        peek();
        return output.pop();
    }

    public int peek() {
        if (output.empty())
            while (!input.empty())
                output.push(input.pop());
        return output.peek();
    }

    public boolean empty() {
        return input.empty() && output.empty();
    }
}
```

```java
//my wrong method
//stack和temp的size是变化的，所以不能用这种遍历方式
public void push(int x) {
    for (int i=0; i<stack.size(); i++) {
        temp.push(stack.pop());
    }
    stack.push(x);
    for (int i=0; i<temp.size(); i++) {
        stack.push(temp.pop());
    }
}
```

```java
//我的耗时方法
//每次push一个元素时将栈中元素颠倒顺序
public class MyQueue {
    Stack<Integer> stack, temp;

    public MyQueue() {
        this.stack = new Stack<>();
        this.temp = new Stack<>();
    }
    
    public void push(int x) {
        while (!stack.isEmpty()) {
            temp.push(stack.pop());
        }
        stack.push(x);
        while (!temp.isEmpty()) {
            stack.push(temp.pop());
        }
    }
    
    public int pop() {
        return stack.pop();
    }
    
    public int peek() {
        return stack.peek();
    }
    
    public boolean empty() {
        return stack.isEmpty();
    }
}

```

***

## 520. Detect Capital

Given a word, you need to judge whether the usage of capitals in it is right or not.

We define the usage of capitals in a word to be right when one of the following cases holds:

All letters in this word are capitals, like "USA".
All letters in this word are not capitals, like "leetcode".
Only the first letter in this word is capital if it has more than one letter, like "Google".


```java
public boolean detectCapitalUse(String word) {
    return word.equals(word.toUpperCase()) || 
           word.equals(word.toLowerCase()) ||
           Character.isUpperCase(word.charAt(0)) && 
           word.substring(1).equals(word.substring(1).toLowerCase());
}
```

```java
public boolean detectCapitalUse(String word) {
    int numUpper = 0;
    for (int i=0;i<word.length();i++) {
        if (Character.isUpperCase(word.charAt(i))) numUpper++;
    }
    if (numUpper == 1) return Character.isUpperCase(word.charAt(0));
    return numUpper == 0 || numUpper == word.length();
}
```

```java
public boolean detectCapitalUse(String word) {
    return word.matches("[A-Z]+|[a-z]+|[A-Z][a-z]+");
}
```

```java
//my solution
public class Solution {
    public boolean detectCapitalUse(String word) {
        char first = word.charAt(0);
        String sub = word.substring(1,word.length());
        if (Character.isUpperCase(first)) {  //Upper
            if(isSubUpper(sub))  return true;
            else if (isSubLower(sub))  return true;
            else return false;
        } else {
            if (isSubLower(sub))  return true;
            else return false;
        }
    }
    public boolean isSubUpper(String str) {
        if (str.length() == 0)  return true;
        char first = str.charAt(0);
        if(!Character.isUpperCase(first))  return false;
        String sub = str.substring(1,str.length());
        if (isSubUpper(sub))  return true;
        else return false;
    }
    public boolean isSubLower (String str) {
        if (str.length() == 0)  return true;
        char first = str.charAt(0);
        if(!Character.isLowerCase(first))  return false;
        String sub = str.substring(1,str.length());
        if (isSubLower(sub))  return true;
        else return false;
    }
}
```


***

## 459. Repeated Substring Pattern

Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. 

Example 1:

```
Input: "abab"
Output: True
Explanation: It's the substring "ab" twice.
```

Example 2:

```
Input: "aba"
Output: False
```

思路： 
首先找到可以被len整除的子串的最大长度。   
检测该子串是否和其它子串相等。  
若不等则减小字符串的长度，继续检测。  
{: .notice} 

```java
public class Solution {
    public boolean repeatedSubstringPattern(String s) {
        int len = s.length();
        for (int i=len/2; i>0; i--) {
            if (len % i == 0) {
                int m = len / i;
                String sub = s.substring(0, i);
                int j = 0;
                for (j=1; j<m; j++) {
                    if (!sub.equals(s.substring(i*j, i*j+i)))  break;
                }
                if (j == m) return true;
            }
        }
        return false;
    }
}
```


***

## 434. Number of Segments in a String

Count the number of segments in a string, where a segment is defined to be a contiguous sequence of non-space characters.

Please note that the string does not contain any non-printable characters.

Example:

```
Input: "Hello, my name is John"
Output: 5
```

没有考虑到很多空格连起来的情况

```java
//my solution
public class Solution {
    public int countSegments(String s) {
        int count = 0;
        String[] strs = s.split(" ");
        for (int i=0; i<strs.length; i++) {
            if (strs[i].length() != 0) count++;
        }
        return count;
    }
}
```

```java
public int countSegments(String s) {
    int res=0;
    for(int i=0; i<s.length(); i++)
        if(s.charAt(i)!=' ' && (i==0 || s.charAt(i-1)==' '))
            res++;        
    return res;
}
```


***

## 14. Longest Common Prefix

最长公共前缀

```java
public class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length == 0) return "";
        String pre = strs[0];
        for (int i=1; i<strs.length; i++) {
            while (strs[i].indexOf(pre) != 0) 
                pre = pre.substring(0, pre.length()-1);
        }
        return pre;
    }
}
```

***

## 383. Ransom Note

Given an arbitrary ransom note string and another string containing letters from all the magazines, write a function that will return true if the ransom note can be constructed from the magazines ; otherwise, it will return false.

Each letter in the magazine string can only be used once in your ransom note.

Note:
You may assume that both strings contain only lowercase letters.

canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true


```java
//my solution
public class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        Map<Character, Integer> map = new HashMap<>();
        for (int i=0; i<magazine.length(); i++) {
            char c = magazine.charAt(i);
            map.put(c, map.getOrDefault(c, 0)+1);
        }
        for (int i=0; i<ransomNote.length(); i++) {
            char c = ransomNote.charAt(i);
            if (map.containsKey(c) && map.get(c)>0)  
                map.put(c, map.get(c)-1);
            else  
                return false;
            
        }
        return true;
    }
}
```

```java
public class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] hash = new int[26];
        for(char c : magazine.toCharArray()) {
            hash[c-'a']++;
        }
        for(char c : ransomNote.toCharArray()) {
            if(--hash[c-'a']<0)  return false;
        }
        return true;
    }
}
```

***

## 38. Count and Say

```java
public class Solution {
    public String countAndSay(int n) {
        String result = "1";
        for (int outer = 1; outer < n; outer++) {
            String previous = result; //前一个String
            result = "";
            int count = 1; //重复的数字个数
            char say = previous.charAt(0);

            for (int i = 1; i < previous.length(); i++) { //内循环
                if (previous.charAt(i) != say) { //若不和前面的重复
                    result = result + count + say; //拼接
                    count = 1;
                    say = previous.charAt(i);
                } else count++;
            }
            result = result + count + say;
        }
        return result;
    }
}
```

***

## 541. Reverse String II

Given a string and an integer k, you need to reverse the first k characters for every 2k characters counting from the start of the string. If there are less than k characters left, reverse all of them. If there are less than 2k but greater than or equal to k characters, then reverse the first k characters and left the other as original.
Example:
Input: s = "abcdefg", k = 2
Output: "bacdfeg"


```java
public class Solution {
    public String reverseStr(String s, int k) {
        int len = s.length();
        char[] ch = s.toCharArray();
        
        for (int i=0; i<len; i+=2*k) {
            swap(ch, i, i+k);
        }
        return new String(ch);
        //return String.valueOf(ch);
    }
    
    public void swap(char[] ch, int i, int j) {
        j = Math.min(j, ch.length)-1;
        for (;i<j; i++,j--) {
            char temp = ch[i];
            ch[i] = ch[j];
            ch[j] = temp;
        }
    }
}
```

***

## 67. Add Binary

Given two binary strings, return their sum (also a binary string).

For example,
a = "11"
b = "1"
Return "100".

思路：  
从两个数的最右端开始计算，遇到进位就存入carry中。  
将结果顺序翻转。  
{: .notice} 

```java
public class Solution {
    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int i = a.length() - 1, j = b.length() -1, carry = 0; //carry是进位
        while (i >= 0 || j >= 0) {
            int sum = carry; //sum是每一位相加的和
            if (j >= 0) sum += b.charAt(j--) - '0';
            if (i >= 0) sum += a.charAt(i--) - '0';
            sb.append(sum % 2);
            carry = sum / 2;
        }
        if (carry != 0) sb.append(carry);
        return sb.reverse().toString();
    }
}
```

***

## 367. Valid Perfect Square

```java
public boolean isPerfectSquare(int num) {
     int i = 1;
     while (num > 0) {
         num -= i;
         i += 2;
     }
     return num == 0;
 }
```

```java
// we have to use long for mid to avoid mid*mid from overflow
public boolean isPerfectSquare(int num) {
    int low = 1, high = num;
    while (low <= high) {
        long mid = (low + high) >>> 1;
        if (mid * mid == num) {
            return true;
        } else if (mid * mid < num) {
            low = (int) mid + 1;
        } else {
            high = (int) mid - 1;
        }
    }
    return false;
}
```


***

## 441. Arranging Coins

```java
public class Solution {
    public int arrangeCoins(int n) {
        int i = 0;
        while (n > 2*i) {
            n -= i;
            i++;
        }
        return i;
    }
}
```

```java
//(mid*(mid+1)*0.5) <= n ???
public class Solution {
    public int arrangeCoins(int n) {
        int start = 0;
        int end = n;
        int mid = 0;
        while (start <= end){
            mid = (start + end) >>> 1;
            if ((0.5 * mid * mid + 0.5 * mid ) <= n){
                start = mid + 1;
            }else{
                end = mid - 1;
            }
        }
        return start - 1;
    }
}
```

```java
public class Solution {
    public int arrangeCoins(int n) {
        return (int) ((Math.sqrt(1 + 8.0 * n) - 1) / 2);
    }
}
```

***

## 

```
[1] 搜索值不是数组元素，且在数组范围内，从1开始计数，得“ - 插入点索引值”；
[2] 搜索值是数组元素，从0开始计数，得搜索值的索引值；
[3] 搜索值不是数组元素，且大于数组内元素，索引值为 – (length + 1);
[4] 搜索值不是数组元素，且小于数组内元素，索引值为 – 1。
```

```java

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


## 104. Maximum Depth of Binary Tree

```java
public class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null) return 0;
        int left = maxDepth(root.left);
        int right = maxDepth(root.right);
        return (left == 0 || right == 0) ? left + right + 1: Math.max(left,right) + 1;     
    }
}
```

```java
public int maxDepth(TreeNode root) {
    if(root==null)  return 0;  
    return 1+Math.max(maxDepth(root.left),maxDepth(root.right));
}
```

***
***

## 110. Balanced Binary Tree  

思路：  
需要满足三个条件：  
1.左右子树深度差值<=1；  
2.左子树是平衡二叉树；  
3.右子树是平衡二叉树。  
{: .notice} 

```java
//比较深度
public class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null)  return true;
        int left = depth(root.left);
        int right = depth(root.right);
        return Math.abs(left-right) <= 1 && isBalanced(root.left) && isBalanced(root.right);
        
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

## 257. Binary Tree Paths  

思路：  
若一个节点有左结点，就将自己的路径和值传给左结点。  
若一个节点有右结点，就将自己的路径和值传给右结点。  
若没有左右节点，说明已经遍历到了叶子，就把完整的路径加入到answer中。  
{: .notice} 

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
    SearchBT(res, root, sb);
    return res;
}

private void SearchBT(List<String> res, TreeNode root, StringBuilder sb) {
    if(root == null)  return;
    
    sb.append(root.val);
    if(root.left == null && root.right == null) {
        res.add(sb.toString());
    } else {
        sb.append("->");
        SearchBT(res, root.left, sb);
        SearchBT(res, root.right, sb);
    }
    sb.setLength(sb.length());
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

## 112. Path Sum  

Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

```java
public class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null)  return false;
        if (root.left==null && root.right==null && sum-root.val==0)  return true;
        //if(root.left == null && root.right == null) return sum == root.val;
        return hasPathSum(root.left,sum-root.val) || hasPathSum(root.right,sum-root.val);
    }
}
```

***

## 100. Same Tree  

思路：  
若两个节点的值相等，就去比较它们的左子树和右子树是否也相等。  
若两个节点值不等，return false。  
{: .notice} 

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

## 

```java

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

## 

```java

```