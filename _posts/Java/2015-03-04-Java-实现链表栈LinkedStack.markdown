---
layout: post
date: 2015-03-04
category: Java
title: "Java 实现链表栈LinkedStack"
comment: true
---
<p class="intro"><span class="dropcap">栈</span>又称堆栈，是一种受限制的线性表，其限制是只允许在表的一端进行插入和删除。允许操作的一端称为栈顶（top），不允许操作的称为栈底(bottom)，每次删除的数据元素总是最后插入的数据元素, 其特点为LIFO，即后进先出（Last in, first out）.</p>
<img src="{{ site.url }}/assets/img/stack.png" alt="">
####栈的储存结构：   
  * 顺序储存结构（顺序栈)        
  *  **链式储存结构（链式栈)** 

####栈的主要操作：    
1. T pop() 出栈操作，弹出(删除)栈顶元素.   
2. void push(T e) 入栈操作    
3. T peek() 返回栈顶元素,不对栈顶元素做删除操作      
4. boolean isEmpty() 栈是否为空  
####代码实现：
{% highlight ruby %}
1  import java.util.NoSuchElementException;
  
2  //Lastin-first-out(LIFO) stack of generic items.
3  
4  public class LinkedStack<T> {
5  	//inner class for encapsulation
6  	class Node<T>{
7  		T item;
8  		Node<T> next;
9  
10 		public Node(T item, Node<T> next){
11 			this.item = item;
12 			this.next = next;
13 		}
14 		//accessor methods
15 		public T getItem(){
16 			return item;
17 		}
18 		public Node<T> getNext(){
19 			return next;
20 		}
21 		//mutator
22 		public void setNext(Node<T> n){
23 			next = n;
24 		}
25 	}
26 	private Node<T> head;
27 	private int size;
28 	//constructor
29 	//construct a empty stack
30 
31 	public LinkedStack(){
32 		head = null;
33 		size = 0;
34 		assert check();
35 	}
36 	//methods
//判断栈是否为空， 空返回true， 否则false
37 	public boolean isEmpty(){
38 		return head == null;
39 	}
//返回栈的元素个数
40 	public int size(){
41 		return size;
42 	}
//将元素压进栈
43 	public void push(T item){
44 		head = new Node<T>(item, head);
45 		size ++;
46 		assert check();
47 	}
//弹出一个栈元素， 如果栈空， 抛出NoSuchElementException异常
48 	public T pop(){
49 		if(head == null) 
50 			throw new NoSuchElementException("Stack underflow");
51 		T result = head.getItem();
52 		head = head.getNext();
53 		size --;
54 		assert check();
55 		return result;
56 	}
//返回栈顶元素 如果栈空， 抛出NoSuchElementException异常
57 	//peek is an operation on abstract data type,like sequential 
58  //collections such as stacks and queues, which returns the
59 	//value of the top of the collection without removing the value 
60 	//from the data.
61 	public T peek(){
62 		if(head == null)
63 			throw new NoSuchElementException("Stack underflow");
64 		return head.getItem();
65 	}
//返回栈的所有元素组成的字符串
66 	@Override
67 	public String toString(){
68 		StringBuilder s = new StringBuilder();
69 		Node<T> temp = head;
70 		while(temp != null){
71 			s.append(temp.getItem() + " ");
72 			temp = temp.getNext();
73 		}
74 		return s.toString();
75 	}
//保证LinkedStack类的(Class Invariant)不变式
76 	//check internal invariants
77 	private boolean check(){
78 		if(size == 0){
79 			return head == null;
80 		}
81 		//check internal consistency of instance variable size
82 		int numberOfNodes = 0;
83 		for(Node<T> temp = head; temp != null; temp = temp.getNext()){
84 			numberOfNodes ++;
85 		}
86 		if(numberOfNodes != size) return false;
87 		return true;
88 	}
89 	//Unit test
90 	public static void main(String... args){
91 		LinkedStack<String> stack = new LinkedStack<String>();
92 		stack.push("!");
93 		stack.push("you");
94 		stack.push(" love ");
95 		stack.push("I");
96 		System.out.println(stack);
97 		stack.pop();
98 		System.out.println(stack);
99 		stack.peek();
100		System.out.println(stack);
101		System.out.println("The size of the stack: " + stack.size());
102	}
103}
{% endhighlight %}
####总结：
1. 当栈满的时候,继续使用push()时,会引发栈溢出(stack overflow)   
   当栈空的时候,继续使用pop()时, 会引发堆栈下溢(stack underflow)    
   两者都有可能引发程序崩溃, 所以要做好相应检测.    
2. 类中的不变式(class invariant)    
   类中的check()方法目的就是保证类的不变式.保证类中数据的关系,比如上面代码中size和链表中的实际元素个数应该是一致. 如果链表上有3个元素,而size=4, 那么就出问题了。通过check()方法的检测, 可以有效减少这种错误.不变式能体现一个对象的表示何时良好何时不好.   
   In computer Programming, specifically object-oriented programming, a class invariant is an invariant used to constrain objects of a class. Methods of the class should preserve the invariant. The class invariant constraint the state stored in the object.
   [Class Invariant][Wiki]   
3. assert的使用   
 assert booleanExpression;   
 assert BooleanExpression : errorMessage;   
  如果assert后面的booleanExpression返回值为false, 就会
  抛出一个AssertionError并使程序停止.Debugg.   
  运行程序时， 需要使用以下格式: java -ea fileName
[Wiki]:http://en.wikipedia.org/wiki/Class_invariant

