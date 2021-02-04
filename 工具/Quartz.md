# Quartz

## 概述

Quartz 是 OpenSymphony 开源组织在 Job Scheduling 领域又一个开源项目，它可以与 J2EE 与 J2SE 应用程序相结合也可以单独使用。Quartz 可以用来创建简单或为运行十个，百个，甚至是好几万个 Jobs 这样复杂的程序。Jobs 可以做成标准的 Java 组件或 EJBs。

## 为什么使用 Quartz？

Quartz 是一个任务调度框架。比如你遇到这样的问题：

- 每天 02:00 发送一份工作邮件给工作组成员并抄送给老板（假装自己很努力的工作到深夜）
- 每月 2 号提醒自己还信用卡或自动还款
- 每秒钟发 N 笔脏数据给竞争对手公司的服务器（！←_←）

这些问题总结起来就是：在某一个有规律的时间点干某件事。并且时间的触发的条件可以非常复杂，复杂到需要一个专门的框架来干这个事。Quartz 就是来干这样的事，你给它一个触发条件的定义，它负责到了时间点，触发相应的 Job 起来干活（睡 NMB 起来嗨！）。

## 什么是 cron 表达式？

cron 是 Linux 系统用来设置计划任务的，比如：每天晚上 12 点重启服务器。

### 格式

一个 cron 表达式具体表现就是一个字符串，这个字符串中包含 6~7 个字段，字段之间是由空格分割的，每个字段可以由任何允许的值以及允许的特殊字符所构成，下面表格列出了每个字段所允许的值和特殊字符

| 字段       | 允许值            | 允许的特殊字符  |
| ---------- | ----------------- | --------------- |
| 秒         | 0-59              | `, - * /`       |
| 分         | 0-59              | `, - * /`       |
| 小时       | 0-23              | `, - * /`       |
| 日期       | 1-31              | `, - * / L W C` |
| 月份       | 1-12 或者 JAN-DEC | `, - * /`       |
| 星期       | 1-7 或者 SUN-SAT  | `, - * / L C #` |
| 年（可选） | 留空, 1970-2099   | `, - * /`       |

- `*` 字符被用来指定所有的值。如：`*` 在分钟的字段域里表示“每分钟”。
- `-` 字符被用来指定一个范围。如：`10-12` 在小时域意味着“10点、11点、12点”
- `,` 字符被用来指定另外的值。如：`MON,WED,FRI` 在星期域里表示“星期一、星期三、星期五”.
- `?` 字符只在日期域和星期域中使用。它被用来指定“非明确的值”。当你需要通过在这两个域中的一个来指定一些东西的时候，它是有用的。
- `L` 字符指定在月或者星期中的某天（最后一天）。即 “Last” 的缩写。但是在星期和月中 “Ｌ” 表示不同的意思，如：在月字段中 “L” 指月份的最后一天 “1月31日，2月28日”，如果在星期字段中则简单的表示为“7”或者“SAT”。如果在星期字段中在某个 value 值得后面，则表示 “某月的最后一个星期 value” ,如 “6L” 表示某月的最后一个星期五。
- `W` 字符只能用在月份字段中，该字段指定了离指定日期最近的那个星期日。
- `#` 字符只能用在星期字段，该字段指定了第几个星期 value 在某月中

每一个元素都可以显式地规定一个值（如 6），一个区间（如 9-12 ），一个列表（如 9，11，13 ）或一个通配符（如 *）。“月份中的日期”和“星期中的日期”这两个元素是互斥的，因此应该通过设置一个问号（?）来表明你不想设置的那个字段。

