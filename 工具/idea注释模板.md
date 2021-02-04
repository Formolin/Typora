```sh

```

```sh
/**
 * description: $description$
 * param: $params$
 * return: $returns$
 * author: liujiang
 * date: $date$ $time$
 */
```

# IntelliJ IDEA 自定义方法注解模板（转载自 昌昌93）

最近没啥事开始正式用Eclipse 转入 idea工具阵营，毕竟有70%的开发者在使用idea开发，所以它的魅力可想而知。刚上手大概有一天，就知道它为啥取名为 intelli（智能化）了，确实很智能，这还是一些小小的细节，当然idea也有它不足之处，就是我们今天要讲的“自定义**方法**注解模板”，类注解模板可以很简单的设置，这里我就不重复，而网上很多关于自定义方法注解模板的文章大多是抄袭获取转发根本自己就没有验证，本文经过自己亲自验证有效。



1. File->settings->Editor->Live Templates
   ![img](/Users/liujiang/Documents/Typora/imgs/Center-20191013114142562.png)

   

2. 点击右上角的绿色+号，然后选择template group ，然后输入group的name，随便输入就好了：myGroup。然后点ok
   ![img](/Users/liujiang/Documents/Typora/imgs/Center.png)

   

3. 选中刚才创建的myGroup，然后再次点击右侧的绿色+，这次选择的是第一个1. Live Template。取名为*，取名可以随便，只是个人觉得方便，你可以取别的。
   ![img](/Users/liujiang/Documents/Typora/imgs/Center-20191013114142956.png) 

   

4. 配置模板，下面是我的模板，大家仔细看我的模板开头是*而不是/*,这就是关键所在。

   [html]

    

   view plain

    

   copy

   

   

   

   

   1. \*  
   2.  \*    
   3.  \*   
   4.  \* @author chenmc  
   5.  \* @date $date$ $time$  
   6.  \* @param $params$  
   7.  \* @return $returns$  
   8.  */  

   ```html
   *
   
   
   
    *  
   
   
   
    * 
   
   
   
    * @author chenmc
   
   
   
    * @date $date$ $time$
   
   
   
    * @param $params$
   
   
   
    * @return $returns$
   
   
   
    */
   ```

   注意只有当变量被$$包围是 右侧的Edit variables才可以点击哦。

5. 再编辑好模板之后就只为参数添加$$添加变量，点击右侧的 Editor variables，
   ![img](/Users/liujiang/Documents/Typora/imgs/Center-20191013114142997.png)

   

6. 完成之后点击Apply然后点击OK，注意了，重点的地方，在你的方法上输入 /*然后加上模板的名称*，这就是我为什么不取字母而取名为*，因为这样很快速，**再按Tab键（注意啊不是按Enter键，而是按Tab键）**就会出现你想要的注释了，很多教程是直接输入模板名称按tab键，但是这种方法的@param和@return是获取不到的，**再重复一遍正确的方式**：/** 然后按Tab键；如果你取名为abc那就是  /*abc 然后按Tab。效果如下：
   ![img](/Users/liujiang/Documents/Typora/imgs/Center-20191013114142782.png)

   

7. 但是还有一个问题，细心的读者会发现在@param这个参数上报错了，这是因为idea强大的检查功能，有两种解决办法:

   [html]

    

   view plain

    

   copy

   

   

   

   

   1. File-**>**settings-**>**Editor-**>**Inspections-**>**javadoc issues下面第四项将红色 Error改为黄色Warning即可  

   ```html
   File->settings->Editor->Inspections->javadoc issues下面第四项将红色 Error改为黄色Warning即可
   ```

   

   [html]

    

   view plain

    

   copy

   

   

   

   

   1. 第二种是点击idea的右下角的人头图像，然后选择高亮级别，调为Syntax模式即可  

   ```html
   第二种是点击idea的右下角的人头图像，然后选择高亮级别，调为Syntax模式即可
   ```

   