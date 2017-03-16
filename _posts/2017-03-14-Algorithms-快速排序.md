---
layout: post
title:  "Algorithms-快速排序"
date:   2017-03-09
excerpt: "本篇介绍了Algorithms中的快速排序算法"
tag:
- Algorithms 
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">原地切分</a>  
><a href="#2">快速排序</a>   
><a href="#3">算法改进</a>    

***

<a name="1"></a>

## <center>原地切分</center>

```java
private static int partition(Comparable[] a, int lo, int hi) {
	int i = lo, j = hi+1; //左右扫描指针起始点
	Comparable v = a[lo]; //切分元素设为第一个元素
	while (true) {
		//由左向右扫描，若比切分元素小则继续，扫描至最右侧则停止
		while (less(a[++i], v)) if (i == hi) break;
		//由右向左扫描，若比切分元素大则继续，扫描至最左侧则停止
		while (less(v, a[--j])) if (j == lo) break;
		//左右扫描指针相遇时停止扫描
		if (i >= j) break;
		exch(a, i, j);
	}
	exch(a, lo, j);
	return j;
}
```

***

<a name="2"></a>

## <center>快速排序</center>  

```java
public static void sort(Comparable[] a) {
	sort(a, 0, a.length-1);
}

private static void sort(Comparable[] a, int lo, int hi) {
	if(hi <= lo) return; // 数组长度为1时停止排序
	int j = partition(a, lo, hi); //切分
	sort(a, lo, j-1); //左半部分排序
	sort(a, j+1, hi); //右半部分排序
}
```

### 快速排序特点：  

它是原地排序（只需要一个很小的辅助栈）。  
将长度为N的数组排序所需的时间和NlgN成正比。  
内循环比大多数排序算法要短。  
缺点是非常脆弱，在实现时要非常小心才能避免低劣的性能。
{: .notice} 

***

<a name="3"></a>

## <center>算法改进</center>  

### 在排序小数组时切换到插入排序  

将sort()中的语句`if(hi <= lo) return;` // 数组长度为1时停止排序
替换为`if(hi <= lo+M) { Insertion.sort(a, lo, hi); return; }`  
(M的最佳值和系统有关，在5-15间大多数情况都能令人满意)  

