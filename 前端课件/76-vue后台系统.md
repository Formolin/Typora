# 主页开发

```vue
<template>
  <el-container class="bg">
    <el-header>
      AISHANG
      <el-button type="info" plain>退出</el-button>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu
          default-active="2"
          class="el-menu-vertical-demo"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
        >
          <el-submenu index="1">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>导航一</span>
            </template>
            <el-menu-item index="1-4-1">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>导航一</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  methods: {
    loginout() {
      sessionStorage.clear();
      this.$router.push("/login");
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.el-header {
  font-size: 26px;
  font-weight: bold;
  line-height: 60px;
  background-color: #545c64;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
  .el-button--info.is-plain {
    background-color: #545c64;
    color: white;

    &:hover {
      background-color: white;
      color: #919399;
    }
  }
}
.el-aside {
  background-color: #545c64;
  li {
    width: 200px;
  }
}
.el-main {
  background-color: #eaeef3;
}
.el-container {
  height: 100%;
}
</style>

```

# 请求拦截器

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
axios.interceptors.request.use(config=>{
    console.log(config);
    //固定写法，必须return config
    return config
})
Vue.prototype.$http = axios

// 引入qs解决axios，post请求 后台接收为空的情况
import qs from 'qs';
Vue.prototype.$qs = qs;


// 消息提示框  Message 组件名 不能变
// Vue.prototype.$message = Message



