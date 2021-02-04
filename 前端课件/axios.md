# Axios

## 一、安装

> npm i axios

## 二、特色

1. 浏览器发起XMLHTTPRequest请求
2. node端发起http请求
3. 支持Promise API
4. 拦截请求和返回
5. 转化请求和返回
6. 取消请求
7. 自动转化json数据
8. 客户端支持抵御XSRF（跨域请求伪造）

## 三、使用

### 1、导入

```vue
import Axios from 'axios';
//给Vue原型改在一个属性
Vue.prototype.$axios = Axios;
```

### 2、请求

```vue
this.$axios.get('http://127.0.0.1/webapp/getPro')
.then(res => {
	console.log(res)
	//this.data = res.data.message
})
.catch(err => {
	console.log(err)
})
```

### 3、合并请求

