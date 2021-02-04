# layui

> layui（谐音：类UI) 是一款采用自身模块规范编写的前端 UI 框架，遵循原生 HTML/CSS/JS 的书写与组织形式，门槛极低，拿来即用。其外在极简，却又不失饱满的内在，体积轻盈，组件丰盈，从核心代码到 API 的每一处细节都经过精心雕琢，非常适合界面的快速开发。layui 首个版本发布于2016年金秋，她区别于那些基于 MVVM 底层的 UI 框架，却并非逆道而行，而是信奉返璞归真之道。准确地说，她更多是为服务端程序员量身定做，你无需涉足各种前端工具的复杂配置，只需面对浏览器本身，让一切你所需要的元素与交互，从这里信手拈来。

https://www.layui.com/

```html
 ├─css //css目录
  │  │─modules //模块css目录（一般如果模块相对较大，我们会单独提取，比如下面三个：）
  │  │  ├─laydate
  │  │  ├─layer
  │  │  └─layim
  │  └─layui.css //核心样式文件
  ├─font  //字体图标目录
  ├─images //图片资源目录（目前只有layim和编辑器用到的GIF表情）
  │─lay //模块核心目录
  │  └─modules //各模块组件
  │─layui.js //基础核心库
  └─layui.all.js //包含layui.js和所有模块的合并文件
```

## 快速上手

获得 layui 后，将其完整地部署到你的项目目录（或静态资源服务器），你只需要引入下述两个文件：

```html
./layui/css/layui.css
./layui/layui.js //提示：如果是采用非模块化方式（最下面有讲解），此处可换成：./layui/layui.all.js
```

html

```html
layui.code
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
  <title>开始使用layui</title>
  <link rel="stylesheet" href="../layui/css/layui.css">
</head>
<body>
 
<!-- 你的HTML代码 -->
 
<script src="../layui/layui.js"></script>
<script>
//一般直接写在一个js文件中
layui.use(['layer', 'form'], function(){
  var layer = layui.layer
  ,form = layui.form;
  
  layer.msg('Hello World');
});
</script> 
</body>
</html>
      
```

