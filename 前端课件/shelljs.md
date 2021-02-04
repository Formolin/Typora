# NodeJS shelljs 模块

![](shelljs/粘贴的图片2019_12_6_下午3_25.png)

安装

```js
npm install --save shelljs
```

`Shell`是`linux`下的脚本语言解析器，拥有丰富且强大的底层操作权限。`Shelljs`就是基于`node`的一层命令封装插件，让前端开发者可以不依赖`linux`也不依赖类似于`cmder`的转换工具，而是直接在我们最熟悉不过的`javascript`代码中编写`shell`命令实现功能。

### 官方示例（包含注释）

```js
//引入shelljs
var shell = require('shelljs')
 
//检查控制台是否可以运行`git `开头的命令
if (!shell.which('git')) {
  //在控制台输出内容
  shell.echo('Sorry, this script requires git');
  // 以退出码为code退出当前进程
  shell.exit(1);
}
 
shell.rm('-rf','out/Release');//强制递归删除`out/Release目录`
shell.cp('-R','stuff/','out/Release');//将`stuff/`中所有内容拷贝至`out/Release`目录
 
shell.cd('lib');//进入`lib`目录
//找出所有的扩展名为js的文件，并遍历进行操作
shell.ls('*.js').forEach(function (file) {
  /* 这是第一个难点：sed流编辑器,建议专题学习，-i表示直接作用源文件 */
  //将build_version字段替换为'v0.1.2'
  shell.sed('-i', 'BUILD_VERSION', 'v0.1.2', file);
  //将包含`REMOVE_THIS_LINE`字符串的行删除
  shell.sed('-i', /^.*REMOVE_THIS_LINE.*$/, '', file);
  //将包含`REPLACE_LINE_WITH_MACRO`字符串的行替换为`macro.js`中的内容
  shell.sed('-i', /.*REPLACE_LINE_WITH_MACRO.*\n/, shell.cat('macro.js'), file);
});
 
//返回上一级目录
shell.cd('..');
 
// 同步执行所传入的命令
if (shell.exec('git commit -am "Auto-commit"').code !== 0){
    shell.echo('Error: Git commit failed');
    shell.exit(1);
}
```

### 命令解释

- rm

  > 删除一个目录中一个或多个*文件*或*目录*，一旦删除，无法恢复。 `常用参数`：
  >
  > - -f:强制删除文件;
  > - -i:删除之前先询问用户;
  > - -r:递归处理目录;
  > - -v:显示处理过程;

- **cp([options,] source_array, dest)**

  > 用来将一个或多个源文件或目录复制到指定的文件或目录。 `常用参数`:
  >
  > - -f:强制删除文件;
  > - -i:删除之前先询问用户;
  > - -r:递归处理目录;

- **ls**

  > 用来显示目标列表。 `常用参数`:
  >
  > - -a:显示所有文件;
  > - -C:多列显示查询结果;
  > - -l:单列长格式显示查询结果(与-C相反);
  > - -R:递归处理目录;

- **sed([options,] search_regex, replacement, file_array**

  > 将`file_array`中符合`search_regex`的内容替换为`replacement`，支持正则的捕获组自引用。一次处理一行内容，处理完成后把缓冲区内容送往屏幕，然后处理下一行，循环直至结束。功能丰富且用法较复杂，建议自行百度进行专题学习。
  >
  > - -i:直接作用源文件

- **cat**

  > 将一个或多个文件内容读入，指定一个文件时读入该文件，指定多个文件时将内容连接在一起读入。

- exec(command,[, options][, callback])

  > 执行所传入的命令
  >
  > - async:是否异步执行,默认`false`，传入callback时自动开启
  > - slient:不输出信息到console,默认`false`
  > - encoding:默认`utf8`

### 文档中其他API概览

- **chmod**

  > 设置文件调用权限
  >
  > - **基本语法** ：chmod [-cfvR] [--help] [--version] mode file...
  > - -c：若文件权限确实被更改，才显示更改动作
  > - -f: 权限无法被更改时不显示错误信息
  > - -v: 显示权限变更的详细资料
  > - -R: 递归，对其目录下所有文件和子文件执行相同操作
  > - **mode字段格式** : [ugoa...][[+-=][rwxX]...][,...]
  > - u表示该文件拥有者，g表示同一群体者，o表示其他，a表示所有
  > - +表示增加权限，-表示取消权限，=表示唯一设定权限
  > - r表示可读，w表示可写，x表示可执行，X表示当该文件是个子目录？

- **find(path[,path...])**

  > 寻找路径

- **grep([options,] regex_filter,file)**

  > 从指定文件中抓取符合正则的行
  >
  > - -v:翻转正则匹配
  > - -l:仅打印符合条件的文件名

- **head([{'-n':<num>,}] file)**

  > 显示指定文件中的前N行
  >
  > - -n<num>:显示前`&lt;num&gt;`行

- **mv**

  > 移动文件

- **pwd**

  > 返回当前目录

- **set**

  > 设置全局变量的值

- **sort**

  > 将文件的内容逐行排序
  >
  > - -r:反转结果
  > - -n:依据数值对比

- **tail**

  > 读取指定文件的末尾n行，对比`head`命令进行理解

- **test（）**

  > 评估一个表达式是否为真(以下仅为最常见的参数用例)
  >
  > - -d,path:如果path是一个路径则返回`true`
  > - -e,path:如果path存在则返回`true`

- **ShellString()**

  > 构造器，将一个字符串转化为Shell字符串,转化后的字符串支持链式调用特殊的shell命令

- **ShellString.Prototype.to()**

  > 将`shellString`输出至指定文件,相当于脚本语言中的`&gt;`

- **ShellString.Prototype.toEnd()**

  > 将`shellString`追加至指定文件,相当于脚本语言中的`&gt;&gt;`

- **touch([options,]file)**

  > 生成文件
  >
  > - -m：仅修改编辑时间
  > - -c：不创建任何文件
  > - -d DATE:指定时间
  > - -r FILE:用FILE的时间替代新文件时间

- **env['VAR_NAME']**

  > 指向`process.env`

- Pipes链式调用支持

  > `sed`,`grep`,`cat`,`exec`,`to`,`toEnd`均支持链式调用。

## 案列

创建一个js文件，通过切换到指定目录后执行命令

```js
var shell = require("shelljs");
shell.cd("/Users/liujiang/Documents/myblog").exec("npm run dev")
```

执行 node 文件名.js