```

# 获取左侧菜单

页面在进入home的时候，需要发送请求，获取菜单数据

```html
<template>
  <el-container class="bg">
    <el-header>
       <span>AISHANG <span class="san"><el-button @click="toggle" type="text" style="color:white">三</el-button></span></span>
      <el-button type="info" plain>退出</el-button>
    </el-header>
    <el-container>
      <el-aside :width="collapse?'64px':'200px'">
        <el-menu
          default-active="2"
          class="el-menu-vertical-demo"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
          unique-opened
          :collapse="collapse"
          :collapse-transition="false"
        >
          <!-- index只接收字符串 -->
          <el-submenu v-for="item in menuList" :key="item.id" :index="item.id+''">
            <template slot="title">
              <i :class="iconList[item.id]"></i>
              <span>{{item.menuName}}</span>
            </template>
            <el-menu-item :index="items.id + ''" v-for="items in item.children" :key="items.id">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{items.menuName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      menuList: [],
      iconList: {
        "101": "el-icon-s-shop",
        "201": "el-icon-s-opportunity",
        "301": "el-icon-s-custom",
        "401": "el-icon-s-claim",
        "501": "el-icon-thumb"
      },
      collapse:false
    };
  },
  created() {
    this.getMenuList();
  },
  methods: {
    loginout() {
      sessionStorage.clear();
      this.$router.push("/login");
    },
    async getMenuList() {
      let { data: result } = await this.$http.get("/home/menu");
      if (result.status != 200) return this.$message.error("网络错误");
      this.menuList = result.data;
    },
    toggle(){
      this.collapse = !this.collapse
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.el-header {
  font-size: 26px;
  font-weight: bold;
  line-height: 60px;
  background-color: #545c64;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
  .el-button--info.is-plain {
    background-color: #545c64;
    color: white;

    &:hover {
      background-color: white;
      color: #919399;
    }
  }
  .san{
    font-size: 16px;
    position: absolute;
    top:-2px;
    margin-left: 10px;
  }
}
.el-aside {
  background-color: #545c64;
}
.el-main {
  background-color: #eaeef3;
}
.el-container {
  height: 100%;
}
.el-menu{
  border: 0;
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
    let password = req.body.password;
    //console.log(req.query);//"获取get提交参数" req.query.name
    if(username=="admin"&&password=="123"){
        res.send({msg:"登陆成功",status:200})
    }else{
        res.send({msg:"登陆失败",status:400});
    }
   
})

// 获取首页菜单
app.get(url+'/home/menu',(req,res)=>{
    res.send({
        data:[
            {
                id:101,
                menuName:"商品管理",
                path:null,
                children:[
                    {
                        id:104,
                        menuName:"商品列表",
                        path:null,
                        children:[]
                    }
                ]
            },
            {
                id:201,
                menuName:"权限管理",
                path:null,
                children:[
                    {
                        id:204,
                        menuName:"角色列表",
                        path:null,
                        children:[]
                    },
                    {
                        id:205,
                        menuName:"权限列表",
                        path:null,
                        children:[]
                    }
                ]
            },
            {
                id:301,
                menuName:"用户管理",
                path:null,
                children:[
                    {
                        id:304,
                        menuName:"用户列表",
                        path:null,
                        children:[]
                    }
                ]
            },
            {
                id:401,
                menuName:"订单管理",
                path:null,
                children:[
                    {
                        id:404,
                        menuName:"订单列表",
                        path:null,
                        children:[]
                    }
                ]
            },
            {
                id:501,
                menuName:"数据统计",
                path:null,
                children:[
                    {
                        id:504,
                        menuName:"订单统计",
                        path:null,
                        children:[]
                    },
                    {
                        id:505,
                        menuName:"用户统计",
                        path:null,
                        children:[]
                    },
                    {
                        id:506,
                        menuName:"商品统计",
                        path:null,
                        children:[]
                    }
                ]
            }
        ],
        status:200
    })
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

# 欢迎页面

```js
//router.js
import Vue from 'vue'
import Router from 'vue-router'
// import login from '@/components/login'
// import login from '../components/login'
// 写法都一样
import login from '../components/login.vue'
import home from '../components/home.vue'
import welcome from '../components/welcome.vue'

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
      component:home,
      redirect:'/welcome',
      children:[
       {
        path:'/welcome',
        component:welcome
       }
      ]
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

# 左侧菜单连接

| router | 是否使用 vue-router 的模式，启用该模式会在激活导航时以 index 作为 path 进行路由跳转 | boolean | —    | false |
| ------ | ------------------------------------------------------------ | ------- | ---- | ----- |
|        |                                                              |         |      |       |

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
    let password = req.body.password;
    //console.log(req.query);//"获取get提交参数" req.query.name
    if(username=="admin"&&password=="123"){
        res.send({msg:"登陆成功",status:200})
    }else{
        res.send({msg:"登陆失败",status:400});
    }
   
})

// 获取首页菜单
app.get(url+'/home/menu',(req,res)=>{
    res.send({
        data:[
            {
                id:101,
                menuName:"商品管理",
                path:null,
                children:[
                    {
                        id:104,
                        menuName:"商品列表",
                        path:"/product",
                        children:[]
                    }
                ]
            },
            {
                id:201,
                menuName:"权限管理",
                path:null,
                children:[
                    {
                        id:204,
                        menuName:"角色列表",
                        path:"/roles",
                        children:[]
                    },
                    {
                        id:205,
                        menuName:"权限列表",
                        path:"/authc",
                        children:[]
                    }
                ]
            },
            {
                id:301,
                menuName:"用户管理",
                path:null,
                children:[
                    {
                        id:304,
                        menuName:"用户列表",
                        path:"/user",
                        children:[]
                    }
                ]
            },
            {
                id:401,
                menuName:"订单管理",
                path:null,
                children:[
                    {
                        id:404,
                        menuName:"订单列表",
                        path:"/order",
                        children:[]
                    }
                ]
            },
            {
                id:501,
                menuName:"数据统计",
                path:null,
                children:[
                    {
                        id:504,
                        menuName:"订单统计",
                        path:"orderTotal",
                        children:[]
                    },
                    {
                        id:505,
                        menuName:"用户统计",
                        path:"userTotal",
                        children:[]
                    },
                    {
                        id:506,
                        menuName:"商品统计",
                        path:"productTotal",
                        children:[]
                    }
                ]
            }
        ],
        status:200
    })
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

```vue
 <el-menu
          default-active="2"
          class="el-menu-vertical-demo"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
          unique-opened
          :collapse="collapse"
          :collapse-transition="false"
          :router="true"
        >
          <!-- index只接收字符串 -->
          <el-submenu v-for="item in menuList" :key="item.id" :index="item.id+''">
            <template slot="title">
              <i :class="iconList[item.id]"></i>
              <span>{{item.menuName}}</span>
            </template>
            <el-menu-item :index="items.path + ''" v-for="items in item.children" :key="items.id">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{items.menuName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
```

# 用户列表

- 点击后高亮

```html
<template>
  <el-container class="bg">
    <el-header>
       <span>AISHANG <span class="san"><el-button @click="toggle" type="text" style="color:white">三</el-button></span></span>
      <el-button type="info" @click="loginout" plain>退出</el-button>
    </el-header>
    <el-container>
      <el-aside :width="collapse?'64px':'200px'">
        <el-menu
          :default-active="saveActive"
          class="el-menu-vertical-demo"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
          unique-opened
          :collapse="collapse"
          :collapse-transition="false"
          :router="true"
        >
          <!-- index只接收字符串 -->
          <el-submenu v-for="item in menuList" :key="item.id" :index="item.id+''">
            <template slot="title">
              <i :class="iconList[item.id]"></i>
              <span>{{item.menuName}}</span>
            </template>
            <el-menu-item :index="items.path + ''" @click="save(items.path + '')" v-for="items in item.children" :key="items.id">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{items.menuName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      menuList: [],
      iconList: {
        "101": "el-icon-s-shop",
        "201": "el-icon-s-opportunity",
        "301": "el-icon-s-custom",
        "401": "el-icon-s-claim",
        "501": "el-icon-thumb"
      },
      collapse:false,
      saveActive:""
    };
  },
  created() {
    this.getMenuList();
    this.saveActive = sessionStorage.getItem("path")
  },
  methods: {
    loginout() {
      sessionStorage.clear();
      this.$router.push("/login");
    },
    async getMenuList() {
      let { data: result } = await this.$http.get("/home/menu");
      if (result.status != 200) return this.$message.error("网络错误");
      this.menuList = result.data;
    },
    toggle(){
      this.collapse = !this.collapse
    },
    save(active){
      sessionStorage.setItem("path",active)
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.el-header {
  font-size: 26px;
  font-weight: bold;
  line-height: 60px;
  background-color: #545c64;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
  .el-button--info.is-plain {
    background-color: #545c64;
    color: white;

    &:hover {
      background-color: white;
      color: #919399;
    }
  }
  .san{
    font-size: 16px;
    position: absolute;
    top:-2px;
    margin-left: 10px;
  }
}
.el-aside {
  background-color: #545c64;
}

.el-container {
  height: 100%;
}
.el-menu{
  border: 0;
}
</style>

```

- 面包屑导航区

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">
        用户管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
  </div>
</template>

<script>
export default {};
</script>

<style>
</style>
```

- 卡片视图区

```html
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input placeholder="请输入内容" v-model="input3" class="input-with-select">
              <el-button slot="append" icon="el-icon-search"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary">添加用户</el-button>
          </el-col>
        </el-row>
      </div>
      <div v-for="o in 4" :key="o" class="text item">{{'列表内容 ' + o }}</div>
    </el-card>
```

- 列表数据

```js
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input placeholder="请输入内容" class="input-with-select">
              <el-button slot="append" icon="el-icon-search"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary">添加用户</el-button>
          </el-col>
        </el-row>
      </div>
      <div>
        <el-table :data="userList" style="width: 100%" stripe>
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.roles==1?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch v-model="scope.row.state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑"></el-table-column>
        </el-table>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userList: []
    };
  },
  created() {
    this.getUserList();
  },
  methods: {
    async getUserList() {
      const { data: ulist } = await this.$http.get("/user/userlist");
      this.userList = ulist.users;
      console.log(this.userList);
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
</style>
```



# 编辑按钮

```html
 <el-table-column prop="edit" label="编辑">
            <template slot-scope="scope">
              <el-button type="primary" icon="el-icon-edit"></el-button>
              <el-button type="danger" icon="el-icon-delete"></el-button>
              <el-tooltip :enterable="false" class="item" effect="dark" content="分配权限" placement="top">
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
```

# 分页

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input placeholder="请输入内容" class="input-with-select">
              <el-button slot="append" icon="el-icon-search"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary">添加用户</el-button>
          </el-col>
        </el-row>
      </div>
      <div>
<!--slice() 方法可从已有的数组中返回选定的元素。-->
        <el-table :data="userList.slice((currentPage-1)*PageSize,currentPage*PageSize)" style="width: 100%" stripe>
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.roles==1?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch v-model="scope.row.state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑" width="240">
            <template>
              <el-button type="primary" icon="el-icon-edit"></el-button>
              <el-button type="danger" icon="el-icon-delete"></el-button>
              <el-tooltip
                :enterable="false"
                class="item"
                effect="dark"
                content="分配权限"
                placement="top"
              >
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="pageSizes"
          :page-size="PageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userList: [],
      // 默认显示第几页
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      total: 1,
      // 个数选择器（可修改）
      pageSizes: [4, 8, 12, 16],
      // 默认每页显示的条数（可修改）
      PageSize: 4
    };
  },
  created() {
    this.getUserList();
    this.getTotal();
  },
  methods: {
    async getUserList() {
      const { data } = await this.$http.get("/user/userlist");

      this.userList = data;
      console.log(data);
    },
    async getTotal() {
      const { data } = await this.$http.get("/user/total");
      this.total = data[0].total;
    },
    // 分页
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
```

# 修改用户状态

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input placeholder="请输入内容" v-model="search" class="input-with-select">
              <el-button slot="append" icon="el-icon-search"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary">添加用户</el-button>
          </el-col>
        </el-row>
      </div>
      <div>
        <!-- slice() 方法可从已有的数组中返回选定的元素。 -->
        <el-table :data="userList.slice((currentPage-1)*PageSize,currentPage*PageSize)" style="width: 100%" stripe>
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.roles==1?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch @change="saveState(scope.row)" v-model="scope.row.state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑" width="240">
            <template>
              <el-button type="primary" icon="el-icon-edit"></el-button>
              <el-button type="danger" icon="el-icon-delete"></el-button>
              <el-tooltip
                :enterable="false"
                class="item"
                effect="dark"
                content="分配权限"
                placement="top"
              >
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="pageSizes"
          :page-size="PageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userList: [],
      // 默认显示第几页
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      total: 1,
      // 个数选择器（可修改）
      pageSizes: [4, 8, 12, 16],
      // 默认每页显示的条数（可修改）
      PageSize: 4,
      search:''
    };
  },
  created() {
    this.getUserList();
    this.getTotal();
  },
  methods: {
    async getUserList() {
      const { data } = await this.$http.get("/user/userlist");

      this.userList = data;
      console.log(data);
    },
    async getTotal() {
      const { data } = await this.$http.get("/user/total");
      this.total = data[0].total;
    },
    // 分页
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    },
   async saveState(user){
       const { data } = await this.$http.put(`/user/${user.id}/state/${user.state}`)
       console.log(data);
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
```

Server.js

```js
// 修改用户状态
app.put(url+'/user/:id/state/:state',(req,res)=>{
    console.log(req.params.state);
    let id = req.params.id
    let state = req.params.state
    console.log("id="+id);
    console.log("state="+state);
    
    
    let conn = mysql.getConnection();
    conn.connect()
    let sql = `update tb_users set state=${state} where id=${id}`
    conn.query(sql,function (err,result){
        if (err) {
            return "sql err";
        }
        res.send(result)
    })
    conn.end()
})
```

# 搜索

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input placeholder="请输入内容" v-model="search" class="input-with-select">
              <el-button slot="append" icon="el-icon-search" @click="getSearchName"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary">添加用户</el-button>
          </el-col>
        </el-row>
      </div>
      <div>
        <!-- slice() 方法可从已有的数组中返回选定的元素。 -->
        <el-table :data="userList.slice((currentPage-1)*PageSize,currentPage*PageSize)" style="width: 100%" stripe>
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.roles==1?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch @change="saveState(scope.row)" v-model="scope.row.state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑" width="240">
            <template>
              <el-button type="primary" icon="el-icon-edit"></el-button>
              <el-button type="danger" icon="el-icon-delete"></el-button>
              <el-tooltip
                :enterable="false"
                class="item"
                effect="dark"
                content="分配权限"
                placement="top"
              >
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="pageSizes"
          :page-size="PageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userList: [],
      // 默认显示第几页
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      total: 1,
      // 个数选择器（可修改）
      pageSizes: [4, 8, 12, 16],
      // 默认每页显示的条数（可修改）
      PageSize: 4,
      search:''
    };
  },
  created() {
    this.getUserList();
    this.getTotal();
  },
  methods: {
    async getUserList() {
      const { data } = await this.$http.get("/user/userlist",{
        params:this.search
      });

      this.userList = data;
      console.log(data);
    },
    async getTotal() {
      const { data } = await this.$http.get("/user/total",{
        params:this.search
      });
      this.total = data[0].total;
    },
    async getSearchName(){
      this.getTotal()
      this.getUserList()
    },
    // 分页
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    },
   async saveState(user){
       const { data } = await this.$http.put(`/user/${user.id}/state/${user.state}`)
       this.$message.success("修改状态成功")
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
```

```js
// 获取用户列表数据
app.get(url + '/user/userlist', (req, res) => {
    let search = req.query[0];  
    let sql  = "select * from tb_users where 1=1 ";
    if(search!=undefined){
       sql+=" and name like '%"+search+"%'";
    }
    var connection = mysql.getConnection()
    connection.connect();
    let arr=[]

    connection.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }
        result.forEach(element => {
            if (element.state == 0) {
                element.state = false;
                arr.push(element)
            } else {
                element.state = true;
                arr.push(element)
            }
        });
        res.send(arr)
    });

    connection.end();
});
//获取总记录数
app.get(url+'/user/total',(req, res)=>{
    var connection = mysql.getConnection()

    let search = req.query[0];  
    let sql  = "select count(*) total from tb_users where 1=1 ";
    if(search!=undefined){
       sql+=" and name like '%"+search+"%'";
    }
    connection.connect();
    connection.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }
        console.log(result);
        
        res.send(result)
    });

    connection.end();
})
```

# 添加用户

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input
              placeholder="请输入内容"
              @clear="getUserList"
              clearable
              v-model="search"
              class="input-with-select"
            >
              <el-button slot="append" icon="el-icon-search" @click="getSearchName"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary" @click="dialogVisible = true">添加用户</el-button>
            <el-dialog title="提示" :visible.sync="dialogVisible" width="50%">
              <el-form
                status-icon
                :model="addForm"
                :rules="addrules"
                ref="addForm"
                label-width="100px"
              >
                <el-form-item label="用户名" prop="name">
                  <el-input v-model="addForm.name"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                  <el-input type="password" v-model="addForm.password"></el-input>
                </el-form-item>
                <el-form-item label="邮箱" prop="email">
                  <el-input v-model="addForm.email"></el-input>
                </el-form-item>
                <el-form-item label="电话" prop="tel">
                  <el-input v-model="addForm.tel"></el-input>
                </el-form-item>
                <el-form-item label="权限" prop="rolse">
                  <el-select v-model="value" placeholder="请选择">
                    <el-option
                      v-for="item in options"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    ></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="是否启用" prop="state">
                  <el-switch v-model="state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
                </el-form-item>
              </el-form>
              <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
              </span>
            </el-dialog>
          </el-col>
        </el-row>
      </div>
      <div>
        <!-- slice() 方法可从已有的数组中返回选定的元素。 -->
        <el-table
          :data="userList.slice((currentPage-1)*PageSize,currentPage*PageSize)"
          style="width: 100%"
          stripe
        >
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.roles==1?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch
                @change="saveState(scope.row)"
                v-model="scope.row.state"
                active-color="#13ce66"
                inactive-color="#ff4949"
              ></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑" width="240">
            <template>
              <el-button type="primary" icon="el-icon-edit"></el-button>
              <el-button type="danger" icon="el-icon-delete"></el-button>
              <el-tooltip
                :enterable="false"
                class="item"
                effect="dark"
                content="分配权限"
                placement="top"
              >
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="pageSizes"
          :page-size="PageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    // 自定义验证规则
    var validateEmail = (rule, value, callback) => {
      var regEmail = /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/;
      if (regEmail.test(value)) {
        //  合法
        return callback();
      }
      callback(new Error("请输入正确的邮箱"));
    };
    var validateTel = (rule, value, callback) => {
      var regTel = /^(?:(?:\+|00)86)?1(?:(?:3[\d])|(?:4[5-7|9])|(?:5[0-3|5-9])|(?:6[5-7])|(?:7[0-8])|(?:8[\d])|(?:9[1|8|9]))\d{8}$/;
      if (regTel.test(value)) {
        //  合法
        return callback();
      }
      callback(new Error("请输入正确的手机号"));
    };
    return {
      userList: [],
      // 默认显示第几页
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      total: 1,
      // 个数选择器（可修改）
      pageSizes: [4, 8, 12, 16],
      // 默认每页显示的条数（可修改）
      PageSize: 4,
      search: "",
      dialogVisible: false,
      addForm: {
        name: ""
      },
      options: [
        {
          value: "0",
          label: "超级管理员"
        },
        {
          value: "1",
          label: "普通管理员"
        }
      ],
      value: "0",
      state: true,
      addrules: {
        name: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 3, max: 15, message: "长度在 3 到 15 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 3, max: 15, message: "长度在 3 到 15 个字符", trigger: "blur" }
        ],
        email: [
          { required: true, message: "请输入邮箱", trigger: "blur" },
          { validator: validateEmail, trigger: "blur" }
        ],
        tel: [
          { required: true, message: "请输入电话", trigger: "blur" },
          { validator: validateTel, trigger: "blur" }
        ]
      }
    };
  },
  created() {
    this.getUserList();
    this.getTotal();
  },
  methods: {
    async getUserList() {
      const { data } = await this.$http.get("/user/userlist", {
        params: this.search
      });

      this.userList = data;
      console.log(data);
    },
    async getTotal() {
      const { data } = await this.$http.get("/user/total", {
        params: this.search
      });
      this.total = data[0].total;
    },
    async getSearchName() {
      this.getTotal();
      this.getUserList();
    },
    // 分页
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    },
    async saveState(user) {
      const { data } = await this.$http.put(
        `/user/${user.id}/state/${user.state}`
      );
      this.$message.success("修改状态成功");
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
```

