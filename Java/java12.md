# 静态

### 程序

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		Person person = new Person();
		person.name = "tom";
		person.age = 18;
		person.sleep();
	}
}

class Person {
	String name;
	int age;
  void speak() {
		System.out.println(name+" speak  Chinese");
	}
	void sleep() {
		System.out.println("ZZzz..");
	}
}

```

通过上面代码，我们分析，如果有成员变量，那么在堆空间开辟的空间有意义，那么如果一个类里，没有成员变量，意味着，当我创建对象的时候，我没有指向堆空间，但是，就白白开辟了堆空间，浪费了内存，而sleep这个方法，并没有访问成员变量，那么这对象就没有存在的意义了，那么怎么办？

- 因为sleep方法没有访问对象中的数据，因此我可以让他不属于对象，让他属于类，那么怎么让他属于类？java就给我提供了方法

## 一、静态方法

- 被static修饰的方法叫静态方法，可以通过类名直接访问，也可以通过类中的对象访问，被所有对象共享

- 修改程序，演示两种使用方法

  ```java
  package com.aishang.day12;
  
  public class Demo {
  	public static void main(String[] args) {
  		Person person = new Person();
  		person.name = "tom";
  		person.age = 18;
  		person.sleep();
  		Person.sleep();
  	}
  }
  
  class Person {
  	String name;
  	int age;
  	void speak() {
  		System.out.println(name+" speak  Chinese");
  	}
  	static void sleep() {
  		System.out.println("ZZzz..");
  	}
  }
  
  ```

  那么静态方法使用这么方便，都不用创建对象，那么我们以后都用静态方法呗？不是

- 什么时候用静态方法？

  - 看这个方法是否访问了类中的成员变量，如果这个方法没有访问这个类中的成员变量，即认为这个方法与上下文无关，那么就可以使用static修饰,那么不就是所有对象共享

- 修改程序

  - 把speak用static修饰，发现报错，原因是静态方法只能访问静态变量，为什么？
  - 静态方法属于类，而name属于对象，从加载时机上考虑，是不是类先加载，现有的字节码文件，此时你必须创建对象吗？没有吧
  - 比如教室我先进来的，我知道下一个人进来的是谁吗？不知道，但是，后进来的一定知道屋里的有谁
  - 那么他也就不能与this，super共存，加载时机不同
  - 那么我把变量也用static修饰，程序就可以了
  - 那么非静态方法能否访问静态变量？可以，加载时机

- 静态方法和普通方法的区别

## 二、静态变量

- 被static修饰的变量就静态变量

- 同样静态变量，就不属于对象了，而属于类，被所有对象共享

  程序

  ```java
  package com.aishang.day12;
  
  public class Demo {
  	public static void main(String[] args) {
  		Person person = new Person();
  		Person person1 = new Person();
  		person.age = 10;
  		person.show();
  		person1.age = 20;
  		person1.show();
  		System.out.println(person.age);
  	}
  }
  
  class Person {
  	static String name;
  	static int age;
  	static void speak() {
  		System.out.println(name+" speak  Chinese");
  	}
  	static void sleep() {
  		System.out.println("ZZzz..");
  	}
  	void show() {
  		System.out.println(age);
  	}
  }
  
  ```

  发现年龄被修改了，因为静态变量被所有对象共享

- 静态变量和成员变量的区别

  - 范围不同：静态变量属于类，成员变量属于对象
  - 调用方式：静态变量可以被类直接调用，也可以被对象调用，成员变量只能被对象调用
  - 加载时机不同：静态变量随着类的加载而加载，成员变量随着对象的加载而加载
  - 存储区域不同：静态变量存储在方法区中，成员变量存储在堆区

- 程序：圆形面积

  ```java
  package com.aishang.day12;
  
  public class Demo {
  	public static void main(String[] args) {
  		Yuan yuan = new Yuan();
  		double area = yuan.getArea(3);
  		System.out.println(area);
  	}
  }
  
  class Yuan {
  	double pi = 3.14;
  	double getArea(int r) {
  		return pi*r*r;
  	}
  }
  
  ```

  - 目前，这个程序，方法不能静态，因为使用了成员变量

  - 分析，pi能否用静态修饰？可以，因为pi是共享的，想让共享就用static修饰,而且不能修改

    ```java
    package com.aishang.day12;
    
    public class Demo {
    	public static void main(String[] args) {
    		Yuan yuan = new Yuan();
    		double area = yuan.getArea(3);
    		System.out.println(area);
    	}
    }
    
    class Yuan {
    	final static double pi = 3.14;
    	double getArea(int r) {
    		return pi*r*r;
    	}
    }
    
    ```

  - 什么时候使用静态变量：当该成员变量被所有对象共享，且该成员变量的值在每个对象中都一致时，则该成员变量用static修饰，使其中普通成员变量修改为静态成员变量。

## 三、静态代码块

- static{}

  ```java
  package com.aishang.day12;
  
  public class Demo {
  	public static void main(String[] args) {
  		Person.run();
  	}
  }
  class Person{
  	public Person() {
  		// TODO Auto-generated constructor stub
  		System.out.println("构造方法");
  	}
  	static void run () {
  		System.out.println("静态方法");
  	}
  	static {
  		System.out.println("静态代码块");
  	}
  }
  
  
  ```

  结果先执行静态代码块，在执行静态方法，静态代码块随着类的加载而加载，优先执行，而且只执行一次

## 四、构造代码块

{}写在方法外，类里

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		Person person = new Person();
	}
}
class Person{
	public Person() {
		// TODO Auto-generated constructor stub
		System.out.println("构造方法");
	}
	{
		System.out.println("构造代码块");
	}
	static void run () {
		System.out.println("静态方法");
	}
	static {
		System.out.println("静态代码块");
	}
}

//结果
静态代码块
构造代码块
构造方法

```



