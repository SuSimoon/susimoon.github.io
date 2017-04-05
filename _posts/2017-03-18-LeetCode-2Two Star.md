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