| 表达式                     | 意义                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `0 0 12 * * ?`             | 每天中午 12 点触发                                           |
| `0 15 10 ? * *`            | 每天上午 10:15 触发                                          |
| `0 15 10 * * ?`            | 每天上午 10:15 触发                                          |
| `0 15 10 * * ? *`          | 每天上午 10:15 触发                                          |
| `0 15 10 * * ? 2005`       | 2005 年的每天上午 10:15 触发                                 |
| `0 * 14 * * ?`             | 在每天下午 2 点到下午 2:59 期间的每 1 分钟触发               |
| `0 0/5 14 * * ?`           | 在每天下午 2 点到下午 2:55 期间的每 5 分钟触发               |
| `0 0/5 14,18 * * ?`        | 在每天下午 2 点到 2:55 期间和下午 6 点到 6:55 期间的每 5 分钟触发 |
| `0 0-5 14 * * ?`           | 在每天下午 2 点到下午 2:05 期间的每 1 分钟触发               |
| `0 10,44 14 ? 3 WED`       | 每年三月的星期三的下午 2:10 和 2:44 触发                     |
| `0 15 10 ? * MON-FRI`      | 周一至周五的上午 10:15 触发                                  |
| `0 15 10 15 * ?`           | 每月 15 日上午 10:15 触发                                    |
| `0 15 10 L * ?`            | 每月最后一日的上午 10:15 触发                                |
| `0 15 10 ? * 6L`           | 每月的最后一个星期五上午 10:15 触发                          |
| `0 15 10 ? * 6L 2002-2005` | 2002 年至 2005 年的每月的最后一个星期五上午 10:15 触发       |
| `0 15 10 ? * 6#3`          | 每月的第三个星期五上午 10:15 触发                            |

## Spring Boot 集成 Quartz

### 创建项目

创建一个名为 `hello-quartz` 的项目

### POM

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>hello-quartz</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>hello-quartz</name>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-quartz</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```



主要增加了 `org.springframework.boot:spring-boot-starter-quartz` 依赖

### Application

```text
package com.funtl.hello.quatrz;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@EnableScheduling
@SpringBootApplication
public class HelloQuatrzApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloQuatrzApplication.class, args);
    }
}
```



使用 `@EnableScheduling` 注解来开启计划任务功能

### 创建任务

我们创建一个每 5 秒钟打印当前时间的任务来测试 Quartz

```text
package com.funtl.hello.quatrz.tasks;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.text.SimpleDateFormat;
import java.util.Date;

@Component
public class PrintCurrentTimeTask {
    @Scheduled(cron = "0/5 * * * * ? ")
    public void printCurrentTime() {
        System.out.println("Current Time is:" + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
    }
}
```



启动服务，控制台打印效果如下：

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g35s5em3zgj30za0e3gml.jpg)



```xml
<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz-jobs -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <version>2.3.0</version>
</dependency>

<!-- 如果想使用log4 导此依赖，整合slf4j-log4j12 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.12</version>
    <scope>test</scope>
</dependency>

```

demo

```java
public  class PrintWordsJob implements Job{

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        String printTime = new SimpleDateFormat("yy-MM-dd HH-mm-ss").format(new Date());
        System.out.println("PrintWordsJob start at:" + printTime + ", prints: Hello Job-" + new Random().nextInt(100));

    }
}
```



```java
package com.aishang.controller;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Random;
import java.util.concurrent.TimeUnit;

import org.quartz.Job;
import org.quartz.JobBuilder;
import org.quartz.JobDetail;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.SchedulerFactory;
import org.quartz.SimpleScheduleBuilder;
import org.quartz.Trigger;
import org.quartz.TriggerBuilder;
import org.quartz.impl.StdSchedulerFactory;

public class Demo00 {
	public static void main(String[] args) throws SchedulerException, InterruptedException {
		 // 1、创建调度器Scheduler
        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        Scheduler scheduler = schedulerFactory.getScheduler();
        // 
        JobDetail jobDetail = JobBuilder.newJob(PrintWordsJob.class)
                                        .withIdentity("job1", "group1").build();
        // 3、构建Trigger实例,每隔1s执行一次
        Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "triggerGroup1")
                .startNow()//立即生效
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                .withIntervalInSeconds(1)//每隔1s执行一次
                .repeatForever()).build();//一直执行

        //4、执行
        scheduler.scheduleJob(jobDetail, trigger);
        System.out.println("--------scheduler start ! ------------");
        scheduler.start();

        //睡眠
        TimeUnit.MINUTES.sleep(1);
        scheduler.shutdown();
        System.out.println("--------scheduler shutdown ! ------------");
	}
}