![Window_和_index_html_—_layui](https://ws3.sinaimg.cn/large/006tNc79ly1g2tln418m2j31oq0u0jwc.jpg)

## 模块化的用法（推荐）

```js
<script>
layui.config({
  base: '/res/js/modules/' //你存放新模块的目录，注意，不是layui的模块目录
}).use('index'); //加载入口
</script>    
    
```

上述的 index 即为你 /res/js/modules/ 目录下的 index.js，它的内容应该如下：

```js
/**
  项目JS主入口
  以依赖layui的layer和form模块为例
**/    
layui.define(['layer', 'form'], function(exports){
  var layer = layui.layer
  ,form = layui.form;
  
  layer.msg('Hello World');
  
  exports('index', {}); //注意，这里是模块输出的核心，模块名必须和use时的模块名一致
});    
```

#### 示例

```html
layui.code
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
  <title>开始使用layui</title>
  <link rel="stylesheet" href="./layui/css/layui.css">
</head>
<body>
 
<!-- 你的HTML代码 -->
 
<script src="./layui/layui.js"></script>
<script>
    layui.config({
      base: 'js/' //你存放新模块的目录，注意，不是layui的模块目录
    }).use('index'); //加载入口
    </script> 
</body>
</html>
      
```

```js
/**
  项目JS主入口
  以依赖layui的layer和form模块为例
**/    
layui.define(['layer', 'form'], function(exports){
    var layer = layui.layer
    ,form = layui.form;
    
    layer.msg('Hello World');
    
    exports('index', {}); //注意，这里是模块输出的核心，模块名必须和use时的模块名一致
  }); 
```

![Window_和_index_html_—_layui](https://ws4.sinaimg.cn/large/006tNc79ly1g2tlun8fzuj31rg0u044a.jpg)

## 全局配置

方法：*layui.config(options)*

你可以在使用模块之前，全局化配置一些参数，尽管大部分时候它不是必须的。所以我们目前提供的全局配置项非常少，这也是为了减少一些不必要的工作，尽可能让使用变得更简单。目前支持的全局配置项如下：

```js
layui.config({
  dir: '/res/layui/' //layui.js 所在路径（注意，如果是script单独引入layui.js，无需设定该参数。），一般情况下可以无视
  ,version: false //一般用于更新模块缓存，默认不开启。设为true即让浏览器不缓存。也可以设为一个固定的值，如：201610
  ,debug: false //用于开启调试模式，默认false，如果设为true，则JS模块的节点会保留在页面
  ,base: '' //设定扩展的Layui模块的所在目录，一般用于外部模块扩展
});   
```

## 定义模块

方法：*layui.define([mods], callback)*

通过该方法可定义一个 *Layui模块*。参数mods是可选的，用于声明该模块所依赖的模块。callback即为模块加载完毕的回调函数，它返回一个exports参数，用于输出该模块的接口。

```js
layui.code
layui.define(function(exports){
  //do something
  
  exports('demo', function(){
    alert('Hello World!');
  });
});
```

跟Requirejs最大不同的地方在于接口输出，exports是一个函数，它接受两个参数，第一个参数为模块名，第二个参数为模块接口，当你声明了上述的一个模块后，你就可以在外部使用了，demo就会注册到layui对象下，即可通过 *layui.demo()* 去执行该模块的接口。

你也可以在定义一个模块的时候，声明该模块所需的依赖，如：

```js
layui.define(['layer', 'laypage'], function(exports){
  //do something
  
  exports('demo', function(){
    alert('Hello World!');
  });
});
```

## 加载所需模块

方法：*layui.use([mods], callback)*

Layui的内置模块并非默认就加载的，他必须在你执行该方法后才会加载。它的参数跟上述的 define方法完全一样。 
另外请注意，mods里面必须是一个合法的模块名，不能包含目录。如果需要加载目录，建议采用extend建立别名（详见模块规范）

```js
layui.use(['laypage', 'layedit'], function(){
  var laypage = layui.laypage
  ,layedit = layui.layedit;
  
  //do something
});
```

## 动态加载CSS

方法：*layui.link(href)*

href即为css路径。注意：该方法并非是你使用Layui所必须的，它一般只是用于动态加载你的外部CSS文件。

## 本地存储

本地存储是对 localStorage 和 sessionStorage 的友好封装，可更方便地管理本地数据。

> - localStorage 持久化存储：*layui.data(table, settings)，数据会永久存在，除非物理删除。*
> - sessionStorage 会话性存储：*layui.sessionData(table, settings)*，页面关闭后即失效。注：*layui 2.2.5 新增*

上述两个方法的使用方式是完全一样的。其中参数 *table* 为表名，*settings*是一个对象，用于设置key、value。下面与 layui.data 方法为例：

```js
//【增】：向test表插入一个nickname字段，如果该表不存在，则自动建立。
layui.data('test', {
  key: 'nickname'
  ,value: '贤心'
});
 
//【删】：删除test表的nickname字段
layui.data('test', {
  key: 'nickname'
  ,remove: true
});
layui.data('test', null); //删除test表
  
//【改】：同【增】，会覆盖已经存储的数据
  
//【查】：向test表读取全部的数据
var localTest = layui.data('test');
console.log(localTest.nickname); //获得“贤心”
```

## 其它

其实除此之外，layui.js内部还提供了许多底层引擎，他们同样是整个Layui框架体系的有力支撑，所以有必要露个脸，尽管你可能并不会用到：

| 方法/属性                                | 描述                                                         |
| :--------------------------------------- | :----------------------------------------------------------- |
| layui.cache                              | 静态属性。获得一些配置及临时的缓存信息                       |
| layui.extend(options)                    | 拓展一个模块别名，如：layui.extend({test: '/res/js/test'})   |
| layui.each(obj, fn)                      | 对象（Array、Object、DOM对象等）遍历，可用于取代for语句      |
| layui.getStyle(node, name)               | 获得一个原始DOM节点的style属性值，如：layui.getStyle(document.body, 'font-size') |
| layui.img(url, callback, error)          | 图片预加载                                                   |
| layui.sort(obj, key, desc)               | 将数组中的对象按某个成员重新对该数组排序，如：layui.sort([{a: 3},{a: 1},{a: 5}], 'a') |
| layui.router()                           | 获得location.hash路由，目前在Layui中没发挥作用。对做单页应用会派上用场。 |
| layui.hint()                             | 向控制台打印一些异常信息，目前只返回了error方法：layui.hint().error('出错啦') |
| layui.stope(e)                           | 阻止事件冒泡                                                 |
| layui.onevent(modName, events, callback) | 自定义模块事件，属于比较高级的应用。有兴趣的同学可以阅读layui.js源码以及form模块 |
| layui.event(modName, events, params)     | 执行自定义模块事件，搭配onevent使用                          |
| layui.factory(modName)                   | 用于获取模块对应的 define 回调函数                           |