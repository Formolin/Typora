## 多方法程序设计

### 回顾：

- 数组

- 冒泡排序

  ```java
  int[] arr = { 5, 4, 2, 4, 9, 1 };
  for (int i = 0; i < arr.length-1; i++) {
      for (int j = 0; j < arr.length-1-i; j++) {
          if (arr[j]>arr[j+1]) {
              int temp = arr[j];
              arr[j] = arr[j+1];
              arr[j+1] = temp;
          }
      }
  }
  for (int i : arr) {
      System.out.println(i);
  }
  ```

  

- 选择排序

  ```java
  for (int i = 0; i < arr.length-1; i++) {
  			int min = i;
  			for (int j = i+1; j < arr.length; j++) {
  				if (arr[min]>arr[j]) {
  					min = j;
  				}
  			}
  			if (min!=i) {
  				int temp = arr[i];
  				arr[i] = arr[min];
  				arr[min] = temp;
  			}
  		}
  ```

  

- 插入排序

  ```java
  for (int i = 1; i < arr.length; i++) {
  			for (int j = i; j > 0; j--) {
  				if (arr[j]<arr[j-1]) {
  					int temp = arr[j];
  					arr[j] = arr[j-1];
  					arr[j-1] = temp;
  				}else {
  					break;
  				}
  			}
  		}
  ```

  

- 普通排序

  ```java
  for (int i = 0; i < arr.length-1; i++) {
  			for (int j = i+1; j < arr.length; j++) {
  				if (arr[i]>arr[j]) {
  					int temp = arr[i];
  					arr[i] = arr[j];
  					arr[j] = temp;
  				}
  			}
  		}
  ```

  

通过上面的排序，我们发现个问题，每次换参数的代码是不是都得写一遍，都是重复的吧，费劲吧

那么，有没有改进的办法？

### 今天的内容

#### 什么是方法？

- 我们所说的方法，也叫函数，假如我玩植物大战僵尸，发射子弹，是不是重复发射，那么每次代码好比我需要写100行代码，那么这段代码需要重复利用，这样程序变得很臃肿，可读性也非常差，如果一段代码重复写过3次以上，那么设计就有问题，为了解决代码重复问题，我可以把重复的代码放到一个{}中，并给他起个名字，这样每次发射炮弹的地方，通过这个名字来调用发射炮弹的代码就ok了，那么，提取出来的代码，可以看做程序中定义的一个方法，所以：方法就是完成某个功能的代码，程序在需要发射炮弹的地方，调用改方法即可

方法的定义:完成特定功能的代码块

格式：我们对比主函数来看

```java
修饰符 返回值类型 方法名(参数类型 参数名1，参数类型 参数名2...){
   // 功能的代码
    return 返回值；
}
```

- 修饰符
  - 能够修饰方法的东西有很多种，目前记住public，是公共的意思，权限最大，权限修饰符ppt
- static
  - 静态的意思，代表该方法是静态方法，那么记住静态方法只能调用静态方法
- 返回值类型
  - void 该方法没有返回值
  - return 该方法有返回值，返回什么类型，那么方法上就用什么类型，反过来说，方法上的返回值类型是什么，那么return的就是什么类型
- 方法名，自己定义，方法名小写，驼峰式
- 参数
  - 实际参数
    - 实际参与运算的
  - 形式参数
    - 在方法定义上（）里的就是形参，主要接收实际参数
- return
  - 一个自定义方法可以没有返回值，也可以有返回值，但最多只能返回一个值，但可能有多个return语句，当执行第一个return语句时，方法结束。
  - 结束方法

#### 主函数解析

- 主函数是静态修饰，那么只能调用静态方法

#### 自定义方法

需求：将*单独抽取成方法，随时调用

```java
public static void main(String[] args) {
		    System.out.println("********************");
			System.out.println("Hello Java");
	        System.out.println("********************");
	}
```



案例

```java
public static void main(String[] args) {
		printStar();
		System.out.println("java");
		printStar();
	}
	public static void printStar() {
		System.out.println("***********");
	}
```

