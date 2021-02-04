## 项目初始化

- 前台
  - vue-shop
- 后台
  - vue-shop-admin
- 服务器
  - vue-shop-server

- router.js

```js
import Vue from 'vue'
import Router from 'vue-router'
// import login from '@/components/login'
// import login from '../components/login'
// 写法都一样
import login from '../components/login.vue'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      redirect:'/login'
    },
    {
      path:'/login',
      component:login
    }
  ]
})

```

- Main.js

```js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router/router'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

```

- login.vue

```vue
<template>
<div class="login">
  login
</div>
</template>

<script>
export default {
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>

```



- app.vue

```vue
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
}
</script>

<style>

</style>

```

### 区别

```sh
我们在使用 npm install 安装模块的模块的时候 ，一般会使用下面这几种命令形式：


npm install moduleName # 安装模块到项目目录下
 
npm install -g moduleName # -g 的意思是将模块安装到全局，具体安装到磁盘哪个位置，要看 npm config prefix 的位置。
 
npm install -save moduleName # -save 的意思是将模块安装到项目目录下，并在package文件的dependencies节点写入依赖。
 
npm install -save-dev moduleName # -save-dev 的意思是将模块安装到项目目录下，并在package文件的devDependencies节点写入依赖。
那么问题来了，在项目中我们应该使用四个命令中的哪个呢？这个就要视情况而定了。下面对这四个命令进行对比，看完后你就不再这么问了。

npm install moduleName 命令
1. 安装模块到项目node_modules目录下。
2. 不会将模块依赖写入devDependencies或dependencies 节点。
3. 运行 npm install 初始化项目时不会下载模块。

npm install -g moduleName 命令
1. 安装模块到全局，不会在项目node_modules目录中保存模块包。
2. 不会将模块依赖写入devDependencies或dependencies 节点。
3. 运行 npm install 初始化项目时不会下载模块。

npm install -save moduleName 命令
1. 安装模块到项目node_modules目录下。
2. 会将模块依赖写入dependencies 节点。
3. 运行 npm install 初始化项目时，会将模块下载到项目目录下。
4. 运行npm install --production或者注明NODE_ENV变量值为production时，会自动下载模块到node_modules目录中。

npm install -save-dev moduleName 命令
1. 安装模块到项目node_modules目录下。
2. 会将模块依赖写入devDependencies 节点。
3. 运行 npm install 初始化项目时，会将模块下载到项目目录下。
4. 运行npm install --production或者注明NODE_ENV变量值为production时，不会自动下载模块到node_modules目录中。

总结
devDependencies 节点下的模块是我们在开发时需要用的，比如项目中使用的 gulp ，压缩css、js的模块。这些模块在我们的项目部署后是不需要的，所以我们可以使用 -save-dev 的形式安装。像 express 这些模块是项目运行必备的，应该安装在 dependencies 节点下，所以我们应该使用 -save 的形式安装。
```



### 安装less

```sh
cnpm i less less-loader --save-dev
```

### 全局样式

```css
/* 全局样式 在main.js中引入*/
html,body,#app{
    margin: 0;
    width: 100%;
    height: 100%;
    padding: 0;
}
```

```js
// 引入全局样式
import "./assets/base.css"
```

### assets与static的区别

相同点：资源在html中使用，都是可以的。

不同点：使用assets下面的资源，在js中使用的话，路径要经过webpack中file-loader编译，路径不能直接写。

assets中的文件会经过webpack打包，重新编译，推荐该方式。而static中的文件，不会经过编译。项目在经过打包后，会生成dist文件夹，static中的文件只是复制一遍而已。简单来说，static中建议放一些外部第三方，自己的放到assets，别人的放到static中。

注意：如果把图片放在assets与static中，html页面可以使用；但在动态绑定中，assets路径的图片会加载失败，因为webpack使用的是commenJS规范，必须使用require才可以，具体代码如下：

html

```html
<div id="hook">
    <h3>演示钩子的组件</h3>
    <p>直接使用路径</p>
    <img src="../../assets/11.png" alt="图片加载失败" title="assets中的图片">
    <img src="../../../static/11.png" alt="图片加载失败" title="static中的图片">
    <br>
    <p>动态绑定路径</p>
    <img :src="assetsURL" alt="图片加载失败" title="assets中的图片">
    <img :src="staticURL" alt="图片加载失败" title="static中的图片">
  </div>

```

```js
data (){
      return {
        assetsURL: require('../../assets/11.png'),
        staticURL: '../../../static/11.png'
      }
    }
```

![img](/Users/liujiang/Documents/Typora/imgs/70.png)

