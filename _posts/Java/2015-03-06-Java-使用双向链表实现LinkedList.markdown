---
layout: post
category: Java
title: "Java双向链表实现泛型LinkedList"
comment: true
date: 2015-03-06
---

<p class="intro">Java 中LinkedList 是一个双向链表，它也可以被当作堆栈，队列或双端队列进行操作。LinkedList随机访问效率较低,随机插入、随机删除效率低O(n)，顺序访问效率高， 比如使用迭代器进行遍历,常用于多删除,插入操作应用,允许使用Iterator以达到常数级插入和删除。整体上ArrayList比LinkedList更加通用。
</p>
####内部实现机制
1. 使用双向链表实现   
	双向链表也叫双链表，是链表的一种，它的每个数据结点中都有两个指针，分别指向直接后继和直接前驱。所以，从双向链表中的任意一个结点开始，都可以很方便地访问它的前驱结点和后继结点。
<figure>
	<img src = "{{ site.url }}/assets/img/double.jpg" alt="">
	<figcaption>Fig1. - 不带哨兵(sentinel)的双向链表
</figure>
<figure>
	<img src = "{{ site.url }}/assets/img/header.gif" alt="">
	<figcaption>Fig1. - 头尾带哨兵(sentinel)的双向链表
</figure>

2. 由其实现方式可知，LinkedList不存在容量不足问题。但由于每个结点除了存储一个内部类E,还需要额外存储两个指向上下结点的索引， 所有内存开销会相应增加.     