```

运行程序，可以看到程序每隔1s会打印出内容，且在一分钟后结束

JobDetail用来绑定Job，为Job实例提供许多属性：

name
group
jobClass
jobDataMap
JobDetail绑定指定的Job，每次Scheduler调度执行一个Job的时候，首先会拿到对应的Job，然后创建该Job实例，再去执行Job中的execute()的内容，任务执行结束后，关联的Job对象实例会被释放，且会被JVM GC清除。

为什么设计成JobDetail + Job，不直接使用Job

JobDetail定义的是任务数据，而真正的执行逻辑是在Job中。
这是因为任务是有可能并发执行，如果Scheduler直接使用Job，就会存在对同一个Job实例并发访问的问题。而JobDetail & Job 方式，Sheduler每次执行，都会根据JobDetail创建一个新的Job实例，这样就可以规避并发访问的问题。
obExecutionContext
JobExecutionContext中包含了Quartz运行时的环境以及Job本身的详细数据信息。
当Schedule调度执行一个Job的时候，就会将JobExecutionContext传递给该Job的execute()中，Job就可以通过JobExecutionContext对象获取信息。
主要信息有：

![img](/Users/liujiang/Documents/Typora/imgs/70-20191021141124924.png)

Trigger是Quartz的触发器，会去通知Scheduler何时去执行对应Job。

```sh
new Trigger().startAt():表示触发器首次被触发的时间;
new Trigger().endAt():表示触发器结束触发的时间;
```

SimpleTrigger可以实现在一个指定时间段内执行一次作业任务或一个时间段内多次执行作业任务。
下面的程序就实现了程序运行5s后开始执行Job，执行Job 5s后结束执行：

```java
Date startDate = new Date();
startDate.setTime(startDate.getTime() + 5000);

 Date endDate = new Date();
 endDate.setTime(startDate.getTime() + 5000);

        Trigger trigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "triggerGroup1")
                .usingJobData("trigger1", "这是jobDetail1的trigger")
                .startNow()//立即生效
                .startAt(startDate)
                .endAt(endDate)
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                .withIntervalInSeconds(1)//每隔1s执行一次
                .repeatForever()).build();//一直执行

```

## CronTrigger

CronTrigger功能非常强大，是基于日历的作业调度，而SimpleTrigger是精准指定间隔，所以相比SimpleTrigger，CroTrigger更加常用。CroTrigger是基于Cron表达式的，先了解下Cron表达式：
由7个子表达式组成字符串的，格式如下：

```sh
[秒] [分] [小时] [日] [月] [周] [年]
```

![image-20191021194559755](/Users/liujiang/Documents/Typora/imgs/image-20191021194559755.png)

![image-20191021194744350](/Users/liujiang/Documents/Typora/imgs/image-20191021194744350.png)

![image-20191021194913768](/Users/liujiang/Documents/Typora/imgs/image-20191021194913768.png)

Cron表达式的语法比较复杂，
如：* 30 10 ? * 1/5 *
表示（从后往前看）
[指定年份] 的[ 周一到周五][指定月][不指定日][上午10时][30分][指定秒]

又如：00 00 00 ？ * 10,11,12 1#5 2018
表示2018年10、11、12月的第一周的星期五这一天的0时0分0秒去执行任务。

可通过在线生成Cron表达式的工具：<http://cron.qqe2.com/> 来生成自己想要的表达式。

![img](/Users/liujiang/Documents/Typora/imgs/70-20191021141753506.png)

![image-20191021195005907](/Users/liujiang/Documents/Typora/imgs/image-20191021195005907.png)

下面的代码就实现了每周一到周五上午10:30执行定时任务

```java
/**
 * Created by wanggenshen
 * Date: on 2018/7/7 20:06.
 * Description: XXX
 */
