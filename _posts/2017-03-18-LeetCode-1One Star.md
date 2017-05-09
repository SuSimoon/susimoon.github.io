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

## 20. Valid Parentheses

```java
public class Solution {
    public boolean isValid(String s) {
       
       Stack<Character> stack = new Stack<>();
       
       for (char c : s.toCharArray()) {
            if (c == '[')       stack.push(']');
            else if (c == '(')  stack.push(')');
            else if (c == '{')  stack.push('}');
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


```
[1] 搜索值不是数组元素，且在数组范围内，从1开始计数，得“ - 插入点索引值”；
[2] 搜索值是数组元素，从0开始计数，得搜索值的索引值；
[3] 搜索值不是数组元素，且大于数组内元素，索引值为 – (length + 1);
[4] 搜索值不是数组元素，且小于数组内元素，索引值为 – 1。
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
        return hasPathSum(root.left, sum-root.val) || hasPathSum(root.right, sum-root.val);
    }
}
```

***

## 231. Power of Two

Given an integer, write a function to determine if it is a power of two.

思路：  
若该数是2的次方，则这个数换成二进制时只有一个比特是1。  
将只有一个bit是1的数-1后，相与是0。  
{: .notice} 

```java
public class Solution {
    public boolean isPowerOfTwo(int n) {
        return ((n & (n-1))==0 && n>0);
    }
}
```

***

## 268. Missing Number

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


```java
public int missingNumber(int[] nums) {
    int xor = 0, i = 0;
    for (i = 0; i < nums.length; i++) {
        xor = xor ^ i ^ nums[i];
    }
    return xor ^ i;
}
```

***

## 507. Perfect Number

```java
public class Solution {
    public boolean checkPerfectNumber(int num) {
        if (num == 1)  return false;
        int sum = 0;
        for (int i=2; i<Math.sqrt(num); i++) {
            if (num % i == 0) {
                sum += i;
                if (i != num/i)
                    sum += num/i;
            }
        }
        return ++sum == num;
    }
}
```

***

## 9. Palindrome Number

```java
public boolean isPalindrome(int x) {
    if (x<0 || (x!=0 && x%10==0)) return false;
    int rev = 0;
    while (x>rev){
        rev = rev*10 + x%10;
        x = x/10;
    }
    return (x==rev || x==rev/10);
}
```



***

## 437. Path Sum III

```java
public int pathSum(TreeNode root, int sum) {
    HashMap<Integer, Integer> preSum = new HashMap();
    preSum.put(0,1);
    return helper(root, 0, sum, preSum);
}

