以火车站售票案例

```java
package com.aishang.day14;

public class Demo {
	public static void main(String[] args) {
		Ticket ticket = new Ticket();
		Thread t1 = new Thread(ticket);
		Thread t2 = new Thread(ticket);
		Thread t3 = new Thread(ticket);
		
		t1.start();
		t2.start();
		t3.start();
	}
}
class Ticket implements Runnable{
	int ticket = 100;

	@Override
	public void run() {
		for (int i = 1; i <= 100; i++) {
			if (ticket>0) {
				try {
					Thread.sleep(10);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"正在出售第"+(ticket--)+"票");
			}
		}
	}
	
}
```

打印结果是，出现重复票和负数票

原因：

产生线程安全问题的原因：

- 是否是多线程环境
- 是否有共享数据
- 是否有多条语句操作共享数据

同步代码块

```java
package com.aishang.day14;

public class Demo {
	public static void main(String[] args) {
		Ticket ticket = new Ticket();
		Thread t1 = new Thread(ticket);
		Thread t2 = new Thread(ticket);
		Thread t3 = new Thread(ticket);
		
		t1.start();
		t2.start();
		t3.start();
	}
}
class Ticket implements Runnable{
	int ticket = 100;
	@Override
	public void run() {
		for (int i = 1; i <= 100; i++) {
			synchronized (new Object()) {
				if (ticket>0) {
					try {
						Thread.sleep(10);
					} catch (InterruptedException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
					System.out.println(Thread.currentThread().getName()+"正在出售第"+(ticket--)+"票");
				}
			}
			
		}
	}
	
}
```

假设我就两个窗口，我模拟下，人为干预下，第一个窗口卖几张就全部由第二个窗口出售

```java
package com.aishang.day14;

public class Demo {
	public static void main(String[] args) {
		Ticket ticket = new Ticket();
		Thread t1 = new Thread(ticket);
		Thread t2 = new Thread(ticket);
		
		t1.start();
		for (int i = 0; i < 10; i++) {
			Thread.sleep(100);
			ticket.setFlag(i%2==0?true:false);
		}
		t2.start();
        for (int i = 0; i < 10; i++) {
			Thread.sleep(100);
			ticket.setFlag(i%2==0?true:false);
		}
	}
}
class Ticket implements Runnable{
	int ticket = 100;
	private boolean flag = true;
	public void setFlag(boolean flag) {
		this.flag = flag;
	}
	@Override
	public void run() {
		for (int i = 1; i <= 100; i++) {
			if (flag) {
				synchronized (new Object()) {
					if (ticket>0) {
						try {
							Thread.sleep(10);
						} catch (InterruptedException e) {
							// TODO Auto-generated catch block
							e.printStackTrace();
						}
						System.out.println(Thread.currentThread().getName()+"true正在出售第"+(ticket--)+"票");
					}
				}
			}else {
				synchronized (new Object()) {
					if (ticket>0) {
						try {
							Thread.sleep(10);
						} catch (InterruptedException e) {
							// TODO Auto-generated catch block
							e.printStackTrace();
						}
						System.out.println(Thread.currentThread().getName()+"false正在出售第"+(ticket--)+"票");
					}
				}
			}
			
			
		}
	}
	
}
```

由于使用的不是同一把锁，还是有线程安全问题

```java
package com.aishang.day14;

public class Demo {
	public static void main(String[] args) {
		Ticket ticket = new Ticket();
		Thread t1 = new Thread(ticket);
		Thread t2 = new Thread(ticket);
		
		t1.start();
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		ticket.setFlag(false);
		t2.start();
	}
}
class Ticket implements Runnable{
	int ticket = 100;
	private boolean flag = true;
	Object object = new Object();
	public void setFlag(boolean flag) {
		this.flag = flag;
	}
	@Override
	public void run() {
		for (int i = 1; i <= 100; i++) {
			if (flag) {
				synchronized (object) {
					if (ticket>0) {
						try {
							Thread.sleep(10);
						} catch (InterruptedException e) {
							// TODO Auto-generated catch block
							e.printStackTrace();
						}
						System.out.println(Thread.currentThread().getName()+"true正在出售第"+(ticket--)+"票");
					}
				}
			}else {
				synchronized (object) {
					if (ticket>0) {
						try {
							Thread.sleep(10);
						} catch (InterruptedException e) {
							// TODO Auto-generated catch block
							e.printStackTrace();
						}
						System.out.println(Thread.currentThread().getName()+"false正在出售第"+(ticket--)+"票");
					}
				}
			}
			
			
		}
	}
	
}
```

