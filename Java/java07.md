# 抽象类和接口

## 一、抽象类

在将抽象类之类之前，我们回想我们昨天的动物，猫狗的代码

```java
package com.aishang.day06.a;

public class Test {
	public static void main(String[] args) {
		
	}
}
class Animal {
	String name;
	void sound() {
		System.out.println();
	}
}
class Dog {
	String name;
	void sound() {
		System.out.println("汪汪叫");
	}
}
class Cat {
	String name;
	void sound() {
		System.out.println("喵喵叫");
	}
}

```

看我们现在的代码，猫狗叫没问题，但是动物叫不合适吧，因为动物有很多种，叫声都不一样，我要是写动物叫也不合适，那么怎么办，类是不是我们抽象出来，对一类事物的描述，那么叫声，我也不知道该怎么描述了，那么我就不要这个方法体了，OK吧.

- 看老师是不是报错了，不是不可以，因为你需要一个关键词abstract修饰
- 那么一个方法要是被abstract修饰的，我们叫抽象方法
- 依然报错，这里需要注意，如果一个类里有抽象方法，那么，这个类也必须用abstract修饰，那么背abstract修饰的类，我们叫抽象类

### 抽象类存在有什么意义？

- 因为有了抽象方法，就不用描述具体事物了，类已经是我抽象来的，他还是在类的基础上在抽象，就是为了子类重写，我知道这件事必须做，但具体怎么做，由子类重写完成
- 开发项目，项目经理，不会给你编代码。他会根据业务需求，编写好抽象类，相当于这个需求必须有，但怎么做，由你们去做，是不是符合面向对象的思想。

简单总结：就是让子类重写抽象方法

那么，我现在再写一个老虎类

```java
abstract class Animal {
	String name;
	abstract void sound();
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
class Tiger extends Animal{
	
}
```

继承之后为什么报错？

- 一个类继承了一个抽象类，必须重写该类的所有抽象方法！
- 抽象类实际上是一种约束，早晚有一天你会当领导，你会让别人干，至于怎么干我不管，我要结果

### 抽象类能不能实例化？

实例化：就是创建对象的意思

- 不能，也就是不能创建对象，new
- 因为本身就是类的在抽象，我都不知道怎么描述，对象怎么具体化

### 抽象类有没有构造方法？

- 有
- 因为父类的成员变量可能私有，一但私有，需要通过父类的构造方法帮子类初始化值，super(name.age)

### 总结

如果一个类中的某个方法没有具体的实现，那么就可以不写方法，没有方法体的方法必须用abstract修饰，
这个方法就是抽象方法，如果这个类中存在抽象方法，
那么这个类就无法描述具体的事物，那么这个类也需要
abstract修饰，那么被abstract修饰的类就是抽象类

抽象类不能创建对象，因为没有办法描述
存在构造方法，原因是要为子类初始化继承的成员变量

存在的意义：要有子类，为子类提供具体约束
Animal a = new Animal()
报错  
Animal a = new Dog()
a.name
a.sound

### 抽象类可不可以有普通方法？

- 可以

  ```java
  abstract class Animal {
  	String name;
  	abstract void sound();
  	void sleep() {
  		
  	}
  }
  ```

### 多态程序1,

狗有特有属性爱好吃骨头，怎么访问子类特有的属性

```java
package com.aishang.day06.a;

public class Test {
	public static void main(String[] args) {
		Animal animal = new Dog();
		System.out.println(animal.hobby);
	}
}

abstract class Animal {
	String name;
	abstract void sound();
	void sleep() {
		
	}
	void show() {
		System.out.println(name);
	}
}

class Dog extends Animal {
	String hobby;
	public Dog() {
		this.hobby = hobby;
	}
	void sound() {
		System.out.println("汪汪叫");
	}
	void show() {
		super.show();
		System.out.println("爱吃" + hobby);
	}
}
```

违反多态原则，编译看左边

那怎么办？

- 需要强制转换

```java
package com.aishang.day06.a;

public class Test {
	public static void main(String[] args) {
		Animal animal = new Dog();
//		System.out.println(animal.hobby);
		Dog dog = (Dog)animal;
		dog.name = "tom";
		dog.hobby = "骨头";
		dog.show();
	}
}

abstract class Animal {
	String name;
	abstract void sound();
	void sleep() {
		
	}
	void show() {
		System.out.println(name);
	}
}

class Dog extends Animal {
	String hobby;
	public Dog() {
		this.hobby = hobby;
	}
	void sound() {
		System.out.println("汪汪叫");
	}
	void show() {
		super.show();
		System.out.println(name+"爱吃" + hobby);
	}
}

```

- 程序有没有问题？

- 有个大bug，看我改下你们能不能看出来？

  ```java
  public static void main(String[] args) {
  		Animal animal = new Cat();
  //		System.out.println(animal.hobby);
  		Dog dog = (Dog)animal;
  		dog.name = "tom";
  		dog.hobby = "骨头";
  		dog.show();
  	}
  
  
  
  Exception in thread "main" java.lang.ClassCastException: com.aishang.day06.a.Cat cannot be cast to com.aishang.day06.a.Dog
  	at com.aishang.day06.a.Test.main(Test.java:7)
  ```

- 报错，因为你可以说猫狗是动物，你能说动物就是猫吗？

  ```java
  Animal animal = new Dog();//new Cat();
  		if(animal instanceof Cat) {
  			Cat cat = (Cat)animal;
  			cat.sound();
  		}else {
  			System.out.println("类型转换错误");
  		}
  
  
  子类类型可以自动转换为父类类型
  父转子需强制转换
  保证程序的健壮性，务必要判断类型
  ```

