### 树的深度

```java
public int depth(TreeNode root) {
    if (root == null)  return 0;
    return Math.max(depth(root.left), depth(root.right))+1;
}
```

### 树的高度

```java
public int height(TreeNode root) {
    if (root == null)  return 0;
    return Math.max(height(root.left), height(root.right))+1;
}
```

### 数的反转  

```java
public int reverse(int x) {
    long rev = 0;
    while (x != 0) {
        rev = rev*10 + x%10;
        x = x/10;
    }
    return (int) rev;
}
```