那么 我改进下代码，把他抽取成一个方法

```java
package com.aishang.day14;

public class Demo {
	public static void main(String[] args) {
		Ticket ticket = new Ticket();
		Thread t1 = new Thread(ticket);
		Thread t2 = new Thread(ticket);
		t1.start();
		try {
			Thread.sleep(500);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		ticket.setFlag(false);
		t2.start();
		try {
			Thread.sleep(500);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		ticket.setFlag(true);

	}
}

class Ticket implements Runnable {
	int ticket = 100;
	Object obj = new Object();
	Boolean flag = true;

	public void setFlag(Boolean flag) {
		this.flag = flag;
	}

	@Override
	public void run() {
		// TODO Auto-generated method stub
		
			for (int i = 1; i <= 105; i++) {
				if (flag) {
				synchronized (new Dog()) {
					if (ticket > 0) {
						try {
							Thread.sleep(10);
						} catch (InterruptedException e) {
							// TODO Auto-generated catch block
							e.printStackTrace();
						}
						System.out.println(Thread.currentThread().getName() + "true正在出售第" + (ticket--) + "张票");
					}
				}
			} else {
				show();
			}

		}
	}

	public synchronized void show() {

		if (ticket > 0) {
			try {
				Thread.sleep(10);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName() + "false正在出售第" + (ticket--) + "张票");
		}

	}

}

class Dog {

}
```

那么，如果一个方法一进来就是同步代码块，那么我们何不这么写，这就叫同步方法！

```java
public synchronized void show() {
		
			if (ticket>0) {
				try {
					Thread.sleep(10);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+"false正在出售第"+(ticket--)+"票");
			}
		
	}
```

那么问题来了，同步方法的锁是什么？

- 我先运行下，看出没出安全问题，如果有那么就证明不是一个锁

- this

  ```java
  package com.aishang.day14;
  
  public class Demo {
  	public static void main(String[] args) {
  		Ticket ticket = new Ticket();
  		Thread t1 = new Thread(ticket);
  		Thread t2 = new Thread(ticket);
  		
  		t1.start();
  		try {
  			Thread.sleep(100);
  		} catch (InterruptedException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}
  		ticket.setFlag(false);
  		t2.start();
  		try {
  			Thread.sleep(100);
  		} catch (InterruptedException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}
  		ticket.setFlag(true);
  	}
  }
  class Ticket implements Runnable{
  	int ticket = 100;
  	private boolean flag = true;
  	Object object = new Object();
  	public void setFlag(boolean flag) {
  		this.flag = flag;
  	}
  	@Override
  	public void run() {
  		for (int i = 1; i <= 100; i++) {
  			if (flag) {
  				synchronized (this) {
  					if (ticket>0) {
  						try {
  							Thread.sleep(100);
  						} catch (InterruptedException e) {
  							// TODO Auto-generated catch block
  							e.printStackTrace();
  						}
  						System.out.println(Thread.currentThread().getName()+"true正在出售第"+(ticket--)+"票");
  					}
  				}
  			}else {
  				show();
  			}
  			
  			
  		}
  	}
  	
  	public synchronized void show() {
  		
  			if (ticket>0) {
  				try {
  					Thread.sleep(100);
  				} catch (InterruptedException e) {
  					// TODO Auto-generated catch block
  					e.printStackTrace();
  				}
  				System.out.println(Thread.currentThread().getName()+"false正在出售第"+(ticket--)+"票");
  			}
  		
  	}
  	
  }
  ```

