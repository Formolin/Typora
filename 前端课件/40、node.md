### 一、为什么要学习服务器端开发基础

- 能够和后端程序员更加紧密的配合

- 网站业务逻辑前置，学习前端技术需要后端技术支撑（Ajax）

- 扩宽知识视野，能够站在更高的角度审视整个项目

### 二、服务器端开发要做的事情

- 实现网站的业务逻辑
- 数据的增删改查

### 三、 为什么选择Node

- 使用JavaScript语法开发后端应用

- 一些公司要求前端工程师掌握Node开发

- 生态系统活跃，有大量开源库可以使用

- 前端开发工具大多基于Node开发

### 四、Node是什么

Node是一个基于Chrome V8引擎的JavaScript代码运行环境。

#### 运行环境

浏览器（软件）能够运行JavaScript代码，浏览器就是JavaScript代码的运行环境
Node（软件）能够运行JavaScript代码，Node就是JavaScript代码的运行环境



 Node.js运行环境安装 

![image-20191103191943217](/Users/liujiang/Documents/Typora/imgs/image-20191103191943217.png)

- LTS = Long Term Support 长期支持版 稳定版
- Current 拥有最新特性 实验版

![image-20191103193056222](/Users/liujiang/Documents/Typora/imgs/image-20191103193056222.png)

### 五、Node环境安装失败解决办法

![image-20191103192024658](/Users/liujiang/Documents/Typora/imgs/image-20191103192024658.png)

- 错误代号2502、2503

  - 失败原因：系统帐户权限不足。

  - 解决办法：
    以管理员身份运行powershell命令行工具
    输入运行安装包命令 msiexec /package node安装包位置

### 六、Node.js环境安装失败解决办法 

- 执行命令报错

- 失败原因：Node安装目录写入环境变量失败

- 解决办法：将Node安装目录添加到环境变量中

```js
node -v
```



### 七、PATH环境变量

存储系统中的目录，在命令行中执行命令的时候系统会自动去这些目录中查找命令的位置。



### 八、Node.js 的组成

JavaScript 由三部分组成，ECMAScript，DOM，BOM。

Node.js是由ECMAScript及Node 环境提供的一些附加API组成的，包括文件、网络、路径等等一些更加强大的 API。

![image-20191103193503147](/Users/liujiang/Documents/Typora/imgs/image-20191103193503147.png)

### 九、Node.js基础语法

所有ECMAScript语法在Node环境中都可以使用。

在Node环境下执行代码，使用Node命令执行后缀为.js的文件即可

```js
//hello.js
var str = "hello node js";
console.log(str)

node hello.js
```

### 十、模块化开发

- js开发的弊端

![image-20191103205927427](/Users/liujiang/Documents/Typora/imgs/image-20191103205927427.png)

- 模块化开发

![image-20191103210031432](/Users/liujiang/Documents/Typora/imgs/image-20191103210031432.png)

一个功能就是一个模块，多个模块可以组成完整应用，抽离一个模块不会影响其他功能的运行。

![image-20191103210145349](/Users/liujiang/Documents/Typora/imgs/image-20191103210145349.png)

### 十一、Node.js中模块化开发规范

- Node.js规定一个JavaScript文件就是一个模块，模块内部定义的变量和函数默认情况下在外部无法得到
- 模块内部可以使用exports对象进行成员导出， 使用require方法导入其他模块。

![image-20191103192656560](/Users/liujiang/Documents/Typora/imgs/image-20191103192656560.png)

#### 模块成员导出

```js
  // a.js
  // 在模块内部定义变量
 let version = 1.0;
 // 在模块内部定义方法
 const sayHi = name => `您好, ${name}`;
 // 向模块外部导出数据 
 exports.version = version;
 exports.sayHi = sayHi;

```

![image-20191103211755591](/Users/liujiang/Documents/Typora/imgs/image-20191103211755591.png)

```js
//b.js 引入a模块
let a = require("./hello.js")
console.log(a.version)
console.log(a.sayHi("Tony	老师"))

//运行node b.js
```

#### 另一种模块成员导出的方式

```js
module.exports.version = version
module.exports.sayHi = sayHi
```

exports是module.exports的别名，导出对象最终以module.exports为准

a.js

- 相同的name
- 结果是 haha

```js
const name = 'tom'
const say = name=>`${name}`
exports.name = name
exports.say = say
module.exports.name = 'haha'
```

b.js

```js
const app = require('./a.js')
console.log(app.name);
```

- 如果导出的是对象

a.js

