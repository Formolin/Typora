数组

【程序1】熟悉数组的声明、数组元素的赋值（从键盘输入值）、运算、输出

【程序2】将程序1的数组元素的输出改写成循环（用两种方法）

```java
Scanner scanner = new Scanner(System.in);
int[] arr = new int[3];
arr[0] = 12;
System.out.println("请输入一个数字");
arr[1] = scanner.nextInt();
arr[2] = arr[0]+arr[1];
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
for (int i : arr) {
    System.out.println(i);
}
```



【程序3】声明一个字符串数组，并初始化一些顾客的名字，显示出所有顾客的名字，通过一个随机整数的产生，代表一个数组的下标，获取对应的一个顾客的名字，作为幸运者。

```java
String[] names = { "张三", "赵四", "王五", "tom" };
for (int i = 0; i < names.length; i++) {
    System.out.println("第" + (i + 1) + "位是" + names[i]);
}
int index = (int)(Math.random()*names.length);
System.out.println("幸运者是"+names[index]);
```

【程序4】声明一个整型数组，可以存储多个学生的成绩，通过循环获取控制台窗口中输入的每个学生的成绩，每次循环获取一个成绩，最后显示出来每个成绩，再计算出所有成绩的平均分。

```java
package com.aishang.day02;

import java.util.Scanner;

public class Demo3 {
	public static void main(String[] args) {
		int[] arr = new int[3];
		Scanner scanner = new Scanner(System.in);
		
		double avg = 0;
		int sum = 0;
		for (int i = 0; i < arr.length; i++) {
			System.out.println("请输入成绩");
			arr[i] = scanner.nextInt();
		}
		
		for (int i : arr) {
			System.out.println(i);
			sum+=i;
		}
		avg = sum/(arr.length+0.0);
		System.out.println(avg);
	}
}

```



【程序5】给程序4增加统计最高分的功能。

```java
int max = 0;
for (int i = 0; i < scores.length; i++) {
    if (max<scores[i]) {
        max = scores[i];
    }
}
System.out.println(max);
//快速排序 获取最大值
Arrays.sort(scores);
System.out.println(scores[scores.length-1]);
```



【程序6】编写程序DyadicArray,二维数组，定义了三行三列的二维数组

```java
int[][] arr = new int[3][3];
		for (int i = 0; i < arr.length; i++) {
			for (int j = 0; j < arr[i].length; j++) {
				arr[i][j] = j;
			}
		}
		
		
		for (int i = 0; i < arr.length; i++) {
			for (int j = 0; j < arr[i].length; j++) {
				System.out.print(arr[i][j]+",");
			}
			System.out.println();
		}
```



【程序7】不规则二维数组

```java
int[][] arr = new int[3][];
arr[0] = new int[2];
arr[1] = new int[5];
arr[2] = new int[8];

for (int i = 0; i < arr.length; i++) {
    for (int j = 0; j < arr[i].length; j++) {
        arr[i][j] = j;
    }
}
for (int i = 0; i < arr.length; i++) {
    for (int j = 0; j < arr[i].length; j++) {
        System.out.print(arr[i][j]);
    }
    System.out.println();
}
```



【程序8】利用Arrays带有的排序方法快速排序

```java
int arr[] = {2,4,56,67,234,36,6,8,769,6,4,234};
Arrays.sort(arr);
for (int i : arr) {
    System.out.println(i);
}
```



【程序9】冒泡排序算法(升序): 

- 对相邻的元素进行两两比较，顺序相反则进行交换，这样，每一趟会将最小或
  最大的元素“浮”到顶端，最终达到完全有序

```java
int arr[] = {2,4,56,67,234,36,6,8,769,6,4,234};
		for (int i = 0; i < arr.length-1; i++) {
			if (arr[i]>arr[i+1]) {
				int temp = arr[i];
				arr[i] = arr[i+1];
				arr[i+1] = temp;
			}
		}
		for (int i : arr) {
			System.out.print(i+",");
		}
		System.out.println();
		for (int i = 0; i < arr.length-1-1; i++) {
			if (arr[i]>arr[i+1]) {
				int temp = arr[i];
				arr[i] = arr[i+1];
				arr[i+1] = temp;
			}
		}
		for (int i : arr) {
			System.out.print(i+",");
		}
		System.out.println();
		for (int i = 0; i < arr.length-1-2; i++) {
			if (arr[i]>arr[i+1]) {
				int temp = arr[i];
				arr[i] = arr[i+1];
				arr[i+1] = temp;
			}
		}
		for (int i : arr) {
			System.out.print(i+",");
		}
```



```java
//		for (int i = 0; i < arr.length-1; i++) {
//			for (int j = 0; j < arr.length-1-i; j++) {
//				if (arr[j]>arr[j+1]) {
//					int temp = arr[j];
//					arr[j] = arr[j+1];
//					arr[j+1] = temp;
//				}
//			}
//		}
```



【程序10】选择排序算法（升序）:

- 是最简单直观的一种算法
  基本思想为每一趟从待排序的数据元素中选择最小（或最大）的一个元素作为首元素，直到所有元素排完为止。

```java
int arr[] = {2,4,56,67,234,36,6,8,769,6,4,234};
	for (int i = 0; i < arr.length-1; i++) {
			int min = i;
			for (int j = i+1; j < arr.length; j++) {
				if (arr[min]>arr[j]) {
					min = j;
				}
			}
			if (min!=i) {
				int temp = arr[min];
				arr[min] = arr[i];
				arr[i] = temp;
			}
		}
```

【程序10】插入排序算法（升序）:

```java
int[] a = { 5, 4, 2, 4, 9, 1 };
		for (int i = 1; i < a.length; i++) {
			for (int j = i; j > 0; j--) {
				if (a[j] < a[j - 1]) {
					int temp = a[j - 1];
					a[j - 1] = a[j];
					a[j] = temp;
				} else
					break;
			}

		}
		// 遍历数组
		for (int i : a) {
			System.out.print(i);
		}
```

