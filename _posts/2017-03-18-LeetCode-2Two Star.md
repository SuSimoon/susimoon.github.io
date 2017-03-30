---
layout: post
title:  "LeetCode-Two Star"
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
