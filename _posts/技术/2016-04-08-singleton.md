---
layout: post
title: 单例模式
category: 技术
tags: 设计模式
keywords: singleton
description: 
---

　　从今天开始会开始更新一些设计模式的文章，一个新的学习过程，就从简单的单例模式来吧。

　　单例模式经常会出现在面试题中，特别是对初级的程序面面试，面试官往往会说：“咳咳，设计模式有了解吗。。。吧啦吧啦吧啦。。。写个单例模式瞧瞧吧。”还记得刚开始了解到单例模式的时候，我认真反复的练习了大概有3种写法吧，什么饿汉式、懒汉式、线程安全不安全的，感觉很熟了，然后过一段时间突然想起来单例模式这个东东，好像又突然记不全了。说白了还是不了解原理，还有就是对各种写法的区别刚开始还是分不太清的。

　　这里整理了6中单例模式的写法，后面的每一种都有所区别于前面的写法，循序渐进，真正理解了这六种单例模式的实现方式，我觉得应该差不多了。

###1、单例模式之——饿汉式
	/**
	 * @Description 单例模式之——饿汉式
	 * @author lin
	 * @since 2016.4.8
	 * 
	 * 饿汉式的缺点是不能延迟创建对象，而有时候为了减轻系统的负载我们希望延迟创建对象
	 */
	public class SingleTon1 {
		private static SingleTon1 singleton = new SingleTon1();
		
		private SingleTon1(){}
		
		public static SingleTon1 getInstance(){
			return singleton;
		}
	}

###2、单例模式之——线程不安的懒汉式

	/**
	 * @Description 单例模式之——线程不安的懒汉式
	 * @author lin
	 * @since 2016.4.8
	 * 
	 * 这种方式可以实现延迟创建对象，但显然这是线程不安全的
	 */
	public class SingleTon2 {
	
		private static SingleTon2 singleton;
		
		private SingleTon2(){}
		
		public SingleTon2 getInstance(){
			
			if(singleton == null){            //存在复合操作
				singleton = new SingleTon2();
			}
			return singleton;
			
		}
			
	}

###3、单例模式之——线程安的懒汉式1

	/**
	 * @Description 单例模式之——线程安的懒汉式1
	 * @author lin
	 * @since 2016.4.8
	 * 
	 * 这种方式可以实现延迟创建对象，而且是安全的，但是用了同步锁来实现线程安全，比较影响性能
	 * 注意，这种写法只有在jdk1.5之后才会真正的保证线程安全，因为volatile关键字在jdk1.5之后才真正能保证jvm不会对指令进行优化重新排序
	 * volatile关键字有两个作用：
	 * 1、保证内存的可见性，保证一个线程对变量进行了修改之后另一个线程再去访问该变量得到的是修改后的值
	 * 2、可以保证jvm不会改变指令的执行顺序（实际上在jdk1.5之后才做到这一点）
	 */
	public class SingleTon3 {
		
		private static volatile SingleTon3 singleton;
		
		private SingleTon3(){
		}
		
		private SingleTon3 getInstance(){
			
			synchronized (SingleTon3.class) {
				if(singleton == null){
					singleton = new SingleTon3();
				}
			}
			return singleton;
			
		}
		
	}

###4、单例模式之——线程安的懒汉式2(效率优化)

	/**
	* @Description 单例模式之——线程安的懒汉式2(效率优化)
	* @author lin
	* @since 2016.4.8
	* 
	* 单例模式中new操作的频率非常的少，所以先加一个非空判断就可以排除掉绝大多数的加锁操作，提高性能
	*/
	public class SingleTon4 {
		private static volatile SingleTon4 singleton;
		
		private SingleTon4(){}
		
		public static SingleTon4 getInstance(){
			if(singleton==null){
				synchronized(SingleTon4.class){
					if(singleton == null)
						singleton = new SingleTon4();
				}
			}
			return singleton;
		}
		
	}

###5、单例模式之——静态内部类的实现方式

	/**
	* @Description 单例模式之——静态内部类的实现方式
	* @author lin
	* @since 2016.4.8
	* 
	* 写在内部类中防止了在类加载的时候就创建对象，而且静态内部类只会加载一次，所以是线程安全的
	* 注意：
	* 静态属性和静态代码块在类加载的时候（项目启动时 ）就被执行了
	* 而静态方法是在被调用的时候才执行的
	*/
	public class SingleTon5 {
	
		private static class MySingleTon{
			private static SingleTon5 singleton = new SingleTon5();
		}
		
		private SingleTon5(){
			System.out.println("构造器被执行了");
		}
		
		public static SingleTon5 getInstance(){
			return MySingleTon.singleton;
		}
		
	}

###6、单例模式之——枚举实现

	/**
	* @Description 单例模式之——枚举实现
	* @author lin
	* @since 2016.4.8
	* 
	* 前面的几种单例模式的实现方式都有两个缺点：
	* 1、需要额外的操作实现序列化
	* 2、可以通过反射的方式调用私有构造器（当然我们可以修改构造器让它在构造第二个对象的时候抛异常）
	* 枚举的方式除了线程安全的和防止反射之外，而且还实现了自动序列化机制，防止反序列化的收创建新的对象
	*/
	public enum SingleTon6 {
		INSTANCE;
		
		private String name;
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
		
	}

