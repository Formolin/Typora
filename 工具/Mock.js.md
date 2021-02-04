## Mock.js

## 一、Express入门

#### 1、安装

新建一个目录，切换到当前目录下，npm init 初始化package.json

```node
liujiangdeMacBook-Air:Mockjs liujiang$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (mockjs)
```

输入项目名：例如expressdemo（注意单词不能有大写字母）

输入版本：1.0.0

项目描述description:excpressDemo

入口（默认是index.js）：可以改成app.js

test command:跳过

git repository:跳过

keywords:excrepss

作者：liujiang

license:跳过

```node
About to write to /Users/liujiang/Documents/HBuilderProjects/Mockjs/package.json:

{
  "name": "express",
  "version": "1.0.0",
  "description": "excpressDemo",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "express"
  ],
  "author": "liujiang",
  "license": "ISC"
}


Is this OK? (yes) yes


   ╭───────────────────────────────────────────────────────────────╮
   │                                                               │
   │       New minor version of npm available! 6.4.1 → 6.6.0       │
   │   Changelog: https://github.com/npm/cli/releases/tag/v6.6.0   │
   │               Run npm install -g npm to update!               │
   │                                                               │
   ╰───────────────────────────────────────────────────────────────╯

liujiangdeMacBook-Air:Mockjs liujiang$
```

成功！

#### 2、HelloExpress

```node
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(3000, () => console.log('Example app listening on port 3000!'))
```

- 先创建app.js

- 把代码粘贴过去

- 运行 `node app.js`

- 报错Error: Cannot find module 'express'，因为没有依赖

- 安装依赖，`npm install express --save`，—save安装到package.json

  ```node
  {
    "name": "expressdemo",
    "version": "1.0.0",
    "description": "excpressDemo",
    "main": "app.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [
      "express"
    ],
    "author": "liujiang",
    "license": "ISC",
    "dependencies": {
      "express": "^4.16.4"//添加了依赖
    }
  }
  ```

- 运行 node app.js 访问http://localhost:3000/，成功！control+c停止

## 二、使用生成器快速构建

#### 通过应用生成器工具 `express-generator` 可以快速创建一个应用的骨架。

`express-generator` 包含了 `express` 命令行工具。通过如下命令即可安装：

```sh
$ npm install express-generator -g
```

`-h` 参数可以列出所有可用的命令行参数：

```sh
$ express -h

  Usage: express [options] [dir]

  Options:

    -h, --help          输出使用方法
        --version       输出版本号
    -e, --ejs           添加对 ejs 模板引擎的支持
        --hbs           添加对 handlebars 模板引擎的支持
        --pug           添加对 pug 模板引擎的支持
    -H, --hogan         添加对 hogan.js 模板引擎的支持
        --no-view       创建不带视图引擎的项目
    -v, --view <engine> 添加对视图引擎（view） <engine> 的支持 (ejs|hbs|hjs|jade|pug|twig|vash) （默认是 jade 模板引擎）
    -c, --css <engine>  添加样式表引擎 <engine> 的支持 (less|stylus|compass|sass) （默认是普通的 css 文件）
        --git           添加 .gitignore
    -f, --force         强制在非空目录下创建
```

例如，如下命令创建了一个名称为 *demo* 的 Express 应用。此应用将在当前目录下的 *Mockjs* 目录中创建：

```
liujiangdeMacBook-Air:Mockjs liujiang$ mkdir demo
liujiangdeMacBook-Air:Mockjs liujiang$ cd demo
liujiangdeMacBook-Air:Mockjs liujiang$ express demo
liujiangdeMacBook-Air:demo liujiang$ npm install
```

![app_js_—_Mockjs](https://ws2.sinaimg.cn/large/006tNc79ly1fzikpsrv5oj306e08xweo.jpg)

在 MacOS 或 Linux 中，通过如下命令启动此应用：

```sh
$ npm start//这里需进入第二个demo的路径，下次可以不这么创建项目，直接新建一个项目，按照官网进行
```

然后在浏览器中打开 `http://localhost:3000/` 网址就可以看到这个应用了。

![Express](https://ws3.sinaimg.cn/large/006tNc79ly1fzil0y04coj308k080wep.jpg)

通过生成器创建的应用一般都有如下目录结构：

```sh
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug

7 directories, 9 files
```

## 三、supervisor

### 1、修改内容

在routers下的index.js

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express--test' });//修改
});

