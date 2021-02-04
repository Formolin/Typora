# 项目目录结构

```md
#### 项目整体文件说明
- `config` 配置文件目录
  - `default.json` 默认配置文件（其中包含数据库配置，jwt(JSON WEB TOKEN)配置）
- `dao` 数据访问层，存放对数据库的增删改查操作
  - `DAO.js` 提供的公共访问数据库的方法
- `models` 存放具体数据库 ORM 模型文件
- `modules` 当前项目模块
  - `authorization.js` API权限验证模块
  - `database.js` 数据库模块（数据库加载基于 nodejs-orm2 库加载）
  - `passport.js` 基于 passport 模块的登录搭建
  - `resextra.js` API 统一返回结果接口
- `node_modules` 项目依赖的第三方模块
- `routes` 统一路由
  - `api` 提供 api 接口
  - `mapp` 提供移动APP界面
  - `mweb` 提供移动web站点
- `services` 服务层，业务逻辑代码在这一层编写，通过不同的接口获取的数据转换成统一的前端所需要的数据
- `app.js` 主项目入口文件
- `package.json` 项目配置文件
```

![image-20200322102649004](电商后台api项目/image-20200322102649004.png)



# 什么是JWT

> Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（[(RFC 7519](https://link.jianshu.com?t=https://tools.ietf.org/html/rfc7519)).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。

### 传统的session认证

我们知道，http协议本身是一种无状态的协议，而这就意味着如果用户向我们的应用提供了用户名和密码来进行用户认证，那么下一次请求时，用户还要再一次进行用户认证才行，因为根据http协议，我们并不能知道是哪个用户发出的请求，所以为了让我们的应用能识别是哪个用户发出的请求，我们只能在服务器存储一份用户登录的信息，这份登录信息会在响应时传递给浏览器，告诉其保存为cookie,以便下次请求时发送给我们的应用，这样我们的应用就能识别请求来自哪个用户了,这就是传统的基于session认证。

但是这种基于session的认证使应用本身很难得到扩展，随着不同客户端用户的增加，独立的服务器已无法承载更多的用户，而这时候基于session认证应用的问题就会暴露出来.

#### 基于session认证所显露的问题

**Session**: 每个用户经过我们的应用认证之后，我们的应用都要在服务端做一次记录，以方便用户下次请求的鉴别，通常而言session都是保存在内存中，而随着认证用户的增多，服务端的开销会明显增大。

**扩展性**: 用户认证之后，服务端做认证记录，如果认证的记录被保存在内存中的话，这意味着用户下次请求还必须要请求在这台服务器上,这样才能拿到授权的资源，这样在分布式的应用上，相应的限制了负载均衡器的能力。这也意味着限制了应用的扩展能力。

**CSRF**: 因为是基于cookie来进行用户识别的, cookie如果被截获，用户就会很容易受到跨站请求伪造的攻击。

## 基于token的鉴权机制

基于token的鉴权机制类似于http协议也是无状态的，它不需要在服务端去保留用户的认证信息或者会话信息。这就意味着基于token认证机制的应用不需要去考虑用户在哪一台服务器登录了，这就为应用的扩展提供了便利。

流程上是这样的：

- 用户使用用户名密码来请求服务器
- 服务器进行验证用户的信息
- 服务器通过验证发送给用户一个token
- 客户端存储token，并在每次请求时附送上这个token值
- 服务端验证token值，并返回数据

这个token必须要在每次请求时传递给服务端，它应该保存在请求头里， 另外，服务端要支持`CORS(跨来源资源共享)`策略，一般我们在服务端这么做就可以了`Access-Control-Allow-Origin: *`。

那么我们现在回到JWT的主题上。

## JWT长什么样？

JWT是由三段信息构成的，将这三段信息文本用`.`链接一起就构成了Jwt字符串。就像这样:

```css
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

## JWT的构成

第一部分我们称它为头部（header),第二部分我们称其为载荷（payload, 类似于飞机上承载的物品)，第三部分是签证（signature).

### header

jwt的头部承载两部分信息：

- 声明类型，这里是jwt
- 声明加密的算法 通常直接使用 HMAC SHA256

完整的头部就像下面这样的JSON：

```bash
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```

然后将头部进行base64加密（该加密是可以对称解密的),构成了第一部分.

```undefined
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

### playload

载荷就是存放有效信息的地方。这个名字像是特指飞机上承载的货品，这些有效信息包含三个部分

- 标准中注册的声明
- 公共的声明
- 私有的声明

**标准中注册的声明** (建议但不强制使用) ：

-  **iss**: jwt签发者
-  **sub**: jwt所面向的用户
-  **aud**: 接收jwt的一方
-  **exp**: jwt的过期时间，这个过期时间必须要大于签发时间
-  **nbf**: 定义在什么时间之前，该jwt都是不可用的.
-  **iat**: jwt的签发时间
-  **jti**: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。

**公共的声明** ：
 公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息.但不建议添加敏感信息，因为该部分在客户端可解密.

**私有的声明** ：
 私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为base64是对称解密的，意味着该部分信息可以归类为明文信息。

定义一个payload:

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

然后将其进行base64加密，得到Jwt的第二部分。

```undefined
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9
```

### signature

jwt的第三部分是一个签证信息，这个签证信息由三部分组成：

- header (base64后的)
- payload (base64后的)
- secret

这个部分需要base64加密后的header和base64加密后的payload使用`.`连接组成的字符串，然后通过header中声明的加密方式进行加盐`secret`组合加密，然后就构成了jwt的第三部分。

```csharp
// javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

将这三部分用`.`连接成一个完整的字符串,构成了最终的jwt:

```css
  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

**注意：secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。**

### 如何应用

一般是在请求头里加入`Authorization`，并加上`Bearer`标注：

```bash
fetch('api/user/1', {
  headers: {
    'Authorization': 'Bearer ' + token
  }
})
```

服务端会验证token，如果验证通过就会返回相应的资源。整个流程就是这样的:

![image-20200322102559657](电商后台api项目/image-20200322102559657.png)

## 总结

### 优点

- 因为json的通用性，所以JWT是可以进行跨语言支持的，像JAVA,JavaScript,NodeJS,PHP等很多语言都可以使用。
- 因为有了payload部分，所以JWT可以在自身存储一些其他业务逻辑所必要的非敏感信息。
- 便于传输，jwt的构成非常简单，字节占用很小，所以它是非常便于传输的。
- 它不需要在服务端保存会话信息, 所以它易于应用的扩展

### 安全相关

- 不应该在jwt的payload部分存放敏感信息，因为该部分是客户端可解密的部分。
- 保护好secret私钥，该私钥非常重要。
- 如果可以，请使用https协议

# config配置文件目录

### default.json

```json
{
	"config_name" : "develop",
	"jwt_config" : {
		"secretKey":"aishang",
		"expiresIn":86400
	},
	"upload_config":{
		"baseURL":"http://127.0.0.1:8888",
		"upload_ueditor":"uploads/ueditor",
		"simple_upload_redirect":"http://127.0.0.1/reload"
	},
	"db_config" : {
		"protocol" : "mysql",
		"host" : "127.0.0.1",
		"database" : "vueshop",
		"user" : "root",
		"password" : "password",
		"port" : 3306
	}
}
```

### 上传文件配置

```json
/* 前后端通信相关的配置,注释只允许使用多行方式 */
module.exports = {
    /* 上传图片配置项 */
    "imageActionName": "uploadimage", /* 执行上传图片的action名称 */
    "imageFieldName": "upfile", /* 提交的图片表单名称 */
    "imageMaxSize": 2048000, /* 上传大小限制，单位B */
    "imageAllowFiles": [".png", ".jpg", ".jpeg", ".gif", ".bmp"], /* 上传图片格式显示 */
    "imageCompressEnable": true, /* 是否压缩图片,默认是true */
    "imageCompressBorder": 1600, /* 图片压缩最长边限制 */
    "imageInsertAlign": "none", /* 插入的图片浮动方式 */
    "imageUrlPrefix": "", /* 图片访问路径前缀 */
    "imagePathFormat": "/ueditor/ue/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
                                /* {filename} 会替换成原文件名,配置这项需要注意中文乱码问题 */
                                /* {rand:6} 会替换成随机数,后面的数字是随机数的位数 */
                                /* {time} 会替换成时间戳 */
                                /* {yyyy} 会替换成四位年份 */
                                /* {yy} 会替换成两位年份 */
                                /* {mm} 会替换成两位月份 */
                                /* {dd} 会替换成两位日期 */
                                /* {hh} 会替换成两位小时 */
                                /* {ii} 会替换成两位分钟 */
                                /* {ss} 会替换成两位秒 */
                                /* 非法字符 \ : * ? " < > | */
                                /* 具请体看线上文档: fex.baidu.com/ueditor/#use-format_upload_filename */

    /* 涂鸦图片上传配置项 */
    "scrawlActionName": "uploadscrawl", /* 执行上传涂鸦的action名称 */
    "scrawlFieldName": "upfile", /* 提交的图片表单名称 */
    "scrawlPathFormat": "/ueditor/ue/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
    "scrawlMaxSize": 2048000, /* 上传大小限制，单位B */
    "scrawlUrlPrefix": "", /* 图片访问路径前缀 */
    "scrawlInsertAlign": "none",

    /* 截图工具上传 */
    "snapscreenActionName": "uploadimage", /* 执行上传截图的action名称 */
    "snapscreenPathFormat": "/ueditor/ue/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
    "snapscreenUrlPrefix": "", /* 图片访问路径前缀 */
    "snapscreenInsertAlign": "none", /* 插入的图片浮动方式 */

    /* 抓取远程图片配置 */
    "catcherLocalDomain": ["www.escook.cn"],
    "catcherActionName": "catchimage", /* 执行抓取远程图片的action名称 */
    "catcherFieldName": "source", /* 提交的图片列表表单名称 */
    "catcherPathFormat": "/ueditor/ue/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
    "catcherUrlPrefix": "", /* 图片访问路径前缀 */
    "catcherMaxSize": 2048000, /* 上传大小限制，单位B */
    "catcherAllowFiles": [".png", ".jpg", ".jpeg", ".gif", ".bmp"], /* 抓取图片格式显示 */

    /* 上传视频配置 */
    "videoActionName": "uploadvideo", /* 执行上传视频的action名称 */
    "videoFieldName": "upfile", /* 提交的视频表单名称 */
    "videoPathFormat": "/ueditor/ue/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
    "videoUrlPrefix": "", /* 视频访问路径前缀 */
    "videoMaxSize": 102400000, /* 上传大小限制，单位B，默认100MB */
    "videoAllowFiles": [
        ".flv", ".swf", ".mkv", ".avi", ".rm", ".rmvb", ".mpeg", ".mpg",
        ".ogg", ".ogv", ".mov", ".wmv", ".mp4", ".webm", ".mp3", ".wav", ".mid"], /* 上传视频格式显示 */

    /* 上传文件配置 */
    "fileActionName": "uploadfile", /* controller里,执行上传视频的action名称 */
    "fileFieldName": "upfile", /* 提交的文件表单名称 */
    "filePathFormat": "/ueditor/ue/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
    "fileUrlPrefix": "", /* 文件访问路径前缀 */
    "fileMaxSize": 51200000, /* 上传大小限制，单位B，默认50MB */
    "fileAllowFiles": [
        ".png", ".jpg", ".jpeg", ".gif", ".bmp",
        ".flv", ".swf", ".mkv", ".avi", ".rm", ".rmvb", ".mpeg", ".mpg",
        ".ogg", ".ogv", ".mov", ".wmv", ".mp4", ".webm", ".mp3", ".wav", ".mid",
        ".rar", ".zip", ".tar", ".gz", ".7z", ".bz2", ".cab", ".iso",
        ".doc", ".docx", ".xls", ".xlsx", ".ppt", ".pptx", ".pdf", ".txt", ".md", ".xml"
    ], /* 上传文件格式显示 */

    /* 列出指定目录下的图片 */
    "imageManagerActionName": "listimage", /* 执行图片管理的action名称 */
    "imageManagerListPath": "/ueditor/ue/", /* 指定要列出图片的目录 */
    "imageManagerListSize": 20, /* 每次列出文件数量 */
    "imageManagerUrlPrefix": "", /* 图片访问路径前缀 */
    "imageManagerInsertAlign": "none", /* 插入的图片浮动方式 */
    "imageManagerAllowFiles": [".png", ".jpg", ".jpeg", ".gif", ".bmp"], /* 列出的文件类型 */

    /* 列出指定目录下的文件 */
    "fileManagerActionName": "listfile", /* 执行文件管理的action名称 */
    "fileManagerListPath": "/ueditor/ue/", /* 指定要列出文件的目录 */
    "fileManagerUrlPrefix": "", /* 文件访问路径前缀 */
    "fileManagerListSize": 20, /* 每次列出文件数量 */
    "fileManagerAllowFiles": [
        ".png", ".jpg", ".jpeg", ".gif", ".bmp",
        ".flv", ".swf", ".mkv", ".avi", ".rm", ".rmvb", ".mpeg", ".mpg",
        ".ogg", ".ogv", ".mov", ".wmv", ".mp4", ".webm", ".mp3", ".wav", ".mid",
        ".rar", ".zip", ".tar", ".gz", ".7z", ".bz2", ".cab", ".iso",
        ".doc", ".docx", ".xls", ".xlsx", ".ppt", ".pptx", ".pdf", ".txt", ".md", ".xml"
    ] /* 列出的文件类型 */

}
```

# model

使用node-orm

```js
//定义表结构Model
module.exports = function (db, cb) {
    //define方法的第一个参数为表名
    db.define('xxx', {
        innerid: { type: 'serial', key: true } , //主键
        name: String,
        identifying: String,
        purpose_code: Number,
        org_id: String,
        position_x: Number,
        position_y: Number,
        isenabled: Number,
        isdeleted: Number
    });
    return cb();
}
```

```js
module.exports = function(db,callback){
	// 商品参数
	db.define("AttributeModel",{
		attr_id : {type: 'serial', key: true},
		attr_name : String,
		cat_id : Number,
		attr_sel : ["only", "many"],	// only:输入框(唯一)  many:后台下拉列表/前台单选框
		attr_write: ["manual","list"],	// manual:手工录入  list:从列表选择
		attr_vals: String,
		delete_time : Number
	},{//对应的数据库表名
		table : "sp_attribute"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("CategoryModel",{
		cat_id : {type: 'serial', key: true},
		cat_name : String,
		cat_pid : Number,//分类父id
		cat_level : Number,//分类层级 0: 顶级 1:二级 2:三级
		cat_deleted: Boolean,//是否删除 1为删除
		cat_icon:String,//分类图标
		cat_src:String,//分类列表页面
	},{
		table : "sp_category"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("GoodAttributeModel",{
		id : {type: 'serial', key: true},
		goods_id : Number,//商品id
		attr_id : Number,//属性id
		attr_value : String,//商品对应属性的值
		add_price : Number//该属性需要额外增加的价钱
	},{
		table : "sp_goods_attr"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	// 用户模型
	db.define("GoodModel",{
		goods_id : {type: 'serial', key: true},
		cat_id : Number,//类型id
		goods_name : String,//商品名称
		goods_price : Number,//商品价格
		goods_number : Number,//商品数量
		goods_weight : Number,//商品重量
		goods_introduce : String,//商品详情介绍
		goods_big_logo : String,//图片logo大图
		goods_small_logo : String,//图片logo小图
		goods_state : Number,	// 0：未审核 1: 审核中 2: 已审核
		is_del : ['0','1'],	// 0: 正常 , 1: 删除
		add_time : Number,//添加商品时间
		upd_time : Number,//修改商品时间
		delete_time : Number,//软删除标志字段
		hot_mumber : Number,//热卖数量
		is_promote : Boolean,//是否促销
		cat_one_id : Number,//一级分类id
		cat_two_id : Number,//二级分类id
		cat_three_id : Number//三级分类id

	},{
		table : "sp_goods",
		methods: {
		getGoodsCat: function () {
			return this.cat_one_id + ',' + this.cat_two_id + ',' + this.cat_three_id;
		}
	}
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("GoodPicModel",{
		pics_id : {type: 'serial', key: true},
		goods_id : Number,//商品id
		pics_big : String,//相册大图800*800
		pics_mid : String,//相册中图350*350
		pics_sma : String//相册小图50*50
	},{
		table : "sp_goods_pics"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("ManagerModel",{
		mg_id : {type: 'serial', key: true},
		mg_name : String,//名称
		mg_pwd : String,//密码
		mg_time : Number,//注册时间
		role_id : Number,//角色id
		mg_mobile : String,//电话
		mg_email : String,//邮箱
		mg_state : Number//1：表示启用 0:表示禁用
	},{
		table : "sp_manager"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	// 用户模型
	db.define("OrderGoodModel",{
		id : {type: 'serial', key: true},
		order_id : Number,//订单id
		goods_id : Number,//商品id
		goods_price : Number,//商品单价
		goods_number : Number,//购买单个商品数量
		goods_total_price : Number//商品小计价格
	},{
		table : "sp_order_goods"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("OrderModel",{
		order_id : {type: 'serial', key: true},
		user_id : Number,//下订单会员id
		order_number : String,//订单编号
		order_price : Number,//订单总金额
		order_pay : [1,2,3],//支付方式  0未支付 1支付宝  2微信  3银行卡
		is_send : ["是","否"],//订单是否已经发货
		trade_no : String,//支付宝交易流水号码
		order_fapiao_title : ["个人","公司"],//发票抬头 个人 公司
		order_fapiao_company : String,//公司名称
		order_fapiao_content : String,//发票内容
		consignee_addr : String,//consignee收货人地址
		pay_status : ['0','1'],//订单状态： 0未付款、1已付款
		create_time : Number,//记录生成时间
		update_time : Number//记录修改时间
	},{
		table : "sp_order"
	});
	return callback();
}
```

```js
module.exports = function(db,callback) {
	db.define("PermissionAPIModel",{
		id : {type: 'serial', key: true},
		ps_id : Number,//权限id
		ps_api_service : String,//调用service
		ps_api_action : String,//service下的方法
		ps_api_path : String,//请求路径
		ps_api_order : Number//顺序

	},{
		table : "sp_permission_api"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("PermissionModel",{
		ps_id : {type: 'serial', key: true},
		ps_name : String,//权限名称
		ps_pid : Number,//父id
		ps_c : String,//控制器
		ps_a : String,//操作方法
		ps_level : String//权限等级 '0','2','1'
	},{
		table : "sp_permission"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	// 报表模型1
	db.define("ReportOneModel",{
		id : {type: 'serial', key: true},
		rp1_user_count : Number,//用户数
		rp1_area : Number,//地区
		rp1_date : { type: "date", time: false }//创建日期
	},{
		table : "sp_report_1"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	// 报表模型2
	db.define("ReportTwoModel",{
		id : {type: 'serial', key: true},
		rp2_page : String,//页面地址
		rp2_count : Number,//访问总记录数
		rp2_date : { type: "date", time: false }//生成时间
	},{
		table : "sp_report_2"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	db.define("RoleModel",{
		role_id : {type: 'serial', key: true},//
		role_name : String,//角色名称
		ps_ids : String,//权限ids,101,0,104,105,116
		ps_ca : String,//控制器-操作Goods-showlist,Goods-tianjia,Category-showlist,Order-showlist,Order-dayin,Order-tianjia
		role_desc : String//角色描述 技术负责人
	},{
		table : "sp_role"
	});
	return callback();
}
```

```js
module.exports = function(db,callback){
	// 报表模型3
	db.define("ReportThreeModel",{
		id : {type: 'serial', key: true},
		rp3_src : String,//用户来源
		rp3_count : Number,//数量
		rp3_date : { type: "date", time: false }//生成时间
	},{
		table : "sp_report_3"
	});
	return callback();
}
```

# App.js

```js
const fs = require('fs')
var express = require('express')
var bodyParser = require('body-parser')
var path = require('path')
// 路由加载
var mount = require('mount-routes')

var app = express()


/**
 *
 * 公共系统初始化
 *
 */
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))

// 初始化数据库模块
var database = require('./modules/database')
database.initialize(app, function(err) {
  if (err) {
    console.error('连接数据库失败失败 %s', err)
  }
})

/**
 *
 *	后台管理系统初始化
 *
 */
// 获取管理员逻辑模块
var managerService = require(path.join(process.cwd(), 'services/ManagerService'))
// 获取角色服务模块
var roleService = require(path.join(process.cwd(), 'services/RoleService'))

// 设置跨域和相应数据格式
app.all('/api/*', function(req, res, next) {
  res.header('Access-Control-Allow-Origin', '*')
  res.header('Access-Control-Allow-Headers', 'X-Requested-With, mytoken')
  res.header('Access-Control-Allow-Headers', 'X-Requested-With, Authorization')
  res.setHeader('Content-Type', 'application/json;charset=utf-8')
  res.header('Access-Control-Allow-Headers', 'Content-Type,Content-Length, Authorization, Accept,X-Requested-With')
  res.header('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS')
  res.header('X-Powered-By', ' 3.2.1')
  if (req.method == 'OPTIONS') res.send(200)
  /*让options请求快速返回*/ else next()
})

// 初始化统一响应机制
var resextra = require('./modules/resextra')
app.use(resextra)

// 初始化 后台登录 passport 策略
admin_passport = require('./modules/passport')
// 设置登录模块的登录函数衔接 passport 策略
admin_passport.setup(app, managerService.login)
// 设置 passport 登录入口点
app.use('/api/private/v1/login', admin_passport.login)
// 设置 passport 验证路径
app.use('/api/private/v1/*', admin_passport.tokenAuth)

// 获取验证模块
var authorization = require(path.join(process.cwd(), '/modules/authorization'))

// 设置全局权限
authorization.setAuthFn(function(req, res, next, serviceName, actionName, passFn) {
  if (!req.userInfo || isNaN(parseInt(req.userInfo.rid))) return res.sendResult('无角色ID分配')
  // 验证权限
  roleService.authRight(req.userInfo.rid, serviceName, actionName, function(err, pass) {
    passFn(pass)
  })
})

/**
 *
 * 初始化路由
 *
 */
// 带路径的用法并且可以打印出路有表
mount(app, path.join(process.cwd(), '/routes'), true)

app.all('/ueditor/ue', function(req, res, next) {
  res.header('Access-Control-Allow-Origin', '*')
  res.header('Access-Control-Allow-Headers', 'X-Requested-With, mytoken')
  res.header('Access-Control-Allow-Headers', 'X-Requested-With, Authorization')
  res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With, X_Requested_With')
  res.header('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS')
  res.header('X-Powered-By', ' 3.2.1')
  if (req.method == 'OPTIONS') res.send(200)
  /*让options请求快速返回*/ else next()
})

// 富文本编辑器上传
var ueditor = require(path.join(process.cwd(), '/modules/ueditor'))
// 富文本控件处理qing q
app.use('/ueditor/ue', ueditor)
//. 设置富文本空间地址
app.use('/ueditor', express.static('public/ueditor'))

app.use('/tmp_uploads', express.static('tmp_uploads'))
app.use('/x/common', express.static('uploads/common'))
app.use('/uploads/goodspics', express.static('uploads/goodspics'))

var upload_config = require('config').get('upload_config')
app.use('/' + upload_config.get('upload_ueditor'), express.static(upload_config.get('upload_ueditor')))

const logistics = require('./modules/Logistics.js')
app.get('/api/private/v1/kuaidi/:orderno', logistics.getLogisticsInfo)

// 定义日志
// var log4js = require('./modules/logger');
// log4js.use(app);

/**
 *
 * 统一处理无响应
 *
 */
// 如果没有路径处理就返回 Not Found
app.use(function(req, res, next) {
  res.sendResult(null, 404, 'Not Found')
})

app.listen(8888)

module.exports = app

```

# Database.js

```js
require('mysql');
var fs = require("fs");
var orm = require("orm");
var Promise = require("bluebird");
var path = require("path");

/*
	app: 应用程序环境
	config: 数据库配置
	callback: 回调
*/
function initialize(app,callback) {

	// 加载配置文件
	var config = require('config').get("db_config");
	
	// 从配置中获取数据库配置
	var opts = {
		protocol : config.get("protocol"),
		host : config.get("host"),
		database : config.get("database"),
		port : config.get("port"),
		user : config.get("user"),
		password : config.get("password"),
		query : {pool: true,debug: true}
	};

	
	console.log("数据库连接参数 %s",JSON.stringify(opts));

	// 初始化ORM模型
	app.use(orm.express(opts, {
		define: function (db, models, next) {

			app.db = db;
			global.database = db;

			// 获取映射文件路径
			var modelsPath = path.join(process.cwd(),"/models");
			
			// 读取所有模型文件
			fs.readdir(modelsPath,function(err, files) {
				// 存放所有的加载模型函数
				var loadModelAsynFns = new Array();
				// console.log("开始加载 ORM 模型层文件 ");
				for (var i = 0; i < files.length; i++) {
					var modelPath = modelsPath + "/" +files[i];
					// console.log("加载模型 %s",modelPath);
					loadModelAsynFns[i] = db.loadAsync(modelPath);
				}
				
				Promise.all(loadModelAsynFns)
				.then(function(){
					// console.log("ORM 模型加载完成");
					// 挂载模型集合

					for(var modelName in db.models){
						models[modelName] = db.models[modelName];
					}
					app.models = models;
					callback(null);
					next();
				})
				.catch(function(error){
					console.error('加载模块出错 error: ' + err);
					callback(error);
					next();
				});
			});
		}
	}));	
}

module.exports.initialize = initialize;
module.exports.getDatabase = function() {
	return  global.database;
}
```



# 