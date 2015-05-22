---
layout: post
date: 2015-03-01
category: Java
comment: true
title: "Java LinkedList 应用小程序"
---
<p class="intro">
<span class="dropcap">L</span>inkedList类是双向列表,列表中的每个节点都包含了对前一个和后一个元素的引用.  为了熟悉LinkedList的使用， 所以写了下面的小程序, 只是为了练手,practice makes perfect.文章涉及Scanner类，LinkedList类的使用,使用LinkedList的多种方法， 同时使用了ListIterator。 </p>

<p> LinkedList的构造函数如下 <br>
 1. public LinkedList():  生成空的链表     <br>
 2. public LinkedList(Collection col):  复制构造函数   
</p>

####LinkedList使用情景
  如果涉及到“栈”、“队列”、“链表”等操作，考虑List，具体选择哪个List，可以由下面的规则来判断。   
* 对于需要快速插入，删除元素，应该使用LinkedList。   
* 对于需要快速随机访问元素，应该使用ArrayList。   
LinkedList的具体介绍：[ClickMe][link]
####程序介绍：
  完成对学生信息的输入、查询、输出、删除等处理。   
  功能模块划分：   
1. 输入学生信息(Enter Information)   
2. 打印学生信息(Display Information)    
3. 修改学生信息(Change Information)     
4. 删除所有学生信息(Clear All Information)    
5. 删除指定学生信息(Delete Specified Information)    
6. 查找学生信息(Find Information)    
7. 帮助菜单(Show Help)    
8. 退出程序(Exit)    
开发环境： jdk1.8.0_40, Ubuntu14.04Desktop version, Vim editor
####学生类
	public class Student{
		private String name;
		private int age;
		private char sex;
		private String phone;
		private String major;
		public static final String school = "GDUT";

		//Constructor
		public Student(String n, int a, char s, String p, String m){
			name = n;
			age = a;
			sex = s;
			phone = p;
			major = m;
		}
		//Accessor methods
		public String getName(){
			return name;	
		}
		public int getAge(){
			return age;
		}
		public char getSex(){
			return sex;
		}
		public String getPhone(){
			return phone;
		}
		public String getMajor(){
			return major;
		}
		//Mutator
		//...
		@Override
		public String toString(){
			return ("Name: " + name + " Age: " + age + " Sex: " + sex
			+ "Phone: "+ phone+ " Major: "+ major + " School: " +school);
		}
	}	
####程序

