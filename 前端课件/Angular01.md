# Angular01

Angualr是谷歌开发的一款开源的 web前端框架，诞生于2009年，由Misko Hevery等人创建，后为Google所收购。是一款优秀的前端JS框架，已经被用于 Google的多款产品当中。

根据项目数统计angular （1.x、2.x、4.x、5.x、6.x、7.x、8.x、9.x）是现在网上使用量最大的框架。

Angualr基于TypeScript， 和 react、vue相比 Angular更适合中大型企业级项目。

目前2019年12月25日 angular最新版本 angular9.x。根据官方介绍，Angular每过几个月就会更新一个版本。

Angular2.x以后所有的Angular版本用法都是一样的，

## 1、安装

脚手架只需安装一次

```js
cnpm install -g @angular/cli
```

测试，看见logo 安装成功

```js
#输入命令
ng v


     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/
    

Angular CLI: 11.1.2
Node: 13.8.0
OS: darwin x64

Angular: 
... 
Ivy Workspace: 

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.1101.2 (cli-only)
@angular-devkit/core         11.1.2 (cli-only)
@angular-devkit/schematics   11.1.2 (cli-only)
@schematics/angular          11.1.2 (cli-only)
@schematics/update           0.1101.2 (cli-only)
```

## 2、创建项目

```js
ng new 项目名称

ng new demo
#是否增加路由，添加，下节课用
#使用css还是less等
#注意此时就是下载、安装依赖，但是比较慢
#我们可以看到只要package.json生成了，我们就结束程序，使用cnpm install进行安装即可
cd demo
cnpm install
```

运行项目

```js
ng serve --open
```

## 3、组件

```js
ng g component component/news
```

这个名利是创建组件，自动在app文件夹下创建一个component文件夹，并且创建一个news组件

查看app.module.ts文件，默认会帮我们添加新建的组件

使用：

在根组件app.component.html调用新闻组件

news.component.html

```js
<h1>我是news组件</h1>
```

app.component.html

```js
<h1>根组件</h1>
<app-news></app-news>
```

启动

```js
ng serve --open
```

数据渲染

在news.compontent.ts文件中创建数据

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  // 添加数据
  title="我是一个新闻组件";

  constructor() { }

  ngOnInit(): void {
  }

}

```

news.component.html数据渲染

```html
<h1>我是news组件</h1>
<h1>{{title}}</h1>
```

声明属性的几种方式:

- public
  - 共有（默认）可以在这个类里面使用、也可以在类外面使用
- protected
  - 保护类型 他只有在当前类和它的子类里面可以访问
- private
  - 私有 只有在当前类才可以访问这个属性

```js
public title="我是一个新闻组件";
protected msg="我是一个新闻组件";
private hi="我是一个新闻组件";
```

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  // 添加数据
  public title="我是一个新闻组件";
  protected msg="我是一个新闻组件";
  private hi="我是一个新闻组件";
  obj = {
    name:"tom",
    age:18
  }
  constructor() { }

  ngOnInit(): void {
  }

}
```

```js
<h1>我是news组件</h1>
<h1>{{title}}</h1>
<h1>{{obj.name}}</h1>
<h1>{{obj.age}}</h1>
```

通过构造函数可以改变属性的值

```js
constructor() { 
    this.obj.name = "lisi"
}
```

### 绑定属性

```js
<div [class]="title">
    在标签上添加属性,动态数据加[] 等号后面是我们自己的数据
</div>
```

### 绑定HTML

```js
content="<h2>绑定HTML</h2>"
```

```html
<div>
    {{content}}
</div>
```

此时是按字符串解析的,修改如下

```js
<div [innerHTML]="content">
    
</div>
```

## 4、循环列表

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  arr=[11,22,'abc']
  constructor() { 
  }
  ngOnInit(): void {
  }
}

```

```js
<ul>
    <li *ngFor="let item of arr;let key=index;">
     {{key}}   {{item}}
    </li>
</ul>
```

## 5、引入图片

把图片复制到assets文件夹中

```js
<img src="assets/need.png" alt="">
```

远程图片地址

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  arr=[11,22,'abc']
  imgUrl = "https://i2.hdslb.com/bfs/space/768cc4fd97618cf589d23c2711a1d1a729f42235.png@.webp"
  constructor() { 
  }
  ngOnInit(): void {
  }
}

```

```html
<img [src]="imgUrl" alt="">
```

## 6、条件判断

### ngIf

```html
<div *ngIf="flag">
    <img src="assets/need.png" alt="">
</div>
<div *ngIf="!flag">
    <img [src]="imgUrl" alt="">
</div>
<div *ngIf="key==1">
    <img [src]="imgUrl" alt="">
</div>
```

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  imgUrl = "https://i2.hdslb.com/bfs/space/768cc4fd97618cf589d23c2711a1d1a729f42235.png@.webp"
  flag=true
  key=1
  constructor() { 
  }
  ngOnInit(): void {
  }
}

```

### ngSwitch

```html

<div [ngSwitch]="key">
    <div *ngSwitchCase="1">key=1</div>
    <div *ngSwitchCase="2">key=2</div>
    <div *ngSwitchDefault>hello</div>
</div>

```

### ngClass

```css
.red{
    color:red;
}
```

```html
<h1 [ngClass]="{'red': true}">hello</h1>
```

```css
.red{
    color:red;
}
.blue{
    color:blue;
}
```

```html
<h1 [ngClass]="{'red': false,'blue':true}">hello</h1>
```

### ngStyle

设置attr=‘’red‘’

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  imgUrl = "https://i2.hdslb.com/bfs/space/768cc4fd97618cf589d23c2711a1d1a729f42235.png@.webp"
  flag=true
  key=1
  attr='red'
  constructor() { 
  }
  ngOnInit(): void {
  }
}

```

