#### 1. Two Sum

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

#### 26. Remove Duplicates from Sorted Array  

Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

> 容易把k初始化为0，这样会使得重复的第一个元素没有存入  
> 将k和i指针指向第二个元素 

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
public class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;
        for (int n : nums)
            if (i == 0 || n > nums[i-1])
                nums[i++] = n;
        return i;
    }
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

## 532. K-diff Pairs in an Array  

Given an array of integers and an integer k, you need to find the number of unique k-diff pairs in the array. Here a k-diff pair is defined as an integer pair (i, j), where i and j are both numbers in the array and their absolute difference is k.

```
Example 1:
Input: [3, 1, 4, 1, 5], k = 2
Output: 2
Explanation: There are two 2-diff pairs in the array, (1, 3) and (3, 5).
Although we have two 1s in the input, we should only return the number of unique pairs.

Example 2:
Input:[1, 2, 3, 4, 5], k = 1
Output: 4
Explanation: There are four 1-diff pairs in the array, (1, 2), (2, 3), (3, 4) and (4, 5).

Example 3:
Input: [1, 3, 1, 5, 4], k = 0
Output: 1
Explanation: There is one 0-diff pair in the array, (1, 1).
```
 

思路：  
当k!=0时，用set来解决。  
当k=0时，验证该元素是否和下一个元素相等但不和前一个相等。  
{: .notice} 

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

***

#### 485. Max Consecutive Ones

Given a binary array, find the maximum number of consecutive 1s in this array.
1连续的最大数

Example 1:
Input: [1,1,0,1,1,1]
Output: 3
Explanation: The first two digits or the last three digits are consecutive 1s.
The maximum number of consecutive 1s is 3.

```java
public class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int max = 0, count = 0;
        for (int i=0; i<nums.length; i++) {
            if (nums[i] == 0)  {
                count = 0;
                continue;
            }
            count ++;
            max = Math.max(max, count);
        }
        return max;
    }
}
```

***

## 66. Plus One

Given a non-negative integer represented as a non-empty array of digits, plus one to the integer.
You may assume the integer do not contain any leading zero, except the number 0 itself.
The digits are stored such that the most significant digit is at the head of the list.

思路： 
从最后一位开始遍历数组，若该数字小于9，则不需要进位，直接+1后返回该数组。  
若该数大于9，既然能够遍历到这个数字，说明它前一个遍历的数有进位，则该位需要置为0。 
若整个循环结束没有返回，说明该数是1000...，需要创建一个比原始数组长度多一位的新数组，并将首个元素置为1。 
{: .notice} 

```java
public class Solution {
    public int[] plusOne(int[] digits) {   
        int n = digits.length;
        for (int i=n-1; i>=0; i--) {
            if (digits[i] < 9) {
                digits[i] ++;
                return digits; 
            }       
            digits[i] = 0; 
        }    
        int[] newNumber = new int [n+1]; //运行到这步说明是1000...
        newNumber[0] = 1;
        
        return newNumber;
    }
}
```

思路：  
进位的方式。  
{: .notice} 

```java
public class Solution {
    public int[] plusOne(int[] digits) {
        int len = digits.length;
        int carry = 1;
        for (int i=len-1; i>=0; i--) {
            int x = (digits[i] + carry) % 10;
            carry =  (digits[i] + carry) / 10;
            digits[i] = x;
        }    
        
        if (carry == 1) {
            int[] num = new int[len+1];
            num[0] = 1;
            return num;
        } else {
            return digits;
        }
    }
}
```

***

## 167. Two Sum II - Input array is sorted

Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution and you may not use the same element twice.

```
Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2
```

思路：(注意数组已经排好序了)  
1.i，j指针分别指向数组的首尾两个元素。  
2.计算i和j指向的元素的和。  
3.若和sum比target小，则j左移；若大则i右移。  
{: .notice} 


```java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] res = new int[2];
        int i = 0, j = nums.length - 1;
       
        while (i < j) {
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
```

***

## 268. Missing Number

Given an array containing n distinct numbers taken from 0, 1, 2, ..., n, find the one that is missing from the array.

For example,
Given nums = [0, 1, 3] return 2.


```java
public class Solution {
    public int missingNumber(int[] nums) {
        int sum = nums.length;
        for (int i=0; i<nums.length; i++) {
            sum += i - nums[i];
        }
        return sum;
    }
}
```

亦或（位）：如果a、b两个值不相同，则异或结果为1。如果a、b两个值相同，异或结果为0。  
亦或（数）：相同的数亦或为0，0和任何数亦或等于其本身。

```java
public int missingNumber(int[] nums) {
    int xor = 0, i = 0;
    for (; i < nums.length; i++) {
        xor = xor ^ i ^ nums[i];
    }
    return xor ^ i; //数组比完整的少一个数
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

> 没有想到简洁for和while结构

```java
public void moveZeroes(int[] nums) {
    if (nums == null || nums.length == 0) return;        

    int i = 0;
    for (int num: nums) {
        if (num != 0) 
            nums[i++] = num;
    }        

    while (i < nums.length) {
        nums[i++] = 0;
    }
}
```

```java
import java.util.*;
import java.util.Arrays;
//import java.Math.*;
// IMPORT LIBRARY PACKAGES NEEDED BY YOUR PROGRAM
// SOME CLASSES WITHIN A PACKAGE MAY BE RESTRICTED
// DEFINE ANY CLASS AND METHOD NEEDED
// CLASS BEGINS, THIS CLASS IS REQUIRED
public class Solution {
    // METHOD SIGNATURE BEGINS, THIS METHOD IS REQUIRED
    ArrayList<Integer> fence(int numOfPlanks, int numOfColors, int numOfSteps,
                             int[][] steps) {
        // WRITE YOUR CODE HERE
        ArrayList<Integer> list = new ArrayList<>();
        int[] planks = new int[numOfPlanks];
        
        for (int i=0; i<numOfSteps; i++) {
            int left = steps[i][0]-1;
            int right = steps[i][1]-1;
            int color = steps[i][2];
            
            for (int j=0; j<numOfPlanks; j++) {
                if (j>=left && j<=right ) {
                    planks[j] = color;
                }
            }
            int[] planks2 = planks;
            Arrays.sort(planks2);
            int count = 1;
            int max = 0;
            for (int k=1; k<numOfPlanks; k++) {
                if (planks2[k] == planks2[k-1]) {
                    count ++;
                } else {
                    max = Math.max(max, count);
                    count = 1;
                }
            }
            list.add(max);
            //l.clear();
        }
        return list;
    }
    // METHOD SIGNATURE ENDS
}
```