{% highlight ruby  %}
1   import java.util.*;
2   public class Information{
3   	private int number = 0;
4   	public LinkedList<Student> infor = new LinkedList<>();
5   	public static void main(String... args){
6   		Information list = new Information(); 
7   		int option = list.menu();
8   		list.select(list, option);
9   	}
//展示欢迎菜单， 同时提醒用户输入选项
10  	public int menu(){
11  		String format = "| %-50s|%n";
12  		
13  		System.out.format("------------------------------------------------------%n");
14  		System.out.format(format, "Options:");
15  		System.out.format(format, "1. Enter Information");
16  		System.out.format(format, "2. Display Information");
17  		System.out.format(format, "3. Change Information.");
18  		System.out.format(format, "4. Clear all Information.");
19  		System.out.format(format, "5. Delete Specific Information.");
20  		System.out.format(format, "6. Find Information.");
21  		System.out.format(format, "7. Show Help.");
22  		System.out.format(format, "8. Exit.");
23  		System.out.format("------------------------------------------------------%n");
24  		System.out.println();
25  		System.out.print("Please enter an option: ");
26  		System.out.println("Make sure the integer(0~8): ");
27  		int option = getInteger(true);
28  		return option;
29  	}
//由于程序多次需要输入一个整型， 所以将其封装为
//private方法， 可以多次调用， 由于有些对integer
//范围有要求， 所有传入一个boolean作为条件验证
//boolean为true时， 返回一个1～8的整型
30  		private int getInteger(boolean test){
31  			Scanner input = new Scanner(System.in);
32  			int integer;
33  			while(true){
34  				String in = input.nextLine();
35  				try{
36  					integer = Integer.parseInt(in);
37  					if(test && integer>0 && integer < 9)
38  						return integer;
39  					else if(test == false)
40  						return integer;
41  					else
42  						continue;
43  				}catch(NumberFormatException e){
44  					continue;
45  				}
46  			}
47  	}
//根据用户输入的选项， 调用不同的方法
//执行相应选项后， 返回主菜单
48  	public void select(Information list, int option){
49  		boolean running = true;
50  		while(running){
51  			switch(option){
52  				case 1:
53  					list.enterInformation(list);
54  					sleep(1200);
55  					option = list.menu();
56  					break;
57  				case 2:
58  					displayInformation(list);
59  					sleep(1200);
60  					option = list.menu();
61  					break;
62  				case 3:
63  					changeInformation(list);
64  					sleep(1200);
65  					option = list.menu();
66  					break;
67  				case 4:
68  					list.infor.clear();
69  					sleep(1200);
70  					System.out.println("The information are all cleared.");
71  					option = list.menu();
72  					break;
73  				case 5:
74  					deleteInformation(list);
75  					sleep(1200);
76  					option = list.menu();
77  					break;
78  				case 6:
79  					findInformation(list);
80  					sleep(1200);
81  					option = list.menu();
82  					break;
83  				case 7:
84  					showHelp();
85  					sleep(1200);
86  					option = list.menu();
87  					break;
88  				case 8:
89  					System.out.println("Thanks for use, the program is exiting!");
90  					sleep(3000);
91  					System.exit(0);
92  
93  				default:
94  					System.out.println("Enter the number 1~8, Man");
95  					option = list.menu();
96  					break;
97  			}
98  		}
99  	}
//采集学生信息  输入成功后，会提醒是否
//继续输入学生信息， 否则返回主菜单
100 	public void enterInformation(Information list){
101 		boolean add = true;
102 		char choice;
103 		while(add){
104 			list.infor.add(getItem());
105 			System.out.println("Added Success!");
106 			System.out.println("Want to add another?(Y|y), Hit any key to go back to the menu: ");
107 			Scanner input = new Scanner(System.in);
108 			choice = input.next(".").charAt(0);
109 			if(choice == 'Y' || choice == 'y')
110 				add = true;
111 			else
112 				add = false;
113 		}
114 	
115 	}
//多次需要学生信息采集模块， 所以将其封装为private method调用
116 	private Student getItem(){
117 		System.out.println("Please enter the inforamtion:"
118 		+ "Name: Age: Sex: Phone: Major: ");
119 		Scanner input = new Scanner(System.in);
120 		String name;
121 		int age;
122 		char sex;
123 		String phone;
124 		String major;
125 		
126 		while(true){
127 			System.out.println("Make sure enter the 
					right format input.");
128 			try{
129 				name = input.next();
130 				if(input.hasNextInt())
131 					age = input.nextInt();
132 				else
133 					throw new InputMismatchException();
134 				sex = input.next(".").charAt(0);
135 				phone = input.next();
136 				major = input.next();
137 				Student one = new Student(name, age, sex, phone, major);
138 				return one;
139 			}catch(InputMismatchException e){
140 				input.nextLine(); //Consume the remained garbage
141 				continue;	
142 			}
143 		}
144 	}
//展示所有学生信息， 采用ListIterator进行遍历
145 	public void displayInformation(Information list){
146 		ListIterator listitor = list.infor.listIterator();
147 		int record = list.infor.size();
148 		System.out.println("You have " + record+" records in the system.");
149 		if(record == 0)
150 			return ;
151 		while(listitor.hasNext()){
152 			System.out.println(listitor.next());
153 		}
154 		System.out.println();
155 	}
//修改指定学生信息， 用户输入学生index(Console所以做得粗糙)
//对index进行验证后， 执行相应修改，调用获取合法index方法
156 	public void changeInformation(Information list){
157 		int index = 0;
158 		if(validIndex(list) != -1){
159 			System.out.println("The index is:" + index);
160 			Student modify = getItem();
161 			list.infor.set(index, modify);
162 			System.out.println("Update Success!");
163 		}
164 		else
165 			return ;
166 	}
//返回一个合法的index， (0<=index<=LinkedList.size()-1)
//多次使用， 所有封装成private方法
//返回-1时， 表示用户放弃当前操作
167 	private int validIndex(Information list){
168 		boolean condition = true;
169 		int index = -1;
170 		while(condition){
171 			System.out.println("Please enter the index of the 
				information:"); 
				//Super sucks...bu GUI will save the life.
172 			condition =false;
173 			index = getInteger(false);
174 			if(index<0 || index>list.infor.size()-1){
175 				System.out.println("The information index 
					dosen't exist, want to enter another:");
176 				System.out.println("Enter Y|y to confirm, hit any 
						key to return to the menu.");
177 				Scanner input = new Scanner(System.in);
178 				char again = input.next(".").charAt(0);
179 				if(again == 'Y' || again == 'y')
180 					condition = true;
181 				else
182 					index = -1;
183 			}
184 		}
185 		return index;
186 	}
187 //删除指定的学生信息， 同样调用获取合法index方法
188 	public void deleteInformation(Information list){
189 		int index = validIndex(list);	
190 		if(index != -1){
191 			//delete something.
192 			list.infor.remove(index);
193 			System.out.println("Delete Success!");
194 		}
195 		else 
196 			return ;
197 	}
198 //查找指定学生信息， 输入学生名 采用foreach结构进行遍历匹配
//， 成功则输出， 否则显示未找到， 提醒是否进行下次查找
199 	public void findInformation(Information list){
200 		Scanner input = new Scanner(System.in);
201 		boolean condition = true;
202 		boolean found = false;
203 		String name;
204 		while(condition){
205 			condition = false;
206 			System.out.println("Please enter the name of the Student: ");
207 			name = input.nextLine();
208 			System.out.println("The searching result: ");
209 			for(Student e: list.infor){
210 				if(e.getName().equals(name)){
211 					System.out.println(e);
212 					found = true;
213 				}
214 			}
215 			if(found == false )
216 				System.out.println("Cannot be found!");
217 			System.out.println("Wanna perform another search?(Y|y)");
218 			char choice = input.next(".").charAt(0);
219 			if(choice == 'Y'|| choice == 'y'){
220 				found = false;
221 				condition = true;
222 				input.nextLine();
223 			}
224 		}
225 	}
//显示帮助菜单
226 	public void showHelp(){
227 		System.out.println("You got Me!");
228 		System.out.println("Happy Coding!");
229 	}
230//暂停指定时间
231 	private void sleep(int time){
232 		try{
233 			Thread.sleep(time);
234 		}catch(InterruptedException ex){
235 			Thread.currentThread().interrupt();
236 		}
237 	}
238 }
{% endhighlight %}
程序源码：      
[Informatiom.java][link2]   
[Student.java][link3]   
[link]:http://www.tutorialspoint.com/java/java_linkedlist_class.htm
[link2]:  {{ site.url }}/fileStore/Information.java
[link3]:  {{ site.url }}/fileStore/Student.java

###总结
1. 代码贴了两三天后,想写总结感觉有点懒,所以说打铁要趁热～_～||还有就是一些写代码过程中的想法也不见了。特别是在这两天看到了ArrayList和LinkedList的使用， 觉得这个小程序应该使用ArrayList
	* 修改学生信息用到set(int index, E element)算法时间杂度为O(n)而使用Arrayist set(index, E elemet)可以达到O(1)   
	* 删除指定学生时， LinkedList使用迭代器进行查找 O(n) ArrayList由于要移动元素算法时间复杂度也是O(n)    
	* 查找学生信息, 如果人数多， LinkedList遍历O(n) 而是使用Arraylist() 可以采取相应策略如二分查找降低算法复杂度   
	* LinkedList 由于使用double-list实现，需要多存储两个指针,会消耗更多的内存。同时， 对链表的操作涉及更多的基础操作,比如新建节点,修改节点指向,这些导致了linked-list相比Array-base的方法代价更大.

2. 使用Scanner如何保证从用户那里获取一个整数， 如何处理输入异常， 使用try-catch机制可以较好地处理这问题。    
3. 遇到重复的代码模块可以将其封装为private私有方法来解决重写代码问题。  
4. 事实上， 对于LinkedList，Arrayist等Collection没必要记住它的使用， 有需要的话直接查文档， 当然熟悉常用的还是应该的。 不过记不住部分都是情有可原的。 更加重要的是知道什么时候用哪个， 当然， 能够知道其背后的实现原理更好， 这样可以帮你更好地选择。  


