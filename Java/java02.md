# 循环结构

今天我们学循环结构，现在有一个需求，在控制台输出10次helloworld，你复制可以，要是输出100000次呢？你怎么玩？那我今天就不用讲课了，我就在这差数就可以了。

这样写是可以实现的，但是太麻烦了，那么这就需要我们今天要学的循环语句

> 循环语句可以在满足循环条件的情况下，反复执行某一段代码，这段被重复执行的代码被称为循环体语句，当反复执行这个循环体时，需要在合适的时候结束循环，否则循环将一直执行下去，形成死循环

## 1、while循环

语法结构：

while（判断条件语句）{

​	循环体语句

}



循环输出10次helloworld

```java
int x = 1;
while(x<=10) {
    System.out.println("这是第"+x+"次输出hello world");
    x = x+1;
}
```

必须有一个条件，改变变量的值，否则出现死循环

### 案例1

重写三个学生的案列

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入姓名：");
String name = scanner.next();
System.out.println("请输入年龄：");
int age = scanner.nextInt();
System.out.println("成绩：");
double score = scanner.nextDouble();
System.out.println("姓名是:"+name+",年龄是:"+age+",成绩是"+score);


System.out.println("请输入姓名：");
String name1 = scanner.next();
System.out.println("请输入年龄：");
int age1 = scanner.nextInt();
System.out.println("成绩：");
double score1 = scanner.nextDouble();
System.out.println("姓名是:"+name1+",年龄是:"+age1+",成绩是"+score1);


System.out.println("请输入姓名：");
String name2 = scanner.next();
System.out.println("请输入年龄：");
int age2 = scanner.nextInt();
System.out.println("成绩：");
double score2 = scanner.nextDouble();
System.out.println("姓名是:"+name2+",年龄是:"+age2+",成绩是"+score2);
```

循环改造

```java
Scanner scanner = new Scanner(System.in);
		int x = 1;
		while(x<=3) {
			System.out.println("请输入姓名：");
			String name = scanner.next();
			System.out.println("请输入年龄：");
			int age = scanner.nextInt();
			System.out.println("成绩：");
			double score = scanner.nextDouble();
			System.out.println("姓名是:"+name+",年龄是:"+age+",成绩是"+score);
			x = x+1;
		}
```

那么看我改一下代码，你们能不能理解，把x=x+1改成x++

## 2、前置后置++、—，a+=b

- a++	
  
  - 先赋值，后运算，自增1
- a--
  
  - 先赋值，后运算，自减1
- —a
  
  - 先运算，后自减1
- ++a
  
  - 先运算，后自增1
- a+=b
  
  - 相当于a= a+b;
  
  ![02_-_登陆功能实现_-_1_avi](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3mqaenc4xj30ss05wjsf.jpg)

如果都明白了，那么我出几个题

### 案例：

```java
int a = 1;
int b = a++;
System.out.println("a="+a+",b="+b);
//a=2 b=1

int a = 1;
int b = --a;
System.out.println("a="+a+",b="+b);
//a=0 b=0

int a = 1;
System.out.println(a++);//1
System.out.println(a+=1);//3
```

## 3、do..while循环

```java
do{
    循环体语句
}while(判断条件语句)
```

案例：

```java
int x= 1;
do {
    System.out.println("第"+x+"次输出helloworld");
    x++;
} while (x<=3);
```

- 看结果，是不是跟while循环的一样，那老师你玩我呢，都一样学这个干什么？，我把程序改一下，当x<1的时候，什么结果？只输出一次，那么这就是区别，无论条件是否满足，都输出一次，ok吧

- 如果明白了，我现在有一个需求，就是昨天我们做的用ifelse输出1-7之间的数字，是不是输出完就结束了，我想让用户可以一直选择输入，怎么做？用循环，那么while能不能满足这个需求，但是用while循环，用户没法控制什么时候结束吧

案例：

```java
	Scanner scanner = new Scanner(System.in);
		do {
			System.out.println("请输入1-7之间的数字");
			int num = scanner.nextInt();
			if (num == 1) {
				System.out.println("一帆风顺");
			}else if (num == 2) {
				System.out.println("二龙戏珠");
			}else if(num == 3) {
				System.out.println("三阳开泰");
			}else {
				System.out.println("输入有误");
			}
			System.out.println("是否继续，y:继续 n：结束");
			String go = scanner.next();
		} while ("y".equals(go));
```

这里go为什么报错，涉及作用域的问题，因为go变量在{}里，外面是获取不到的，那怎么办？

## 4、for循环

```java
for(初始化语句；判断条件语句；控制条件语句){
    循环体语句；
}
```

```java
for (int i = 0; i < 3; i++) {
			System.out.println("第"+i+"次输出helloworld");
		}

for (int i = 10; i > 1; i--) {
			System.out.println("第"+i+"次输出helloworld");
		}
```

需求1-10的和

```java
int num = 0;
		for (int i = 1; i <= 10; i++) {
			num+=i;
		}
		System.out.println(num);
```

验证码

```java
String code = "";
		for (int i = 0; i < 4; i++) {
			code+=(int)(Math.random()*10);
		}
		System.out.println(code);
```

## 5、break，continue，三元运算符

break跳出循环

continue跳出本次循环

```java
for (int i = 0; i < 100; i++) {
			if (i==50) {
				break;
			}
			System.out.println("第"+i+"次循环");
		}
```

```java
for (int i = 0; i < 100; i++) {
			if (i==50) {
				continue;
			}
			System.out.println("第"+i+"次循环");
		}
```

三元运算符

```java
条件表达式？变量值1:变量值2
如果满足条件，就等于变量值1，否则等于变量值2
		int a = 5;
		int b = 3;
		int z = a>b?a:b;
		System.out.println(z);

```

## 循环语句的区别

优先考虑for，其次while，最后dowhile

## 程序题

1. 打印水仙花数

   ```java
   //水仙花数，必须是三位数，条件：该数本身等于每位数的立方数的和
   		//153 = 1*1*1+5*5*5+3*3*3  = 1+125 +27 = 153
   		for(int i = 100;i<1000;i++) {
   			int ge = i%10;
   			int shi = i/10%10;
   			int bai = i/10/10%10;
   			if (i == (ge*ge*ge+shi*shi*shi+bai*bai*bai)) {
   				System.out.println(i);
   			}
   		}
   ```

2. 回文数

   ```java
   //回文数 12321  五位数
   		for (int i = 10000; i < 100000; i++) {
   			int ge = i%10;
   			int shi = i/10%10;
   			int bai = i/10/10%10;
   			int qian = i/10/10/10%10;
   			int wan = i/10/10/10/10%10;
   			if ((ge==wan)&&(shi == qian)) {
   				System.out.println(i);
   			}
   		}
   ```

   

3. 输出图形

   ```java
   *****
   *****
   *****
   
   
   		for (int i = 0; i < 4; i++) {
   			for (int j = 0; j < 5; j++) {
   				System.out.print("*");
   			}
   				System.out.println();
           }
   ```

4. 直角三角形

   ```java
   for (int i = 0; i < 5; i++) {
   			for (int j = 0; j <= i; j++) {
   				System.out.print("*");
   			}
   			System.out.println();
   		}
   
   一行一个星
   二行二个星
   那么星就等行  i=j
   ```

5. 正三角形

```java
for (int i = 1; i <=5; i++) {
			for (int j = 1; j <= 5-i; j++) {
				System.out.print(" ");
			}
			for (int j = 1; j <=i*2-1; j++) {
				System.out.print("*");
			}
			System.out.println();
		}
```

