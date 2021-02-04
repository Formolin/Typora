# Scala

## 安装

官网传送门 
http://www.scala-lang.org/download/

下载后进入安装包所在目录进行解压操作(我下载的是：scala-2.11.8.tgz)

### 配置环境变量

Mac修改 .bash_profile 文件，此文件是mac 当前用户的环境配置文件。

```sh
sudo vi .bash_profile

//添加如下信息
export SCALA_HOME=你Scala的路径/scala
export PATH=$PATH:$SCALA_HOME/bin

重启终端
```

### 检验结果

在终端输入scala 命令，进入scala解释器，然后输入1＋2，查看计算结果。

输入 :q 退出scala解释器。 



## 配置IDEA

1. 打开IDEA工具，如图：点击Configure

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5c081ddfsj30ky09yt9k.jpg)

2. 点击Plugins

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5c07zvpsej30cg086759.jpg) 

3. 下载scala包

   

4. [http://plugins.jetbrains.com/plugin/1347-scala/versions](http://plugins.jetbrains.com/plugin/1347-scala/versions)找到对应版本

5. ![img](06、Scala/006tNc79ly1g5c14l36vaj30zc0m6duv.jpg)

   

6. 创建Maven项目

7. 选择scala，创建，下载，选择版本

8. ![image-20190725144121553](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5c2wguiisj311c0u0tax.jpg)

9. 

![image-20190725144249164](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5c2xwvdi7j31rr0u0qbf.jpg)



```scala
package com.aishang.hello.scala

object Demo {
  def main(args: Array[String]): Unit = {
    println("hello scala")
    print()
    Demo.print()
    print1()
//   没有参数，括号可以省略
    print2

    println("sum="+sum(1,2))
    println("sum="+sum1(1,20))
  }

  def print():Unit = {
    println("自定义的scala方法")
  }

  def print1() = {
    println("没有返回值，可以省略")
  }

  def print2() {
    println("=号也可以省略")
  }

  def sum(a:Int,b:Int):Int = {
    return a+b;
  }


  def sum1(a:Int,b:Int)=  {
     a+b;
  }
}

```

![image-20190725145902277](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5c3erxai4j312u0nsqar.jpg)

## 变量&特性

```scala
package com.aishang.hello.scala

object Demo2 {
  def main(args: Array[String]): Unit = {
    var username = "tom"
    username = "jerry"
    val age = 20
//    age = 200  相当于final
    println(username)
  }
}

快捷键 "王五".按下回车 自动补全
```

### 循环

```scala
for(i <- arr){
    println()
}
```



### 数组

```scala
val arr = Array(1,2,3)
val arr1 = Array(4,5,6)
arr = arr1
#不可以，引用不可变
arr1(0) = 10 //可以，引用的内容可变
```

### 表达式和块级表达式

```scala
package com.aishang.hello.scala

object Demo3 {
  //  表达式和块级表达式
  def main(args: Array[String]): Unit = {
    var a = 1
    var res = if (a>0) 100 else -1
    println(res)


    var res2 = {
      if (a>=1)
        1000
      else
        200
    }
    println(res2)
//    返回值是语句最后一条
    var res3 = {
      1000
      1
      2
      3
      100
      "fff"
    }
    println(res3)
  }
}

```

### 方法

```scala
package com.aishang.hello.scala

object Demo3 {
  def main(args: Array[String]): Unit = {
    val i = add(1,2)
    println(i)
  }
  def add(x:Int,y:Int)={
    x+y
  }
}

```

### 函数

```scala
package com.aishang.hello.scala

object Demo3 {
  def main(args: Array[String]): Unit = {
   val f = (x:Int,y:Int)=>x+y
    println(f(1,2))


    val f1 = (x:Int,y:Int)=>{
      x+y
      x*x
    }
    println(f(2,2))

    val f2 = ()=>println("haha")
    //f2  错误
    f2()
  }
}

```

### 数组

```scala
package com.aishang.hello.scala

object Demo3 {
  def main(args: Array[String]): Unit = {
    //    创建数组的方式
    val arr1 = Array(1, 23, 4)
    arr1.foreach(println(_))
    val arr2 = Array[Int](2, 3, 4, 3, 5, 6)
    arr2.foreach(println(_))
    val arr3 = new Array[Int](10) //长度为10
    arr3(0) = 122
    val length = arr3.length
    println(length)
    arr3.foreach(print(_))

    println("---------------")
    var sum = 0;
    val arr = Array(1, 2,43,54,65,67,67,87,8,534,4,23,2,4,5,4,5)
    for (i <- arr) {
      sum+=i
    }
    println("sum="+sum)
    println(arr.sum)
    println(arr.max)
    println(arr.min)
    arr.sorted//排序
    arr.foreach(print(_))
    println("--------------")
    arr.sorted.reverse//倒序
    for (i <- arr){
      print(i)
    }
  }
}

```

### List

```scala
package com.aishang.hello.scala

object Demo3 {
  def main(args: Array[String]): Unit = {
    val list = List(1,2,3)
    list.foreach(print(_))
    println()
    import scala.collection.mutable.ListBuffer
    var list1 = ListBuffer(1,2,3)
    list1+=4
    list1.append(5)
    list1.foreach(print(_))
    var list2 = ListBuffer(4,5,6)
    var ll = list2:+999//这种追加会产生一个新的list
    ll.foreach(print(_))
    val l = list1 ++ list2
    l.foreach(print(_))

    println()
    println()

    list2.map(_*10).foreach(println(_))
    println()
    println()
    list2.map(e=>e*10).foreach(println(_))
    println()
    println()
    list2.map{
      e=>
      println("oo")
      e*1000
    }.foreach(println(_))

    println()
    println()
    list2.map{
        _*77
    }.foreach(println(_))
      
      
    //    获取集合中前几个
    println(list.take(2))
  }
}

```

### Map

```scala
package com.aishang.hello.scala

object Demo3 {
  def main(args: Array[String]): Unit = {
    val score = Map("lisi"->100,"zhangsan"->78)
    println(score("lisi"))
    //println(score("li"))//报错
    println(score.getOrElse("li",-1))//如果有这个key 返回值，没有返回-1
    println(score.isEmpty)
    println(score.keys)
    println(score.get("zhangsan").get)
    println(score.values)

    /*
    100
    -1
    false
    Set(lisi, zhangsan)
    78
    MapLike(100, 78)
     */


    import scala.collection.mutable.Map
    val s1 = Map("a"->10,"b"->100)
    s1("a")=999
    s1("aaaa")=1//键不存在，添加
    println(s1.values)
    /*
    MapLike(100, 78)
    HashMap(100, 999, 1)
    可变的
     */

    for((k,v)<-s1){
      println("k:"+k+",v:"+v)
    }
    /*
      k:b,v:100
      k:a,v:999
      k:aaaa,v:1
     */
  }
}

```

## 样例类

```scala
package com.aishang.hello.scala

object CaseDemo {
  def main(args: Array[String]): Unit = {
//    样例类
    case class Person(name:String,age:Int,sex:String)

    val p1 = Person("tom",18,"男")
    println(p1.name)
  }
}
```