# 修改用户

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input
              placeholder="请输入内容"
              @clear="getUserList"
              clearable
              v-model="search"
              class="input-with-select"
            >
              <el-button slot="append" icon="el-icon-search" @click="getSearchName"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary" @click="dialogVisible = true">添加用户</el-button>
            <el-dialog title="添加用户" @close="addClose" :visible.sync="dialogVisible" width="50%">
              <el-form
                status-icon
                :model="addForm"
                :rules="addrules"
                ref="addForm"
                label-width="100px"
              >
                <el-form-item label="用户名" prop="name">
                  <el-input v-model="addForm.name"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                  <el-input type="password" v-model="addForm.password"></el-input>
                </el-form-item>
                <el-form-item label="邮箱" prop="email">
                  <el-input v-model="addForm.email"></el-input>
                </el-form-item>
                <el-form-item label="电话" prop="tel">
                  <el-input v-model="addForm.tel"></el-input>
                </el-form-item>
                <el-form-item label="权限" prop="rolse">
                  <el-select v-model="addForm.rolse" placeholder="请选择">
                    <el-option
                      v-for="item in options"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    ></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="是否启用" prop="state">
                  <el-switch
                    v-model="addForm.state"
                    active-color="#13ce66"
                    inactive-color="#ff4949"
                  ></el-switch>
                </el-form-item>
              </el-form>
              <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="addUser">确 定</el-button>
              </span>
            </el-dialog>
          </el-col>
        </el-row>
      </div>
      <div>
        <!-- slice() 方法可从已有的数组中返回选定的元素。 -->
        <el-table
          :data="userList.slice((currentPage-1)*PageSize,currentPage*PageSize)"
          style="width: 100%"
          stripe
        >
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.rolse==0?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch
                @change="saveState(scope.row)"
                v-model="scope.row.state"
                active-color="#13ce66"
                inactive-color="#ff4949"
              ></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑" width="240">
            <template slot-scope="scope">
              <el-button type="primary" icon="el-icon-edit" @click="updateUser(scope.row.id)"></el-button>
              <el-button type="danger" icon="el-icon-delete"></el-button>
              <el-tooltip
                :enterable="false"
                class="item"
                effect="dark"
                content="分配权限"
                placement="top"
              >
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 修改用户 -->
        <el-dialog title="修改用户"  @close="updateClose" :visible.sync="updateUserModel" width="50%">
          <el-form
            status-icon
            :model="updateForm"
            :rules="addrules"
            ref="updateForm"
            label-width="100px"
          >
            <el-form-item label="用户名" prop="name">
              <el-input :disabled="true" v-model="updateForm.name"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="password">
              <el-input type="password" v-model="updateForm.password"></el-input>
            </el-form-item>
            <el-form-item label="邮箱" prop="email">
              <el-input v-model="updateForm.email"></el-input>
            </el-form-item>
            <el-form-item label="电话" prop="tel">
              <el-input v-model="updateForm.tel"></el-input>
            </el-form-item>
            <el-form-item label="权限" prop="rolse">
              <el-select v-model="updateForm.rolse" placeholder="请选择">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="是否启用" prop="state">
              <el-switch v-model="updateForm.state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="updateUserModel = false">取 消</el-button>
            <el-button type="primary" @click="editUser">确 定</el-button>
          </span>
        </el-dialog>

        <!-- 分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="pageSizes"
          :page-size="PageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
