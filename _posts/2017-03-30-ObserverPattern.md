---
layout: post
title: "观察者模式"
date: 2017/3/30 23:01:23 
description: "设计模式之观察者模式"
categories: [设计模式]
tags: [设计模式, 观察者]
---

# 观察者模式

----

## 什么是观察者模式

观察者模式就是一种“发布者——订阅者”模式。

也被称为“模型——视图”模式，“源——监听者”模式等。

在这种模式中，由一个目标对象来管理所有依赖于它的观察者对象，当这个目标对象自身发生变化时，会主动向它的观察者们发出通知。

## 观察者模式类图


## 观察者模式实例代码

	// Store 商店主题接口
	public interface Store {
		void registerObserver(Observer o);
		void removeObserver(Observer o);
		void notifyAllObserver();
	}

	// 书店，实现Store接口
	public class BookStore implements Store{
		private List<Observer> readerList;
		private List<String> books;
		
		public BookStore() {
			readerList = new ArrayList<Observer>();
			books = new ArrayList<String>();
		}
		
		@Override
		public void registerObserver(Observer o) {
			readerList.add(o);
		}
	
		@Override
		public void removeObserver(Observer o) {
			readerList.remove(o);
		}
	
		@Override
		public void notifyAllObserver() {
			for (Observer o : readerList) {
				o.update(this);
			}
		}
		
		public void addBooks(String book) {
			this.books.add(book);
			notifyAllObserver();
		}
		
		public List<String> getBooks() {
			return books;
		}
		
		@Override
		public String toString() {
			return books.toString();
		}
	}

	// 观察者接口
	public interface Observer {
		void update(Store s);
	}

	// 读者，实现Observer接口
	public class Reader implements Observer {
		private String name;
		
		public Reader(String username) {
			this.name = username;
		}
		
		@Override
		public void update(Store s) {
			System.out.println(name + ", a new book is on sale");
			System.out.println(s.toString());
		}
	}

	// 测试类
	public class ObserverPattern {
		public static void main(String[] args) {
			BookStore bookStore = new BookStore();
			
			bookStore.registerObserver(new Reader("Zhang san"));
			bookStore.registerObserver(new Reader("Li si"));
			bookStore.registerObserver(new Reader("Wang wu"));
			
			bookStore.addBooks("《深入理解计算机系统》");
		}
	}


运行结果为：

> Zhang san, a new book is on sale

> [《深入理解计算机系统》]

> Li si, a new book is on sale

> [《深入理解计算机系统》]

> Wang wu, a new book is on sale

> [《深入理解计算机系统》]




## 观察者模式的应用

各种 Listener。
