## 1、什么是WEB服务器

- WEB服务器也称为网页服务器或HTTP服务器
- WEB服务器使用的是HTTP(不加密)和HTTPS协议(加密)

HTTP协议端口号：80
HTTPS协议端口号：443

## 2、WEB服务器发布软件

1）微软：IIS（可以发布WEB网站和FTP站点）
2）Liunx：Apache/LAMP/Tomcat/nginx
3）第三方：phpstudy XAMPP

## 3、部署WEB服务器

1）服务器配置静态IP
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736256.png)
2）安装WEB服务软件
这里把FTP也装上
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736314.png)
3）打开软件
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736449.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736568.png)
4）检查80端口是否打开
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736667.png)
打开浏览器输入IP查看默认站点
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736829.png)
默认网站路径
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736971.png)
5）编写网页
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135736948.png)
6）发布网站
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135737084.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135737154.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135737238.png)
选择网站路径并启用匿名访问
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739017.png)
动态网页需要勾选运行脚本
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135738967.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739160.png)
移到最上面
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739075.png)
xp访问服务器
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739123.png)
如果在一个服务器需要发布不同的站点需要指定不同的端口

## 4、DNS解析不同域名站点

利用DNS在同一个服务器上发布不同的站点（IP/端口相同）绑定的域名不同
1）新建两个网页京东和淘宝
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739133.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739662.png)
2）安装DNS组件并创建两个区域
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739867.png)
3）新建主机
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739280.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739717.png)
4）XP上指定DNS
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135740279.png)
5）正常情况下同一个服务器上一个端口只提供一个服务
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739951.png)
jd在占用80端口，taobao是停止状态

6）添加主机头值
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135739789.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135740260.png)
启动站点
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135740270.png)
7）XP验证
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135740356.png)
![在这里插入图片描述](10、IIS WEB服务器/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109135740504.png)
完成相同IP，相同端口，不同域名绑定