import { async } from 'q';
export default {
  data() {
    // 自定义验证规则
    var validateEmail = (rule, value, callback) => {
      var regEmail = /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/;
      if (regEmail.test(value)) {
        //  合法
        return callback();
      }
      callback(new Error("请输入正确的邮箱"));
    };
    var validateTel = (rule, value, callback) => {
      var regTel = /^(?:(?:\+|00)86)?1(?:(?:3[\d])|(?:4[5-7|9])|(?:5[0-3|5-9])|(?:6[5-7])|(?:7[0-8])|(?:8[\d])|(?:9[1|8|9]))\d{8}$/;
      if (regTel.test(value)) {
        //  合法
        return callback();
      }
      callback(new Error("请输入正确的手机号"));
    };
    return {
      userList: [],
      // 默认显示第几页
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      total: 1,
      // 个数选择器（可修改）
      pageSizes: [4, 8, 12, 16],
      // 默认每页显示的条数（可修改）
      PageSize: 4,
      search: "",
      dialogVisible: false,
      addForm: {
        name: "",
        state: true,
        rolse: 0
      },
      options: [
        {
          value: 0,
          label: "超级管理员"
        },
        {
          value: 1,
          label: "普通管理员"
        }
      ],
      addrules: {
        name: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 2, max: 15, message: "长度在 3 到 15 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 3, max: 15, message: "长度在 3 到 15 个字符", trigger: "blur" }
        ],
        email: [
          { required: true, message: "请输入邮箱", trigger: "blur" },
          { validator: validateEmail, trigger: "blur" }
        ],
        tel: [
          { required: true, message: "请输入电话", trigger: "blur" },
          { validator: validateTel, trigger: "blur" }
        ]
      },
      // 修改用户对话框
      updateUserModel: false,
      updateForm: {},
    };
  },
  created() {
    this.getUserList();
    this.getTotal();
  },
  methods: {
    async getUserList() {
      const { data } = await this.$http.get("/user/userlist", {
        params: this.search
      });

      this.userList = data;
      console.log(data);
    },
    async getTotal() {
      const { data } = await this.$http.get("/user/total", {
        params: this.search
      });
      this.total = data[0].total;
    },
    async getSearchName() {
      this.getTotal();
      this.getUserList();
    },
    // 分页
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    },
    async saveState(user) {
      const { data } = await this.$http.put(
        `/user/${user.id}/state/${user.state}`
      );
      this.$message.success("修改状态成功");
    },
    // 点击取消，表单重置
    addClose() {
      this.$refs.addForm.resetFields();
    },
    // 添加user提交表单验证
    addUser() {
      this.$refs.addForm.validate(async vali => {
        if (!vali) return this.$message.error("请填写内容");
        const { data: res } = await this.$http.post(
          "/adduser",
          this.$qs.stringify(this.addForm)
        );
        if (res.status == 500) return this.$message.error("网络错误，添加失败");
        this.$message.success("添加成功");
        //添加后隐藏对话框
        this.dialogVisible = false;
        //重新获取数据
        this.getUserList();
      });
    },
    async updateUser(id) {
      this.updateUserModel = true;
      const { data: res } = await this.$http.get(`/updateUserInfo/${id}`);
      this.updateForm = res[0];
      console.log(res[0]);
      
    },
    //提交修改信息
    editUser(){
      this.$refs.updateForm.validate(async vali=>{
         if (!vali) return this.$message.error("请填写内容");
        const { data: res } = await this.$http.post(
          "/edituser",
          this.$qs.stringify(this.updateForm)
        );
        
        if (res.status == 500) return this.$message.error("网络错误，修改失败");
        this.$message.success("修改成功");
        //添加后隐藏对话框
        this.updateUserModel = false;
        //重新获取数据
        this.getUserList();
      })
    },
    updateClose(){
       this.$refs.updateForm.resetFields();
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
```

```js
const express = require('express');
const Mock = require('mockjs');
const mysql = require('./mysql');


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

// 登陆验证
app.post(url + "/login", (req, res) => {
    console.log(req.body.username);
    let username = req.body.username;
    let password = req.body.password;
    //console.log(req.query);//"获取get提交参数" req.query.name
    if (username == "admin" && password == "123") {
        res.send({ msg: "登陆成功", status: 200 })
    } else {
        res.send({ msg: "登陆失败", status: 400 });
    }

})

// 获取首页菜单
app.get(url + '/home/menu', (req, res) => {
    res.send({
        data: [
            {
                id: 101,
                menuName: "商品管理",
                path: null,
                children: [
                    {
                        id: 104,
                        menuName: "商品列表",
                        path: "/product",
                        children: []
                    }
                ]
            },
            {
                id: 201,
                menuName: "权限管理",
                path: null,
                children: [
                    {
                        id: 204,
                        menuName: "角色列表",
                        path: "/roles",
                        children: []
                    },
                    {
                        id: 205,
                        menuName: "权限列表",
                        path: "/authc",
                        children: []
                    }
                ]
            },
            {
                id: 301,
                menuName: "用户管理",
                path: null,
                children: [
                    {
                        id: 304,
                        menuName: "用户列表",
                        path: "/user",
                        children: []
                    }
                ]
            },
            {
                id: 401,
                menuName: "订单管理",
                path: null,
                children: [
                    {
                        id: 404,
                        menuName: "订单列表",
                        path: "/order",
                        children: []
                    }
                ]
            },
            {
                id: 501,
                menuName: "数据统计",
                path: null,
                children: [
                    {
                        id: 504,
                        menuName: "订单统计",
                        path: "orderTotal",
                        children: []
                    },
                    {
                        id: 505,
                        menuName: "用户统计",
                        path: "userTotal",
                        children: []
                    },
                    {
                        id: 506,
                        menuName: "商品统计",
                        path: "productTotal",
                        children: []
                    }
                ]
            }
        ],
        status: 200
    })
})
// 获取用户列表数据
app.get(url + '/user/userlist', (req, res) => {
    let search = req.query[0];
    let sql = "select * from tb_users where 1=1 ";
    if (search != undefined) {
        sql += " and name like '%" + search + "%'";
    }
    sql += " order by id desc"
    var connection = mysql.getConnection()
    connection.connect();
    let arr = []

    connection.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }
        result.forEach(element => {
            if (element.state == 0) {
                element.state = false;
                arr.push(element)
            } else {
                element.state = true;
                arr.push(element)
            }
        });
        res.send(arr)
    });

    connection.end();
});
//获取总记录数
app.get(url + '/user/total', (req, res) => {
    var connection = mysql.getConnection()

    let search = req.query[0];
    let sql = "select count(*) total from tb_users where 1=1 ";
    if (search != undefined) {
        sql += " and name like '%" + search + "%'";
    }
    connection.connect();
    connection.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }

        res.send(result)
    });

    connection.end();
})