## 五、局部代码块

{}写在方法里，控制作用域

## 六、单例模式

有些时候，我只想让一个类只有一个对象，例如，钢铁侠只有一个

我要是可以随便创建，那么钢铁侠就满天飞了

那怎么办，单例模式

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		IronMan ironMan = new IronMan();
		IronMan ironMan2 = new IronMan();
		System.out.println(ironMan==ironMan2);
	}
	
	
}
class IronMan {
	
	
	public IronMan() {

	}
	void fly() {
		System.out.println("正在飞。。");
	}
}


```

目前是钢铁侠满天飞，我想new的时候，就只有一个

单例分两种

- 饿汉式：类一加载就创建该类对象

  - 先把构造方法私有，外接无法创建了

  - 在内部创建一个私有的对象

  - 写个外界可以获取的实例的方法

    ```java
    package com.aishang.day12;
    
    public class Demo {
    	public static void main(String[] args) {
    		IronMan i1 = IronMan.getInstance();
    		IronMan i2 = IronMan.getInstance();
    		System.out.println(i1==i2);
    	}
    	
    	
    }
    class IronMan {
    	private IronMan() {
    	}
    	private static final IronMan ironMan = new IronMan();
    	public static IronMan getInstance() {
    		return ironMan;
    	}
    	void fly() {
    		System.out.println("man正在飞。。");
    	}
    }
    
    
    ```

    

- 懒汉式：什么时候用在创建对象

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		IronMan i1 = IronMan.getInstance();
		IronMan i2 = IronMan.getInstance();
		i1.fly();
		System.out.println(i1==i2);
	}
	
	
}
class IronMan {
	private IronMan() {
	}
	private static IronMan ironMan = null;
	public static IronMan getInstance() {
		if (ironMan == null) {
			ironMan = new IronMan();
		}
		return ironMan;
	}
	void fly() {
		System.out.println("man正在飞。。");
	}
}


```

程序

1、小孩堆雪人，打印当前有几个小孩在堆雪人

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		Child.play();
		Child.count++;
		System.out.println(Child.count);
		Child.play();
		Child.count++;
		System.out.println(Child.count);
	}
	
	
}
class Child {
	static int count ;
	static void play() {
		System.out.println("在堆雪人");
	}
}


```

改造代码

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		Child.play();
		System.out.println(Child.count);
		Child.play();
		System.out.println(Child.count);
	}
	
	
}
class Child {
	static int count ;
	static void play() {
		System.out.println("在堆雪人");
		count++;
	}
}


```



2、统计每个学生缴费金额，计算总学费

```java
package com.aishang.day12;

public class Demo {
	public static void main(String[] args) {
		Student student = new Student();
		student.money = 10000;
		student.total();
		Student student1 = new Student();
		student1.money = 1000;
		student1.total();
		Student student2 = new Student();
		student2.money = 2000;
		student2.total();
	}
	
	
}
class Student {
	int money;
	static int count;
	void total() {
		count+=money;
		System.out.println(count);
	}
}


```



## 七、改造jdbc代码

