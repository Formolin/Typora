## NIO(非阻塞式IO)

- io面向流
  - 单向的，阻塞
- nio面向缓冲区
  - 双向的
  - 通道只负责连接，缓冲区负责存储

### 1、缓冲区基础

> 负责存取数据，缓冲区就是数组，用于存储不同数据类型的数据,根据数据类型不同(boolean)，提供了相应类型的缓冲区
>
> - ByteBuffer 常用
> - CharBuffer
> - ShortBuffer
> - IntBuffer
> - LongBuffer
> - FloatBuffer
> - DoubleBuffer
>
> 上述缓冲区的管理方式几乎一致，通过allocate()获取缓冲区



#### 四个核心属性

- 容量capacity
  - 缓冲区能够容纳的数据元素的最大数量。这一容量在缓冲区创建时被设定，并且永远不能改变
- 界限limit
  - 表示缓冲区中可以操作数据的大小，limit后不能进行读写
- 位置position
  - 缓冲区中正在操作数据的位置，位置会自动由相应的get和put函数更新
  - Position <= limit <= capacity
- 标记mark
  - 一个备忘位置。表示记录当前position的位置。可以通过reset（）恢复到mark的位置。标记在设定前是未定义的（undefined）
  - 0 <=mark<=position<=limit<=capacity

```java
	@Test
	public void test1() {
		//分配一个指定大小的缓冲区
		ByteBuffer buffer = ByteBuffer.allocate(1024);
		System.out.println(buffer.position());
		System.out.println(buffer.limit());
		System.out.println(buffer.capacity());
	}


0
1024
1024
  
  
  	//存入数据到缓冲区
		String str = "abcde";
		buffer.put(str.getBytes());
		
		
		System.out.println(buffer.position());
		System.out.println(buffer.limit());
		System.out.println(buffer.capacity());




5
1024
1024
  
  
  //切换到读数据的模式
		buffer.flip();
		
		
		System.out.println(buffer.position());
		System.out.println(buffer.limit());
		System.out.println(buffer.capacity());



0
5
1024
  
  
  
  //利用get读取缓冲区中的数据,get之前需要flip
		byte[] dst = new byte[buffer.limit()];
		buffer.get(dst);
		System.out.println(new String(dst,0,dst.length));
		
		
		System.out.println(buffer.position());
		System.out.println(buffer.limit());
		System.out.println(buffer.capacity());


abcde
5
5
1024




	//rewind()可重复读数据
		buffer.rewind();
		System.out.println(buffer.position());
		System.out.println(buffer.limit());
		System.out.println(buffer.capacity());


0
5
1024
  
  
  		//清空缓冲区，但是里面的数据还在，他处于被遗忘状态，
		buffer.clear();
		System.out.println(buffer.position());
		System.out.println(buffer.limit());
		System.out.println(buffer.capacity());
  
  
  
  0
1024
1024
    
  	System.out.println((char)buffer.get());
a
    
    
```



```java
@Test
	public void test2() {
		String str = "abcde";
		ByteBuffer buffer = ByteBuffer.allocate(1024);
		buffer.put(str.getBytes());
		buffer.flip();
		byte[] buf = new byte[buffer.limit()];
		buffer.get(buf,0,2);
		System.out.println(new String(buf,0,2));
		
		System.out.println(buffer.position());
		
		//标记
		buffer.mark();//position 2
		
		buffer.get(buf, 2, 2);
		System.out.println(new String(buf,2,2));
		
		System.out.println(buffer.position());//position 4
		
		buffer.reset();//恢复到mark的位置
		
		System.out.println(buffer.position());//position 2
	}


ab
2
cd
4
2




//判断缓冲区中是否还有剩余数据
		if (buffer.hasRemaining()) {
			//获取缓冲区中可以操作的数量
			System.out.println(buffer.remaining());
		}
		

之前回到mark标记，position=2，剩余cde
3
```



### 2、缓冲区分类

- 字节缓冲区ByteBUffer
- 字符CharBUffer
- 双精浮点DoubleBuffer
- 单精度浮点FloatBuffer
- 整型IntBuffer
- 长整型LongBuffer
- 短整型ShortBuffer

都提供了get（取）、put（存）方法

### 3、缓冲区操作

```java
	public static void main(String[] args) {
		//创建指定长度的缓冲区
		IntBuffer buffer = IntBuffer.allocate(10);
		int[] arr = {1,2,4,5};
		//使用数组来创建一个缓冲区试图
		buffer = buffer.wrap(arr);
		//利用数组的某一个区间来创建试图
//		buffer = buffer.wrap(arr, 0, 2);
		//对缓冲区某个位置上面进行元素修改
		buffer.put(0,7);
		//遍历缓冲区数据
		System.out.println("数据如下：");
		for (int i = 0; i < buffer.limit(); i++) {
			System.out.println(buffer.get()+"\t");
		}
		System.out.println(buffer);
		buffer.flip();//对缓冲区进行反转
        System.out.println(buffer);
		buffer.clear();
        System.out.println(buffer);
	}

数据如下：
7	
2	
4	
5	
java.nio.HeapIntBuffer[pos=4 lim=4 cap=4]
java.nio.HeapIntBuffer[pos=0 lim=4 cap=4]
java.nio.HeapIntBuffer[pos=0 lim=4 cap=4]

```

```java
//复制一个新的缓冲区
		IntBuffer buffer2 = buffer.duplicate();
		System.out.println(buffer2);
```

- 直接缓冲区与非直接缓冲区
  - 非直接缓冲区：通过allocate()方法分配缓冲区，将缓冲区建立在jvm的内存中
  - 直接缓冲区：通过allocateDirect()方法分配直接缓冲区，将缓冲区建立在物理内存中

