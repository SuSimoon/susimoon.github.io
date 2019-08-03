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
        //Math.abs(left-right) <= 1不能放在最后，因为此时root指向已经变了
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

> 字符数组的最长公共前缀  
> 思路：把第一个字符串作为pre，不断的缩小其长度  
> substring的S小写

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

> 两个指针分别指向首和末字符  
> 首末指针向中间扫描，直到停在字母处  
> 判断两个字符是否相等，不等则返回false
> 相等则继续扫描

> Character.isLetterOrDigit没有记住
> Character.toLowerCase没有考虑到
> 第二种方法没有想到 

```java
//while形式
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
//if形式
public class Solution {
    public boolean isPalindrome(String s) {
        if (s.isEmpty())  return true;
        int start = 0, end = s.length()-1;
        char[] chars = s.toCharArray();
        
        while (start < end) {
                if (!Character.isLetterOrDigit(chars[start]))  start ++;
                else if (!Character.isLetterOrDigit(chars[end]))  end --;
                else {
                    if (Character.toLowerCase(chars[start]) != Character.toLowerCase(chars[end]))
                        return false;        
                        start ++;
                        end --;
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
        //Math.abs(left-right) <= 1不能放在最后，因为此时root指向已经变了
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