我们看这个抽象类

如果我吧抽象方法删掉，那么意味着什么，这个类里，没有抽象方法，那就意味着这是一个普通类，那么普通类你还用abstract修饰，那我就创建不了对象，那你认为这个类还有用吗？

所以java也想到了这个问题，他就给我提供了接口

## 接口

抽象类开发用的不多，都用接口

接口的关键字是interface，我们通常说一个类继承另一个类，那么接口我们通常说一个类实现了一个接口

实现的关键字是implements

一个实现接口的类，我们叫接口的实现类

- 什么是接口？

  - 是抽象类的在抽象
  - 接口中方法必须都是抽象方法

  ```java
  interface A {
  	public abstract void sound();
  	public abstract void eat();
  }
  ```

- 如果我把public abstract删掉，不报错，因为接口中定义方法就是抽象的，你不写，他也在方法前给你加这两个修饰符，不写不代表是默认，那么我们到底写不写，不写，阿里的开发规范中，不要求写，提高一丢丢效率
- 子类不能缩小父类的权限

### 接口有没有成员变量？

- 有
- 但是是final static 修饰的
- 你只声明就报错，用final修饰的不能改变
- 为什么用final修饰？
  - 接口的目的就是让子类实现的，如果我实现了就相当于有了成员变量，我这是可以改吧，我把税点改了，别人用的时候值就变了吧！

### 接口有没有构造方法？

- 没有
- 因为所有方法必须是抽象的，也就意味着没有方法体，并且成员变量是final的，构造方法是赋值的，你final的怎么赋值

### 接口的作用？

- 提高程序的扩展性![img](https://img.mubu.com/document_image/269fa96a-ddec-4fc0-8c75-1281f579c1ed-2051352.jpg)

### 

- 通过这个图，java中单继承，但接口是多实现，多继承的

  - 一个类可以实现多个接口

    - TIger implements Inter,Inter

      - 报错，实现一个接口，必须实现所有方法

      - 实现飞，说话方法

        ```java
        interface A {
        }
        interface C {
        }
        interface B {
        }
        class Demo implements A,B,C{
        	
        }
        ```

        

  - 一个类既继承了类，有实现了接口

    - 继承在前，实现在后

      ```java
      class Demo extends Animal implements A,B,C{
      
      	@Override
      	void sound() {
      		// TODO Auto-generated method stub
      		
      	}
      	
      }
      ```

      

  - 接口可以多继承

    ```java
    interface A {
    }
    interface C extends A,B {
    }
    interface B {
    }
    ```

    为什么可以多继承？因为我继承的都是抽象方法，扩展

- 接口的开闭原则

  - 对修改关闭、对扩展开放
  - 提供了约束、规范

- 接口是类吗？

  - 不是，类是单继承

###  接口和抽象类的区别？

## 接口的多态？

程序：编写笔记本案例，笔记本的usb是不是可以插鼠标和u盘，那么我们模拟下，扩展吗？

```java
package com.aishang.day06.a;

public class Test {
	public static void main(String[] args) {
		Mouse mouse = new Mouse();
		mouse.insert();
		mouse.remove();
		Upan upan = new Upan();
		upan.insert();
		upan.remove();
	}
}

class Mouse {
	void insert() {
		System.out.println("mouse insert");
	}
	void remove() {
		System.out.println("mouse remove");
	}
}
class Upan {
	void insert() {
		System.out.println("Upan insert");
	}
	void remove() {
		System.out.println("Upan remove");
	}
}

```



```java
package com.aishang.day06.a;

public class Test {
	public static void main(String[] args) {
		Mouse mouse = new Mouse();
		mouse.insert();
		mouse.remove();
		Upan upan = new Upan();
		upan.insert();
		upan.remove();
		
		Usb usb = new Mouse();
		usb.insert();
		usb.remove();
	}
}
interface Usb {
	void insert();
	void remove();
}
class Mouse implements Usb{

	@Override
	public void insert() {
		System.out.println("mouse insert");
	}

	@Override
	public void remove() {
		System.out.println("mouse remove");
	}
	
}
class Upan implements Usb{

	@Override
	public void insert() {
		System.out.println("Upan remove");
	}

	@Override
	public void remove() {
		System.out.println("Upan remove");
	}
	
}

```

## 适配器模式

- 设计模式（Design pattern）代表了最佳的实践，通常被有经验的面向对象的软件开发人员所采用。设计模式是软件开发人员在软件开发过程中面临的一般问题的解决方案。这些解决方案是众多软件开发人员经过相当长的一段时间的试验和错误总结出来的。

- 23种设计模式

- 需求，接口的实现类是不是必须全部实现接口的方法

  - 现在接口里有五个方法

  - 但我只想用其中2个怎么？

    ![img](https://img.mubu.com/document_image/f1cf2f4a-00e9-487c-9262-ed1ed697efb3-2051352.jpg)

代码

```java
package com.aishang.day06.a;

public class Test {
	public static void main(String[] args) {
		
	}
}
interface YiChan {
	void eat();
	void play();
	void sleep();
	void pay();
	void debt();
}

class LeiFeng implements YiChan {

	@Override
	public void eat() {
	}

	@Override
	public void play() {
	}

	@Override
	public void sleep() {
	}

	@Override
	public void pay() {
	}

	@Override
	public void debt() {
	}
	
}

class My extends LeiFeng {

	@Override
	public void sleep() {
		super.sleep();
	}

	@Override
	public void pay() {
		super.pay();
	}
	
}

```