### 4、通道channel

负责源节点和目标节点的连接，本身不存储任何数据，配合缓冲区进行传输

- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

nio的创新

- 缓冲区
- 通道

4.1 FileChannel文件通道

- 文件通道，阻塞模式
- 这个对象不能直接创建
- 线程安全的

4.2 socket通道

- 非阻塞

4.3获取通道的三种方式

- 通过getChannel()方法
- Jdk1.7中的nio2 针对各个通道提供了静态方法open()
- Jdk1.7中的nio2的Files工具类的newByteChannel()

#### 案列1:利用通过完成文件的复制

```java
package com.aishang.nio;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.Buffer;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

import org.junit.Test;

public class TestChannel {

	FileInputStream fileInputStream = null;
	FileOutputStream fileOutputStream = null;
	FileChannel inChannel = null;
	FileChannel outChannel = null;

	// 1利用通过完成文件的复制
	@Test
	public void test1() {
		try {
			fileInputStream = new FileInputStream("/Users/liujiang/Downloads/C3P0连接池jar包（完整版）.zip");
			fileOutputStream = new FileOutputStream("C3P0连接池jar包（完整版）.zip");

			// 获取通道
			inChannel = fileInputStream.getChannel();
			outChannel = fileOutputStream.getChannel();

			// 分配指定大小缓冲区
			ByteBuffer buf = ByteBuffer.allocate(1024);

			// 将通道中的数据存到缓冲区
			while (inChannel.read(buf) != -1) {
				// 切换读取数据模式
				buf.flip();
				// 将缓冲区的数据写入通道
				outChannel.write(buf);
				// 清空缓冲区
				buf.clear();
			}

		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		} finally {
			try {
				// 关闭资源
				if (outChannel != null) {
					outChannel.close();
				}
				if (inChannel != null) {
					inChannel.close();
				}
				if (fileOutputStream != null) {
					fileOutputStream.close();
				}
				if (fileInputStream != null) {
					fileInputStream.close();
				}
			} catch (Exception e2) {
				// TODO: handle exception
			}

		}

	}
}

```

#### 案列2:直接使用缓冲区完成文件复制(内存映射文件)

```java
	@Test
	public void test2() throws IOException {
		FileChannel inChannel = FileChannel.open(Paths.get("/Users/liujiang/Downloads/C3P0连接池jar包（完整版）.zip"), StandardOpenOption.READ);
		//StandardOpenOption.CREATE_NEW  文件存在就报错，不存在就创建
		//StandardOpenOption.CREATE  文件存在就覆盖，不存在就创建
		FileChannel outChannel = FileChannel.open(Paths.get("c3p0"), StandardOpenOption.WRITE,StandardOpenOption.READ,StandardOpenOption.CREATE_NEW);
		
		//内存映射文件
		MappedByteBuffer inBuffer = inChannel.map(MapMode.READ_ONLY,0,inChannel.size());
		MappedByteBuffer outBuffer = outChannel.map(MapMode.READ_WRITE, 0, inChannel.size());
		
		//直接对缓冲区进行数据的读写操作
		byte[] dst = new byte[inBuffer.limit()];
		inBuffer.get(dst);
		outBuffer.put(dst);
		
		inChannel.close();
		outChannel.close();
	}
```

#### 案列：通道之间的数据传输

```java
@Test
	public void test3() throws IOException {
		//直接缓冲区
		FileChannel inChannel = FileChannel.open(Paths.get("/Users/liujiang/Downloads/C3P0连接池jar包（完整版）.zip"), StandardOpenOption.READ);
		FileChannel outChannel = FileChannel.open(Paths.get("c3p0"), StandardOpenOption.WRITE,StandardOpenOption.READ,StandardOpenOption.CREATE_NEW);
		
		inChannel.transferTo(0, inChannel.size(), outChannel);
//		outChannel.transferFrom(inChannel, 0, inChannel.size());
		
		inChannel.close();
		outChannel.close();
	}
```

- 分散读取
  - 将通道中的数据分散到多个缓冲区中
  - 按照缓冲区的顺序，从channel中读取的数据依次将buffer填满
- 聚集写入
  - 将多个缓存区中的数据聚集到通道中
  - 按照缓冲区的顺序，写入position和limit之间的数据到channel

```java
@Test
	public void test4() throws IOException {
		RandomAccessFile rFile = new RandomAccessFile("1.txt", "rw");
		// 获取通道
		FileChannel channel = rFile.getChannel();
		// 分配指定大小的缓冲区
		ByteBuffer b1 = ByteBuffer.allocate(100);
		ByteBuffer b2 = ByteBuffer.allocate(1000);

		// 分散读写
		ByteBuffer[] buffers = { b1, b2 };
		channel.read(buffers);

		for (ByteBuffer byteBuffer : buffers) {
			byteBuffer.flip();
		}

		System.out.println(new String(buffers[0].array(), 0, buffers[0].limit()));
		System.out.println("-------------------");
		System.out.println(new String(buffers[1].array(), 0, buffers[1].limit()));

		// 聚集写入
		RandomAccessFile rFile2 = new RandomAccessFile("2.txt", "rw");
		// 获取通道
		FileChannel channel2 = rFile2.getChannel();
		channel2.write(buffers);
		
		
	}
```



### 5、Selector

允许单线程处理多个channel

案例

- 通过读取文件内容，写到bytebuffer中，在从bytebuffer中获取数据，显示到控制台

  ```java
  //随机读取文件
  RandomAccessFile ra = new RandomAccessFile(filename,"rw")//读和写
  
  ```

  