---
layout: post
title: "装饰者模式"
date: 2017/3/29 21:13:58 
description: "设计模式之装饰者模式"
categories: [设计模式]
tags: [设计模式, 装饰者]
---

# 装饰者模式

-----

## 什么是装饰者模式

装饰者模式，就是动态地把职责附加到已有对象上去，实现功能扩展。

这种特性，使得装饰者模式提供了比继承更有弹性的解决方案。

## 装饰者模式类图



## 装饰者模式示例代码

	// 抽象类 Girl
	public abstract class Girl {
		String description = "no particular";

		public String getDescription() {
			return description;
		}	
	}

	// 美国女孩
	public class AmericanGirl extends Girl {
		public AmericanGirl() {
			description = "American girl";
		}
	}

	// 中国女孩
	public class ChineseGirl extends Girl {
		public ChineseGirl() {
			description = "Chinese Girl";
		}
	}

	// 装饰者类
	public abstract class GirlDecorator extends Girl {
		public abstract String getDescription();
	}

	// 扩展美国女孩，加上金发
	public class GoldenHair extends GirlDecorator {
		private Girl girl;
		public GoldenHair(Girl g) {
			this.girl = g;
		}
		
		@Override
		public String getDescription() {
			return girl.getDescription() + "+with golden hair";
		}
	}

	// 测试
	public class Test {
		public static void main(String[] srgs) {
			Girl g1 = new AmericanGirl();
			System.out.println(g1.getDescription());
			
			GoldenHair g2 = new GoldenHair(g1);
			System.out.println(g2.getDescription());
		}
	}



## 装饰者模式的应用

当需要动态地给对象添加功能，实现功能扩展的时候，就可以使用装饰者模式。

经典应用：Java IO 类中，BufferedReader 装饰了 InputStreamReader。

	BufferedReader input = new BufferedReader(new InputStreamReader(System.in));


## 装饰者模式、适配器模式的区别

* 关于新职责：适配器也可以在转换时增加新的功能，但其主要目的不在于此；而装饰者模式的主要目的，就是给被装饰这增加新职责。
* 关于原接口：适配器模式是用新接口来调用原接口，原接口对新系统来说是不可见或者不可用的；而装饰者模式原封不动地使用原接口，系统对装饰的对象也通过原接口来完成使用。
* 关于被包裹的对象：适配器是知道被适配者的详细情况的（就是那个类或者接口）；而装饰者只知道其接口是什么，至于其具体类型（是基类还是其他派生类），只有在运行期间才知道。