public int helper(TreeNode root, int currSum, int target, HashMap<Integer, Integer> preSum) {
    if (root == null) {
        return 0;
    }
    
    currSum += root.val; //当前的和
    int res = preSum.getOrDefault(currSum - target, 0); //res等于map取出当前和与所给值的
    preSum.put(currSum, preSum.getOrDefault(currSum, 0) + 1); //
    
    res += helper(root.left, currSum, target, preSum) + helper(root.right, currSum, target, preSum);
    //
    preSum.put(currSum, preSum.get(currSum) - 1); //
    return res;
}
```

***

## 226. Invert Binary Tree

思路：  
将左子树和右子树翻转后交换。  
{: .notice} 

```java
//DFS
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    TreeNode tempRight = root.right;
    root.right = invertTree(root.left);
    root.left = invertTree(tempRight);
    return root;
}
```

```java
//BFS
public class Solution {
    public TreeNode invertTree(TreeNode root) {
        
        if (root == null) {
            return null;
        }

        final Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            TreeNode left = node.left;
            node.left = node.right;
            node.right = left;

            if(node.left != null) {
                queue.offer(node.left);
            }
            if(node.right != null) {
                queue.offer(node.right);
            }
        }
        return root;
    }
}
```

***

## 108. Convert Sorted Array to Binary Search Tree

思路：  
二叉搜索树的根结点的值正好是所有节点值的中间值。  
{: .notice} 

```java
public class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums.length == 0)  return null;
        TreeNode head = helper(nums, 0, nums.length-1);
        return head;
    }
    public TreeNode helper(int[] nums, int low, int high) {
        if (low > high) return null;
        int mid = (low+high)/2;
        TreeNode node = new TreeNode(nums[mid]);
        node.left = helper(nums, low, mid-1);
        node.right = helper(nums, mid+1, high);
        return node;
    }
}
```

***

## 543. Diameter of Binary Tree  

思路：  
最大的直径一定是某个节点左子树最大高度与右子树最大高度之和+1。  
设置全局变量max来记录当前节点最大直径

```java
public class Solution {
    int max = 0;
    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return max;
    }
    public int maxDepth(TreeNode root) {
        if (root == null)  return 0;
        int left = maxDepth(root.left);
        int right = maxDepth(root.right);
        max = Math.max(max, left+right);
        return Math.max(left, right)+1;
    }
}
```
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
2.设置左右两个指针，表示滑窗的**左右边界**。每一次循环left固定而right右移。  
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
        
        for (char c : p.toCharArray()) 
            hash[c]++;

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

            if (right-left == p.length() ) {
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

## 401. Binary Watch  

思路：  
为了不使小时与分混合，将h左移6位，所以有h*64。  
计算所有时间的bit个数，若符合num，则加入到list中。  
{: .notice} 

```java
public List<String> readBinaryWatch(int num) {
    List<String> list = new ArrayList<>();
    for (int h=0; h<12; h++)
        for (int m=0; m<60; m++)
            if (Integer.bitCount(h * 64 + m) == num) 
            //Integer.bitCount(m) + Integer.bitCount(h)
                list.add(String.format("%d:%02d", h, m)); //%02d输出2位，不足在前面补零
    return list;        
}
```

***

## 453. Minimum Moves to Equal Array Elements

思路：  
将其中一个数-1等同于将其它所有值+1。 
解决方案是找到最小的那个值，其它的值都减到这个最小值。  
{: .notice}  

```java
public class Solution {
    public int minMoves(int[] nums) {
        if (nums.length == 0) return 0;
        int min = nums[0];
        for (int n : nums) min = Math.min(min, n);
        int res = 0;
        for (int n : nums) res += n - min;
        return res;
    }
}
```

***

## 415. Add Strings

思路：  
当需要进行数相加计算时，要设置进位carry以及从高位到低位循环。  
注意循环终止条件i >= 0 || j >= 0 || carry == 1
{: .notice} 

```java
public class Solution {
    public String addStrings(String num1, String num2) {
        StringBuilder sb = new StringBuilder();
        int carry = 0;
        for(int i = num1.length()-1, j = num2.length()-1; i>=0 || j>=0 || carry== 1; i--, j--){
            int x = i < 0 ? 0 : num1.charAt(i) - '0';
            int y = j < 0 ? 0 : num2.charAt(j) - '0';
            sb.append((x + y + carry) % 10);
            carry = (x + y + carry) / 10;
        }
        return sb.reverse().toString();
    }
}
```

***

## 400. Nth Digit

思路：  
将数列分为1-9，10-99，100-999 ...  
每组数的个数分别为9*1、90*2、90*3 ...  
先判断所给数n在哪个组，其次判断在该组的那个数，再其次判断在这个数的哪一位上。  
{: .notice} 

```java
public int findNthDigit(int n) {
    int len = 1;
    long count = 9; //不能写成int
    int start = 1;

    while (n > len * count) {
        n -= len * count;
        len += 1;
        count *= 10;
        start *= 10;
    }

    start += (n - 1) / len;
    String s = Integer.toString(start);
    return Character.getNumericValue(s.charAt((n-1) % len));
}
```

***

## 66. Plus One

思路：  
进位的方式。  
{: .notice} 

```java
//my solution
public class Solution {
    public int[] plusOne(int[] digits) {
        int[] res = new int[digits.length];
        int carry = 0;
        for (int i=digits.length-1; i>=0 || carry==1; i--) {
            if (i<0 && carry==1) {
                int[] r = new int[digits.length+1]; 
                r[0] = 1;
                for (int j=1; j<res.length; j++) {
                    r[j] = res[j];
                }
                return r;
            }
            int x = (i < 0 ? 0 : digits[i]);
            if (i==digits.length-1) {
                res[i] = (x+1+carry) % 10;
                carry = (x+1+carry) / 10;
            } else {
                res[i] = (x+carry) % 10;
                carry = (x+carry) / 10;
            }
        }
        
        return res;
    }
}
```

思路：  
由于数组的数字只能是1-9，所以若该数字小于9，则不需要进位，直接+1后返回该数组。  
若该数字是9，该位需要进位，也就是>9，要置为0。  
若整个循环结束没有返回，说明该数是1000...，需要创建一个比原始数组长度多一位的新数组，并将首个元素置为1.  
{: .notice} 

```java
public int[] plusOne(int[] digits) {
        
    int n = digits.length;
    for(int i=n-1; i>=0; i--) {
        if(digits[i] < 9) {
            digits[i]++;
            return digits; //若该位不需要进位，直接+1后返回该数组
        }
        
        digits[i] = 0; //若该位需要进位，也就是>9，要置为0
    }
    
    int[] newNumber = new int [n+1]; //运行到这步说明是1000...
    newNumber[0] = 1;
    
    return newNumber;
}
```

***

## 7. Reverse Integer

```
Integer.MAX_VALUE:2^31 -1 即：2147483647
Integer.MIN_VALUE -2147483648
```

```java
// 不懂
public int reverse(int x)
{
    int result = 0;

    while (x != 0)
    {
        int tail = x % 10;
        int newResult = result * 10 + tail;
        if ((newResult - tail) / 10 != result)
        { return 0; }
        result = newResult;
        x = x / 10;
    }

    return result;
}
```

```java
public int reverse(int x) {
    long rev = 0;
    while (x != 0) {
        rev = rev*10 + x%10;
        x = x/10;
        if( rev > Integer.MAX_VALUE || rev < Integer.MIN_VALUE)
            return 0;
    }
    return (int) rev;
}
```

***

## 172. Factorial Trailing Zeroes

思路：  
检测有多少对2和5。由于2是充足的，所以只需要检测有多少个5。  
其中，每5个数增加1个5，每25个数增加两个5，每125个数增加3个5...  
即：n/5 + n/25 + n/125 + n/625
{: .notice} 

```java
return n == 0 ? 0 : n / 5 + trailingZeroes(n / 5);
```

```java
public class Solution {
    public int trailingZeroes(int n) {
        int cnt = 0;
        while(n>0){
            cnt += n/5;
            n/=5;
        }
        return cnt;
    }
}
```

***

## 204. Count Primes

```java
public class Solution {
    public int countPrimes(int n) {
       boolean[] isPrime = new boolean[n];
       for (int i = 2; i < n; i++) {
          isPrime[i] = true;
       }
       for (int i = 2; i * i < n; i++) {
          if (!isPrime[i]) continue;
          for (int j = i * i; j < n; j += i) {
             isPrime[j] = false;
          }
       }
       
       int count = 0;
       for (int i = 2; i < n; i++) {
          if (isPrime[i]) count++;
       }
       return count;
    }
}
```


```java
public class Solution {
    public int countPrimes(int n) {
        boolean[] notPrime = new boolean[n];
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (notPrime[i] == false) {
                count++;
                for (int j = 2; i*j < n; j++) {
                    notPrime[i*j] = true;
                }
            }
        }
        
