## 一、NTFS权限概述

1、通过设置NTFS权限，实现不同的用户访问不同对象（文件/文件夹）的权限。
2、分配了正确的访问权限后，用户才能访问资源。
3、设置权限防止资源被篡改和删除。
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113542211.png)

## 二、文件系统概述

文件系统（存储文件的方式）即在外部存储设备上组织文件的方法。
常见的文件系统：
格式化 == 制作文件系统

```powershell
*FAT   windows
*NTFS  windows
*EXT   liunx 常见
```

格式化的过程就是把硬盘的所有数据抹除，然后重新在该硬盘上打格子（存储单元）格子有大有小，默认4096字节/4KB
![在这里插入图片描述](6、NTFS安全权限/20200329102909215.png)

## 三、NTFS文件系统的特点

1、提高磁盘读写性能
2、可靠性，加密文件系统，访问控制列表-ACL（设置权限）
3、磁盘利用率，压缩，磁盘配额（为不同的用户设置使用空间）
4、支持单个文件大于4个G

## 四、修改NTFS权限

文件权限
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113547466.png)
文件夹权限
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113549050.png)
**4.1、取消权限继承**

作用：取消后可以任意修改权限列表
方法：文件夹右键属性 – 安全 – 高级 – 去掉第一个对号 – 选择复制即可
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113546278.png)
**4.2 文件及文件夹权限**

在D盘创建目录 名为：最高机密的文件夹
文件夹右键属性 – 安全
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113542210.png)
发现普通用户组的权限有些默认是已经勾选的且无法修改，这是因为该文件夹的权限继承了它的父级权限也是就是D盘的权限
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113548131.png)
那么我们想要修改D盘下的最高机密文件夹的权限就需要先取消与D盘的继承关系如下图所示把勾选去掉：
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113550751.png)
取消继承后在查看该文件夹的权限表，发现此时就可以修改了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329120914283.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)
案例：
在D盘目录下创建文件夹并命名为最高机密，进入最高机密文件夹在创建一个文件夹命名为密码，且在创建一个txt结尾的文件 并写入内容
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113546888.png)
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113548131-4892948.png)
需求：
管理员创建两个账户a和b，a用户对最高机密文件夹只有打开和读取权限，b用户对最高机密文件夹有读取和写入权限
（1）取消最高机密文件夹和D盘的继承关系
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113549443.png)
（2）创建a，b两个账户
![在这里插入图片描述](6、NTFS安全权限/20200329121654525.png)
（3）将a，b账户添加到表中

删除其他用户只留一个管理员，同时添加a，b用户
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329125010826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)
（4）查看a，b现有的权限并修改

**a账户对最高机密文件夹的权限**
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113549777.png)
a用户默认能打开和读取该文件，权限不用修改

**b账户对最高机密文件夹的权限**
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113550266.png)
修改权限：只留写入和列出文件夹目录（打开文件）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329124140337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)
**验证权限：**

登录a用户进入D盘的最高机密文件夹，无法对其删除/写入/修改，只能读取
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113550484.png)
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113550990.png)
登录b用户进入D盘的最高机密文件夹，无法对其读取/删除/修改，只能创建和打开文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329125819530.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)

> 同理子文件夹（密码）的权限继承了他的父级文件夹（最高机密）得权限

## 五、权限累加

当用户同时属于多个组时权限是累加的

b用户权限：
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113551336.png)
b用户又属于users这个组，users组权限为：
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113612988.png)
**b用户最终权限 = b用户权限 + users组权限**

多方权限累加

## 六、拒绝最大

相同的权限允许和拒绝发生碰撞拒绝最大

## 七、取得所有权

只有管理员组的用户可以操作

b用户新创建一个文件夹，并删除表中的管理员的权限
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113611322.png)
登录管理员账户并访问b账户创建的文件夹是无法访问的，没有权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329133044774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)
文件夹右键属性 – 安全
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113607058.png)
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113607057.png)
登录b用户查看，安全的选项都没有了
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113604983.png)

## 八、强制继承

作用：对下强制继承父子关系
方法：文件夹右键属性 – 安全 – 高级 – 勾选第二个对号
![在这里插入图片描述](6、NTFS安全权限/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109113615084.png)

## 九、文件复制和移动对权限的影响

文件复制后，文件的权限会被目标文件的权限所覆盖（无论同分区还是跨分区）。
跨分区移动文件权限会被覆盖，同分区移动文件权限会被保留。