- 如果我方法改成静态的呢？

- 锁是字节码文件

  ```java
  package com.aishang.day14;
  
  public class Demo {
  	public static void main(String[] args) {
  		Ticket ticket = new Ticket();
  		Thread t1 = new Thread(ticket);
  		Thread t2 = new Thread(ticket);
  		
  		t1.start();
  		Thread.sleep(100);
  		ticket.setFlag(false);
  		t2.start();
  		for (int i = 0; i <20; i++) {
  			Thread.sleep(100);
  			ticket.setFlag(i%2==0?true:false);
  		}
  	}
  }
  class Ticket implements Runnable{
  	static int ticket = 100;
  	private boolean flag = true;
  	Object object = new Object();
  	public void setFlag(boolean flag) {
  		this.flag = flag;
  	}
  	@Override
  	public void run() {
  		for (int i = 1; i <= 100; i++) {
  			if (flag) {
  				synchronized (Ticket.class) {
  					if (ticket>0) {
  						try {
  							Thread.sleep(100);
  						} catch (InterruptedException e) {
  							// TODO Auto-generated catch block
  							e.printStackTrace();
  						}
  						System.out.println(Thread.currentThread().getName()+"true正在出售第"+(ticket--)+"票");
  					}
  				}
  			}else {
  				show();
  			}
  			
  			
  		}
  	}
  	
  	public static synchronized void show() {
  		
  			if (ticket>0) {
  				try {
  					Thread.sleep(100);
  				} catch (InterruptedException e) {
  					// TODO Auto-generated catch block
  					e.printStackTrace();
  				}
  				System.out.println(Thread.currentThread().getName()+"false正在出售第"+(ticket--)+"票");
  			}
  		
  	}
  	
  }
  ```

- 线程的死锁

  ```java
  package com.aishang.day14;
  
  public class Demo2 {
  	public static void main(String[] args) {
  		Person person1 = new Person(true);
  		Person person2 = new Person(false);
  		Thread t1 = new Thread(person1);
  		Thread t2 = new Thread(person2);
  		
  		t1.start();
  		t2.start();
  	}
  }
  class MyClock {
  	public static Object clock1 = new Object();
  	public static Object clock2 = new Object();
  }
  class Person implements Runnable{
  	boolean  flag ;
  	public Person(boolean  flag) {
  		this.flag = flag;
  	}
  	@Override
  	public void run() {
  		if (flag) {
  			synchronized (MyClock.clock1) {
  				System.out.println("true-----MyClock.clock1");
  				synchronized (MyClock.clock2) {
  					System.out.println("true-----MyClock.clock2");
  				}
  			}
  		}else {
  			synchronized (MyClock.clock2) {
  				System.out.println("false-----MyClock.clock2");
  				synchronized (MyClock.clock1) {
  					System.out.println("false-----MyClock.clock1");
  				}
  			}
  		}
  		
  	}
  	
  }
  ```

  

作业1

```java
package com.aishang.day14.home;

public class Home1 {
	public static void main(String[] args) {
//		（一）模拟多个人通过一个山洞的模拟。这个山洞每次只能通过一个人，
//		每个人通过山洞的时间为5秒，有10个人同时准备过此山洞，显示每次通过山洞人的姓名和顺序。
		String[] name = new String[10];
		for (int i = 0; i < name.length; i++) {
			name[i] = "admin00"+i;
		}
		Dong dong = new Dong(name);
		for (int i = 0; i < name.length; i++) {
			Thread t = new Thread(dong, name[i]);
			t.start();
		}
		
		
	}
}
class Dong implements Runnable {
	String[] names;
	int index = 1;
  Object obj = new Object();
	public Dong(String[] names) {
		this.names = names;
	}
	@Override
	public void run() {
		synchronized (obj) {
			System.out.println("通过山洞的第"+(index++)+"个人是"+Thread.currentThread().getName());
		
		}
			
		
	}
	
}
```

作业2:设计4个线程，其中两个线程每次对j增加1，另外两个线程对j每次减少1。

