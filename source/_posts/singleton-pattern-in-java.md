title: Singleton Pattern In JAVA
date: 2014-03-16 15:06:30
tags: Design Pattern
categories: Java
---

### 为什么要用单例模式

当一个类需要用来管理共享资源的时候，我们就只需要一个对象，比方说：线程池、缓存、日志对象等。如果说制造出了多个实例，就会导致许多问题的产生，例如：程序异常，资源使用过量，结果不一致等。<!-- more -->

## Java实现单例思想

1. 私有构造函数 → 其他类不能实例化一个新的对象。
2. 私有化引用 → 不能进行外部修改。
3. 公有静态方法是唯一可以获得对象的方式。


### 实现方法

#### 经典实现方法

	package com.singleton;
	/**
	 * 懒汉写法  缺点：没有达到lazy loading的效果
	 * 金典单例实现只适用于单线程的情况，当有多个线程并发时，
	 * 它们都要执行这段代码，都执行到第6行，这时 sl都为空，于是乎就会实例出多个对象了，这就违背了单例模式的思想了。
	 *  * @author yonney
	 */
	public class LazySingleton {
	     private static LazySingleton singleton;
	     private LazySingleton(){
	           
	     }
	     public static LazySingleton getInstance(){
	            if( singleton== null){
	                 singleton = new LazySingleton();
	           }
	            return singleton;
	     }
	}


#### getInstance()方法上加上synchronized关键字


	public class Singleton {
	    private static Singleton sl;
	    private Singleton() {
	    }
	    public static synchronized Singleton getInstance() {
	        if(sl == null) {
	            sl = new Singleton();
	        }
	        return sl;
	    }
	    //其他的方法
	}


**注意**：但是我们可以想象得到，真正需要同步的是在第一次调用时，之后就不再需要同步这个方法了。之后的每次调用，同步都会是一种累赘。程序执行的效率就会大大降低。

#### 恶汉写法(JVM虚拟机在加载这个类时就会立马创建此唯一的单例。)


	public class HungrySingleton {
	     private static HungrySingleton singleton = new HungrySingleton();
	     private HungrySingleton(){
	           
	     }
	     public static HungrySingleton getInstance(){
	            return singleton;
	     }
	}

#### ThreadLocal实现

	/**
	 * 使用ThreadLocal实现线程范围内的共享
	 */
	public class ThreadLocalSingleton {
	    private static ThreadLocal<ThreadLocalSingleton> map = new ThreadLocal<ThreadLocalSingleton>();

	    public static ThreadLocalSingleton getThreadInstance() {
	        ThreadLocalSingleton instance = map.get();
	        if (instance == null) {
	            instance = new ThreadLocalSingleton();
	            map.set(instance);
	        }
	        return instance;
	    }
	}


#### 双重校验锁，在当前的内存模型中无效


	public class LockSingleton {
	     private volatile static LockSingleton singleton;
	     private LockSingleton(){
	           
	     }
	     public static LockSingleton getInstance(){
	            if( singleton == null){
	                 synchronized (LockSingleton. class) {
	                      if( singleton == null){
	                            singleton = new LockSingleton();
	                     }
	                }
	           }
	            return singleton;
	     }
	}


#### 内部静态方法


	/**
	 * 内部静态方法  优点：加载时不会初始化静态变量INSTANCE，因为没有主动使用，达到Lazy loading
	 * @author yonney
	 *
	 */
	public class InternalSingleton {
	     private static class SingletonHolder{
	            private final static InternalSingleton INSTANCE = new InternalSingleton();
	     }
	     private InternalSingleton(){
	           
	     }
	     public static InternalSingleton getInstance(){
	            return SingletonHolder. INSTANCE;
	     }
	}

    
#### 枚举实现


	/**
	 * 枚举，《Effective Java》作者推荐使用的方法，优点：不仅能避免多线程同步问题，而且还能防止反序列化重新创建新的对象
	 * @author yonney
	 */
	public enum EnumSingleton {
	     INSTANCE;
	     public static EnumSingleton getInstance(){
	            return INSTANCE ;
	     }
	     public Object o;
	     public Object testObject(){
	            if( o== null){
	                 return new Object();
	           }
	            return o;
	     }
	     public static void main(String args[]){
	           if(EnumSingleton. getInstance().testObject()==EnumSingleton. getInstance().testObject()){
	           }
	     }
	}


---