####不带哨兵的双链表实现
{% highlight ruby %}
1.	import java.util.NoSuchElementException;
2.	public class DoubleLink<E>{
3.		private Node head = null;//头指针
4.		private Node tail = null;//尾指针
5.		private int size;//链表元素个数
6.	
7.		public DoubleLink(){ //构造一个空LinkedList
8.			size = 0;
9.		}
10.	   //内部类 
11.		private class Node{
12.			E element;
13.			Node next;
14.			Node prev;
15.			public Node(E element, Node next, Node prev){
16.				this.element = element;
17.				this.prev = prev;
18.				this.next = next;
19.			}
20.			public Node(E element){
21.				this(element, null, null);
22.			}
23.		}
		//返回元素个数
24.		public int size(){
25.			return size;
26.		}
27.		//判断是否为空
28.		public boolean isEmpty(){
29.			return size == 0;
30.		}
31.	   //添加链首元素 算法时间复杂度O(1)
32.		public void addFirst(E element){
33.			Node tmp = new Node(element, head, null);
34.			if(head != null){ head.prev = tmp; }
35.			head = tmp;
36.			if(tail == null) { tail = tmp; }
37.			size ++;
38.		}
		//添加链尾元素 算法时间复杂度:O(1)
39.		public void addLast(E element){
40.			Node tmp = new Node(element, null, tail);
41.			if(head == null)
42.				head = tmp;
43.			if(tail != null)
44.				tail.next = tmp;
45.			tail = tmp;
46.			size ++;
47.		}
		//从头开始遍历 O(n)
48.		public String iterateForward(){
49.			Node temp = head;
50.			StringBuilder s = new StringBuilder();
51.			while(temp != null){
52.				s.append(temp.element + " ");
53.				temp = temp.next;
54.			}
55.			return s.toString();
56.		}
		//从尾部开始遍历 算法时间复杂度:O(n)
57.		public String iterateBackward(){
58.			Node temp = tail;
59.			StringBuilder s = new StringBuilder();
60.			while(temp != null){
61.				s.append(temp.element + " ");
62.				temp = temp.prev;
63.			}
64.			return s.toString();
65.		}
		//移除链首元素  算法时间复杂度:O(1)
66.		public E removeFirst(){
67.			if(head == null)
68.				throw new NoSuchElementException();
69.			E element = head.element;
70.			head = head.next;
71.			if(head == null)
72.				tail = null;
73.			else
74.				head.prev = null;
75.			size --;
76.			return element;
77.		}
		//移除链尾元素 算法时间复杂度:O(1)
78.		public E removeLast(){
79.			if(tail == null)
80.				throw new NoSuchElementException();
81.			E element = tail.element;
82.			tail = tail.prev;
83.			if(tail == null)
84.				head = null;
85.			else
86.				tail.next = null;
87.			size --;
88.			return element;
89.		}
		//指定位置 添加元素 平均算法时间复杂度:O(n)
90.		public void add(int index, E element){
91.			//index can only be 0 ~ size()
92.			if(index < 0 || index > size)
93.				throw new IndexOutOfBoundsException();
94.			if(index == 0)
95.				addFirst(element);
96.			else if(index == size)
97.				addLast(element);
98.			else{
99.				Node temp = head;
100.				for(int i=0; i<index; i++){
101.					temp = temp.next;
102.				}
103.				Node insert = new Node(element, temp, temp.prev);
104.				temp.prev.next = insert;
105.				temp.prev = insert;
106.				size ++;
107.			}
108.		}
			//移除指定位置元素 算法时间复杂度:O(n) 
109.		public E remove(int index){
110.			E element = null;
111.			if(index < 0 || index >=size)
112.				throw new IndexOutOfBoundsException();
113.			if(index == 0)
114.				removeFirst();
115.			else if(index == size-1)
116.				removeLast();
117.			else{
118.				Node temp = head;
119.				for(int i=0; i<index; i++){
120.					temp = temp.next;
121.				}
122.				element = temp.element;
123.				temp.next.prev = temp.prev;
124.				temp.prev.next = temp.next;
125.				temp.next = null;
126.				temp.prev = null;
127.				size --;
128.			}
129.			return element;
130.		}
			//判断是否包含某一元素 平均算法时间复杂度:O(n)
131.		public boolean contains(E element){
132.			return indexOf(element) != -1;
133.		}
134.		//return the index of the first occurence 
135.		public int indexOf(E element){
136.			Node temp = head;
137.			int index = -1;
138.			while(temp != null){
139.				index ++;
140.				if(temp.element.equals(element)){
141.					return index;
142.				}
143.				temp = temp.next;
144.			}
145.			return -1;
146.		}
			//修改指定位置元素 平均算法时间复杂度:O(n)
147.		public void set(int index, E element){
148.			if(index<0 || index>=size)
149.				throw new IndexOutOfBoundsException();
150.			else{
151.				Node temp = head;
152.				for(int i=0; i<index; i++){
153.					temp = temp.next;
154.				}
155.				temp.element = element;
156.			}
157.		}
			//获得指定位置的元素 平均算法时间复杂度:O(n)
158.		public E get(int index){
159.			if(index<0 || index>=size)
160.				throw new IndexOutOfBoundsException();
161.			else{
162.				Node temp = head;
163.				for(int i=0; i<index; i++){
164.					temp = temp.next;
165.				}
166.				return temp.element;
167.			}
168.		}
169.		//这里可以直接设置head = tail = null
170.		public void clear(){
171.			Node temp = head;
172.			while(head != null){
173.				temp = head.next;
174.				head.prev = head.next = null;
175.				head = temp;
176.			}
177.			temp = null;
178.			tail.prev = tail.next = null;
179.			size = 0;
180.		}
			//部分测试
181.		public static void main(String[] args){
182.			DoubleLink<String> dlink = new DoubleLink<>();
183.			dlink.addFirst(" To ");
184.			dlink.addFirst("Welcome");
185.			dlink.addLast(" Here ");
186.			dlink.addLast(" Shady");
187.			dlink.addLast(" Eric ");
188.			System.out.println(dlink.iterateForward());
189.	
190.			System.out.println(dlink.iterateBackward());
191.			if(!dlink.isEmpty())
192.				System.out.println("The size of the linked list: " + dlink.size());
193.			dlink.removeFirst();
194.			System.out.println(dlink.iterateForward());
195.			dlink.removeLast();
196.			System.out.println(dlink.iterateForward());
197.			dlink.add(0," Life ");
198.			System.out.println(dlink.iterateForward());
199.			System.out.println("The size of the doubleLink: " + dlink.size());
200.			System.out.println(dlink.iterateForward());
201.			dlink.add(4," kife ");
202.			System.out.println("The size of the doubleLink: " + dlink.size());
203.			System.out.println(dlink.iterateForward());
204.			dlink.set(4,"wife");
205.			System.out.println("The size of the doubleLink: " + dlink.size());
206.			System.out.println(dlink.iterateForward());
207.			System.out.println("The get:::::" + dlink.get(4));
208.			dlink.remove(4);
209.			System.out.println("The size of the doubleLink: " + dlink.size());
210.			System.out.println(dlink.iterateForward());
211.			System.out.println("The result: "+dlink.contains(" Shady"));
212.			dlink.clear();
213.		}
214.	}
{% endhighlight %}
####带有头尾哨兵的双向链表   

