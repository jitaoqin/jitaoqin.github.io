---
title: 深入理解Java容器
date: 2018-05-02 11:44:59
categories: java
tags:
  - java container
---
![Java container](/images/container.jpg )
<!-- more -->

# java.util.Iterator

  Iterator模式是用于遍历集合类的标准访问方法。它可以把访问逻辑从不同类型的集合类中抽象出来，从而避免向客户端暴露集合的内部结构。在Java中Iterator为一个接口，它只提供了迭代了基本规则，
JDK中定义：对 collection 进行迭代的迭代器。迭代器取代了 Java Collections Framework 中的 Enumeration。迭代器与枚举有两点不同：
  1、迭代器允许调用者利用定义良好的语义在迭代期间从迭代器所指向的 collection 移除元素。
  2、方法名称得到了改进。
  其接口定义如下：  
  
	public interface Iterator {  
		boolean hasNext();	// 判断容器内是否还有可供访问的元素
		Object next();		// 返回值是Object，需要强制转换
		void remove();		// 删除迭代器元素
	}
	
**如果在使用迭代过程中使用集合中的remove()，则代码将出现问题。**  

	import java.util.List ;  
	import java.util.ArrayList ;  
	import java.util.Iterator ;  
	public class IteratorDemo03{  
		public static void main(String args[]){  
			List<String> all=  new ArrayList<String>() ;    //   
			all.add("hello") ;  
			all.add("_") ;  
			all.add("world") ;  
			Iterator<String> iter = all.iterator() ;  // 为Iterator接口实例化  
			while(iter.hasNext()){  // 判断是否有内容  
				String str = iter.next() ;  
				if("_".equals(str)){  
					all.remove(str) ;   // 删除元素       
				}else{  
                System.out.println(str) ;   // 输出内容  
				}  
			}  
        System.out.println("删除之后的集合：" + all) ;  
		}  
	}; 
	// hello
	//删除之后的集合：[hello,world]  
# ListIterator接口
	Iterator接口的主要功能是由前向后单向输出，而此时如果想实现由后向前或者是由前向后的双向输出，则就必须使用Iterator的子接口ListIterator。但是很遗憾的是Collection接口中
并没有定义可以为此类实例化的操作，只有List接口中才存在了ListIterator接口的实例化操作。
ListIterator接口的定义如下：  

	public interface ListIterator<E> extends Iterator<E>
ListIterator实现双向输出：  

	import java.util.ArrayList ;  
	import java.util.List ;  
	import java.util.ListIterator ;  
	public class ListIteratorDemo01{  
			public static void main(String argsp[]){  
			List<String> all = new ArrayList<String>() ;  
			all.add("hello") ;  
			all.add("_") ;  
			all.add("world") ;  
			ListIterator<String> iter = all.listIterator() ;  
			System.out.print("由前向后输出：") ;  
			while(iter.hasNext()){  
				String str = iter.next() ;  
				System.out.print(str + "、") ;  
			}  
			System.out.print("\n由后向前输出：") ;  
			while(iter.hasPrevious()){  
				String str = iter.previous() ;  
				System.out.print(str + "、") ;  
			}  
		}  
	};  


但是，在使用此操作的时候一定要注意一点：一定要先进行由前向后输出，之后才能进行由后向前的输出。
使用ListIterator还可以进行add()增加以及set()替换元素。

总结：
1、Iterator接口的功能是从前向后输出，属于单向的输出。
2、Iterator的主要功能就是完成迭代输出操作的。
3、在使用Iterator的时候最好不要删除数据。
4、如果要想使用ListIterator则只能依靠List接口完成。
5、如果要进行由后向前的输出，则只能先进行由前到后的输出。
3、对于此接口中的增加以及修改操作，用的不是太多，了解即可。
