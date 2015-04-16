---
title: "Java实现泛型二分查找"
layout: post
category: Java
date: 2015-04-09
comment: true
---
<p class="intro"><span class="dropcap">B</span>inarySearch二分查找法，也称为折半搜索、二分搜索，是一种在有序数组中查找某一特定元素的搜索算法。优点是比较次数少， 查找速度快, 平均性能好，其缺点是要求待查表为有序表，且插入删除困难. 折半查找方法适用于不经常变动而查找频繁的有序列表.文章介绍两种实现方法, 递归实现和非递归实现。</p>

####基本思想
1. 搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜素过程结束，返回该值下标；   
2. 如果目标元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较,重复1，2直到找到,或则数组为空。   
3. 如果在某一步骤数组为空，则代表找不到, 返回-1. 
这种搜索算法每一次比较都使搜索范围缩小一半,算法时间杂度为O(log2n)

####递归代码实现   
{% highlight ruby %}
1.	import java.util.Arrays;
2.	public class RSearch{
3.		public static <E extends Comparable<E>> int 
4.				binarySearch(E[] array, E foundMe){
5.			return search(array, 0, array.length-1, foundMe);
6.		}
7.		private static <E extends Comparable<E>> int 
8.				search(E[] array, int low, int high, E foundMe){
9.			if(low > high)
10.				return -1;
11.			else{
12.				int middle = low + (high-low)/2;
13.				int compare = array[middle].compareTo(foundMe);
14.				if(compare < 0)
15.					return search(array, middle+1, high, foundMe);
16.				else if(compare > 0)
17.					return search(array, low, middle-1, foundMe);
18.				else
19.					return middle;
20.			}
21.		}
22.		public static void main(String... args){
23.			Integer[] arr = new Integer[10];
24.			for(int i=0; i<10; i++)
25.				arr[i] = i;
26.		
27.			for(int i=0; i<10; i++){
28.				int result = Test.binarySearch(arr, i);
29.				System.out.println("The target: "+ i+
					"result: " + result);
30.			}
31.		}
32.	}
{% endhighlight %}

####非递归代码实现
{% highlight ruby %}
1.	import java.util.Arrays;
2.	public class NSearch{
3.		public static <E extends Comparable<E>> int 
4.				binarySearch(E[] array, E foundMe){
5.			int low = 0;
6.			int high = array.length -1;
7.			int middle;
8.			while(low <= high){
9.				middle = low + ( high-low )/2;
10.				int compare = array[middle].compareTo(foundMe);
11.				if(compare < 0)
12.					low = middle+1;
13.				else if(compare > 0)
14.					high = middle-1;
15.				else
16.					return middle;
17.			}
18.			return -1;
19.		}
20.		public static void main(String... args){
21.			Integer[] arr = new Integer[10];
22.			for(int i=0; i<10; i++)
23.				arr[i] = i;
24.			for(int i=0; i<10; i++){
25.				int result = NSearch.binarySearch(arr, i+5);
26.				System.out.println("The target: "+ (i+5)+ 
						"result: " + result);
27.			}
28.		}
29.	}
{% endhighlight %}

 2.  如果数组中存在多个目标值，实现通过传入flag指定返回第一次出现或则最后一次出现的目标值下标。这里不实现直接顺序遍历,即找到第一个后，遍历到最后一个出现,该部分算法时间复杂度为O(n),n指的是重复的目标元素个数, 而采用二分法进行判断,则算法时间复杂度为log2n.

###总结
1. BinarySearch的易错点
2. 

