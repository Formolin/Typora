# 权限列表

```html
<template>
  <div>
    <!-- mianbaoxie -->
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/authc' }">权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="box-card">
      <el-tree :data="menu" :props="defaultProps" ></el-tree>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      menu: [],
      defaultProps: {
        children: "children",
        label: "pname"
      }
    };
  },
  created() {
    this.getAllMenu();
  },
  methods: {
    async getAllMenu() {
      const { data: res } = await this.$http.get("/getMenu");
      console.log(res);
      this.menu = res
    }
  }
};
</script>

<style lang="less" spcopd>
.box-card {
  margin-top: 20px;
}
</style>

```

Server.js

```js
app.get(url + "/getMenu", (req, res) => {
    var conn = mysql.getConnection()
    let sql = `select * from tb_permission`

    conn.query(sql, (err, result) => {
        // 数组[ RowDataPacket { id: 1, pname: '商品管理', parentId: 0 },]

        let str = JSON.stringify(result);//把results对象转为字符串，去掉RowDataPacket
        let allMenu = JSON.parse(str);//把results字符串转为json对象
        var rootMenu = []


        allMenu.forEach(nav => {
            // 父节点是0，为根节点
            if (nav.parentId == 0) {
                var menu = {
                    id: 0,
                    pname: '',
                    parentId: 0,
                    path: '',
                    children: []
                }
                menu.id = nav.id
                menu.pname = nav.pname
                menu.parentId = nav.parentId
                rootMenu.push(menu)

            }
        });
        /*
        
        [ { id: 1, pname: '商品管理', parentId: 0, path: '', children: [] },
    { id: 2, pname: '订单管理', parentId: 0, path: '', children: [] },
  { id: 3, pname: '权限管理', parentId: 0, path: '', children: [] },
  { id: 4, pname: '用户管理', parentId: 0, path: '', children: [] },
  { id: 5, pname: '数据统计', parentId: 0, path: '', children: [] } ]
        
        
        */
        //为根菜单设置子菜单，getClild是递归调用的
        rootMenu.forEach(nav => {
            var childList = getChildList(nav.id, allMenu)
            nav.children=childList
        });
        console.log("----------------");
        console.log(rootMenu);
        console.log("----------------");
        res.send(rootMenu)

    })
    conn.end()
})



// 获取子菜单方法
function getChildList(id, allMenu) {

    var childList = [];
    allMenu.forEach(nav => {
        // 遍历所有节点，将所有菜单的父id与传过来的根节点的id比较
        //相等说明：为该根节点的子节点。
        if (nav.parentId == id) {
            var menu = {
                id: 0,
                pname: '',
                parentId: 0,
                path: '',
                children: []
            }
            menu.id = nav.id
            menu.pname = nav.pname
            menu.parentId = nav.parentId
            childList.push(menu)
        }
    })

    /*
        [ { id: 101, pname: '商品列表', parentId: 1, path: '', children: [] },
        { id: 110, pname: '分类参数', parentId: 1, path: '', children: [] },
        { id: 120, pname: '商品分类', parentId: 1, path: '', children: [] } ]
    */




    //递归
    childList.forEach(nav => {
        nav.children=getChildList(nav.id, allMenu)
    })

    //如果节点下没有子节点，返回一个空 递归退出
    if (childList.length == 0) {
        return new Array();
    }
    return childList;

}
```

# 角色列表

```html
<template>
  <div>
    <el-breadcrumb separator=">">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/authc' }">权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="box-card">
      <el-row>
        <el-col>
          <el-button type="primary">添加角色</el-button>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
export default {};
</script>

<style lang="less" spcopd>
.box-card {
  margin-top: 20px;
}
</style>
```

