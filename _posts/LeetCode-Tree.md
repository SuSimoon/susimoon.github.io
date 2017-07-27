## Tree

### 108. Convert Sorted Array to Binary Search Tree *

将有序数组转化为平衡查找二叉树。  

```java
public class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums.length == 0)  return null;
        TreeNode root = helper(nums, 0, nums.length-1);
        return root;
    }
    public TreeNode helper(int[] nums, int low, int high) {
        if (low > high) return null;  <================= 迭代停止条件
        int mid = (low+high)/2;       <================= 不是low+(high-low)
        TreeNode node = new TreeNode(nums[mid]);
        node.left = helper(nums, low, mid-1);
        node.right = helper(nums, mid+1, high);
        return node;
    }
}
```

***

### 226. Invert Binary Tree

翻转树  

     4
   /   \
  2     7
 / \   / \
1   3 6   9

     4
   /   \
  7     2
 / \   / \
9   6 3   1

```java
public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null)  return root;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root); //添加一个元素并返回true。如果队列已满，则返回false
        
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();//移除并返问队列头部的元素。如果队列为空，则返回null
            TreeNode temp = node.left;
            node.left = node.right;
            node.right = temp;
            if (node.left != null)  queue.offer(node.left);
            if (node.right != null)  queue.offer(node.right);
        }    
        return root;
    }
}
```

### 101. Symmetric Tree *

判断是否是镜像树


```java
public class Solution {
    public boolean isSymmetric(TreeNode root) {
        return root == null ||  isSymmetricHelp(root.left, root.right); 
    }
    public boolean isSymmetricHelp(TreeNode left, TreeNode right) {
        if (left==null || right==null)  return left == right;  <================= 迭代停止条件
        if (left.val != right.val)  return false;
        return isSymmetricHelp(left.left, right.right) && isSymmetricHelp(left.right, right.left);
    }
}
```

***

### 543. Diameter of Binary Tree *

两个结点的最长路径。  

```java
public class Solution {
    int max = 0;
    
    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return max;
    }
   
    public int maxDepth(TreeNode root) {
        if (root == null)  return 0;  <================= 迭代停止条件
        int left = maxDepth(root.left);
        int right = maxDepth(root.right);
        max = Math.max(max, left+right);
        return Math.max(left, right)+1;
    }
}
```

***

### 111. Minimum Depth of Binary Tree

最小深度

```java
public class Solution {
    public int minDepth(TreeNode root) {
        if (root == null)  return 0;
        int left = minDepth(root.left);
        int right = minDepth(root.right);
        return (left==0 || right==0) ? right+left+1 : Math.min(left, right)+1; <================= 不是root.left==0
    }
}
```

### 104. Maximum Depth of Binary Tree

最大深度

```java
public class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null)  return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right))+1;
    }
}
```

***

### 110. Balanced Binary Tree  

思路：  
需要满足**三个条件**：  
1.左右子树深度差值<=1；  
2.左子树是平衡二叉树；  
3.右子树是平衡二叉树。  
{: .notice} 

```java
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

### 107. Binary Tree Level Order Traversal II *

层序遍历

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

### 112. Path Sum

根到叶子结点求和

```
1.判断该结点是否为null，是则证明到已搜索到叶节点之后，和并不是sum；--证明false的条件
2.判断该结点是否为叶子结点且和为sum，若是则返回true；--证明true的条件
3.若不符合上一个条件则搜索它的左右子树。
```

```java
public class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null)  return false;
        if (root.left==null && root.right==null && sum-root.val==0)  return true; 
        return hasPathSum(root.left, sum-root.val) || hasPathSum(root.right, sum-root.val);
    }
}
```

### 235. Lowest Common Ancestor of a Binary Search Tree

求两个结点的最小祖先

思路：  
1.判断两个子节点是否都在根节点的一侧。  
2.若不在直接返回根节点；  
3.若在继续验证根节点的左或右结点。
{: .notice}

```java
public class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null)  return root;
        while ((root.val-p.val)*(root.val-q.val) > 0) {
            root = root.val > p.val ? root.left : root.right;
        }
        return root;
    }
}
```


## 257. Binary Tree Paths  

思路：  
若一个节点有左结点，就将自己的路径和值传给左结点。  
若一个节点有右结点，就将自己的路径和值传给右结点。  
若没有左右节点，说明已经遍历到了叶子，就把完整的路径加入到answer中。  
{: .notice} 

```
一般函数要返回链表时，先创建链表，再判断root是否为空，为空返回空链表。
```

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

### 404. Sum of Left Leaves

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

### 100. Same Tree

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