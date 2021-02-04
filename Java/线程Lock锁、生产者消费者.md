- 同步方法和非同步方法的调用
  - 同步方法只影响其他线程调用同一个同步方法时的锁问题，不影响其他线程调用非同步方法，或者用其他锁资源的同步方法

![高并发编程基础02_avi](/Users/liujiang/Documents/Typora/imgs/高并发编程基础02_avi.png)

- 锁可重入
  - 同一个线程，多次调用同步代码，锁定同一个锁对象，可重入
- 锁与异常
  - 当同步方法中发生异常的时候，自动释放所资源，不会影响其他线程的执行

![高并发编程基础03_avi](/Users/liujiang/Documents/Typora/imgs/高并发编程基础03_avi.png)

- 应该结束，但是还是死循环，为什么？

  - 线程不可见

    ```java
    volatile boolean b = true;线程可见
    ```

  - 通知os操作系统底层，在cpu计算过程中，都要检查内存中数据的有效性，保证最新的内存数据被使用

- Atomic

  - 原子操作类型，其中的每个方法都是原子操作，可以保证线程安全

    ![高并发编程基础05_avi](/Users/liujiang/Documents/Typora/imgs/高并发编程基础05_avi.png)

# ![高并发编程基础06_avi](/Users/liujiang/Documents/Typora/imgs/高并发编程基础06_avi.png)Lock锁

```java
package com.aishang.day14;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Demo1 {
	public static void main(String[] args) {
		Ticket ticket = new Ticket();
		Thread t1 = new Thread(ticket);
		Thread t2 = new Thread(ticket);
		t1.start();
		t2.start();
	}
}

class Ticket implements Runnable {
	int num = 100;
	Lock lock = new ReentrantLock();
	@Override
	public void run() {
		// TODO Auto-generated method stub
		for (int i = 0; i < 100; i++) {
			try {
				lock.lock();
				if (num>0) {
					try {
						Thread.sleep(10);
					} catch (InterruptedException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
					System.out.println(Thread.currentThread().getName()+"="+(num--));
				}
			}finally {
				lock.unlock();
			}
			
			
			
		}
	}
	
}

```

# 线程间的通信

- 不同种类的线程间针对同一个资源的操作

### 生产者和消费者

- 资源类：student
- 设置学生数据：SetThread(生产者)
- 获取学生数据：GetThread(消费者)
- 测试类：StudentDemo
- 生产者
  - 先看是否有数据，有就等待，没有就生产，生产完之后通知消费者来消费
- 消费者
  - 先看是否有数据，有就消费，没有就等待
- 为了处理这样的问题，java就提供了一种等待唤醒机制