// 修改用户状态
app.put(url + '/user/:id/state/:state', (req, res) => {
    console.log(req.params.state);
    let id = req.params.id
    let state = req.params.state


    let conn = mysql.getConnection();
    conn.connect()
    let sql = `update tb_users set state=${state} where id=${id}`
    conn.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }
        res.send(result)
    })
    conn.end()
})

// 添加用户
app.post(url + '/adduser', (req, res) => {
    const user = req.body;
    console.log(user);

    let conn = mysql.getConnection();
    conn.connect();
    let sql = `insert into tb_users values(default,"${user.name}","${user.password}","${user.email}",${user.tel},${user.rolse},${user.state})`
    conn.query(sql, function (err, result) {
        if (err) {
            res.send({ status: 500 })
        } else {
            res.send({
                msg: 200
            })
        }

    })
    conn.end()
})
// 修改用户，先根据id回显
app.get(url + '/updateUserInfo/:id', (req, res) => {
    console.log(req.params);
    const conn = mysql.getConnection();
    let sql = `select * from tb_users where id = ${req.params.id}`
    let arr = []
    conn.query(sql, (err, result) => {

        result.forEach(element => {
            if (element.state == 0) {
                element.state = false;
                arr.push(element)
            } else {
                element.state = true;
                arr.push(element)
            }
        });
        res.send(arr)
    })
})
app.post(url + '/edituser', (req, res) => {
    console.log(req.body);
    let user = req.body
    const conn = mysql.getConnection();
    let sql = `update tb_users set password = "${user.password}", email="${user.email}",tel = "${user.tel}",rolse=${user.rolse},state=${user.state} where id = ${user.id} `
    conn.query(sql, (err, result) => {
        if (err) {
            res.send({ status: 500 })
        } else {
            res.send({
                msg: 200
            })
        }
    })
    conn.end()
})
// 程序监听3000端口
app.listen(3000);
```

# 删除

Server.js

```js
const express = require('express');
const Mock = require('mockjs');
const mysql = require('./mysql');


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

