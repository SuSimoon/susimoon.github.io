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

参考：![](http://blog.csdn.net/litong09282039/article/details/46332127)

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

1.找到数组中最小的元素，将它和第一个元素交换位置。  
2.在剩下的元素中找到最小的元素，将它与第二个元素交换位置。  
3.如此往复，直到将整个数组排序。
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
{: .notice}

### 复杂度：  

对于长度为N的数组，需要大约N²/2次比较和N次交换。    
{: .notice}


***

<a name="2"></a>

## <center>插入排序</center>  

1.从第一个元素开始，该元素可以认为已经被排序。  
2.取出下一个元素，与已经排序的元素从后向前比较。小于则交换，大于则停止。  
3.如此往复，直到将整个数组排序。
{: .notice}


```java
public static void sort(Comparable[] a) {
	int N = a.length;                                 //1.设置长度
	for(int i=1; i<N; i++) {	                      //2.外循环：从第二个元素开始
		for(int j=i; j>0 && less(a[j],a[j-1]); j--) { //3.内循环：索引>0且小于前一个元素
			exch(a,j,j-1);                                //交换    
		}
	}
}
```

### 特点：  
排序所需时间取决于输入中元素的初始顺序  
对于实际应用中某些类型的非随机素组很有效
{: .notice}

### 复杂度：

平均情况：~N²/4次比较、~N²/4次交换  
最好情况：N-1次比较、0次交换  
最坏情况：~N²/2次比较、~N²/2次交换
{: .notice}


***

<a name="3"></a>

## <center>希尔排序</center> 

希尔排序通过将待比较的元素划分为几个区域来提升插入排序的效率。  
让元素可以一次性的朝最终位置迈进一大步，然后算法再取越来越小的步长进行排序。  
最后一步就是步长为1的普通的插入排序的，但是这个时候，整个序列已经是近似排好序的，所以效率高。  
{: .notice} 



```java
public static void sort(Comparable[] a) {
	int N = a.length;
	int h = 1;      
	while(h < N/3) {               //1.计算h的大小
		h = 3*h + 1;
	}
	while(h>=1) {                  //2.h步长递减
		for(int i=h; i<N; i++) {   //3.插入排序
			for (int j=i; j>=h && less(a[j],a[j-h]); j-=h) {
				exch(a, j, j-h);
			}
		}
		h = h/3;
	}
}
```









