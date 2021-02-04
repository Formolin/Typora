# Angular02

## 1、父组件获取子组件的方法

在用到子组件的标签内定义名称

app.component.html

```html
<h1>根组件</h1>
<button (click)="show()">调用news里的方法</button>
<app-news #zi></app-news>
```

我们想点击按钮调用show方法，实际帮我们调用子组件的方法

news.component.ts

```js
import { Component, OnInit, ViewChild } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {

  constructor() { 
    
  }

  // 子组件的方法
  run(){
    console.log("我是news的run方法");
  }
}

```

app.component.ts

```js
import { Component, ViewChild } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'demo';
  @ViewChild('zi') zi;
  show(){
    this.zi.run()
  }
}
```

## 2、父组件给子组件传值-@input

父组件调用子组件的时候传入数据

```html
<app-news [msg]="msg"></app-news>
```

子组件引入input模块

```java
import { Component, OnInit, ViewChild,Input } from '@angular/core';
```

父组件

```js
<h1>根组件</h1>
<button (click)="show()">调用news里的方法</button>
<app-news [title]="title"></app-news>
```

```ts
import { Component, ViewChild } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = '我是父组件的title';
  @ViewChild('zi') zi;
  show(){
    this.zi.run()
  }
}
```

子组件

```js
import { Component, OnInit, ViewChild,Input } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {

  @Input() title;
    
  constructor(public service:MyServiceService) { 
    
  }
  ngOnInit(): void {
  }

}

```

```js
<div #myBox>
    我是一个dom节点
</div>
<h1>{{title}}</h1>
```

### 传方法

父组件

```js
<h1>根组件</h1>
<button (click)="show()">调用news里的方法</button>
<app-news [title]="title" [foo]="foo"></app-news>
```

```js
import { Component, ViewChild } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = '我是父组件的title';
  @ViewChild('zi') zi;
  show(){
    this.zi.run()
  }
  foo(){
    console.log("我是父组件的foo方法");
  }
}

```

子组件

```js
import { Component, OnInit, ViewChild,Input } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.css']
})
export class NewsComponent implements OnInit {

  @Input() title;
  @Input() foo;
  constructor(public service:MyServiceService) { 
    
  }
  ngOnInit(): void {
  }
 
  // 子组件的方法
  run(){
    console.log("我是news的run方法");
    this.foo()
  }
}

```

```js
<div #myBox>
    我是一个dom节点
</div>
<h1 (click)="run()">{{title}}</h1>
```

## 3、axios使用

```js
cnpm install axios
```

测试数据地址

```js
https://api.github.com/users/Fire-zy/repos
```

news.component.ts

```js

// 引入axios
import axios from 'axios'

  async get(){
    let {data} = await axios.get('https://api.github.com/users/Fire-zy/repos')
    console.log(data);
  }


```

```html
<button (click)="get()">axios获取数据</button>
```

## 4、路由

```js

```