1. 简化操作， 减少special case带来的麻烦,比如链表的边界       
2. 增加共同操作,能够进行代码重用   

#####代码实现(实现部分方法)
{% highlight ruby %}
1.	import java.util.NoSuchElementException;
2.	public class DoubleList<E>{
3.  //inner class内部类		
	private static class Node<E>{
4.		private E element;
5.		private Node<E> prev;
6.		private Node<E> next;
7.		//constructor 构造容器
8.		public Node(E e, Node<E> p, Node<E> n){
9.			element = e;
10.			prev = p;
11.			next = n;
12.		} 
13.		public E getElement(){
14.			return element;
15.		}
16.		public Node<E> getPrev(){
17.			return prev;
18.		}
19.		public Node<E> getNext(){
20.			return next;
21.		}
22.		public void setPrev(Node<E> p){
23.			prev = p;
24.		}
25.		public void setNext(Node<E> n){
26.			next = n;
27.		}
28.		}
29.	    //头节点 和 尾结点
30.		private Node<E> header;
31.		private Node<E> tailer;
32.		private int size;
33.		//Constructor 构造一个带头尾哨兵的空链表 注意构造过程
34.		public DoubleList(){
35.			header = new Node<>(null,null, null);
36.			tailer = new Node<>(null, header,null);
37.			header.setNext(tailer);
38.			size = 0;
39.		}
40.		//methods 返回链表元素个数
41.		public int size(){
42.			return size;
43.		}
		//判定是否为空
44.		public boolean isEmpty(){
45.			return size == 0;
46.		}
		//返回第一个元素,为空时抛出异常 算法时间复杂度:O(1)
47.		public E first(){
48.			if(size == 0)
49.				throw new NoSuchElementException();
50.			return header.getNext().getElement();
51.		}
		//返回最后一个元素,为空时抛出异常 算法时间复杂度:O(1)
52.		public E last(){
53.			if(size == 0)
54.				throw new NoSuchElementException();
55.			return tailer.getPrev().getElement();
56.		}
		//私有方法， 封装共同操作 在传入结点的后面增加一个节点
		//算法时间复杂度 ：O(1)
57.		private void addBetween(Node<E> node,E element){
58.			Node<E> temp = new Node<>(element, node, node.getNext());
59.			node.getNext().setPrev(temp);
60.			node.setNext(temp);
61.			size ++;
62.		}
		//在链表头增加一个节点 算法时间复杂度:O(1)
63.		public void addFirst(E element){
64.			addBetween(header, element);
65.		}
		//在链表尾部增加一个节点 算法时间复杂度:O(1)
66.		public void addLast(E element){
67.			addBetween(tailer.getPrev(), element);
68.		}
		//私有方法 保证封装性 删除传入结点的后一个结点
		//算法时间复杂度 :O(1)
69.		private E remove(Node<E> node){
70.			if(size == 0)
71.				throw new NoSuchElementException();
72.			Node<E> temp = node.getNext();
73.			temp.getNext().setPrev(node);
74.			node.setNext(temp.getNext());
75.			temp.setPrev(null);
76.			temp.setNext(null);
77.			size --;
78.			return temp.getElement();
79.		}
		//移除链表的第一个节点 算法时间复杂度:O(1)
80.		public E removeFirst(){
81.			if(size == 0)
82.				throw new NoSuchElementException();
83.			return remove(header);
84.		}
		//移除链表最后一个节点 算法时间复杂度:O(1)
85.		public E removeLast(){
86.			return remove(tailer.getPrev().getPrev());
87.		}
		//重载toString方法 需要遍历：O(n)
88.		public String toString(){
89.			StringBuilder s = new StringBuilder();
90.			Node<E> temp = header.getNext();
91.			while(temp != tailer){
92.				s.append(temp.getElement() + " ");
93.				temp = temp.getNext();
94.			}
95.			return s.toString();
96.		}
97.		public static void main(String... args){
98.			DoubleList<String> Dlist = new DoubleList<>();
99.			Dlist.addFirst("You!");
100.			Dlist.addFirst(" Love ");
101.			Dlist.addLast("Break");
102.			Dlist.addLast("Even");
103.			System.out.println("The Double List: " + Dlist);
104.			for(int i=0; i<1; i++){
105.				Dlist.removeFirst();
106.			}
107.			System.out.println("The Double List: " + Dlist);
108.			if(!Dlist.isEmpty()){
109.				System.out.println("The size of the Double link: " + Dlist.size());
110.			}
111.			System.out.println("The Double List: " + Dlist);
112.			for(int i=0; i<1; i++){
113.				Dlist.removeLast();
114.			}
115.			System.out.println("The Double List: " + Dlist);
116.	
117.		}
118.	}
{% endhighlight %}

