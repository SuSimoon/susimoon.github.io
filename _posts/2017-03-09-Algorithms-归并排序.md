---
layout: post
title:  "Algorithms-归并排序"
date:   2017-03-09
excerpt: "本篇介绍了Algorithms中的归并排序算法"
tag:
- Algorithms 
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">原地归并的抽象方法</a>  
><a href="#2">插入排序</a>   
><a href="#3">希尔排序</a>    

***


## <center>原地归并的抽象方法</center>  

```java
public static void merge(Comparable[] a, int lo, int mid, int hi) {
	int i = lo, j = mid+1;
	
	for(int k=lo; k<=hi; k++) {  //将a数组复制到aux数组
		aux[k] = a[k];
	}
	
	for(int k=lo; k<=hi; k++) {   
		if (i > mid)                   a[k] = aux[j++];   //左半边用尽，取右半边元素
		else if(j > hi)                a[k] = aux[i++];   //右半边用尽，取左半边元素
		else if(less(aux[j], aux[i]))  a[k] = aux[j++];   //j元素更小，j右移
		else                           a[k] = aux[i++];   //i元素更小，i右移
	}
}
```



***


## <center>自顶向下的归并排序</center> 


算法动态图：[https://www.youtube.com/watch?v=JSceec-wEyw](https://www.youtube.com/watch?v=JSceec-wEyw)

```java
public class Merge {
	private static Comparable[] aux;
	
	public static void sort(Comparable[] a) {
		aux = new Comparable[a.length];
		sort(a, 0, a.length-1);
	}
	
	private static void sort(Comparable[] a, int lo, int hi) {
		if(hi <= lo) return;        //只剩下一个元素时停止
		int mid = lo + (hi - lo)/2; //重新找到中间元素
		sort(a, lo, mid);           //左半边排序
		sort(a, mid+1, hi);         //右半边排序
		merge(a, lo, mid, hi);      //左右归并
	}
}
```


***


## <center>自底向上的归并排序</center> 

```java
public static void sort(Comparable[] a) {
	int N = a.length;
	aux = new Comparable[N];
	for(int sz=1; sz<N; sz=sz+sz) {
		for(int lo=0; lo<N-sz; lo+=sz+sz) {
			merge(a, lo, lo+sz-1, Math.min(lo+sz+sz-1, N-1));
		}
	}
}
```














