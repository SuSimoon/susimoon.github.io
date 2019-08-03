## Tree

```
递归：
第一行停止条件：会使程序停止的步骤
最后一行return：步骤全部完成的情况
```

```
不需要helper的算法：
1.平衡二叉树
2.判断某数是否可由根到叶子结点求和得来
3.same Tree
```


### 108. Convert Sorted Array to Binary Search Tree *

将有序数组转化为平衡查找二叉树。  

1.生成中间节点
2.生成左子树
3.生成右子树

```
注意下标和数组元素不要搞混
new TreeNode(nums[mid]);写成了new TreeNode(mid);
```

```java
public class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums.length == 0)  return null;
        return helper(nums, 0, nums.length-1);
    }
    public TreeNode helper(int[] nums, int lo, int hi) {
        if (lo > hi) return null; 
        int mid = (lo+hi)/2;  
        TreeNode node = new TreeNode(nums[mid]);
        node.left = helper(nums, lo, mid-1);
        node.right = helper(nums, mid+1, hi);
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
        if (root == null)  return root; //此处不能省略，否则TreeNode temp = node.left;会抛出空指针
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

***

### 101. Symmetric Tree 

判断是否是镜像树

1.判断结点的左右结点值是否相等
2.判断左结点的左右结点是否为镜像树

```
开始没有考虑到要传入两个参数，只传入了root。
```

```java
public class Solution {
    public boolean isSymmetric(TreeNode root) {
        return root == null ||  isSymmetricHelp(root.left, root.right); 
    }
    public boolean isSymmetricHelp(TreeNode left, TreeNode right) {
        if (left==null || right==null)  return left == right;  
        if (left.val != right.val)  return false;
        return isSymmetricHelp(left.left, right.right) && isSymmetricHelp(left.right, right.left);
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
        return (left==0 || right==0) ? right+left+1 : Math.min(left, right)+1;
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

### 543. Diameter of Binary Tree **

求树的最长路径。

Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

Example:
Given a binary tree 
          1
         / \
        2   3
       / \     
      4   5    
Return 3, which is the length of the path [4,2,1,3] or [5,2,1,3].

```
最后return的不是最终想要的结果，而是返回给它的父节点它的最大高度
```

注意题意，它要求返回的不包括自己本身的结点。
注意哪里要加1，哪里不加。    

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
        max = Math.max(max, left+right); //这里的left和right已经各自+1
        return Math.max(left, right)+1;
    }
}
```


***

### 110. Balanced Binary Tree  *

```
没有把abs放在第一个条件
```

思路：  
需要满足三个条件：  
1.左右子树深度差值<=1；  
2.左子树是平衡二叉树；  
3.右子树是平衡二叉树。  

注意区分平衡二叉树和二叉排序树

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

### 107. Binary Tree Level Order Traversal II 

层序遍历

```java
//BFS
public class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<TreeNode>(); // 放的不是Integer
        List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
        
        if(root == null) return wrapList; //不是return null
        
        queue.offer(root);
        
        while(!queue.isEmpty()){
            int level = queue.size();
            List<Integer> subList = new LinkedList<Integer>();
            for(int i=0; i<level; i++) {
                TreeNode node = queue.poll();
                if(node.left != null) 
                    queue.offer(node.left);
                if(node.right != null) 
                    queue.offer(node.right);
                subList.add(node.val);
            }
            wrapList.add(0, subList);
        }
        return wrapList;
    }
}
```

***

### 112. Path Sum **

判断某数是否可由根到叶子结点求和得来

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

### 235. Lowest Common Ancestor of a Binary Search Tree *

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
            root = (root.val > p.val ? root.left : root.right);
        }
        return root;
    }
}
```

***

## 257. Binary Tree Paths  ***

return all root-to-leaf paths

   1
 /   \
2     3
 \
  5

["1->2->5", "1->3"]


思路：  
若没有左右节点，说明已经遍历到了叶子，就把完整的路径加入到answer中。
若节点有左结点，就将自己的路径和值传给左结点。  
若节点有右结点，就将自己的路径和值传给右结点。  
{: .notice} 

```
一般函数要返回链表时，先创建链表，再判断root是否为空，为空返回空链表。
```

```java
public class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if (root != null)  
            SearchBT(root, "", res);
        return res;
    }
    public void SearchBT(TreeNode node, String path, List<String> res) {
        if (node.left == null && node.right == null)  res.add(path + node.val); //只判断叶子，不判断是否为null
        if (node.left != null)  SearchBT(node.left, path+node.val+"->", res);
        if (node.right != null)  SearchBT(node.right, path+node.val+"->", res);
    }
}
```

思路：  
第一种方法中在path中使用了String，耗费空间。更好的方式是使用StringBuffer。
1.先将节点值放入buffer
2.若该节点是叶节点，则加入list
3.不是叶节点，则加“—>”后，传入到左右孩子继续添加路径
4.将buffer恢复到上一个节点的路径（因为此节点已经进行完毕）


```java
//上述方法的优化
public List<String> binaryTreePaths(TreeNode root) {
    List<String> res = new ArrayList<>();
    StringBuilder buffer = new StringBuilder();
    helper(res, root, buffer);
    return res;
}

private void helper(List<String> res, TreeNode root, StringBuilder buffer) {
    if (root == null)  return;
    
    int len = buffer.length();
    buffer.append(root.val);
    if (root.left == null && root.right == null) {
        res.add(buffer.toString());
    } else {
        buffer.append("->");
        helper(res, root.left, buffer);
        helper(res, root.right, buffer);
    }
    buffer.setLength(len); //恢复上一层的路径
}
```

***

### 404. Sum of Left Leaves *

        3
       / \
      9  20
        /  \
       15   7

There are two left leaves in the binary tree, with values 9 and 15 respectively. Return 24.

思路：  
1.检测左子树是否是个叶子，是就求和，不是就计算左子树叶子和。    
2.计算右子树的左叶子和。  
{: .notice} 

```java
public class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null)  return 0;
        return helper(root, 0);
    }
    
    public int helper(TreeNode node, int sum) {
        if (node.left != null) {
            if (node.left.left==null && node.left.right==null)
               sum += node.left.val;
            else 
               sum += sumOfLeftLeaves(node.left);
        }
        sum += sumOfLeftLeaves(node.right);
        
        return sum;
    }
}
```

思路： (用queue和stack皆可) 
1.检测左子树是否是个叶子，是就求和，不是就让左子树入栈。 
2.检测右子树是否是个**叶子**，不是就入栈。  

 
> 没有判断：若右子树是个叶子的情况

```java
public int sumOfLeftLeaves(TreeNode root) {
    if(root == null) return 0;
    
    int sum = 0;
    Stack<TreeNode> stack = new Stack<TreeNode>();
    stack.push(root);
    
    while(!stack.empty()) {
        TreeNode node = stack.pop();
        
        if (node.left != null) {
            if (node.left.left == null && node.left.right == null)
                sum += node.left.val;
            else
                stack.push(node.left);
        }

        if (node.right != null) {
            if (node.right.left != null || node.right.right != null)
                stack.push(node.right);
        }
    }
    return sum;
}
```

***

### 100. Same Tree

Given two binary trees, write a function to check if they are equal or not.

思路：  
若两个节点的值相等，就去比较它们的左子树和右子树是否也相等。  
若两个节点值不等，return false。  
{: .notice} 

```java
public class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p==null || q==null)  return p == q;
        if (p.val != q.val)  return false;           
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
     }
}
```