####双向循环链表 (带一个头哨兵)

1. 重载equals方法
2. 重载了clone方法 
	* 在类的定义前添加implements Cloneable,实现Cloneable接口    
	* 事实上， 网上很多吐槽clone， 在Object中clone 设置为protected，
	使用super.clone()会对对象进行一个浅复制(shadow copy)     
	* 重载要将clone设置为public, 在此代码的实现避免了实现super.clone()    
{% highlight ruby %}
1.	import java.util.NoSuchElementException;
2.	public class CircularDouble<E> implements Cloneable{
3.		private static class Node<E>{
4.			private E element;
5.			private Node<E> prev;
6.			private Node<E> next;
7.	
8.			public Node(E e, Node<E> p, Node<E> n){
9.				element = e;
10.				prev = p;
11.				next = n;
12.			}
13.			public E getElement(){
14.				return element;
15.			}
16.			public Node<E> getPrev(){
17.				return prev;
18.			}
19.			public Node<E> getNext(){
20.				return next;
21.			}
22.			public void setElement(E e){
23.				element = e;
24.			}
25.			public void setPrev(Node<E> p){
26.				prev = p;
27.			}
28.			public void setNext(Node<E> n){
29.				next = n;
30.			}
31.		}
32.		//make a deep equals
33.		@Override
34.		public boolean equals(Object o){
35.			if(o == null)
36.				return false;
37.			if(o.getClass() != getClass())
38.				return false;
39.			@SuppressWarnings("unchecked")
40.			CircularDouble<E> other = (CircularDouble<E>)o;
41.			if(other.size() != size)
42.				return false;
43.			//handle the empty list situation
44.			if(other.size() == 0 && size == 0)
45.				return true;
46.			Node<E> walkA = header.getNext();
47.			Node<E> walkB = other.header.getNext();
48.			while(walkA != header){
49.				//Walk through to compare the content of each node.
50.				if(!walkA.getElement().equals(walkB.getElement()))
51.					return false;
52.				walkA = walkA.getNext();
53.				walkB = walkB.getNext();
54.			}
55.			return true;
56.		}
57.		@Override
58.		public CircularDouble<E> clone() throws CloneNotSupportedException{
59.			//Since the super.clone()only make a shadow copy
60.			//therefore there is no need to use it.
61.			//Also remember to make it public
62.			CircularDouble<E> one = new CircularDouble<>();
63.			Node<E> temp = header.getNext();
64.			while(temp != header){
65.				one.addLast(temp.getElement());
66.				temp = temp.getNext();
67.			}
68.			return one;
69.		}
70.		//inplement a circular double linkedList
71.		private int size;
72.		private Node<E> header;
73.		//Only one sentinel
74.		public CircularDouble(){
75.			size = 0;
76.			header = new Node<E>(null,null, null);
77.			header.setPrev(header);
78.			header.setNext(header);
79.		}
80.		public int size(){
81.			return size;
82.		}
83.		public boolean isEmpty(){
84.			return size == 0;
85.		}
86.	
87.		public void addFirst(E element){
88.			Node<E> temp = new Node<E>(element, header, header.getNext());
89.			header.getNext().setPrev(temp);
90.			header.setNext(temp);
91.			size ++;
92.		}
93.		public void addLast(E element){
94.			Node<E> temp = new Node<E>(element, header.getPrev(), header);
95.			header.getPrev().setNext(temp);
96.			header.setPrev(temp);
97.			size ++;
98.		}
99.		public E removeFirst(){
100.			if(size == 0)
101.				throw new NoSuchElementException();
102.			E result = header.getNext().getElement();
103.			Node<E> temp = header.getNext();
104.			temp.getNext().setPrev(header);//what if it is head
105.			header.setNext(temp.getNext());
106.			size --;
107.			return result;
108.		}
109.		public E removeLast(){
110.			if(size == 0)
111.				throw new NoSuchElementException();
112.			E result = header.getPrev().getElement();
113.			Node<E> temp = header.getPrev();
114.			temp.getPrev().setNext(header);//if temp.getPrev() == header
115.			header.setPrev(temp.getPrev());
116.			size --;
117.			return result;
118.		}
119.		public void add(E element, int index){
120.			if(index<0 || index> size)
121.				throw new IndexOutOfBoundsException();
122.			Node<E> temp = header;
123.			for(int i=0; i<index; i++){
124.				temp = temp.getNext();
125.			}
126.			Node<E> node = new Node<E>(element, temp, temp.getNext());
127.			temp.getNext().setPrev(node);
128.			temp.setNext(node);
129.			size ++;
130.		}
131.		public E remove(int index){
132.			if(index<0 || index >= size)
133.				throw new IndexOutOfBoundsException();
134.			Node<E> temp = header;
135.			for(int i=0; i<index; i++){ //Notice that the temp.getNext()
136.				temp = temp.getNext();  //can never be header
137.			}
138.			//Remove the node next to the temp
139.			E result = temp.getNext().getElement();
140.			Node<E> node = temp.getNext();
141.			node.getNext().setPrev(temp);
142.			temp.setNext(node.getNext());
143.			node.setNext(null); //Detach the node from the double linkedList
144.			size --;
145.			return result;
146.		}
147.		//Return the first occurence of the element
148.		//if not found, return the -1, else return the index
149.		public int indexOf(E element){
150.			int index = -1;
151.			if(size == 0)
152.				return index;
153.			Node<E> temp = header.getNext();
154.			while(temp != header){
155.				index ++;
156.				if(temp.getElement().equals(element))
157.					return index;
158.				temp = temp.getNext();
159.			}
160.			return -1;
161.		}
162.		//The index of the last occurence of the element 
163.		public int lastIndexOf(E element){
164.			if(size == 0)
165.				return -1;
166.			int index = size;
167.			Node<E> temp = header.getPrev();
168.			while(temp != header){
169.				index -- ;
170.				if(temp.getElement().equals(element))
171.					return index;
172.				temp = temp.getPrev();
173.			}
174.			return -1;
175.		}
176.		public boolean contains(E element){
177.			return indexOf(element) != -1;
178.		}
179.		public void set(E element, int index){
180.			if(index<0 || index>=size)
181.				throw new IndexOutOfBoundsException();
182.			Node<E> temp = header;
183.			for(int i=0; i<=index; i++){
184.				temp = temp.getNext();
185.			}
186.			temp.setElement(element);
187.		}
188.		public E get(int index){
189.			if(index<0 || index>=size)
190.				throw new IndexOutOfBoundsException();
191.			Node<E> temp = header.getNext();
192.			for(int i=0; i<index; i++){
193.				temp = temp.getNext();
194.			}
195.			return temp.getElement();
196.		}
197.		public void clear(){
198.			Node<E> temp = header.getNext();
199.			while(temp != header){
200.				Node<E> node = temp.getNext();
201.				temp.setNext(null);
202.				temp = node;
203.			}
204.			header.setNext(header);
205.			header.setPrev(header);
206.			size = 0;
207.		}
208.		@Override
209.		public String toString(){
210.			if(size == 0)
211.				return "";
212.			StringBuilder s = new StringBuilder();
213.			Node<E> temp = header.getNext();
214.			while(temp != header){
215.				s.append(temp.getElement() + " ");
216.				temp = temp.getNext();
217.			}
218.			return s.toString();
219.		}
220.		public static void main(String... args){
221.			CircularDouble<String> list = new CircularDouble<>();
222.			list.addFirst("Short!");
223.			list.addFirst("is");
224.			list.addFirst("Life");
225.			list.addLast("What");
226.			list.addLast("It");
227.			list.addLast("Means?");
228.			
229.			System.out.println(list);
230.			if(list.contains("Life"))
231.				System.out.println("Contain(Yes) Index:" + list.indexOf("Life"));
232.			
233.			list.removeFirst();
234.			list.removeLast();
235.			list.add("Three", 3);
236.			list.set("Wow", 3);
237.			System.out.println(list);
238.			list.remove(1);
239.			if(!list.isEmpty())
240.				System.out.println("The size: " + list.size());
241.			System.out.println(list);
242.	
243.			System.out.println("The first occurence of the Wow: " + list.indexOf("Wow"));
244.			System.out.println("The first occurence of the Wow: " + list.lastIndexOf("Wow"));
245.			System.out.println("The element in the list: " + list.get(3));
246.			
247.			//list.clear();	
248.			//Test the equals method.
249.			CircularDouble<String> list1 = new CircularDouble<>();
250.			list1.addFirst("Night");
251.			list1.addLast("is");
252.	//		list1.addLast("dark!");
253.			CircularDouble<String> list2 = new CircularDouble<>();
254.	//		list2.addFirst("Night");
255.	//		list2.addLast("is");
256.	//		list2.addLast("dark!");
257.			System.out.println("The list1: " + list1);
258.			System.out.println("The list2: " + list2);
259.			if(list1.equals(list2))
260.				System.out.println("They are equals!");
261.			else
262.				System.out.println("They are Not equals!");
263.			//Test the clone method
264.			CircularDouble<String> list3 = null;
265.			try{
266.				list3 = list1.clone();
267.			}catch(CloneNotSupportedException e){
268.				e.printStackTrace();
269.			}
270.			System.out.println("The list3: " + list3);
271.			if(list1.equals(list3) && list3.equals(list1))
272.				System.out.println("They are equals!");
273.			else
274.				System.out.println("They are Not equals!");
275.			list1.set("Change", 0);
276.			//Notice that you change the content of the list1, which have not effect
277.			//on the list3, since it is a deep clone
278.			System.out.println("The list1: " + list1);
279.			System.out.println("The list3: " + list3);
280.		}
281.	}
{% endhighlight %}