public class MyScheduler2 {
    public static void main(String[] args) throws SchedulerException, InterruptedException {
        // 1、创建调度器Scheduler
        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        Scheduler scheduler = schedulerFactory.getScheduler();
        // 2、创建JobDetail实例，并与PrintWordsJob类绑定(Job执行内容)
        JobDetail jobDetail = JobBuilder.newJob(PrintWordsJob.class)
                .usingJobData("jobDetail1", "这个Job用来测试的")
                .withIdentity("job1", "group1").build();
        // 3、构建Trigger实例,每隔1s执行一次
        Date startDate = new Date();
        startDate.setTime(startDate.getTime() + 5000);

        Date endDate = new Date();
        endDate.setTime(startDate.getTime() + 5000);

        CronTrigger cronTrigger = TriggerBuilder.newTrigger().withIdentity("trigger1", "triggerGroup1")
                .usingJobData("trigger1", "这是jobDetail1的trigger")
                .startNow()//立即生效
                .startAt(startDate)
                .endAt(endDate)
                .withSchedule(CronScheduleBuilder.cronSchedule("* 30 10 ? * 1/5 2018"))
                .build();

        //4、执行
        scheduler.scheduleJob(jobDetail, cronTrigger);
        System.out.println("--------scheduler start ! ------------");
        scheduler.start();
        System.out.println("--------scheduler shutdown ! ------------");

    }
}

```

![image-20191021181314001](/Users/liujiang/Documents/Typora/imgs/image-20191021181314001.png)

![image-20191021182922160](/Users/liujiang/Documents/Typora/imgs/image-20191021182922160.png)

案例

```java
package com.aishang.controller;

import org.quartz.JobBuilder;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.SimpleScheduleBuilder;
import org.quartz.SimpleTrigger;
import org.quartz.Trigger;
import org.quartz.TriggerBuilder;
import org.quartz.impl.StdSchedulerFactory;

public class HelloScheduler {
	public static void main(String[] args) throws SchedulerException {
		//调度器：从工厂中获取调度实例
		Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
		//任务实例：
		JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)//加载我们的任务类，必须实现job接口
		.withIdentity("job1", "group1")//第一个参数，任务的唯一实例，第二个参数任务组的名称，随便定义，可以把不同的任务分组
		.build();
		//触发器
		Trigger trigger = TriggerBuilder.newTrigger()
		.withIdentity("trigger1", "group1")//触发器的名称,组的名称跟jobdetail没关系，任务组和触发器组
		.startNow()//马上启动触发器
		.withSchedule(SimpleScheduleBuilder.simpleSchedule().repeatSecondlyForever(5))//每5秒重复执行一次
		.build();
		//触发器和 任务关联,按触发器定义的条件执行任务
		scheduler.scheduleJob(jobDetail, trigger);
		//启动
		scheduler.start();
	}
}

```

```java
package com.aishang.controller;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

public class HelloJob implements Job{

	@Override
	public void execute(JobExecutionContext arg0) throws JobExecutionException {
		// TODO Auto-generated method stub
		SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		String format = simpleDateFormat.format(new Date());
		System.out.println("模拟备份数据库数据="+format);
	}

}
模拟备份数据库数据=2019-10-21 19:20:04
模拟备份数据库数据=2019-10-21 19:20:09
模拟备份数据库数据=2019-10-21 19:20:14
```

## CronScheduleBuilder

```java
package com.aishang.controller;

import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;

import org.quartz.CronScheduleBuilder;
import org.quartz.JobBuilder;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.SimpleScheduleBuilder;
import org.quartz.SimpleTrigger;
import org.quartz.Trigger;
import org.quartz.TriggerBuilder;
import org.quartz.impl.StdSchedulerFactory;

