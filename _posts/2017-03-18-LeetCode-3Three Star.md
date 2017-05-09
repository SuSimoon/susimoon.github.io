---
layout: post
title:  "LeetCode-Three Star"
date:   2017-03-18
excerpt: ""
project: true
tag:

comments: false
---


### 350. Intersection of Two Arrays II

Given two arrays, write a function to compute their intersection.

Example:
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2, 2].

思路：  
1.将两个数组进行排序。  
2.设置两个指向数组第一个元素的指针i、j。  
3.若两个元素相等则临时放入arrayList中，更不相等，更小的元素所在数组指针右移。  
4.将list临时存放的数放入一个新的数组里。
{: .notice} 

> 第一种方法忘记写 i++与j++  
> 第二种方法通过

```java
public class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        List<Integer> res = new ArrayList<Integer>();
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        for(int i=0,j=0; i<nums1.length && j<nums2.length;) {
           if(nums1[i] == nums2[j]) {
               res.add(nums1[i]);
               i++; // <---
               j++; // <---
           } else if(nums1[i] < nums2[j]) {
               i++;
           } else {
               j++;
           }
       }
       int[] result = new int[res.size()];
       for(int i=0; i<res.size(); i++) {
           result[i] = res.get(i);
       }
       return result;
    }
}
```

```java
int[] result = new int[res.size()];
int i = 0;
for(Integer num : res) {
    result[i++] = num;
}
```

思路：  
1.遍历nums1数组并存入map中，key为元素值，value为出现该元素的个数。  
2.遍历nums2数组，若有map中的元素，就加入list里，map中该元素个数减一。  
{: .notice} 

```java
public class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        ArrayList<Integer> result = new ArrayList<Integer>();
       
        for(int i = 0; i < nums1.length; i++)
        {
            if(map.containsKey(nums1[i])) 
                map.put(nums1[i], map.get(nums1[i])+1);
            else 
                map.put(nums1[i], 1);
        }
    
        for(int i = 0; i < nums2.length; i++)
        {
            if(map.containsKey(nums2[i]) && map.get(nums2[i]) > 0)
            {
                result.add(nums2[i]);
                map.put(nums2[i], map.get(nums2[i])-1);
            }
        }
    
       int[] r = new int[result.size()];
       for(int i = 0; i < result.size(); i++)
       {
           r[i] = result.get(i);
       }
    
       return r;
    }
}
```

***

### 349. Intersection of Two Arrays

Given two arrays, write a function to compute their intersection.

Example:
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2].

> 第一种方法注意不能写成set.get(i)
> 第二种方法开始时依旧按照map形式写，但应该用set，因为无关乎数量。

```java
public class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> res = new HashSet<Integer>();
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        for(int i=0,j=0; i<nums1.length && j<nums2.length; ) {
            if(nums1[i] == nums2[j]) {
                res.add(nums1[i]);
                i++;
                j++;
            } else if(nums1[i] < nums2[j]) {
                i++;
            } else {
                j++;
            }
        }
        int[] r = new int[res.size()];
        int i = 0;
        for(Integer num : res) {
            r[i++] = num;
        }
        return r;
    }
}
```

```java
public class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>();
        Set<Integer> intersect = new HashSet<>();
        for (int i = 0; i < nums1.length; i++) {  //无需判断set中是否存在该元素
            set.add(nums1[i]);
        }
        for (int i = 0; i < nums2.length; i++) {
            if (set.contains(nums2[i])) {
                intersect.add(nums2[i]);
            }
        }
        int[] result = new int[intersect.size()];
        int i = 0;
        for (Integer num : intersect) {
            result[i++] = num;
        }
        return result;
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

> 第一次误把数组中元素加入了res中，应该把索引加入

```java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] res = new int[2];
        Map<Integer,Integer> map = new HashMap<>();
        for(int i=0; i<nums.length; i++) {
            if(map.containsKey(target-nums[i])) {
                res[0] = map.get(target-nums[i]);
                res[1] = i;
                return res;
            } 
            map.put(nums[i], i);
        }
       return res;
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


## 374. Guess Number Higher or Lower