定义方法好处：

- 可以将功能进行封装：

- 便于对该功能的复用
- 方法只有被调用才能被执行
- 提高了代码的复用性

函数中只能调用函数，不能定义函数

继续改造:把java也抽取出来

```java
public static void printJava() {
		System.out.println("java");
}
```

继续改造,想自定义打印几个*

```java
public static void printStar(int num) {
		for (int i = 0; i < num; i++) {
			System.out.print("*");
		}
		System.out.println();
	}
```

控制台输入，打印星和文本

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入文本");
String str = scanner.next();
printJava(str);
System.out.println("请输入星");
int num = scanner.nextInt();
printStar(num);
```

定义函数时，函数的结果应该返回给调用者，由调用者处理

#### 函数的调用

- 同类
  - 同类中，因为主方法是静态的，所以方法必须用静态修饰，否则报错
- 不同类
  - 在其他类中写方法，不用在写主函数
  - 在有主函数的类里调用其方法
  - 一个测试类，一个方法类
  - 通过类名 参数名 = new 类名();调用

案列

```java
public static void main(String[] args) {
		Demo demo = new Demo();
		demo.avg(10, 3);
		
		int[] arr = {1,34,2,465,6,76,3,567};
		int max = demo.max(arr);
		System.out.println(max);
	}


public void avg(int sum,int num) {
		System.out.println((double)sum/num);
	}
	public int max(int[] arr) {
		int max = 0;
		for (int i = 0; i < arr.length; i++) {
			if (max<arr[i]) {
				max = arr[i];
			}
		}
		return max;
	}
```

#### 数组的查找算法

- 顺序查找:按顺序从头到尾依次往下查找，找到数据，则提前结束查找，找不到便一直查找下去，直到数据最后一位。

  ```java
  public static void main(String[] args) {
  		int[] arr = {1,34,2,465,232,76,3,567};
  		Scanner scanner = new Scanner(System.in);
  		System.out.println("请输入要查找的数字");
  		int key = scanner.nextInt();
  		int index = getIndex(arr, key);
  		System.out.println(index);
  		
  	}
  	public static int getIndex(int[] arr,int key) {
  		int index = 0;
  		for (int i = 0; i < arr.length; i++) {
  			if (key == arr[i]) {
  				index = i;
  				break;
  			}
  		}
  		return index;
  	}
  ```

  

- 二分查找法:前提条件：已排序的数组中查找.首先确定该查找区间的中间点位置： int mid = (low+upper) / 2；然后将待查找的值与中间点位置的值比较：若相等，则查找成功并返回此位置。若中间点位置值大于待查值，则新的查找区间是中间点位置的左边区域。若中间点位置值小于待查值，则新的查找区间是中间点位置的右边区域。下一次查找是针对新的查找区间进行的。

  ```java
  public static void main(String[] args) {
  		int[] arr = { 1, 34, 2, 465, 232, 76, 3, 567 };
  		Scanner scanner = new Scanner(System.in);
  		System.out.println("请输入要查找的数字");
  		int key = scanner.nextInt();
  		Arrays.sort(arr);
  		int index = getIndex(arr, key);
  		System.out.println(index);
  
  	}
  
  	public static int getIndex(int[] arr, int key) {
  		int start = 0;
  		int end = arr.length - 1;
  		while (start <= end) {
  			int mid = (start + end) / 2;
  			if (key > arr[mid]) {
  				start = mid + 1;
  			} else if (key < arr[mid]) {
  				end = mid - 1;
  			}else {
  				return mid;
  			}
  
  		}
  		return -1;
  	}
  ```

  











【程序1】 封装一个加法求和的方法（一种有返回值、一种没有返回值）

【程序2】 封装一个乘法方法，要求用程序1的结果做乘法计算

【程序3】用控制台输入，改造程序2

【程序4】比较两个数是否相等，返回值类型为boolean类型

【程序5】 计算学生成绩的平均值和最大值。在其他类中写方法，在主方法中调用

【程序6】二分查找法