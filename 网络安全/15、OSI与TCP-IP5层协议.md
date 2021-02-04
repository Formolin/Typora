# 一、基础架构

![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153257575.png)

# 二、分层模型

## 1、分层思想

通信需求：定义协议标准
完成每件事需要的协议太多怎么办？
![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153300361.png)
下层为上层提供服务，每一层的工作是独立的，最上层的是小女孩。

将复杂的流程分解为几个功能相对单一的子过程

- 整个流程更加清晰，复杂流程简单化
- 更容易发现问题并针对性的解决问题
  ![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153258550.png)
  ![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153253966.png)
  同层使用相同协议，每一层工作是相互独立的，下层为上层提供服务。

## 2、OSI七层模型

1、osi模型诞生时间
![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153254721.png)
![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153300211.png)

# 三、数据传输过程

TCP/IP 5层协议簇/协议栈

## 1、数据的封装与解封装过程

1）数据封装过程
![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153319825.png)
2）数据解封装过程
![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153303330.png)

## 2、OSI模型对比

![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153322050.png)

## 3、各层的硬件

![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153300590.png)

## 4、各层协议

![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153302154.png)

## 5、各层间通信

![image-20201109153318823](15、OSI与TCP-IP5层协议/image-20201109153318823.png)

# 四、物理层 网线线序

![在这里插入图片描述](15、OSI与TCP-IP5层协议/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70-20201109153303384.png)