```js
const name = 'tom'
const say = name=>`${name}`
exports.name = name
exports.say = say
module.exports.name = 'haha'
module.exports = {
    name:'lisi'
}

```

- 此时以module.exports为准，之前导入的都会变成此对象

b.js

```js
const app = require('./a.js')
console.log(app.name);
```

- 结果是lisi，并且没有say方法

- 如果导出多个对象，后面会覆盖前面的

```js
const name = 'tom'
const say = name=>`${name}`
exports.name = name
exports.say = say
module.exports.name = 'haha'
module.exports = {
    name:'lisi'
}
module.exports = {
   age:19
}
```

- 可以分别起别名

```js
const name = 'tom'
const say = name=>`${name}`
exports.name = name
exports.say = say
module.exports.name = 'haha'
module.exports.q = {
    name:'lisi'
}
module.exports.w = {
   age:19
}

```

- 结果

```js
{
  name: 'haha',
  say: [Function: say],
  q: { name: 'lisi' },
  w: { age: 19 }
}
```



### 十二、什么是系统模块

Node运行环境提供的API. 因为这些API都是以模块化的方式进行开发的, 所以我们又称Node运行环境提供的API为系统模块

![image-20191103213750075](/Users/liujiang/Documents/Typora/imgs/image-20191103213750075.png)

#### 系统模块fs 文件操作

```js
//fs.js
//f：file 文件 ，s：system 系统，文件操作系统。
const fs = require('fs')
//fs.readFile('文件路径/文件名称'[,'文件编码'], callback);
fs.readFile('./hello.js','utf-8',(err,data)=>{
    //第一个参数是err 默认把错误信息封装到第一个参数err里
    //第二个参数，是成功的数据
    if(err==null){
        console.log(data);
    }else{
        console.log(err);
    }
})
```

```js
//fs.writeFile('文件路径/文件名称', '数据', callback);
const fs = require('fs')
const content = '<h3>正在使用fs.writeFile写入文件内容</h3>';
 fs.writeFile('./index.html', content, err => {
   if (err != null) { 
       console.log(err);
       return;
   }
   console.log('文件写入成功');
 });
//自动创建index.html
```

#### 系统模块path 路径操作

为什么要进行路径拼接

- 为什么要进行路径拼接
- /public/uploads/avatar
- Windows 上是 \   
- Linux 上是 /

##### 路径拼接语法

```js
//path.join('路径', '路径', ...)

// 导入path模块
const path = require('path');
// 路径拼接
let finialPath = path.join('aishang', 'node', 'css', 'c.css');
// 输出结果 aishang/node/css/c.css
console.log(finialPath);


```

#### 相对路径VS绝对路径

- 大多数情况下使用绝对路径，因为相对路径有时候相对的是命令行工具的当前工作目录
- 在读取文件或者设置文件路径时都会选择绝对路径
- 使用__dirname获取当前文件所在的绝对路径

```js
fs.readFile(path.join(__dirname,'./hello.js'),'utf-8',(err,data)=>{
    if(err==null){
        console.log(data);
    }else{
        console.log(err);
    }
})
```



#### Node.js全局对象global(了解)

在浏览器中全局对象是window，在Node中全局对象是global。

Node中全局对象下有以下方法，可以在任何地方使用，global可以省略。

console.log()     在控制台中输出
setTimeout()     设置超时定时器
clearTimeout()  清除超时时定时器
setInterval()      设置间歇定时器
clearInterval()   清除间歇定时器

### 十三、什么是第三方模块

别人写好的、具有特定功能的、我们能直接使用的模块即第三方模块，由于第三方模块通常都是由多个文件组成并且被放置在一个文件夹中，所以又名包。

#### 第三方模块有两种存在形式：

- 以js文件的形式存在，提供实现项目具体功能的API接口。
- 以命令行工具形式存在，辅助项目开发

#### 获取第三方模块

npmjs.com：第三方模块的存储和分发仓库

npm (node package manager) ： node的第三方模块管理工具

下载：npm install 模块名称（相当于之引入jquery第三方插件，不需下载了）
卸载：npm unintall package 模块名称

```js
npm install less

安装到当前项目node_modules文件夹
```

#### 全局安装与本地安装

区别为带参数-g和不带参数-g

- 命令行工具：全局安装
  - 安装到公共的目录，可以通过npm root -g命令查看全局安装目录
  - 全局安装后可以供命令行使用，用户可以在命令行中直接运行该组件包支持的命令（例如 终端命令 node、npm等等）
  - 全局安装的方式是没有办法用require调用包的
