# Lombok

每次创建实体类，都需要生成setget方法，如果我封装的时候，需要在新添加一个字段，那么我还需要重写，还需要构造方法，toString等

Lombok 是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得很臃肿的 Java 代码的工具，通过使用对应的注解，可以在编译源码的时候生成对应的方法。

- 官网地址：https://projectlombok.org/

- GitHub：https://github.com/rzwitserloot/lombok

  ## IDEA 安装 Lombok 插件

IDEA 中依次点击 `File` --> `Settings` --> `Plugins` 搜索 Lombok 安装即可

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1512345603.png)

## 查看是否安装成功

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1512345786.png)

## 使用 Lombok

### POM

`pom.xml` 中增加所需依赖，坐标如下：

```text
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.18</version>
</dependency>
```

### 使用 `@Data` 注解简化 POJO

`@Data` 包含了 `@ToString`，`@EqualsAndHashCode`，`@Getter/@Setter` 和 `@RequiredArgsConstructor` 的功能

其他相关注解请自行查阅：http://jnb.ociweb.com/jnb/jnbJan2010.html

### 使用案例

```text
@Data
public class ItemCatNode implements Serializable {
    @JsonProperty(value = "u")
    private String url;
    @JsonProperty(value = "n")
    private String name;
    @JsonProperty(value = "i")
    private List<?> item;
}
```



![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1512346835.png)

# JRebel

JRebel 是一款 Java 虚拟机插件，它使得 Java 程序员能在不进行重部署的情况下，即时看到代码的改变对一个应用程序带来的影响。JRebel 使你能即时分别看到代码、类和资源的变化，你可以一个个地上传而不是一次性全部部署。当程序员在开发环境中对任何一个类或者资源作出修改的时候，这个变化会直接反应在部署好的应用程序上，从而跳过了构建和部署的过程，每年可以省去部署用的时间花费高达 5.25 个星期。

## IDEA 安装 JRebel 插件

IDEA 中依次点击 `File` --> `Settings` --> `Plugins` 搜索 JRebel 安装即可

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1517278615.png)

## 激活 JRebel

- 确保你可以访问国外网站 [http://www.facebook.com](http://www.facebook.com/)，并确保你有一个 Facebook 账号或者 Twitter 账号
- 访问 [https://my.jrebel.com](https://my.jrebel.com/)，使用自己的 Facebook 账号登录

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1517278780.png)

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1517278874.png)

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1517278948.png)

- 将激活码复制到 Intellij IDEA JRebel 激活

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1517279104.png)

- 查看当前 JRebel 状态，绿色图标表示激活状态

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/Lusifer1517279160.png)