module.exports = router;

```

需要重启，npm start,很麻烦

### 2、安装

```sh
npm install -g supervisor
```

### 3、启动

```sh
supervisor ./bin/www
```

在更改内容后就不要再启动了，直接刷新就好了

## 四、初始Mock.js

### 1、什么是Mock.js

是一款模拟数据的生成器，让前端工程师独立开发，不依赖后端

### 2、Mock.js能做什么

- 根据数据模板生成模拟数据
- 模拟ajax请求，生成并返回数据
- 基于html模板生成模拟数据

### 3、为什么用Mockjs

- 开发时，后端还没完成数据输出，前端只能写好静态模拟数据,数据太长了，将数据写在js文件里，完成后挨个改url。

- 某些逻辑复杂的代码，加入或去除模拟数据时容易出错

- 想要尽可能还原真实的数据，要么编写更多的代码，要么手动修改模拟数据

- 特殊的格式，例如ip，随机数，图片，地址，需要去收集，分页显示

### 4、如何正确使用Mockjs

- 安装 `npm install mockjs`
- 使用

```nodejs
var Mock = require('mockjs');
var data = Mock.mock({
    'list|1-10':[{
        'id|+1':1
    }]
});
console.log(JSON.stringify(data,null,4))
```

- 加载Mock

```html
<script src = "http://mockjs.com/dist/mock.js"></script>
```

## 五、Node中集成Mock

### 1、安装

- 新建项目文件夹 node_mock

- npm install mockjs

- 新建demo.js

- copy上面的使用代码

- 运行 node demo.js

  ```node
  {
      "list": [
          {
              "id": 1
          },
          {
              "id": 2
          },
          {
              "id": 3
          },
          {
              "id": 4
          },
          {
              "id": 5
          },
          {
              "id": 6
          },
          {
              "id": 7
          }
      
  ```

  

### 2、融合Express

- 使用之前的express项目(切换到项目目录)，将mockjs打包到express

- npm install mockjs --save

```json
{
  "name": "demo",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.9",
    "express": "~4.16.0",
    "http-errors": "~1.6.2",
    "jade": "~1.11.0",
    "mockjs": "^1.0.1-beta3",
    "morgan": "~1.9.0"
  }
}
```

- 使用supervisor启动  supervisor ./bin/www

- 切换到路由 routes/index.js

- 修改

  ```js
  var express = require('express');
  var router = express.Router();
  
  /* GET home page. */
  router.get('/', function (req, res, next) {
    res.render('index', { title: 'Express--supervisor' });
  });
  // mock
  router.get('/mock', function (req, res, next) {
    var Mock = require('mockjs');
    var data = Mock.mock({
      'list|1-10': [{
        'id|+1': 1
      }]
    });
    var result = JSON.stringify(data, null, 4);
    res.render('index', { title: result });
  });
  //有html的
  //使用send方法，注意两者的区别
  router.get('/mock', function (req, res, next) {
    var Mock = require('mockjs');
    var data = Mock.mock({
      'list|1-10': [{
        'id|+1': 1
      }]
    });
    var result = JSON.stringify(data, null, 4);
    res.send(result);
  });
  module.exports = router;
  
  ```

## 六、mockjs语法规范

### 1、数据模板的定义DTD

- String
- Number
- Boolean
- Object
- Array
- function

**数据模板中的每个属性由 3 部分构成：属性名、生成规则、属性值：**

```
// 属性名   name
// 生成规则 rule
// 属性值   value
'name|rule': value
```

**注意：**

- *属性名* 和 *生成规则* 之间用竖线 `|` 分隔。

- *生成规则* 是可选的。

- 生成规则

   

  有 7 种格式：

  1. `'name|min-max': value`
  2. `'name|count': value`
  3. `'name|min-max.dmin-dmax': value`
  4. `'name|min-max.dcount': value`
  5. `'name|count.dmin-dmax': value`
  6. `'name|count.dcount': value`
  7. `'name|+step': value`

- **生成规则 的 含义 需要依赖 属性值的类型 才能确定。**

- *属性值* 中可以含有 `@占位符`。

- *属性值* 还指定了最终值的初始值和类型。

**生成规则和示例：**

#### 1.1. 属性值是字符串 **String**

1. `'name|min-max': string`

   通过重复 `string` 生成一个字符串，重复次数大于等于 `min`，小于等于 `max`。

2. `'name|count': string`

   通过重复 `string` 生成一个字符串，重复次数等于 `count`。

#### 1.2. 属性值是数字 **Number**

1. `'name|+1': number`

   属性值自动加 1，初始值为 `number`。

2. `'name|min-max': number`

   生成一个大于等于 `min`、小于等于 `max` 的整数，属性值 `number` 只是用来确定类型。

3. `'name|min-max.dmin-dmax': number`

   生成一个浮点数，整数部分大于等于 `min`、小于等于 `max`，小数部分保留 `dmin` 到 `dmax` 位。

```
Mock.mock({
    'number1|1-100.1-10': 1,
    'number2|123.1-10': 1,
    'number3|123.3': 1,
    'number4|123.10': 1.123
})
// =>
{
    "number1": 12.92,
    "number2": 123.51,
    "number3": 123.777,
    "number4": 123.1231091814
}
```

#### 1.3. 属性值是布尔型 **Boolean**

1. `'name|1': boolean`

   随机生成一个布尔值，值为 true 的概率是 1/2，值为 false 的概率同样是 1/2。

2. `'name|min-max': value`

   随机生成一个布尔值，值为 `value` 的概率是 `min / (min + max)`，值为 `!value` 的概率是 `max / (min + max)`。

#### 1.4. 属性值是对象 **Object**

1. `'name|count': object`

   从属性值 `object` 中随机选取 `count` 个属性。

2. `'name|min-max': object`

   从属性值 `object` 中随机选取 `min` 到 `max` 个属性。

#### 1.5. 属性值是数组 **Array**

1. `'name|1': array`

   从属性值 `array` 中随机选取 1 个元素，作为最终值。

2. `'name|+1': array`

   从属性值 `array` 中顺序选取 1 个元素，作为最终值。

3. `'name|min-max': array`

   通过重复属性值 `array` 生成一个新数组，重复次数大于等于 `min`，小于等于 `max`。

4. `'name|count': array`

   通过重复属性值 `array` 生成一个新数组，重复次数为 `count`。

#### 1.6. 属性值是函数 **Function**

1. `'name': function`

   执行函数 `function`，取其返回值作为最终的属性值，函数的上下文为属性 `'name'` 所在的对象。

   ```mock
   {
       'title':'hello mockjs',
       'function':function(){
           return this,title
       }
   }
   ->
   {
       "title":"hello mockjs",
       "function":"hello mockjs"
   }
   ```

   

#### 1.7. 属性值是正则表达式 **RegExp**

1. `'name': regexp`

   根据正则表达式 `regexp` 反向生成可以匹配它的字符串。用于生成自定义格式的字符串。

   ```
   Mock.mock({
       'regexp1': /[a-z][A-Z][0-9]/,
       'regexp2': /\w\W\s\S\d\D/,
       'regexp3': /\d{5,10}/
   })
   // =>
   {
       "regexp1": "pJ7",
       "regexp2": "F)\fp1G",
       "regexp3": "561659409"
   }
   ```

### 2、数据占位符的定义DPD

*占位符* 只是在属性值字符串中占个位置，并不出现在最终的属性值中。

*占位符* 的格式为：

```
@占位符
@占位符(参数 [, 参数])
```

**注意：**

1. 用 `@` 来标识其后的字符串是 *占位符*。
2. *占位符* 引用的是 `Mock.Random` 中的方法。
3. 通过 `Mock.Random.extend()` 来扩展自定义占位符。
4. *占位符* 也可以引用 *数据模板* 中的属性。
5. *占位符* 会优先引用 *数据模板* 中的属性。
6. *占位符* 支持 *相对路径* 和 *绝对路径*。

```js
Mock.mock({
    name: {
        first: '@FIRST',
        middle: '@FIRST',
        last: '@LAST',
        full: '@first @middle @last'
    }
})
// =>
{
    "name": {
        "first": "Charles",
        "middle": "Brenda",
        "last": "Lopez",
        "full": "Charles Brenda Lopez"
    }
}
```

## 七、访问

```js
//用send方法
router.get('/mock1', function (req, res, next) {
  var Mock = require('mockjs');
  var data = Mock.mock({
    "array|1-10": [
      {
        "name|+1": [
          "Hello",
          "Mock.js",
          "!"
        ]
      }
    ]
  });
  var result = JSON.stringify(data, null, 4);
  res.send(result);
});
```



## 八、ajax的跨域实现

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express--supervisor' });
});
// mock
router.get('/mock', function (req, res, next) {
  var Mock = require('mockjs');
  var data = Mock.mock({
    'list|1-10': [{
      'id|+1': 1
    }]
  });
  var result = JSON.stringify(data, null, 4);
  res.render('index', { title: result });
});
//用send方法
router.get('/mock1', function (req, res, next) {
  var Mock = require('mockjs');
  var data = Mock.mock({
    'categorys|3-5':[{
      categoryName:'@cname',
      'categoryID|+1':10,

      logo:'@image(64x64,#eee,Logo)',
      'categoryItems|1-4':[{

        cname:'@cname',
        'cid|+1':100,
        'item|3-7':[{

          name:'@cname',
          'id|+1':1000,
          link:'@url'
        }]
      }],
      'seller|8-15':[{
        name:'@cname@cname',
        url:'@url'
      }]
    }]
  });
  var result = JSON.stringify(data, null, 4);
  // 以ajax跨域 jsonp请求
  var callback = req.query.callback;
  result = callback+"("+result+")";
  res.send(result);
});
module.exports = router;

```

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script src="http://code.jquery.com/jquery-latest.js"></script>
    <script>
        console.log('aaa')
        var urlstr = 'http://localhost:3000/mock1';
        $.ajax({
            type:'get',
            async:false,
            url:urlstr,
            cache:false,
            dataType:'jsonp',
            success : function(json){
                console.log(json);
            },
            error:function(e){
                console.log(e);
            }

        })
    </script>
</head>
<body>
    
</body>
</html>
```

## 九、前端数据绑定

```js
<script>
    var urlstr = 'http://localhost:3000/mock1';
    $.ajax({
        type:'get',
        async:false,
        url:urlstr,
        cache:false,
        dataType:'jsonp',
        success : function(json){
            // console.log(json);
            $('.right li').html('');
            $(json.categorys).each(function(i,categorys){
                // console.log(i+categorys)
                $(categorys.categoryItems).each(function(i,categoryItems){
                    console.log(i+categoryItems.cname)
                    $('.right li').html(categoryItems.cname);
                })
            })

        },
        error:function(e){
            console.log(e);
        }

    })
</script>
```

## 十、项目发布

- 使用全局组件的尽量放到package.json
- 不放入packagejson时，需要在发布的及其上安装全剧组件
- 不清楚使用哪些全局组件，在目标机器上部署后，根据报错信息添加
  - npm install mockjs --save //--node modules(本地)

### 1、整体发布

- 所有都会发布
- 本地不会丢失
- 组件包发布后会非常大，打包也会变慢

### 2、源代码发布

- 体积比较小
- 发布本地包，全局在分别处理

### 3、压缩包

通过压缩包形式打包，不要node_modules文件夹，会根据package.json  npm install初始化



```js
var Mock = require('mockjs');
var data = Mock.mock({
    'news|5-10':[{
        'id|+1':1,
        title:'@title',
        text:'@cparagraph',
        time:'@date',
        number:'@integer(1, 1000)',
        image:'@image(42x42,#eee,Logo)'
    }]
});
console.log(JSON.stringify(data,null,4))
```

