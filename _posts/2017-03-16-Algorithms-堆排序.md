---
layout: post
title:  "Algorithms-堆排序"
date:   2017-03-16
excerpt: "本篇介绍了Algorithms中的堆排序"
tag:
- Algorithms 
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  
 
><a href="#2">由上至下的堆有序化(下沉)</a>   
><a href="#3">堆排序</a>    

***

[https://www.youtube.com/watch?v=MtQL_ll5KhQ](https://www.youtube.com/watch?v=MtQL_ll5KhQ)

<a name="1"></a>

## <center>由上至下的堆有序化(下沉)</center> 

```java
private static void sink(Comparable[] pq, int k, int n) {
    while (2*k <= n) { //到达了堆的底部停止
        int j = 2*k;   //左子节点
        if (j < n && less(pq, j, j+1)) j++;  //子节点存在，找到两个子节点的较大者
        if (!less(pq, k, j)) break;  //比最大的子节点大则停止
        exch(pq, k, j);  //比最大的子节点小则进行交换
        k = j;  //索引改变
    }
}
```


***


<a name="2"></a>

## <center>堆排序</center> 

```java
public static void sort(Comparable[] pq) {
    int n = pq.length;
    for (int k = n/2; k >= 1; k--) //for循环构造最大堆(父节点大于等于子结点)
        sink(pq, k, n);
    while (n > 1) {                //while循环进行排序
        exch(pq, 1, n--);          //将最大值与最后一个元素进行交换
        sink(pq, 1, n);            //重新使堆变得有序
    }
}
```



```java
public class Heap {
    public static void main(String[] args) {
        Integer[] a = new Integer[]{1,5,4,3,2,6,0,7};
        sort(a);
        //assert isSorted(a);
        show(a);
    }
    public static void sort(Comparable[] pq) {
        int n = pq.length;
        for (int k = n/2; k >= 1; k--)
            sink(pq, k, n);
        while (n > 1) {
            exch(pq, 1, n--);
            sink(pq, 1, n);
        }
    }
    
    private static void sink(Comparable[] pq, int k, int n) {
        while (2*k <= n) {
            int j = 2*k;
            if (j < n && less(pq, j, j+1)) j++;
            if (!less(pq, k, j)) break;
            exch(pq, k, j);
            k = j;
        }
    }

    private static boolean less(Comparable[] pq, int i, int j) {
        return pq[i-1].compareTo(pq[j-1]) < 0;
    }

    private static void exch(Object[] pq, int i, int j) {
        Object swap = pq[i-1];
        pq[i-1] = pq[j-1];
        pq[j-1] = swap;
    }

    private static void show(Comparable[] a) {
        for(int i=0; i<a.length; i++) {
            System.out.print(a[i] + " ");
        }
    }
    
}

```