```java
package com.aishang.day12;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import com.mysql.cj.exceptions.RSAException;

public class ConnUtil {
	static String url = "jdbc:mysql://127.0.0.1:3306/1901?useSSL=false";
	static String user = "root";
	static String password = "password";
	static {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} 
	}
	public static Connection getConection() {
		try {
		Connection connection = DriverManager.getConnection(url, user, password);
		}catch (SQLException e) {
			e.printStackTrace();
		}
		return connection;
	}
	
	public static void close(Connection connection, PreparedStatement prepareStatement, ResultSet rs) {
		try {
			if (rs != null) {
				rs.close();
			}
			if (prepareStatement != null) {
				prepareStatement.close();
			}
			if (connection != null) {
				connection.close();
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}

```

UserDao

```java
package com.aishang.day12;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class UserDao {
	Connection connection ;
	PreparedStatement prepareStatement;
	ResultSet rs;
	// 添加的方法
	public Boolean insert(User user) {
		try {
            connection = ConnUtil.getConection();
			prepareStatement = connection.prepareStatement("insert into tb_user values(default,?,?,?,?,?,?)");
			prepareStatement.setString(1, user.getUsername());
			prepareStatement.setString(2, user.getPassword());
			prepareStatement.setString(3, user.getSex());
			prepareStatement.setInt(4, user.getAge());
			prepareStatement.setString(5, user.getAddr());
			prepareStatement.setString(6, user.getTel());
			prepareStatement.executeUpdate();
			return true;
		} catch (SQLException e) {

		} finally {
			DBUtil.close(conn,ps,rs)
		}

		return false;
	}

	// 删除的方法
	public void del(int id) {
		try {
             connection = ConnUtil.getConection();
			prepareStatement = connection.prepareStatement("delete from tb_user where id = ?");
			prepareStatement.setInt(1, id);
			int n = prepareStatement.executeUpdate();
			if (n == 1) {
				System.out.println("删除成功");
			}
		} catch (Exception e) {
			// TODO: handle exception
		} finally {
			try {
				if (prepareStatement != null) {
					prepareStatement.close();
				}
				if (connection != null) {
					connection.close();
				}
			} catch (Exception e) {
			}

		}
	}

	// 修改方法
	public void update(int id, String name) {
		try {
             connection = ConnUtil.getConection();
			prepareStatement = connection.prepareStatement("select * from tb_user where id = ?");
			prepareStatement.setInt(1, id);
			ResultSet rs = prepareStatement.executeQuery();
			if (rs.next()) {
				System.out.println("有该用户");
				prepareStatement = connection.prepareStatement("update tb_user set username = ? where id = ?");
				prepareStatement.setString(1, name);
				prepareStatement.setInt(2, id);
				prepareStatement.executeUpdate();
			} else {
				System.out.println("修改用户不存在");
			}
		} catch (Exception e) {
			// TODO: handle exception
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
				if (prepareStatement != null) {
					prepareStatement.close();
				}
				if (connection != null) {
					connection.close();
				}
			} catch (Exception e) {
			}

		}
	}

	// 模糊查询
	public List<User> findUser(String name) {
		List<User> list = new ArrayList<User>();
		try {
             connection = ConnUtil.getConection();
			prepareStatement = connection.prepareStatement("select * from tb_user where username like ?");
			prepareStatement.setString(1, "%" + name + "%");
			rs = prepareStatement.executeQuery();
			while (rs.next()) {
				User user = new User();
				String username = rs.getString("username");
				user.setId(rs.getInt("id"));
				user.setUsername(username);
				user.setPassword(rs.getString("password"));
				user.setSex(rs.getString("sex"));
				user.setAge(rs.getInt("age"));
				user.setAddr(rs.getString("addr"));
				user.setTel(rs.getString("tel"));
				list.add(user);
			}
			return list;
		} catch (Exception e) {
			// TODO: handle exception
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
				if (prepareStatement != null) {
					prepareStatement.close();
				}
				if (connection != null) {
					connection.close();
				}
			} catch (Exception e) {
			}

		}
		return list;
	}

	// 登录
	public Boolean login(String name, String password) {
		try {
             connection = ConnUtil.getConection();
			prepareStatement = connection.prepareStatement("select * from tb_user where username = ? and password = ?");
			prepareStatement.setString(1, name);
			prepareStatement.setString(2, password);
			rs = prepareStatement.executeQuery();
			if (rs.next()) {
				return true;
			}
		} catch (Exception e) {
			// TODO: handle exception
		}
		return false;
	}
}

```
















