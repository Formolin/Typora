# 一、java简介

1. 概述

   - 首先学习java之前，最起码需要知道这么语言是谁开发的？
   - 詹姆斯，不是打篮球的
   - 最后加入谷歌了
   - 那么谷歌的创始人是谁？拉里佩奇，社会人
     - 谷歌是怎么创立的？李彦宏发表的论文，说以后的网站应该什么什么样，佩奇知道了，那就一起干吧...
   - 语言排行榜
     - Tiobe，最新的是python上升到第三位
   - 其他语言对比
     - c语言：面向过程，应用于底层开发
     - C++:是C语言的继承,面向对象，用于游戏开发
     - c#：完全开源，跨平台，他的对抗目标是java
     - python：面向对象，胶水语言，人工智能，算法库
     - .net：微软，封闭，不跨平台，收费
     - php：快速搭建中小型网站，是世界上最好的语言？
     - js：前端的脚本语言
     - sql：数据库
     - Objective-C:主要原因它是唯一一种可以为iPhone和iPad编程的语言
     - swift:苹果于2014年[WWDC](https://baike.baidu.com/item/WWDC)（苹果开发者大会）发布的新开发语言，可与[Objective-C](https://baike.baidu.com/item/Objective-C)*共同运行于[Mac OS](https://baike.baidu.com/item/Mac%20OS)和[iOS](https://baike.baidu.com/item/iOS/45705)平台，用于搭建基于苹果平台的应用程序,免费开源
     - ruby：在20世纪90年代由日本人松本行弘开发，是一种简单快捷的面向对象脚本语言
   - 那为什么我们选择java呢？
     - 首先从热度上说，排行第一，意味着就业广，可选择多
     - 从他的特点上说，下一点

2. 特点

   - 面向对象
   - 跨平台
     - 面向对象我们后面说，只有学了才能体会
     - 那么什么是跨平台？
     - 从java排行榜看，这么多年一直第一，已经形成了自己的生态圈
       - 因为开源，免费，第三方支持的扩展包多
   - 软件结构分类：
     - B/S结构
       - 浏览器服务端，不用升级下载
     - C/S结构
       - 客户端，速度快，但是需要升级
   - 发展路线：
     - 最早做的是软件服务，像oa，金蝶、用友
     - 现在是平台-淘宝京东
     - 大数据，海量数据，分布式存储，分布式运算
       - 云计算
       - 物联网
       - 区块链
     - 机器学习，分析大数据
     - 移动端，安卓
   - 学完java，在学别的语言就非常快了，面向对象思想基本相同，只是语言实现不一样
   - 码农-组长-项目经理或产品经理-架构师

3. 体系

   - j2se
   - j2ee
   - j2me

4. 课程体系

   - 我们跟别的培训机构最大的区别是实训，培训不管有没有有全都讲，一讲讲一天，等实际开发中发现并没什么卵用。浪费了时间
   - 实训呢，因为编程考验程序员的逻辑能力和解决问题的能力，看视频谁家的都差不多，再讲也讲不出花来，原理就这些，要是能讲出传销和成功学的感觉我也是挺佩服的。不是你看会了就会了，你看会了，不代表你能编出来，编出来的同时，你会发现这其中碰见的各种bug，问题，在以后工作中这就是经验，实训毕业的学员能与公司的实际项目快速接轨。
   - 重点：是敲代码！！！
   - 我们基础讲14天，在数组这块设计到一些算法，一般刚接触编程的同学，到这都觉得难，很正常，因为你的代码量不够，不能够支持你很好的理解，等你做完第一个项目后，在回头看这块的题，你会发现也不过如此

5. jdk

   - 编java程序前，需要编程的环境吧，要不怎么写
   - 这就是需要下载jdk，那什么是jdk？
   - 什么是jre？
     - 简单而言，使用JDK的开发工具完成的java程序，交给JRE去运行。

6. 第一个程序

   - 那我们写一个java第一个程序，感受下他是怎么运行的

   - 我们采用最原始的方式运行

     ```java
     class HelloJava {
         public static void main(String args[]){
             System.out.println("hello java");
         }
     }
     ```

   - 通过执行doc命令，javac先编译生成.class的字节码文件，在用java命令，运行这个java文件

   - 通过这个例子，我们发现有两个问题

     - java是一种强类型编译语言，需要先编译才能运行，对比html
     - 我们在原始的记事本开发，如果写错了，我们需要执行命令编译才发现，麻烦影响效率

7. 工具开发

   - eclipse
   - 其他工具介绍

# 二、开发规范

1. eclipse建项目

   - 右键新建项目java project
   - 起项目名
   - 两个文件
     - 关联jdk，如果没有，右键buildpath-addlib，选择你下载的jdk文件夹
     - src目录是你放源代码的地方

2. 包

   - 右键src，我们第一件事是建包
   - 包就是文件夹，目的隔离路径，同一文件夹内不能有同名文件
   - 建包的规范，三级com.aishang.day01
     - com公司的域名，性质cn的，com，org的
     - 第二层是公司名称
     - 第三层是这个包的功能
       - 我们现在涉及不到，为了养成好习惯必须规范
       - 程序员的潜规则，项目不是你自己的，代码不规范，别人就可能看不懂，浪费时间，你改动一点，就崩了-祖传代码

3. 建java文件，我们叫类class

   - 类首字母大写，如果多个单词拼接，那么每个单词首字母大写

4. 程序入口

   - main方法
   - 写一个刚才的程序，快捷键，快速
     - 如果写错了，直接提示报错
   - 写完后，看文件夹，在src下有个java文件
   - 当我保存的时候，会自动编译字节码文件，在bin目录

5. 类名、文件名一致

6. 注释

   - 单行注释

   - 多行注释

   - 文档注释

     ```java
     package com.aishang.day01;//这里文件所在的包
     /**
      * 这是文档注释，一般写在类的上面
      * @author liujiang
      *	
      * @company 爱尚实训
      */
     public class HelloJava {
     	/*
     	 * 这是多行注释
     	 */
     	public static void main(String[] args) {
     		//这个是控制台输出语句
     		System.out.println("hello java");
     		
     	}
     }
     
     ```

     

# 三、变量

1. 什么是变量？
   - 水果的价格，不是一直不变的吧，他不像工资，长年不动
   - 参数是可变的，我们通过声明一个变量存储
   - 必须先声明后使用
     - 我直接写56，就报错，就需要我们声明一个变量接收（存储它）
     - 存储变量的类型？
2. 基本数据类型(8种)
   - byte
   - short
   - int
   - long
   - float
   - double
   - char
   - boolean
3. 引用类型
   - String

```java
/*
		 * 程序是从上到下执行的，输出结果
		 */
		byte b = 1;
		short s = 2;
		int i = 34;
		long l = 123123;
		float f = 2.1f;//为什么报错？因为java小数默认是double类型
		double d = 2.3;
		char c = 'a';
		boolean flag = true;
		boolean flag1 = false;
		System.out.println(i);
		System.out.println(d);
		//字符串
		String str = "hello";
```

String与其他的区别，他是引用类型，他是一个类，首字母大写，这就为什么规范，一看首字母大写就是类，类一般有一些方法，后面我们会学。

那么为什么控制台能输出结果？这里看下内存图：

![空白](https://ws2.sinaimg.cn/large/006tKfTcly1g0met5xmwnj311s0h10u6.jpg)



4. 强制转换

   ```java
   double d = 2.6;
   int num = (int)d;
   System.out.println(num);
   ```

   结果是2，因为精度丢失

   低精度转高精度，自动转换

   高精度转低精度，需要强制转换，但是结果不是你想要的，因为精度丢失

# 四、运算符

1. 算数运算符

   / + - * %

2. 关系运算符

   < > >= <= == !=

3. 逻辑运算符

   && ||

   ```java
   System.out.println(10 + 2 + "a" + 3 + 4);
   int a = 4;
   int b = 2;
   int c = a+b;
   int d = a/b;
   int e = a%b;
   System.out.println(c);
   System.out.println(d);
   System.out.println(e);
   
   
   int a = 5;
   int b = 2;
   int c = a/b;
   System.out.println(c);//2
   
   double c = a/b;//2.0 默认整型除以整型还是整型
   
   double c = (double)a/b;
   
   System.out.println(true&&true);//true
   System.out.println(true&&false);//false
   System.out.println(false&&true);//false
   System.out.println(false&&false);//false
   
   System.out.println(true||true);//true
   System.out.println(true||false);//true
   System.out.println(false||true);//true
   System.out.println(false||false);//false
   ```

## 案例一

创建Student类，输出姓名、年龄成绩

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入姓名");
String name = scanner.next();
System.out.println("请输入年龄");
int age = scanner.nextInt();
System.out.println("请输入成绩");
double score = scanner.nextDouble();
System.out.println("姓名是"+name+",年龄是"+age+",成绩是"+score);
```



# 五、Scanner

- System.out.println(c);这是控制台输出语句

- 现在我的变量是不是也是写死的，也没动态啊，是不是应该让用户自己去赋值

- 那么我们就需要学习Scanner

  ```java
  Scanner s = new Scanner(System.in);
  ```

## 案列二

修改student类 ，控制台输出小红，小刚，小李的成绩，输出结果

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入姓名");
String name = scanner.next();
System.out.println("请输入年龄");
int age = scanner.nextInt();
System.out.println("请输入成绩");
double score = scanner.nextDouble();

System.out.println("请输入姓名");
String name1 = scanner.next();
System.out.println("请输入年龄");
int age1 = scanner.nextInt();
System.out.println("请输入成绩");
double score1 = scanner.nextDouble();

System.out.println("请输入姓名");
String name2 = scanner.next();
System.out.println("请输入年龄");
int age2 = scanner.nextInt();
System.out.println("请输入成绩");
double score2 = scanner.nextDouble();

System.out.println("姓名是"+name+",年龄是"+age+",成绩是"+score);
System.out.println("姓名是"+name1+",年龄是"+age1+",成绩是"+score1);
System.out.println("姓名是"+name2+",年龄是"+age2+",成绩是"+score2);
```



# 六、程序结构

1. 顺序结构

   - 程序是从上到下，从左向右执行

2. 分支结构

   - if

     ```java
     if(条件){
         符合条件执行的代码
     }
     //多个if，只要满足就执行
     if(条件){
         符合条件执行的代码
     }else{
         不符合条件的代码
     }
     //2选一
     if(){
         
     }else if(){
         
     }else{
         
     }
     //多选1
     ```

   - switch

     ```java
     int num  = scanner.nextInt();
     switch(num){
         case 1:
             break;
         case 2:
             break;
         default:
         	break;    
     }
     ```

     

3. 循环结构

   - 下节课讲

## 案例三

创建login类，实现用户登录功能，只有用户名等于admin，密码是123才能登陆

```java
Scanner scanner = new Scanner(System.in);
System.out.println("欢迎登陆");
System.out.println("请输入账户");
String name = scanner.next();
System.out.println("请输入密码");
String pass = scanner.next();
if ("admin".equals(name)&&"123".equals(pass)) {
    System.out.println("登陆成功");
}else {
    System.out.println("登陆失败");
}
```

## 案例四

为用户添加年龄判断功能

```java
Scanner scanner = new Scanner(System.in);
System.out.println("欢迎登陆");
System.out.println("请输入账户");
String name = scanner.next();
System.out.println("请输入密码");
String pass = scanner.next();
System.out.println("请输入年龄");
int age = scanner.nextInt();
if ("admin".equals(name)&&"123".equals(pass)) {
    System.out.println("登陆成功");
    if (age>18) {
        System.out.println("你是会员");
    }else {
        System.out.println("不符合要求");
    }
}else {
    System.out.println("登陆失败");
}
```

## 案列五

添加验证码功能

```java
Scanner scanner = new Scanner(System.in);
System.out.println("欢迎登陆");
System.out.println("请输入账户");
String name = scanner.next();
System.out.println("请输入密码");
String pass = scanner.next();
System.out.println("请输入年龄");
int age = scanner.nextInt();
String code = "";
code = code + (int) (Math.random() * 10);
code = code + (int) (Math.random() * 10);
code = code + (int) (Math.random() * 10);
code = code + (int) (Math.random() * 10);
System.out.println("验证码是：" + code + "请输入验证码:");
String checkCode = scanner.next();
if ("admin".equals(name) && "123".equals(pass) && code.equals(checkCode)) {
    System.out.println("登陆成功");
    if (age > 18) {
        System.out.println("你是会员");
    } else {
        System.out.println("不符合要求");
    }
} else {
    System.out.println("登陆失败");
}
```

## 案列六

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入1-7的数字");
int num = scanner.nextInt();
if (num==1) {
    System.out.println("一帆风顺");
}else if (num==2) {
    System.out.println("二龙戏珠");
}else if (num==3) {
    System.out.println("三心二意");
}else {
    System.out.println("输入有误");
}
```

## 案列7

```java
Scanner scanner = new Scanner(System.in);
		System.out.println("请输入1-7的数字");
		int num = scanner.nextInt();
		switch (num) {
		case 1:
			System.out.println("一帆风顺");
			break;
		case 2:
			System.out.println("二龙戏珠");
			break;
		case 3:
			System.out.println("三心二意");
			break;
		default:
			System.out.println("输入有误");
			break;
		}
```