// 登陆验证
app.post(url + "/login", (req, res) => {
    console.log(req.body.username);
    let username = req.body.username;
    let password = req.body.password;
    //console.log(req.query);//"获取get提交参数" req.query.name
    if (username == "admin" && password == "123") {
        res.send({ msg: "登陆成功", status: 200 })
    } else {
        res.send({ msg: "登陆失败", status: 400 });
    }

})

// 获取首页菜单
app.get(url + '/home/menu', (req, res) => {
    res.send({
        data: [
            {
                id: 101,
                menuName: "商品管理",
                path: null,
                children: [
                    {
                        id: 104,
                        menuName: "商品列表",
                        path: "/product",
                        children: []
                    }
                ]
            },
            {
                id: 201,
                menuName: "权限管理",
                path: null,
                children: [
                    {
                        id: 204,
                        menuName: "角色列表",
                        path: "/roles",
                        children: []
                    },
                    {
                        id: 205,
                        menuName: "权限列表",
                        path: "/authc",
                        children: []
                    }
                ]
            },
            {
                id: 301,
                menuName: "用户管理",
                path: null,
                children: [
                    {
                        id: 304,
                        menuName: "用户列表",
                        path: "/user",
                        children: []
                    }
                ]
            },
            {
                id: 401,
                menuName: "订单管理",
                path: null,
                children: [
                    {
                        id: 404,
                        menuName: "订单列表",
                        path: "/order",
                        children: []
                    }
                ]
            },
            {
                id: 501,
                menuName: "数据统计",
                path: null,
                children: [
                    {
                        id: 504,
                        menuName: "订单统计",
                        path: "orderTotal",
                        children: []
                    },
                    {
                        id: 505,
                        menuName: "用户统计",
                        path: "userTotal",
                        children: []
                    },
                    {
                        id: 506,
                        menuName: "商品统计",
                        path: "productTotal",
                        children: []
                    }
                ]
            }
        ],
        status: 200
    })
})
// 获取用户列表数据
app.get(url + '/user/userlist', (req, res) => {
    let search = req.query[0];
    let sql = "select * from tb_users where 1=1 ";
    if (search != undefined) {
        sql += " and name like '%" + search + "%'";
    }
    sql += " order by id desc"
    var connection = mysql.getConnection()
    connection.connect();
    let arr = []

    connection.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }
        result.forEach(element => {
            if (element.state == 0) {
                element.state = false;
                arr.push(element)
            } else {
                element.state = true;
                arr.push(element)
            }
        });
        res.send(arr)
    });

    connection.end();
});
//获取总记录数
app.get(url + '/user/total', (req, res) => {
    var connection = mysql.getConnection()

    let search = req.query[0];
    let sql = "select count(*) total from tb_users where 1=1 ";
    if (search != undefined) {
        sql += " and name like '%" + search + "%'";
    }
    connection.connect();
    connection.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }

        res.send(result)
    });

    connection.end();
})