## 安装element

```sh
cnpm i element-ui -S
```

### 按需引入

借助 [babel-plugin-component](https://github.com/QingWei-Li/babel-plugin-component)，我们可以只引入需要的组件，以达到减小项目体积的目的。

首先，安装 babel-plugin-component：

```sh
cnpm install babel-plugin-component -D
```

然后，将 .babelrc 修改为：

```js
{
  "presets": [
    ["env", {
      "modules": false,
      "targets": {
        "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]
      }
    }],
    "stage-2"
  ],
  "plugins": [
    "transform-vue-jsx",
    "transform-runtime",
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}

```

需要在 main.js 中写入以下内容

```js

import 'element-ui/lib/theme-chalk/index.css';
import { Button, Select,Form,Input } from 'element-ui';
Vue.use(Button);
Vue.use(Select);
Vue.use(Form);
Vue.use(Input);
```

### 登陆页面

```vue
<template>
  <div class="login">
    <div class="login-form">
      <div class="avatar">
        <img src="../assets/img/1.gif" alt />
      </div>
      <!-- 登陆表单 -->
      <el-form class="ruleForm">
        <el-form-item prop="name">
          <el-input  prefix-icon="el-icon-user-solid"></el-input>
        </el-form-item>
        <el-form-item prop="name">
          <el-input prefix-icon="el-icon-edit-outline"></el-input>
        </el-form-item>

        <el-form-item class="btns">
          <el-button type="primary">登录</el-button>
          <el-button type="info">重置</el-button>
        </el-form-item>
      </el-form>
      <!-- 按钮 -->
    </div>
  </div>
</template>

<script>
export default {};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.login {
  width: 100%;
  height: 100%;
  background-color: #2b4b6b;
  .login-form {
    width: 28.125rem;
    height: 18.75rem;
    background-color: white;
    border-radius: 3px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    .avatar {
      width: 130px;
      height: 130px;
      border: 1px #eee solid;
      border-radius: 50%;
      padding: 10px;
      box-shadow: 0 0 10px #ddd;
      position: absolute;
      left: 50%;
      transform: translate(-50%, -50%);
      // background-color: white;
      img {
        width: 100%;
        height: 100%;
        border-radius: 50%;
      }
    }
    .ruleForm {
      position: absolute;
      width: 100%;
      bottom: 0;
      padding: 0 10px;
      box-sizing: border-box;
    }
    .btns{
      display: flex;
      justify-content: flex-end;
    }
  }
}
</style>

```

### 安装Express

```sh
cnpm install express -D
```

```js
//启动服务器
const express = require('express');
// 使用框架创建web服务器
const app = express();
// 当客户端以get方式访问/路由时
app.get('/', (req, res) => {
    // 对客户端做出响应 send方法会根据内容的类型自动设置请求头
    res.send('Hello Express'); // <h2>Hello Express</h2> {say: 'hello'}
 });
 // 程序监听3000端口
 app.listen(3000);
```

- 运行server.js

```js
nodemon server.js
```

### 安装Mock.js

```js
cnpm install mockjs
```

```js
const express = require('express');
const Mock = require('mockjs')
// 使用框架创建web服务器
const app = express();
// 当客户端以get方式访问/路由时
app.get('/', (req, res) => {
    // 对客户端做出响应 send方法会根据内容的类型自动设置请求头
    var data = Mock.mock({
        'news|5-10': [
            {
            'id|+1': 1,
            title: '@title',
            text: '@cparagraph',
            time: '@date',
            number: '@integer(1, 1000)',
            image: '@image(42x42,@color,@character)'
        }]
    });
    res.send(data); // <h2>Hello Express</h2> {say: 'hello'}
 });
 // 程序监听3000端口
 app.listen(3000);
```

<http://localhost:3000/>

### 表单重置

![image-20191120073918357](/Users/liujiang/Documents/Typora/imgs/image-20191120073918357.png)

### 表单登陆验证

- 安装axios

```sh
cnpm install axios
```

vue项目使用axios发送post请求时遇到了前端传数据后端接收不到的情况

- axios传值是这样的

![image-20191120084848105](/Users/liujiang/Documents/Typora/imgs/image-20191120084848105.png)

- ajax传值是这样的

![image-20191120084902083](/Users/liujiang/Documents/Typora/imgs/image-20191120084902083.png)

一个Request Payload，一个Form Data。
将Request Payload 转为Form Data格式就可以了

### 使用qs

这个库是 axios 里面包含的，不需要再下载了

```js
// 引入qs解决axios，post请求 后台接收为空的情况
import qs from 'qs';
Vue.prototype.$qs = qs;
```

```js
 // 表单登陆验证
    login() {
      this.$refs.ruleForm.validate(valid => {
        console.log(valid); //返回true  false
        if (!valid) return;
        // this.$http.get('/login?name='+this.ruleForm.username)
        this.$http.post("login", this.$qs.stringify({
          uname: "lisi",
          pwd: 123
        }));
      });
    }
```

```
现象：
使用 ES7 的 async/await 时报错。

原因：这个regeneratorRuntime在浏览器上是不认识的，通过百度，需要安装一个
babel-plugin-transform-runtime插件
```

解决方法：

```
npm i --save-dev babel-plugin-transform-runtime
```

在 .babelrc 文件中添加：

```
"plugins": [
[
  "transform-runtime",
  {
    "helpers": false,
    "polyfill": false,
    "regenerator": true,
    "moduleName": "babel-runtime"
  }
]]
```

然后再去运行，就可以了。

```html
<template>
  <div class="login">
    <div class="login-form">
      <div class="avatar">
        <img src="../assets/img/1.gif" alt />
      </div>
      <!-- 登陆表单 -->
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" class="ruleForm">
        <el-form-item prop="username">
          <el-input v-model="ruleForm.username" prefix-icon="el-icon-user-solid"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input type="password" v-model="ruleForm.password" prefix-icon="el-icon-edit-outline"></el-input>
        </el-form-item>

        <el-form-item class="btns">
          <el-button type="primary" @click="login">登录</el-button>
          <el-button type="info" @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </el-form>
      <!-- 按钮 -->
    </div>
  </div>
</template>

<script>
import { async } from 'q';
export default {
  data() {
    return {
      ruleForm: {
        username: "admin",
        password: "123"
      },
      rules: {
        username: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 2, max: 15, message: "长度在 2 到 15 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 3, max: 12, message: "长度在 3 到 12 个字符", trigger: "blur" }
        ]
      }
    };
  },
  methods: {
    resetForm() {
      console.log(this);
      this.$refs.ruleForm.resetFields();
    },
    // 表单登陆验证
    login() {
      this.$refs.ruleForm.validate(async valid => {
        console.log(valid); //返回true  false
        if (!valid) return;
        // this.$http.get('/login?name='+this.ruleForm.username)
        //解构赋值，我只要data的数据 重新赋值到res里
        const {data:res}=await this.$http.post("login", this.$qs.stringify(this.ruleForm));
        console.log(res);//{msg: "登陆失败", status: 400}
        if(res.status!=200){
          alert("登陆失败")
        }else{
          console.log("登陆成功");
          
        }
        
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.login {
  width: 100%;
  height: 100%;
  background-color: #2b4b6b;
  .login-form {
    width: 28.125rem;
    height: 18.75rem;
    background-color: white;
    border-radius: 3px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    .avatar {
      width: 130px;
      height: 130px;
      border: 1px #eee solid;
      border-radius: 50%;
      padding: 10px;
      box-shadow: 0 0 10px #ddd;
      position: absolute;
      left: 50%;
      transform: translate(-50%, -50%);
      // background-color: white;
      img {
        width: 100%;
        height: 100%;
        border-radius: 50%;
      }
    }
    .ruleForm {
      position: absolute;
      width: 100%;
      bottom: 0;
      padding: 0 10px;
      box-sizing: border-box;
    }
    .btns {
      display: flex;
      justify-content: flex-end;
    }
  }
}
</style>

```

```js
const express = require('express');
const Mock = require('mockjs')
// 使用框架创建web服务器
const app = express();
// 引入body-parser模块  处理接收post请求
const bodyParser = require('body-parser');
// 配置body-parser模块
app.use(bodyParser.urlencoded({ extended: false }));
// 解析以 application/json 和 application/x-www-form-urlencoded 提交的数据
app.use(bodyParser.urlencoded({ extended: false }));
//  后台全局请求地址
const url = "/shop/console"

//设置跨域请求
app.all('*', function (req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With , yourHeaderFeild');
    res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By", ' 3.2.1')
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});


app.post(url + "/login", (req, res) => {
    console.log(req.body.username);
    let username = req.body.username;
    //console.log(req.query);//"获取get提交参数" req.query.name
    if(username=="admin"){
        res.send({msg:"登陆成功",status:200})
    }else{
        res.send({msg:"登陆失败",status:400});
    }
   
})

// 当客户端以get方式访问/路由时
app.get('/', (req, res) => {
    // 对客户端做出响应 send方法会根据内容的类型自动设置请求头
    var data = Mock.mock({
        'news|5-10': [
            {
                'id|+1': 1,
                title: '@title',
                text: '@cparagraph',
                time: '@date',
                number: '@integer(1, 1000)',
                image: '@image(42x42,@color,@character)'
            }]
    });
    res.send(data); // <h2>Hello Express</h2> {say: 'hello'}
});
// 程序监听3000端口
app.listen(3000);
```

![image-20191120090545356](/Users/liujiang/Documents/Typora/imgs/image-20191120090545356.png)

# 消息提示

elm.js

```js
import Vue from 'vue'
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
/* eslint-disable no-new */

// axios
import axios from 'axios'
// Vue.use(axios);
//挂载原型对象上，这样每个组件通过 this.$http都能发送axios请求
axios.defaults.baseURL = "http://localhost:3000/shop/console"
Vue.prototype.$http = axios

// 引入qs解决axios，post请求 后台接收为空的情况
import qs from 'qs';
Vue.prototype.$qs = qs;


//按需导入需要设置，全局不需要，直接使用 消息提示框  Message 组件名 不能变
Vue.prototype.$message = Message



```

```js
 // 表单登陆验证
    login() {
      this.$refs.ruleForm.validate(async valid => {
        console.log(valid); //返回true  false
        if (!valid) return;
        // this.$http.get('/login?name='+this.ruleForm.username)
        //解构赋值，我只要data的数据 重新赋值到res里
        const {data:res}=await this.$http.post("login", this.$qs.stringify(this.ruleForm));
        console.log(res);//{msg: "登陆失败", status: 400}
        if(res.status!=200){
          this.$message.error("登陆失败")
        }else{
          //跳转到主页
          // this.$message.success("登陆成功")
        }
        
      });
```

# 保存数据

```js
// 保存数据到sessionStorage
sessionStorage.setItem('key', 'value');
 
// 从sessionStorage获取数据
var data = sessionStorage.getItem('key');
 
// 从sessionStorage删除保存的数据
sessionStorage.removeItem('key');
 
// 从sessionStorage删除所有保存的数据
sessionStorage.clear();
```

```js
// 表单登陆验证
    login() {
      this.$refs.ruleForm.validate(async valid => {
        console.log(valid); //返回true  false
        if (!valid) return;
        // this.$http.get('/login?name='+this.ruleForm.username)
        //解构赋值，我只要data的数据 重新赋值到res里
        const {data:res}=await this.$http.post("login", this.$qs.stringify(this.ruleForm));
        console.log(res);//{msg: "登陆失败", status: 400}
        if(res.status!=200){
          this.$message.error("登陆失败")
        }else{
          //跳转到主页
          // this.$message.success("登陆成功")
          //登陆成功后 将用户名密码保存到sessionStorage中
          //通过编程时导航跳转到后台主页，路由地址是/home
          sessionStorage.setItem("username",this.ruleForm.username)
          sessionStorage.setItem("password",this.ruleForm.password)
          this.$router.push('/home')
        }
        
      });
```

## 主页

创建home组件

```js
import Vue from 'vue'
import Router from 'vue-router'
// import login from '@/components/login'
// import login from '../components/login'
// 写法都一样
import login from '../components/login.vue'
import home from '../components/home.vue'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      redirect:'/login'
    },
    {
      path:'/login',
      component:login
    },
    {
      path:'/home',
      component:home
    }
  ]
})

```

# 路由守卫

如果有token，可以看见home，如果删除后，依然能看见，非法登陆

to:将要访问的路径

from：从哪个页面路径调换过来

next：放行

```js
import Vue from 'vue'
import Router from 'vue-router'
// import login from '@/components/login'
// import login from '../components/login'
// 写法都一样
import login from '../components/login.vue'
import home from '../components/home.vue'

Vue.use(Router)

const router =  new Router({
  routes: [
    {
      path: '/',
      redirect:'/login'
    },
    {
      path:'/login',
      component:login
    },
    {
      path:'/home',
      component:home
    }
  ]
})
// 挂载路由守卫
router.beforeEach((to,form,next)=>{
  // next()放行  next（‘/login’）强制跳转
  if(to.path==="/login") return next();
  let username = sessionStorage.getItem("username")
  console.log(username);//null
  if(username==null){
      return next('/login')
  }else{
    next()
  }

})
export default router

```

# 退出

清除sessionStorage即可

```vue
<template>
    <div class="home">
        <el-button type="primary" @click="loginout">退出</el-button>
    </div>
</template>

<script>
export default{
  methods:{
    loginout(){
      sessionStorage.clear();
      this.$router.push('/login')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>

</style>

```



