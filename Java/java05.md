# 面向对象

## 一、什么是面向对象

面向过程侧重执行者

面向对象侧重指挥着

买电脑为例

- 什么是类
- 什么是对象

创建对象

使用对象

内存图

## 二、成员变量和局部变量

成员变量与局部变量的区别：

- 生命周期
  - 局部变量随着方法的调用而创建，随着方法的消失而消失
  - 成员变量随着对象的创建而创建，随着对象的消失而消失
- 位置
  - 局部变量写在方法里，或声明在方法上的参数列表里
  - 成员变量写在方法外，类里
- 默认值
  - 成员变量有默认值
  - 局部变量没有默认值
- 内存
  - 成员变量存储在堆内存
  - 局部变量存储在栈内存

## 三、构造方法

什么是构造方法？

作用

默认无参构造

普通方法和构造方法的区别

## 四、this关键字

代表当前类本身,指向调用者

## 五、方法重载

什么是方法重载

在一个类中，与返回值无关，与参数个数，类型有关

## 六、访问修饰符

变成私有，怎么访问，通过setget

## 七、setget方法





【程序1】创建Person类，并调用。目的：理解对象，了解面向对象程序设计的步骤。

```java
public class Person {
	//定义成员变量：特征
	String perName;  //姓名
	int perAge;	     //年龄
	String perSex;   //性别
	
	//定义方法：功能
	void work(){  //工作有关的功能，不需要返回值和参数
		if("男".equals(perSex)){
			System.out.println("作为一个男人要多挣钱，养家糊口");
		}
		else{
			System.out.println("女人负责貌美如花");
		}
	}
	
	void study(){ //学习有关的功能，不需要返回值和参数
		if(perAge<18){
			System.out.println("还未成年，需要好好学习！");
		}
		else{
			System.out.println("已经成年了，需要更多的时间工作，业余时间学习！");
		}
	}
}
```

```java
Person p1 = new Person();
		
		//给属性赋值，类中的方法依赖于属性的值
		p1.perName = "刘晓丽";
		p1.perAge = 15;
		p1.perSex ="女";
		
		//调用方法
		System.out.println("有一个人名字叫："+p1.perName);
		p1.study();
		p1.work();
		
		Person p2 = new Person();
		p2.perName = "张涛";
		p2.perSex = "男";
		p2.perAge = 35;
		
		System.out.println("有一个人名字叫："+p2.perName);
		p2.study();
		p2.work();
		
```