// 修改用户状态
app.put(url + '/user/:id/state/:state', (req, res) => {
    console.log(req.params.state);
    let id = req.params.id
    let state = req.params.state


    let conn = mysql.getConnection();
    conn.connect()
    let sql = `update tb_users set state=${state} where id=${id}`
    conn.query(sql, function (err, result) {
        if (err) {
            return "sql err";
        }
        res.send(result)
    })
    conn.end()
})

// 添加用户
app.post(url + '/adduser', (req, res) => {
    const user = req.body;
    console.log(user);

    let conn = mysql.getConnection();
    conn.connect();
    let sql = `insert into tb_users values(default,"${user.name}","${user.password}","${user.email}",${user.tel},${user.rolse},${user.state})`
    conn.query(sql, function (err, result) {
        if (err) {
            res.send({ status: 500 })
        } else {
            res.send({
                msg: 200
            })
        }

    })
    conn.end()
})
// 修改用户，先根据id回显
app.get(url + '/updateUserInfo/:id', (req, res) => {
    console.log(req.params);
    const conn = mysql.getConnection();
    let sql = `select * from tb_users where id = ${req.params.id}`
    let arr = []
    conn.query(sql, (err, result) => {

        result.forEach(element => {
            if (element.state == 0) {
                element.state = false;
                arr.push(element)
            } else {
                element.state = true;
                arr.push(element)
            }
        });
        res.send(arr)
    })
})
app.post(url + '/edituser', (req, res) => {
    console.log(req.body);
    let user = req.body
    const conn = mysql.getConnection();
    let sql = `update tb_users set password = "${user.password}", email="${user.email}",tel = "${user.tel}",rolse=${user.rolse},state=${user.state} where id = ${user.id} `
    conn.query(sql, (err, result) => {
        if (err) {
            res.send({ status: 500 })
        } else {
            res.send({
                msg: 200
            })
        }
    })
    conn.end()
})

