# Lamda表达式

```java
package com.aishang.controller;
@FunctionalInterface
public interface LamdaDemo {
	void sayHi(String name);
}

```

```java
package com.aishang.controller;

public class LamdaTest {
	public static void main(String[] args) {
		LamdaDemo lamdaDemo = new LamdaDemo() {
			@Override
			public void sayHi(String name) {
				// TODO Auto-generated method stub
				System.out.println("hello"+ name);
			}
		};
		
		lamdaDemo.sayHi("tom");
	}
}

```

```java
package com.aishang.controller;

public class LamdaTest {
	public static void main(String[] args) {
		LamdaDemo lamdaDemo  = (name)->{
			 System.out.println("hello"+name);
		};
		lamdaDemo.sayHi("tom");
	}
}

```

(Param1,param2)->{methodbody}

- 第一部分；一个括号内用逗号分割形式参数
- 第二部分->
- 第三部分：方法体或者表达式

```java
LamdaDemo lamdaDemo  = (name)->System.out.println("hello "+name);
LamdaDemo lamdaDemo  = (name)->"如果有return就这么写，hello"+name;
```