```html
<h1 [ngStyle]="{'color': attr}">ngStyle</h1>
```

## 7、管道

日期格式化

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  time=new Date()
  constructor() { 
  }
  ngOnInit(): void {
  }
}

```

```html
<h1>{{time}}</h1>
```

格式化

```html
<h1>{{time | date:'yyyy-MM-dd HH:mm:ss'}}</h1>
```

## 8、事件

```html
<button (click)="show()">事件</button>
<h1>{{msg}}</h1>
```

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  msg="hello"
  constructor() { 
  }
  ngOnInit(): void {
  }
  show(){
    console.log("show!!!");
    this.msg = "haha"
  }
}

```

事件对象

```html
<button (click)="show($event)">事件</button>
```

```js
  show(e){
    console.log(e);
  }
```

## 9、双向数据绑定

只针对表单

先引入模块，app.module.ts

```js
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
// 双向数据绑定
import {FormsModule} from '@angular/forms'
import { AppComponent } from './app.component';
import { NewsComponent } from './component/news/news.component';

@NgModule({
  declarations: [
    AppComponent,
    NewsComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

```js
<input type="text" [(ngModel)]="keywords"/>
{{keywords}}
```

## 10、服务

一个组件与想调用其他组件的方法是调用不了的，我们可以把公用的方法放到一个地方，这个地方我们就叫服务.之后只要组件想用这个服务的方法我们就直接引入该服务即可。

### 创建服务

```js
ng g service myService

创建到指定目录下
ng g service services/storage   (目录/组件名称)
```

在app.module.ts中配置

```js
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
// 双向数据绑定
import {FormsModule} from '@angular/forms'
import { AppComponent } from './app.component';
import { NewsComponent } from './component/news/news.component';
// 引入并且配置服务
import { MyServiceService} from './my-service.service'
@NgModule({
  declarations: [
    AppComponent,
    NewsComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  // 配置
  providers: [MyServiceService],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

my-service.service.ts

```js
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class MyServiceService {

  constructor() { }
  get(){
    return "这是服务"
  }
}

```

news组件中使用get方法，在news中引入

```js
import { Component, OnInit } from '@angular/core';
// 引入服务
import { MyServiceService} from '../../my-service.service'

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  msg="hello"
  keywords="你好"
  constructor(public service:MyServiceService) { 
    let s = this.service.get()
    console.log(s);
    
  }
  ngOnInit(): void {
  }
  show(e){
    console.log("show!!!");
    this.msg = "haha"
    console.log(e);
    
  }
}

```

## 11、ViewChild操作Dom

```js
<div #myBox>
    我是一个dom节点
</div>
```

```js
import { Component, OnInit, ViewChild } from '@angular/core';
// 引入服务
import { MyServiceService} from '../../my-service.service'

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {
  // 获取dom节点
  @ViewChild('myBox') box;
  constructor(public service:MyServiceService) { 
    
  }
  ngOnInit(): void {
  }
  // 操作dom  ngAfterViewInit:生命周期函数
  ngAfterViewInit(){
    // 打印结果 ElementRef {nativeElement: div}
    console.log(this.box);
    // 获取真正节点nativeElement
    this.box.nativeElement.style.width='200px' 
    this.box.nativeElement.style.height='200px' 
    this.box.nativeElement.style.backgroundColor='red' 
  }
}

```

## 12、生命周期函数

| 生命周期函数            | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| ngOnChanges()           | **初始化输入属性** ，Angular（重新）设置数据绑定输入属性时的响应。该方法接收 `SimpleChanges` 当前和先前属性值的对象。`ngOnInit()` 在一个或多个数据绑定输入属性发生更改 之前和之后调用。 |
| ngOnInit()              | **初始化其他属性**，在 Angular 首次显示数据绑定属性并设置指令/组件的输入属性后初始化指令/组件。在第一次之后 调用一次 `ngOnChanges()` 。 |
| ngDoCheck()             | **组件变更检测**，检测 Angular 无法或不会自行检测的更改并采取相应措施。在每次更改检测运行期间，在 `ngOnChanges()` 和之后立即调用 `ngOnInit()`。 |
| ngAfterContentInit()    | **投影内容初始化**，在 Angular 将外部内容投影到组件的视图/指令所在的视图后进行响应。在第一次之后 调用一次 `ngDoCheck()`。**if you want to change the child component’s props, you cannot do it in ‘ngAfterViewInit’, you need to do it in ‘ngAfterContentInit’.** |
| ngAfterContentChecked() | **针对投影内容的变更检测**在 Angular 检查投射到指令/组件中的内容后响应。在 `ngAfterContentInit()` 随后和随后的每一次调用之后 `ngDoCheck()`。 |
| ngAfterViewInit()       | **初始化完组件视图及其子视图之后调用** ，在 Angular 初始化组件的视图和子视图/指令所在的视图后响应。在第一次之后 调用一次 `ngAfterContentChecked()`。**一般进行 DOM 操作** |
| ngAfterViewChecked()    | **每次做完组件视图和子视图的变更检测之后调用**，在 Angular 检查组件的视图和子视图/指令所在的视图后响应。在 `ngAfterViewInit()` 随后和随后的每一次调用之后 `ngAfterContentChecked()`。 |
| ngOnDestroy()           | **当Angular每次销毁指令/组件之前调用并清扫。 在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏**，就在 Angular 破坏指令/组件之前进行清理。取消订阅 `Observable` 并分离事件处理程序以避免内存泄漏。在 Angular 破坏指令/组件之前 调用。**组件销毁时执行** |