// 删除用户
app.get(url+"/del/:id",(req,res)=>{
    let sql = `delete from tb_users where id = ${req.params.id}`
    const conn = mysql.getConnection()
    conn.query(sql,(err,result)=>{
        if(err){
            res.send({status:500})
        }else{
            res.send({msg:200})
        }
    })
    conn.end()
})
// 程序监听3000端口
app.listen(3000);
```

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/user' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-input
              placeholder="请输入内容"
              @clear="getSearchName"
              clearable
              v-model="search"
              class="input-with-select"
            >
              <el-button slot="append" icon="el-icon-search" @click="getSearchName"></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary" @click="dialogVisible = true">添加用户</el-button>
            <el-dialog title="添加用户" @close="addClose" :visible.sync="dialogVisible" width="50%">
              <el-form
                status-icon
                :model="addForm"
                :rules="addrules"
                ref="addForm"
                label-width="100px"
              >
                <el-form-item label="用户名" prop="name">
                  <el-input v-model="addForm.name"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                  <el-input type="password" v-model="addForm.password"></el-input>
                </el-form-item>
                <el-form-item label="邮箱" prop="email">
                  <el-input v-model="addForm.email"></el-input>
                </el-form-item>
                <el-form-item label="电话" prop="tel">
                  <el-input v-model="addForm.tel"></el-input>
                </el-form-item>
                <el-form-item label="权限" prop="rolse">
                  <el-select v-model="addForm.rolse" placeholder="请选择">
                    <el-option
                      v-for="item in options"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    ></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="是否启用" prop="state">
                  <el-switch
                    v-model="addForm.state"
                    active-color="#13ce66"
                    inactive-color="#ff4949"
                  ></el-switch>
                </el-form-item>
              </el-form>
              <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="addUser">确 定</el-button>
              </span>
            </el-dialog>
          </el-col>
        </el-row>
      </div>
      <div>
        <!-- slice() 方法可从已有的数组中返回选定的元素。 -->
        <el-table
          :data="userList.slice((currentPage-1)*PageSize,currentPage*PageSize)"
          style="width: 100%"
          stripe
        >
          <el-table-column type="index" width="80"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="email" label="邮箱" width="300"></el-table-column>
          <el-table-column prop="tel" label="电话"></el-table-column>
          <el-table-column prop="roles" label="角色">
            <template slot-scope="scope">{{scope.row.rolse==0?'超级管理员':'普通管理员'}}</template>
          </el-table-column>
          <el-table-column prop="state" label="状态">
            <template slot-scope="scope">
              <el-switch
                @change="saveState(scope.row)"
                v-model="scope.row.state"
                active-color="#13ce66"
                inactive-color="#ff4949"
              ></el-switch>
            </template>
          </el-table-column>

          <el-table-column prop="edit" label="编辑" width="240">
            <template slot-scope="scope">
              <el-button type="primary" icon="el-icon-edit" @click="updateUser(scope.row.id)"></el-button>
              <el-button type="danger" icon="el-icon-delete" @click="del(scope.row.id)"></el-button>
              <el-tooltip
                :enterable="false"
                class="item"
                effect="dark"
                content="分配权限"
                placement="top"
              >
                <el-button type="warning" icon="el-icon-setting"></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>

        <!-- 修改用户 -->
        <el-dialog title="修改用户" @close="updateClose" :visible.sync="updateUserModel" width="50%">
          <el-form
            status-icon
            :model="updateForm"
            :rules="addrules"
            ref="updateForm"
            label-width="100px"
          >
            <el-form-item label="用户名" prop="name">
              <el-input :disabled="true" v-model="updateForm.name"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="password">
              <el-input type="password" v-model="updateForm.password"></el-input>
            </el-form-item>
            <el-form-item label="邮箱" prop="email">
              <el-input v-model="updateForm.email"></el-input>
            </el-form-item>
            <el-form-item label="电话" prop="tel">
              <el-input v-model="updateForm.tel"></el-input>
            </el-form-item>
            <el-form-item label="权限" prop="rolse">
              <el-select v-model="updateForm.rolse" placeholder="请选择">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="是否启用" prop="state">
              <el-switch v-model="updateForm.state" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="updateUserModel = false">取 消</el-button>
            <el-button type="primary" @click="editUser">确 定</el-button>
          </span>
        </el-dialog>

        <!-- 分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="pageSizes"
          :page-size="PageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
import { async } from "q";
export default {
  data() {
    // 自定义验证规则
    var validateEmail = (rule, value, callback) => {
      var regEmail = /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/;
      if (regEmail.test(value)) {
        //  合法
        return callback();
      }
      callback(new Error("请输入正确的邮箱"));
    };
    var validateTel = (rule, value, callback) => {
      var regTel = /^(?:(?:\+|00)86)?1(?:(?:3[\d])|(?:4[5-7|9])|(?:5[0-3|5-9])|(?:6[5-7])|(?:7[0-8])|(?:8[\d])|(?:9[1|8|9]))\d{8}$/;
      if (regTel.test(value)) {
        //  合法
        return callback();
      }
      callback(new Error("请输入正确的手机号"));
    };
    return {
      userList: [],
      // 默认显示第几页
      currentPage: 1,
      // 总条数，根据接口获取数据长度(注意：这里不能为空)
      total: 1,
      // 个数选择器（可修改）
      pageSizes: [5, 10, 15, 20],
      // 默认每页显示的条数（可修改）
      PageSize: 5,
      search: "",
      dialogVisible: false,
      addForm: {
        name: "",
        state: true,
        rolse: 0
      },
      options: [
        {
          value: 0,
          label: "超级管理员"
        },
        {
          value: 1,
          label: "普通管理员"
        }
      ],
      addrules: {
        name: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 2, max: 15, message: "长度在 3 到 15 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 3, max: 15, message: "长度在 3 到 15 个字符", trigger: "blur" }
        ],
        email: [
          { required: true, message: "请输入邮箱", trigger: "blur" },
          { validator: validateEmail, trigger: "blur" }
        ],
        tel: [
          { required: true, message: "请输入电话", trigger: "blur" },
          { validator: validateTel, trigger: "blur" }
        ]
      },
      // 修改用户对话框
      updateUserModel: false,
      updateForm: {}
    };
  },
  created() {
    this.getUserList();
    this.getTotal();
  },
  methods: {
    async getUserList() {
      const { data } = await this.$http.get("/user/userlist", {
        params: this.search
      });

      this.userList = data;
      console.log(data);
    },
    async getTotal() {
      const { data } = await this.$http.get("/user/total", {
        params: this.search
      });
      this.total = data[0].total;
    },
    async getSearchName() {
      this.getTotal();
      this.getUserList();
    },
    // 分页
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.PageSize = val;
      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage = 1;
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      this.currentPage = val;
    },
    async saveState(user) {
      const { data } = await this.$http.put(
        `/user/${user.id}/state/${user.state}`
      );
      this.$message.success("修改状态成功");
    },
    // 点击取消，表单重置
    addClose() {
      this.$refs.addForm.resetFields();
    },
    // 添加user提交表单验证
    addUser() {
      this.$refs.addForm.validate(async vali => {
        if (!vali) return this.$message.error("请填写内容");
        const { data: res } = await this.$http.post(
          "/adduser",
          this.$qs.stringify(this.addForm)
        );
        if (res.status == 500) return this.$message.error("网络错误，添加失败");
        this.$message.success("添加成功");
        //添加后隐藏对话框
        this.dialogVisible = false;
        //重新获取数据
        this.getUserList();
      });
    },
    async updateUser(id) {
      this.updateUserModel = true;
      const { data: res } = await this.$http.get(`/updateUserInfo/${id}`);
      this.updateForm = res[0];
      console.log(res[0]);
    },
    //提交修改信息
    editUser() {
      this.$refs.updateForm.validate(async vali => {
        if (!vali) return this.$message.error("请填写内容");
        const { data: res } = await this.$http.post(
          "/edituser",
          this.$qs.stringify(this.updateForm)
        );

        if (res.status == 500) return this.$message.error("网络错误，修改失败");
        this.$message.success("修改成功");
        //添加后隐藏对话框
        this.updateUserModel = false;
        //重新获取数据
        this.getUserList();
      });
    },
    updateClose() {
      this.$refs.updateForm.resetFields();
    },
    // 删除用户  弹出删除提示框
    async del(id) {
      const confirmResult = await this.$confirm("是否删除该用户?", "确认信息", {
        distinguishCancelAndClose: true,
        confirmButtonText: "确认",
        cancelButtonText: "取消",
        type: "warning"
      }).catch(err => err);
      console.log(confirmResult);
      if (confirmResult !== "confirm") {
        return this.$message.info("已取消删除");
      }
      const { data: res } = await this.$http.get(`/del/${id}`);
      if (res.status == 500) {
        return this.$message.error("网络错误，删除失败");
      }
      this.$message.success("删除成功");
      this.getUserList();
      this.getTotal();
    }
  }
};
</script>

<style lang="less" scoped>
.el-breadcrumb {
  margin-bottom: 20px;
}
.el-pagination {
  margin-top: 20px;
}
</style>
```