###总结

1. clear方法问题
	采用上面的clear方法, 遍历链表,将所有索引置空,算法时间复杂度为O(n).   
	若采用直接置空header和tailer的话， 算法时间复杂度为O(1).   
	*  遍历置空的好处是在前面的文章提过, 便于垃圾回收机制进行回收.如果有外部引用链表的某一个结点,不逐一置空可能导致其他结点依旧存在内存,不被回收。
	* 如果有并发的线程对链表进行操作， 能立即知道链表置空， 而不是等到垃圾回收机制开始回收才知道。  
	有兴趣可以看下这里[clear()in java][link]
2. 遍历LinkedList时， 别采用get(int index)方法,否则开销会很大,因为每次调用get(int index)都要实现遍历以获得该元素,      所以可能导致 O(n) * O(n) = O(n^2)
   应该采用ListIterator.[ClickMe][link2]    
3. 相比Arrayist， LinkedList在头部插入元素算法时间复杂度O(1), 而Arrayist由于要移动数组，所有复杂度为O(n), 同时， 对LinkedList执行删除,或则插入, 比如删除特定条件的元素, 采用Iterator遍历, LinkedList也比较有优势。当然get(int index)以及set(int index, E elment)都是O(n), 而Arrayist为O(1).并且ArrayList可实现随机访问, 能实现二分查找等算法.    
4. 上面的LinkedList只是实现了部分方法，没有实现ListIterator接口， 下次再做， 目前还不是很理解Iterator机制, 还有上面的一些方法可以实现优化, 由index和size进行比较， 从而决定从头还是从尾开始遍历, 有机会再修改。   

####程序源码

1. [DoubleLink.java][link3]
2. [DoubleHead.java][link4]   
3. [CircularDouble.java][link5]   
[link]: http://stackoverflow.com/questions/575995/clear-impl-in-javas-linkedlist
[link2]: http://www.tutorialspoint.com/java/java_using_iterator.htm
[link3]: {{ site.url }}/fileStore/DoubleLink.java
[link4]: {{ site.url }}/fileStore/DoubleHead.java
[link5]: {{ site.url }}/fileStore/CircularDouble.java