```java
package com.aishang.day14.home;

public class Home2 {
	public static void main(String[] args) {
		J j1 = new J(true, "j++");
		J j2 = new J(false, "j--");
		Thread thread = new Thread(j1);
		Thread thread2 = new Thread(j1);
		Thread thread3 = new Thread(j2);
		Thread thread4 = new Thread(j2);
		
		thread.start();
		thread2.start();
		thread3.start();
		thread4.start();
	}
}

class J implements Runnable{
	static int num = 0;
	boolean flag;
	String name;
	static Object object = new Object();
	J(Boolean flag,String name){
		this.flag = flag;
		this.name = name;
	}
	@Override
	public void run() {
		if (flag) {
			synchronized (object) {
				num++;
				System.out.println(name+"....."+num);
			}
			
		}else {
			synchronized (object) {
				num--;
				System.out.println(name+"....."+num);
			}
		}
	}
	
}


改造

package com.aishang.day14.home;

public class Home2 {
	public static void main(String[] args) {
		J j1 = new J(true, "j++");
		J j2 = new J(false, "j--");
		Thread thread = new Thread(j1);
		Thread thread2 = new Thread(j1);
		Thread thread3 = new Thread(j2);
		Thread thread4 = new Thread(j2);
		
		thread.start();
		thread2.start();
		thread3.start();
		thread4.start();
	}
}

class J implements Runnable{
	static int num = 0;
	boolean flag;
	String name;
	static Object object = new Object();
	J(Boolean flag,String name){
		this.flag = flag;
		this.name = name;
	}
	@Override
	public void run() {
		if (flag) {
			synchronized (object) {
				System.out.println(name+"....."+(++num));
			}
			
		}else {
			synchronized (object) {
				System.out.println(name+"....."+(--num));
			}
		}
	}
	
}

```

作业3：编写程序实现,子线程循环10次,接着主线程循环20次,接着再子线程循环10次,主线程循环20次,如此反复,循环50次

```java
package com.aishang.day14;

public class ForDemo {
	public static void main(String[] args) throws InterruptedException {
		/*
		 * ThreadTest thread1 = new ThreadTest();
thread1.start();
thread1.start();
thread1.start();

java.lang.IllegalThreadStateException
原因：
   1. 同一个Thread不能重复调用start方法，跟线程的4中状态有关系....

    2. 线程的4种状态：新生状态；可运行状态；阻塞状态；死亡状态

           a. 新生状态：在调用start()方法之前

           b. 可运行状态：调用start()方法后，系统为该线程分配除cpu外的所需资源，对于只有一个cpu的机器而言，任何时刻只能有一个处于可运行的线程占用处理机，获得cpu资源，此时系统正正运行线程的run()方法....

          c. 阻塞状态：一个正在运行的线程因某种原因不能继续运行时，进入阻塞状态。这是【不可运行】的状态，处于这种状态的线程在得到一个特定的事件后会转回可运行状态

          d. 死亡状态：一个线程的run()运行完毕，stop()方法被调用或者在运行过程中出现了未捕获的异常时，线程进入死亡状态。

3. 线程的4中状态除了【可运行状态】与【阻塞状态】可以来回切换，其余的不可逆转

		 */
		
		for (int i = 0; i < 50; i++) {
			ForTwenty forTwenty = new ForTwenty();
			Thread thread = new Thread(forTwenty);
			thread.start();
			Thread.sleep(200);
			forTwenty.mainForTwenty();
	}
		
	}
}

class ForTwenty implements Runnable {
	@Override
	public void run() {
		// TODO Auto-generated method stub
		synchronized (this) {
			
			for (int i = 0; i < 15; i++) {
				
				System.out.println(Thread.currentThread().getName()+"..子线程循环"+i+"次");
				
			}
			
			
			
		}
		
		
	}
	
	public synchronized void mainForTwenty() {
		for (int i = 0; i < 21; i++) {
			
				System.out.println(Thread.currentThread().getName()+"..main"+i+"线程循环"+i+"次");
			
		}
		
		
	}
	
}
```

