
## 557. Reverse Words in a String III

Example 1:
Input: "Let's take LeetCode contest"
Output: "s'teL ekat edoCteeL tsetnoc"

> 用trim方法可以避免在for循环中写if语句

```java
public class Solution {
    public String reverseWords(String str) {
      String[] strs = str.split(" ");
        StringBuilder builder = new StringBuilder();

        for (int i=0; i<strs.length; i++) {
           builder.append(new StringBuilder(strs[i]).reverse()).append(" ");
        }

        return builder.toString().trim();  
    }
}
```

***

## 541. Reverse String II *

Given a string and an integer k, you need to reverse the first k characters for every 2k characters counting from the start of the string. If there are less than k characters left, reverse all of them. If there are less than 2k but greater than or equal to k characters, then reverse the first k characters and left the other as original.
Example:

Input: s = "abcdefg", k = 2
Output: "bacdfeg"

> 注意j = Math.min(j, ch.length)-1; 要-1

```java
public class Solution {
    public String reverseStr(String s, int k) {
        char[] ch = s.toCharArray();
        
        for (int i=0; i<s.length(); i+=2*k) {
            swap(ch, i, i+k); 
        }
        return new String(ch);
        //return String.valueOf(ch);
    }
    
    public void swap(char[] ch, int i, int j) {
        j = Math.min(j, ch.length)-1; //防止i+k>length
        for (;i<j; i++,j--) {
            char temp = ch[i];
            ch[i] = ch[j];
            ch[j] = temp;
        }
    }
}
```

***

## 14. Longest Common Prefix

字符数组的最长公共前缀  

> 思路：把第一个字符串作为pre，不断的缩小其长度  
> substring的S小写
> substring左包括右不包括

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


## 520. Detect Capital

All letters in this word are capitals, like "USA".
All letters in this word are not capitals, like "leetcode".
Only the first letter in this word is capital if it has more than one letter, like "Google".


```java
public boolean detectCapitalUse(String word) {
    return word.equals(word.toUpperCase()) || 
           word.equals(word.toLowerCase()) ||
           Character.isUpperCase(word.charAt(0)) && 
           word.substring(1).equals(word.substring(1).toLowerCase()); //第一个大写其余小写substring(int beginIndex)
}
```

```java
public boolean detectCapitalUse(String word) {
    int numUpper = 0;
    for (int i=0; i<word.length(); i++) {
        if (Character.isUpperCase(word.charAt(i))) 
            numUpper++;
    }
    if (numUpper == 1) return Character.isUpperCase(word.charAt(0));
    return numUpper == 0 || numUpper == word.length();
}
```

***

## 20. Valid Parentheses *

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.

> 将每个左封闭字符所对应的字符依次压入stack中。  
> 当遇到右封闭字符时判断此时是否和栈顶元素相同。  
> *要考虑到一开始就是右封闭字符的情况(最后一个else if)，此时stack是空栈。
> 还要考虑到s为空的情况，此时应返回true，即最后一行return语句。
> 判断栈顶元素和s的元素是否相等时要用==而不是equals，因为比较的是char而不是String。

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

> 首先令子串长度为字符串长度的一半进行检查，若不能拼接则不断减小子串长度来继续判断 

```java
public class Solution {
    public boolean repeatedSubstringPattern(String s) {
        int len = s.length();
        for (int i=len/2; i>0; i--) {
            if (len % i == 0) {
                int group = len / i; //能划分为几组
                String sub = s.substring(0, i); //得到划分的子串
                int num;
                for (num=1; num<group; num++) {
                    if (!sub.equals(s.substring(i*num, i*num+i)))  break;
                }
                if (num == group) return true;
            }
        }
        return false;
    }
}
```

***

## 434. Number of Segments in a String *（第二种方法）

Count the number of segments in a string, where a segment is defined to be a contiguous sequence of non-space characters.

Please note that the string does not contain any non-printable characters.

Example:

```
Input: "Hello, my name is John"
Output: 5
```

> 没有考虑到中间有很多空格连起来的情况

```java
//my solution
public class Solution {
    public int countSegments(String s) {
        int count = 0;
        String[] strs = s.split(" ");
        for (int i=0; i<strs.length; i++) {
            if (strs[i].length() != 0) 
            	count ++;
        }
        return count;
    }
}
```