        return count;
    }
}
```

***

## 168. Excel Sheet Column Title

```java
//my wrong solution
public class Solution {
    public String convertToTitle(int n) {
        StringBuilder sb = new StringBuilder();
        while (n > 0) {
            int x = n % 26;
            char c = (char)((char)x-1+'A');
            sb.append(c);
            n = n / 26;
        }
        
        return sb.reverse().toString();
    }
}
```

```java
public class Solution {
    public String convertToTitle(int n) {
        StringBuilder sb = new StringBuilder();
        while (n > 0) {
            n--;
            int x = n % 26;
            char c = (char)(x+'A');
            sb.append(c); //可用insert(0, c)，这样就不用最后再reverse
            n = n / 26;
        }
        
        return sb.reverse().toString();
    }
}
```

```java
return n == 0 ? "" : convertToTitle(--n / 26) + (char)('A' + (n % 26));
```

***

## 263. Ugly Number

思路：  
把3、2、5都除尽，若剩余的不是1就是false。  
{: .notice} 

```java
public boolean isUgly(int num) {
    if(num==1) return true;
    if(num==0) return false;
    while(num%2==0) num=num/2;
    while(num%3==0) num=num/3;
    while(num%5==0) num=num/5;
    return num==1;
}
```

***

## 258. Add Digits

```java

```


***

## 461. Hamming Distance

```java
public class Solution {
    public int hammingDistance(int x, int y) {
        return Integer.bitCount(x^y);
    }
}
```

```java
public int hammingDistance(int x, int y) {
    int xor = x ^ y, count = 0;
    for (int i=0;i<32;i++) count += (xor >> i) & 1;
    return count;
}
```


***

## 476. Number Complement

```java
public class Solution {
    public int findComplement(int num) {
        int mask = 1;
        while (mask < num)
            mask = (mask << 1) | 1; // 1，11，111，1111...
        return ~num & mask;
    }
}
```

***

## 557. Reverse Words in a String III

```java
public class Solution {
    public String reverseWords(String s) {
        String[] strs = s.split(" ");
        StringBuilder sb = new StringBuilder();
        for (int i=0; i<strs.length; i++) {
            sb.append(new StringBuilder(strs[i]).reverse());
            if (i != strs.length-1)  sb.append(" ");
        }
            
        return sb.toString();
    }
}
```

***

## 

```java

```