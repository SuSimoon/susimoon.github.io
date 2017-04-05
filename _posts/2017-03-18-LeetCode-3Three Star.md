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

## 

```java

```

***

## 

```java

```