注：  
题中有陷阱。  
java boolean 只有true、false，没有1，2。   
二分查找最后找到的数不是和要找的数相等，就是比要找的数大。  
遇到平方问题要考虑溢出，要设为long。  
{: .notice} 

```java
public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int low = 1, high = n;
        int mid = 0;
        while (low <= high) {
            mid = (low + high) >>> 1;
            if (guess(mid)==-1) {
                high = mid - 1;
            } else if (guess(mid)==1) {
                low = mid + 1;
            } else {
                return mid;
            }
        }
        return mid;
    }
}
```

***

## 69. Sqrt(x)  

Implement int sqrt(int x).  

```java
public class Solution {
    public int mySqrt(int x) {
        int low = 0, high = x;
        while (low <= high) {
            long mid = (low + high) >>> 1;
            if (mid*mid == x) {
                return (int)mid;
            } else if (mid*mid < x) {
                low = (int)mid + 1;
            } else {
                high = (int)mid - 1;
            }
        }
        return low - 1;
    }
}
```

```java
long r = x;
    while (r*r > x)
        r = (r + x/r) / 2;
    return (int) r;
```

***

## 235. Lowest Common Ancestor of a Binary Search Tree

```java
public class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while ((root.val-p.val)*(root.val-q.val) > 0) {
            root = p.val<root.val ? root.left : root.right;
        }
        return root;
    }
}
```

***

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
    if(root == null)  return 0;  
    return Math.max(maxDepth(root.left),maxDepth(root.right))+1;
}
```

***

## 26. Remove Duplicates from Sorted Array  

Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

> 容易把k初始化为0，这样会使得重复的第一个元素没有存入。  

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

思路：  
先排除头结点为val的情况。  
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
***

## 136. Single Number

Given an array of integers, every element appears twice except for one. Find that single one.

```java
//my solution
public class Solution {
    public int singleNumber(int[] nums) {
        if (nums.length == 1)  return nums[0];
        Arrays.sort(nums);
        for (int i=1; i<nums.length; i=i+2) {
            if (nums[i] != nums[i-1])
                return nums[i-1];
        }     
        return nums[nums.length-1];
    }
}
```

> 没有想到亦或方式  
> 当遇到有重复元素的数组时要想到亦或

思路：  
亦或运算：两个操作数的位中，相同则结果为0，不同则结果为1。  
所以数和0亦或运算为这个数的本身。  
{: .notice}  

```java
public class Solution {
    public int singleNumber(int[] nums) {
        int xor = 0;
        for (int i=0; i<nums.length; i++) {
            xor ^= nums[i];
        }
        return xor;
    }
}
```

***

## 463. Island Perimeter


> 未把相邻的检测放在if (grid[i][j] == 1)里，导致不是1的格子也进行了检测。  

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

> 忘记了map.values()和map.clear()的写法
> map.put(d, map.getOrDefault(d, 0)+1);忘了+1
> 顺序不同也算是答案，所以要把角公式*2

思路：  
每一个点都和其它点进行比较，计算距离放入map中。  
根据map的value值对数量进行计算。n*(n-1)即是角个数2倍公式。  
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

> 还是没有想到第一种方法，但经过提示可以写出来

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

## 389. Find the Difference

> 没有想到亦或
> 当要找到两组数或字符串唯一一个不同的元素时要考虑到亦或方式

思路：  
用亦或有两种思路，一种是将c初始化为0(需要两次循环)  
另一种是初始化为长的字符串的最后一个字符(需要一次循环)。  
{: .notice} 

```java
public char findTheDifference(String s, String t) {
    char c = 0; 
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

```java
// my solution hash表
public class Solution {
    public char findTheDifference(String s, String t) {
        int[] hash = new int[26];
        for (char c : s.toCharArray()) {
            hash[c-'a'] ++;
        }
        
        for (int i=0; i<t.length(); i++)  {
            char c = t.charAt(i);
            hash[c-'a'] --;
        }
        
        for (int i=0; i<26; i++) {
            if (hash[i] == -1)
            return (char) (i+'a');
        }
        return 0;
    }
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