```java
public int countSegments(String s) {
    int count = 0;
    for(int i=0; i<s.length(); i++)
        if(s.charAt(i)!=' ' && (i==0 || s.charAt(i-1)==' ')) //该char不是‘ ’，前一个char是‘ ’
            count ++;        
    return count;
}
```

ps:

```java
String s = "   ";
System.out.println(s.split(" ").length); //0

String s = "a";
System.out.println(s.split(" ").length); //1

String s = "   ";
System.out.println(s.trim().length());//0

String s = "a  a";//两个空格
System.out.println(s.split(" ").length); //3
```

***


## 383. Ransom Note

第一个字符串是否可以由第二个字符串中的字母构成
Each letter in the magazine string can only be used once in your ransom note.

canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true


```java
public class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] hash = new int[26];
        for(char c : magazine.toCharArray()) {
            hash[c-'a'] ++;
        }
        for(char c : ransomNote.toCharArray()) {
            if(--hash[c-'a']<0)  return false;
        }
        return true;
    }
}
```

```java
//my solution
public class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        Map<Character, Integer> map = new HashMap<>();
        
        for (char c : magazine.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0)+1);
        }
        
        for (char c : ransomNote.toCharArray()) {
            if (map.containsKey(c) && map.get(c)>0) {
                map.put(c, map.get(c)-1);
            } else
                return false;
        }
        return true;
    }
}
```

***


### 345. Reverse Vowels of a String

Write a function that takes a string as input and reverse only the vowels of a string.

```
Example 1:
Given s = "hello", return "holle".

Example 2:
Given s = "leetcode", return "leotcede".
```

思路：  
1.两个指针分别指向字符串的首和尾。  
2.左指针右移，直到遇到元音停止；右指针左移，直到遇到元音停止。  
3.将两个指针所指元素进行交换。  

> string是否为空的条件有两个
> 没有把chars[i]转换成String，即+“”就可以转换成String
> 内循环中i<j，而不是<=，因为会发生越界问题，比如bc举例。外循环写不写=都可

```java
public class Solution {
    public String reverseVowels(String s) {
        if(s==null || s.length()==0) return s;
       
        String vowels = "aeiouAEIOU";
        char[] ch = s.toCharArray();
        int i = 0, j = s.length()-1;
        
        while (i < j){           
            while (i<j && !vowels.contains(ch[i]+"")) i ++;
                  
            while (i<j && !vowels.contains(ch[j]+"")) j --;
            
            char temp = ch[i];
            ch[i] = ch[j];
            ch[j] = temp;
            
            i++;
            j--;
        }
        return new String(ch);
    }
}
```

***

## 67. Add Binary **

Given two binary strings, return their sum (also a binary string).

For example,
a = "11"
b = "1"
Return "100".
  
> 从两个数的最右端开始计算，遇到进位就存入carry中  
> 将结果顺序翻转  

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


### 125. Valid Palindrome *

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.
是否是回文字符串

For example,
"A man, a plan, a canal: Panama" is a palindrome.
"race a car" is not a palindrome.

两个指针分别指向首和末字符  
首末指针向中间扫描，直到停在字母处  
判断两个字符是否相等，不等则返回false
相等则继续扫描

> Character.toLowerCase没有考虑到
> 判断是否相等后忘记移动指针

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


## 28.Implement strStr() **

判断第二个字符串在第一个字符串中出现的位置

Returns the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.


> 1.先判断子串是否已遍历完  
> 2.再判断字符串是否已遍历完（i+j代表的是第一个字符串此时的指针位置，即窗口右端，i代表窗口左端）  
> 3.最后检测两个指针所指字符是否相同  

```java
public int strStr(String haystack, String needle) {
    for (int i = 0; ; i++) {
        for (int j = 0; ; j++) {
            if (j == needle.length()) 
                return i; //子串遍历完
            if (i + j == haystack.length()) //不能放在第一行，若匹配在最后，则直接返回了-1
                return -1; //字符串遍历完
            if (needle.charAt(j) != haystack.charAt(i + j)) //不能放在第一行，因为要先判断结束条件
                break; 
        }
    }
}
```