- 库文件：本地安装
  - 将当前的文件下载到当前项目中，共当前项目使用
  - 可以通过 require() 来引入本地安装的包

```sh
(1) npm install xxx --save  ->npm install xxx -S

这样安装的包,信息写入在package.json中的dependencies中, dependencies是运行时依赖.

(2) npm install xxx --save-dev ->npm install xxx -D

这样安装的包,信息写入在package.json中的devDependencies中, devDependencies是开发时的依赖.

如果是默认安装,npm install xxx ,信息会被写入在package.json中的dependencies对象中.

上述命令执行之后将会在当前的目录下创建一个 node_modules 的目录（如果不存在的话），然后将下载的包保存到这个目录下。


```



### 十四、node_modules文件夹的问题

1. 文件夹以及文件过多过碎，当我们将项目整体拷贝给别人的时候，传输速度会很慢很慢. 
2. 复杂的模块依赖关系需要被记录，确保模块的版本和当前保持一致，否则会导致当前项目运行报错

在传给别人的时候，不需要传node_modules里面的东西

#### package.json文件的作用

项目描述文件，记录了当前项目信息，例如项目名称、版本、作者、github地址、当前项目依赖了哪些第三方模块等。

- 项目根目录 使用npm init -y命令生成。（-y 使用默认值）

```json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "", 描述
  "main": "index.js",主入口文件
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },命令的别名，命令比经长得时候起别名
  "keywords": [],用关键词来描述项目
  "author": "",
  "license": "ISC"默认协议，开放源代码
}

```

- 删除node_module文件夹，给别人，拿到项目后
- 执行 npm install

### 十五、第三方模块 nodemon

nodemon是一个命令行工具，用以辅助项目开发。
在Node.js中，每次修改文件都要在命令行工具中重新执行该文件，非常繁琐。

想达到只要保存，就更新

使用npm install nodemon -g 下载它

在命令行工具中用nodemon命令替代node命令执行文件

```js
nodemon hello.js
 console.log('测试nodemon')
```

更改代码保存自动执行

### 十六、cnpm

- 淘宝团队做的国内镜像，因为npm的服务器位于国外可能会影响安装。淘宝镜像与官方同步频率目前为 10分钟 一次以保证尽量与官方服务同步。
- 安装：命令提示符执行
   `npm install cnpm -g --registry=https://registry.npm.taobao.org` 
-  `cnpm -v` 来测试是否成功安装

### 十七、项目依赖

- 在项目的开发阶段和线上运营阶段，都需要依赖的第三方包，称为项目依赖
- 使用npm install 包名命令，下载的文件会默认被添加到 package.json 文件的 dependencies 字段中

### 十八、 开发依赖

- 在项目的开发阶段需要依赖，线上运营阶段不需要依赖的第三方包，称为开发依赖
- 使用npm install 包名 --save-dev命令将包添加到package.json文件的devDependencies字段中

### 十九、package-lock.json文件的作用

- 锁定包的版本，确保再次下载时不会因为包版本不同而产生问题
- 加快下载速度，因为该文件中已经记录了项目所依赖第三方包的树状结构和包的下载地址，重新安装时只需下载即可，不需要做额外的工作

### 二十、模块查找规则

#### 当模块拥有路径但没有后缀时

```js
require('./find.js');
require('./find');
```

1. require方法根据模块路径查找模块，如果是完整路径，直接引入模块。
2. 如果模块后缀省略，先找同名JS文件再找同名JS文件夹
3. 如果找到了同名文件夹，找文件夹中的index.js
4. 如果文件夹中没有index.js就会去当前文件夹中的package.json文件中查找main选项中的入口文件
5. 如果找指定的入口文件不存在或者没有指定入口文件就会报错，模块没有被找到

#### 当模块没有路径且没有后缀时

```js
require('find');
```

1. Node.js会假设它是系统模块
2. Node.js会去node_modules文件夹中
3. 首先看是否有该名字的JS文件
4. 再看是否有该名字的文件夹
5. 如果是文件夹看里面是否有index.js
6. 如果没有index.js查看该文件夹中的package.json中的main选项确定模块入口文件
7. 否则找不到报错

# 作业

- 安装node
- 测试npm
- 安装cnpm
- 安装nodemon
- 什么是项目依赖，什么是开发依赖？
- 模块查找规则是什么？
- 创建一个项目，安装jquery
  - 分析是项目依赖还是开发依赖
  - 创建html页面，测试jquery是否生效
  - 生效后，把项目发送给他人
  - 接到别人的项目后，使用对方测试jquery的代码，查看结果