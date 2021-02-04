# 继承

今天我们学继承，在学之前，先说下，面向对象的三大特征

- 封装
- 继承
- 多态

那么，我们先看一个程序

```java
class Student{
	String name;
	int age;
	void sleep() {
		System.out.println("ZZZzzzz");
	}
	void study() {
		System.out.println(name+"study");
	}
}
class Teacher {
	String name;
	int age;
	void sleep() {
		System.out.println("ZZZzzzz");
	}
	void teach() {
		System.out.println(name+"teach");
	}
}
```

通过这个程序，我们发现有什么问题？

代码重复了，那么我们是不是可以把重复的代码抽取出来

那么怎么抽取啊？

我们看下图![空白](https://ws3.sinaimg.cn/large/006tKfTcly1g0xslh19wzj30x00hd75q.jpg)

那么，在生活中我们知道继承，程序中怎么定义的？

- 一个类继承另一个类，被继承的叫父类，也叫基类
- 继承的类，叫子类，也叫派生类
- 继承的关键字是extends

继承了什么？

- 我们先看代码

```java
class Person {
	String name;
	int age;
	void sleep() {
		System.out.println("ZZZzzzz");
	}
}
class Student extends Person{
	void study() {
		System.out.println(name+"study");
	}
}
class Teacher extends Person{
	void teach() {
		System.out.println(name+"teach");
	}
}
```

打印

```java
Student student = new Student();
student.name = "tom";
student.age = 15;
student.sleep();
student.study();

Teacher teacher = new Teacher();
teacher.name = "刘老师";
teacher.age = 18;
teacher.sleep();
teacher.teach();
```

通过测试，子类继承了所有成员变量和方法，但是除了构造方法，

为什么，不能继承父类构造方法？

构造方法的作用？就是为对象初始化值

咱们反过来想，如果继承了父类的构造方法

```java
class Student extends Person {
	Person(){
		this.name = name;
	}
	void study() {
		System.out.println(name + "study");
	}
}
```

我是不是可以通过子类给父亲起名了，逆天了啊

继续看刚才我们的图，在java中是单继承还是多继承？

- 单继承

  - 优点：程序可追溯性，如果出现问题，能立即找到问题原因，多继承就比较麻烦了
  - 缺点：降低了程序的扩展性，那么你认为java没想到解决办法吗？那就是多态！

- java虽然是单继承，但是它支持多重继承

  - 好处：传递性，看代码

  ```java
  class A{
  	int a = 10;
  }
  class B extends A{
  }
  class C extends B{
  }
  
  
  
  C c = new C();
  System.out.println(c.a);
  ```

  

![空白](https://ws3.sinaimg.cn/large/006tKfTcly1g0xt7zdoo4j30x00hdwg7.jpg)

## 二、super

this指向调用者

super指向调用者的父类

- super.成员变量，调用父类的成员变量
- super()调用父类构造方法
- super.方法，调用父类的方法

那么我们先看程序

```java
package com.aishang.day05.a;

public class Test {
	public static void main(String[] args) {
		Student student = new Student();
		student.study();

	}
}

class Person {
	 String name;
	 int age;
	public Person() {
        this.name = "tom";
		System.out.println("我是父类的无参构造");
	}
	void sleep() {
		System.out.println("ZZZzzzz");
	}

}

class Student extends Person {
	public Student() {
		super();
		System.out.println("我是子类的无参构造");
	}
	void study() {
        super.sleep();
		System.out.println(super.name+"study");
	}
}

//结果是先到因父类，因为super()，好那么我把super去掉

```

- 结果是：子类继承父类，默认先调用父类的无参构造

- 你写不写super他都存在，为什么他要先调用父类？

  - 因为父类的成员变量可能变私有，一旦私有，子类就无法使用，我继承了一笔遗产，你不让我使用

  - 我通过调用父类构造方法，初始化值

    ```java
    package com.aishang.day05.a;
    
    public class Test {
    	public static void main(String[] args) {
    		Student student = new Student("tom",18);
    		student.study();
    
    	}
    }
    
    class Person {
    	private String name;
    	private int age;
    	public Person(String name,int age) {
    		this.name = name;
    		this.age = age;
    	}
    	void sleep() {
    		System.out.println(name+"ZZZzzzz");
    	}
    	
    
    }
    
    class Student extends Person {
    	public Student(String name,int age) {
    		super(name, age);
    	}
    	void study() {
    		System.out.println("study");
    	}
    }
    
    
    ```

  - 先说这样我是不是赋值了，但是不合理吧

  - 那怎么改，通过setget方法，我就可以在子类中获取name了,在父类生成setget方法，那么子类也相当于继承了，我就可以通过使用getName了

  - 如果子类调用父类的有参构造方法，那么super必须显示，

  - 为什么显示出来，因为默认调用无参构造

  - 并且必须在构造方法的第一行
    为什么是第一行，如果我先调用其他方法，getName方法，他没有初始化之，是不是null

  - 如果懂了，看下面代码

  ```java
  class A {
  	int a;
  	A(int a){
  
  	}
  }
  class B extends A{}
  //报错，子类继承父类默认调动父类的无参构造，而A有有参构造，系统就不提供无参构造
  
  ```

  那怎么改
  1、给父类一个无参构造
  2、子类一个有参构造，之后super（a） 调用父类的的有参构造

## 三、方法重写

看代码

```java
class Fu {
	String name;
	ing age;
	void speak(){
		我说的是英文
	}
}
class Zi extends Fu {
	void speak(){
		我说是中文
	}//如果我里面参数，相当于是两个方法，是子类特有的
}
```

打印结果是什么？中文，为什么？

这就是我们要学的方法重写

- 方法重写，方法名相同，返回值类型相同，参数列表相同，而且在继承关系中
- 方法重载：方法名相同，与返回值无关，参数个数，参数类型不同

注意：如果子类重写了父类的方法，子类重写的方法的权限必须大于或等于父类被重写的方法。

- 子类可以新增自己独有的属性和方法

## 四、多态的初步认识

多态很重要，用的最多的是多态
多态有个前提条件
必须存在关系
继承关系
实现关系  明天
什么是多态？
一个对象在不同情况下的多种状态

提高了程序的扩展性

```java
package com.aishang.day05.a;

public class Test {
	public static void main(String[] args) {
		Dog dog = new Dog();
		dog.sound();
		Cat cat = new Cat();
		cat.sound();
	}
}
class Animal {
	String name;
	void sound() {
		System.out.println("动物叫");
	}
}
class Dog extends Animal{
	void sound() {
		System.out.println("汪汪叫");
	}
}
class Cat extends Animal{
	void sound() {
		System.out.println("喵喵叫");
	}
}

```

如果动物很多呢，每次是不是都需要创建一个对象，调用叫的方法

```java
Animal animal = new Dog();
animal.sound();
```

这就是多态：

左面是父类类型，右面是子类类型

- 父类有，子类有，打印的是子类
- 父类有，子类没有，打印的是父类
- 父类没有，子类有，报错，父类没有sound，你怎么调用
- 总结：编译看左边，运行看右边
- 成员变量呢？编译运行都看左边，想内存图，调用的就是父类里的成员变量

那么怎么就扩展了？

```java
public static void main(String[] args) {
		getAnimal(new Cat());
	}

	public static void getAnimal(Animal animal) {
		animal.sound();
	}
```

传什么就是什么，是不是多态

## 五、final

修饰的类，不能被继承  
修饰的方法，不能重写	
修饰的成员变量，不能被修改	
修饰的对象，不能重新创建 

```java
final Animal animal = new Animal();
		Animal animal2 = new Animal();
		animal = animal2;
```