public class HelloScheduler {
	public static void main(String[] args) throws SchedulerException {
		//调度器：从工厂中获取调度实例
		Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
		//任务实例：
		JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)//加载我们的任务类，必须实现job接口
		.withIdentity("job1", "group1")//第一个参数，任务的唯一实例，第二个参数任务组的名称，随便定义，可以把不同的任务分组
		.usingJobData("message", "可以存储map")
		.build();
		//触发器
		Trigger trigger = TriggerBuilder.newTrigger()
		.withIdentity("trigger1", "group1")//触发器的名称,组的名称跟jobdetail没关系，任务组和触发器组
		.startNow()//马上启动触发器
		.withSchedule(CronScheduleBuilder.cronSchedule("* * * * * ?"))//每秒重复执行
		.build();
		//触发器和 任务关联,按触发器定义的条件执行任务
		scheduler.scheduleJob(jobDetail, trigger);
		//启动
		scheduler.start();
	}
}

```

![image-20191021195523760](/Users/liujiang/Documents/Typora/imgs/image-20191021195523760.png)

## Springboot+quartz

```xml

 <!-- quartz -->
    <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-quartz</artifactId>
     </dependency>
```

创建你的调度任务啊，继承 QuartzJobBean，然后重写方法（自己根据需求编写业务逻辑）

```java
抛异常 SchedulerException: Jobs added with no trigger must be durable.
意思是没有给job添加触发器，你可以在配置文件中加上durable属性即可。// durable, 指明任务就算没有绑定Trigger仍保留在Quartz的JobStore中, 

```

```java
JobDetail job = JobBuilder.newJob(MyJob.class) // MyJob是我实现的Job类
.withIdentity("myjob") // 可以给该JobDetail起一个id，便于之后的检索。也可以 .withIdentity("myjob", "group1")
.requestRecovery() // 执行中应用发生故障，需要重新执行
.storeDurably() // 即使没有Trigger关联时，也不需要删除该JobDetail
.usingJobData("key1", "value1")
.usingJobData("key2", "value2") // 以Key-Value形式关联数据
.build();
```

测试案列

```java
public class FileHandleQuartz extends QuartzJobBean{
private Logger logger = LoggerFactory.getLogger(FileHandleQuartz.class);
    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        logger.info("定时任务执行啦！");
    }
}


@Configuration
public class QuartzConfiguration {
    @Bean
    public JobDetail jobDetail() {
        return JobBuilder.newJob(FileHandleQuartz.class)
                .withIdentity("fileHandleQuartzJobDetail") //自己取名
                .storeDurably().build();
    }
    @Bean
    public Trigger trigger() {
        SimpleScheduleBuilder schedBuilder=SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(5).repeatForever(); //时间自己定，根据方法进行修改时间
        return TriggerBuilder.newTrigger().forJob( jobDetail()).withIdentity("fileHandleQuartzJobTriger") //自己取名
                .withSchedule(schedBuilder).build();
    }
}


CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule("*/5 * * * * ?"); //替换上面的第一行就好


第二种通过注解的方式
@EnableScheduling
@Configuration
public class QuartzAnnotationTest {
    private Logger logger = LoggerFactory.getLogger(QuartzAnnotationTest.class);
    @Scheduled(cron = "*/5 * * * * ?")
    public void testAnnotation(){
       logger.info("注解的方式测试啦！");
    }
}


@Component
@EnableScheduling
public class QuertzTest {
    
    @Scheduled(cron = "0/2 * * * * ?")
    public void testimer() {
        System.err.println("#########");
        System.err.println("#########");
        System.err.println("#########");
        System.err.println("#########");
        System.err.println("#########");
    }
    

}
```

 对于两种方式都可以实现定时任务，区别在于，在使用配置类（文件）的方式的时候，在项目启动的时候就会执行，使用注解的方式会在启动完成之后时间算起执行，少执行一次调度

