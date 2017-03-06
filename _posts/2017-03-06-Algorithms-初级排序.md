---
layout: post
title:  "Algorithms-初级排序"
date:   2017-03-06
excerpt: "本篇介绍了Algorithms中"
tag:
- Algorithms 
feature: http://i.imgur.com/Ds6S7lJ.png
comments: false
---  

><a href="#1">排序模板</a>  
><a href="#2">resultMap实现一对一查询</a>   
><a href="#3">两种方式的比较</a>    

***

<a name="1"></a>

## <center>排序模板</center>  

```java
public class Example {
	//排序
	public static void sort(Comparable[] a) {
	}
	//比较
	private static boolean less(Comparable v, Comparable w) {
		return v.compareTo(w) < 0;
	}
	//交换
	private static void exch(Comparable[] a, int i, int j) {
		Comparable t = a[i];
		a[i] = a[j];
		a[j] = t;
	}
	//打印
	private static void show(Comparable[] a) {
		for(int i=0; i<a.length; i++) {
			System.out.print(a[i] + " ");
		}
	}
	//测试数组是否有序
	private static boolean isSorted(Comparable[] a) {
		for (int i = 1; i < a.length; i++) {
			if(less(a[i], a[i-1])) {
				return false;
			}
		}
		return true;
	}
	//主函数
	public static void main(String[] args) {
	//数组
	sort(a);
	assert isSorted(a);
	show(a);
	}
}
```

***

<a name="1"></a>

## <center>选择排序</center>  

找到数组中最小的元素，将它和第一个元素交换位置。  
在剩下的元素中找到最小的元素，将它与第二个元素交换位置。  
如此往复，直到将整个数组排序。
{: .notice}

```java
public static void sort(Comparable[] a) {
	int N = a.length;                //设置长度
	for (int i=0; i<N; i++) {        //外循环：循环所有元素
		int min = i;                       //1.将最小索引设为i
		for(int j=i+1; j<N; j++) {         //2.开始内循环：在该元素后面的元素中找到最小值
			if(less(a[j], a[min])) {            //有更小的则重新设置min索引
				min = j;
			}
		}
		exch(a, i, min);                   //3.交换：最小元素换到前面位置
	}
}
```


### 特点：  

1.运行时间和输入无关  
2.数据移动是最少的  

### 复杂度：  

对于长度为N的数组，需要大约N²/2次比较和N次交换。  

***

<a name="2"></a>

## <center>插入排序</center>  










