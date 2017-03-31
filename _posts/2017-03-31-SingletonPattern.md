---
layout: post
title: "单例模式"
date: 2017/3/31 22:20:55 
description: "设计模式之单例模式"
categories: [设计模式]
tags: [设计模式, 单例]
---

# 单例模式

----

## 什么是单例模式

顾名思义，被单例的对象只能有一个实例存在。

单例模式的实现方法是：

* 一个类能返回对象的一个引用（永远是同一个）
* 一个获得该唯一实例的方法（必须是静态方法）

通过单例模式，可以保证系统中只有一个实例，从而在某些特定的场景下，达到节约或者控制系统资源的目的。

## 单例模式类图

## 单例模式实例代码

### 饿汉模式

说明：很“饥饿”，一上来就新建一个实例。


	// 饿汉模式
	public class Singleton {

    	// 一上来就新建实例
    	private static final Singleton instance = new Singleton();
    
    	// 默认构造方法，私有
    	private Singleton() {}
    		
    	// 获得实例的方法
    	public static Singleton getInstance() {
			return instance;
    	}
	}


### 懒汉模式

说明：很“懒”，一开始不新建实例，等需要使用的时候，先判断实例是否为空，如果为空才会新建一个实例来使用。

	// 懒汉模式
	public class Singleton {

		// 一开始不新建实例
		private static Singleton instance;
		
		// 默认构造方法，私有
    	private Singleton() {}
		
		// 需要时再新建
		public static Singleton getInstance() {
			if (instance == null) {
				instance = new Singleton();
			}
			return instance;
		} 
	}
    

### 线程安全的懒汉模式

说明：上面的懒汉模式存在严重问题，当多个线程**首次**同时调用 getInstance() 方法时，还是会创建多个实例，单例模式就失效了。

把 getInstance() 改为线程同步的。

	// 线程安全的懒汉模式
	public class Singleton {

		// 一开始不新建实例
		private static Singleton instance;
		
		// 默认构造方法，私有
    	private Singleton() {}
		
		// 需要时再新建，使用同步方法
		public static synchronized Singleton getInstance() {
			if (instance == null) {
				instance = new Singleton();
			}
			return instance;
		} 
	}


### 双重检验锁（double check）

说明：上面的线程安全的懒汉模式解决了多线程的问题，但是效率不高，每次调用 getInstance() 方法都需要同步。但实际上，只有第一次调用时需要进行同步控制。

使用双重检验锁方法。
	
	// 线程安全的懒汉模式，双重锁
	public class Singleton {

		// 一开始不新建实例
		private static Singleton instance;
		
		// 默认构造方法，私有
    	private Singleton() {}
		
		// 需要时再新建，使用双重锁
		public static Singleton getInstance() {
			if (instance == null) {
				synchronized (Singleton.class) {
					if (instance == null) {
						instance = new Singleton();
					}
				}
			}
			return instance;
		} 
	}


使用 volatile 关键字禁止指令重排序优化。

	// 线程安全的懒汉模式，双重锁，volatile 关键字
	public class Singleton {

		// 一开始不新建实例
		private volatile static Singleton instance;
		
		// 默认构造方法，私有
    	private Singleton() {}
		
		// 需要时再新建，使用双重锁
		public static Singleton getInstance() {
			if (instance == null) {
				synchronized (Singleton.class) {
					if (instance == null) {
						instance = new Singleton();
					}
				}
			}
			return instance;
		} 
	}

### 静态内部类方式

说明：上面的方式太复杂，而且 volatile 关键字在老版本的 JDK 中无法正常工作。

使用静态内部类的方式，利用 JVM 自身的机制来保证线程安全，并且只有在调用getInstance() 的时候才会去真正创建实例对象。

	// 静态内部类的方式
	public class Singleton {

		private static class InstanceHolder {
			private static final Singleton instance = new Singleton();
		}
		
		// 默认构造方法，私有
    	private Singleton() {}
		
		public static Singleton getInstance() {
			return InstanceHolder.instance;
		} 
	}

### 枚举的方式

说明：代码简洁，而且创建枚举默认是线程安全的，还可以防止反序列化带来的问题。

	// 枚举
	public enum Singleton {
	
		INSTANCE;
		
		// 自定义的其他任意方法
		public void otherMethod() {}
	}

## 单例模式的应用

当只需要一个实例对象的时候，就可以考虑使用单例模式。