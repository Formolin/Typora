## 一、Fullpage.js

插件功能介绍

- 基于 jQuery 的插件，它能够帮你很方便、很轻松的制作出全屏网站。
- 支持鼠标滚动，支持前进后退和键盘控制，多个回调函数，
  支持手机、平板触摸事件，支持 CSS3 动画，支持窗口缩放，窗口缩放时自动调整，
  可设置滚动宽度、背景颜色、滚动速度、循环选项、回调、文本对齐方式等等。
- 参考文档：http://www.dowebok.com/demo/2014/77/
- 原理：window.onmousewheel = function(){ console.log('ok') };

### 1、引入插件文件

这个插件依赖于jQuery，所以你还需要下载jQuery，并且在Fullpage插件之前引入。

```html
    <link rel="stylesheet" type="text/css" href="./css/jquery.fullpage.css" />
    <script src="./js/jquery-1.11.1.min.js"></script>
    <script type="text/javascript" src="./js/jquery.fullpage.js"></script>
```

### 2、cdn引入方式

```html
<link rel="stylesheet" href="http://cdn.dowebok.com/77/css/jquery.fullPage.css">
<script src="http://cdn.staticfile.org/jquery/1.8.3/jquery.min.js"></script>
<script src="http://cdn.dowebok.com/77/js/jquery.fullPage.min.js"></script>
```

### 3、html代码

默认情况下，每一屏幕的代码都需要有DIV包裹，并且设置DIV的类名为section，默认情况下，第一个setion将作为首页显示在页面上。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="./css/jquery.fullpage.css" />
    <script src="./js/jquery-1.11.1.min.js"></script>
    <script type="text/javascript" src="./js/jquery.fullpage.js"></script>
    <script src="./js/index.js"></script>
    <link rel="stylesheet" href="./css/index.css">
    <style>
        * {
            margin: 0;
        }
    </style>
</head>

<body>
    <div id="fullpage">
        <div class="section back1">第一屏</div>
        <div class="section back2">第二屏</div>
        <div class="section back3">第三屏 </div>
        <div class="section back4">第四屏 </div>
        <div class="section back5">第五屏 </div>
    </div>
</body>

</html>
```

### 4、js初始化

```js
//<script type="text/javascript" src="./js/index.js"></script>html别忘引入index.js文件
$(function(){
    $('#fullpage').fullpage();
})
```

### 5、样式

```css
/*第一屏*/

.back1 {
    background-image: linear-gradient(0deg ,#395ecb 0%, #3657be 35%, #242161 75%, #1a0531 100%);
}
/*第2屏*/

.back2 {
    background-image: -webkit-linear-gradient(90deg, #41b93e 0%, #3b9e3c 35%, #274535 75%, #190530 100%);
}
/*第3屏*/

.back3 {
    background-image: -webkit-linear-gradient(90deg, #395ecb 0%, #3657be 35%, #242161 75%, #1a0531 100%);
}
/*第4屏*/

.back4 {
    background-image: -webkit-linear-gradient(90deg, #ff9f42 0%, #ed923d 35%, #6d3816 75%, #2e0b04 100%);
}
/*第5屏*/

.back5 {
    background-image: -webkit-linear-gradient(90deg, #d74d53 0%, #c44655 35%, #4c184c 75%, #110145 100%);
}
```





## 二、参考文档

| 选项                              | 类型   | 默认值      | 说明                                                         |
| :-------------------------------- | :----- | ----------- | ------------------------------------------------------------ |
| verticalCentered                  | 字符串 | true        | 内容是否垂直居中                                             |
| resize                            | 布尔值 | false       | 字体是否随着窗口缩放而缩放                                   |
| slidesColor                       | 函数   | 无          | 设置背景颜色                                                 |
| anchors                           | 数组   | 无          | 定义锚链接                                                   |
| scrollingSpeed                    | 整数   | 700         | 滚动速度，单位为毫秒                                         |
| easing                            | 字符串 | easeInQuart | 滚动动画方式                                                 |
| menu                              | 布尔值 | false       | 绑定菜单，设定的相关属性与 anchors 的值对应后，菜单可以控制滚动 |
| navigation                        | 布尔值 | false       | 是否显示项目导航                                             |
| navigationPosition                | 字符串 | right       | 项目导航的位置，可选 left 或 right                           |
| navigationColor                   | 字符串 | #000        | 项目导航的颜色                                               |
| navigationTooltips                | 数组   | 空          | 项目导航的 tip                                               |
| slidesNavigation                  | 布尔值 | false       | 是否显示左右滑块的项目导航                                   |
| slidesNavPosition                 | 字符串 | bottom      | 左右滑块的项目导航的位置，可选 top 或 bottom                 |
| controlArrowColor                 | 字符串 | #fff        | 左右滑块的箭头的背景颜色                                     |
| loopBottom                        | 布尔值 | false       | 滚动到最底部后是否滚回顶部                                   |
| loopTop                           | 布尔值 | false       | 滚动到最顶部后是否滚底部                                     |
| loopHorizontal                    | 布尔值 | true        | 左右滑块是否循环滑动                                         |
| autoScrolling                     | 布尔值 | true        | 是否使用插件的滚动方式，如果选择 false，则会出现浏览器自带的滚动条 |
| scrollOverflow                    | 布尔值 | false       | 内容超过满屏后是否显示滚动条                                 |
| css3                              | 布尔值 | false       | 是否使用 CSS3 transforms 滚动                                |
| paddingTop                        | 字符串 | 0           | 与顶部的距离                                                 |
| paddingBottom                     | 字符串 | 0           | 与底部距离                                                   |
| fixedElements                     | 字符串 | 无          |                                                              |
| normalScrollElements              |        | 无          |                                                              |
| keyboardScrolling                 | 布尔值 | true        | 是否使用键盘方向键导航                                       |
| touchSensitivity                  | 整数   | 5           |                                                              |
| continuousVertical                | 布尔值 | false       | 是否循环滚动，与 loopTop 及 loopBottom 不兼容                |
| animateAnchor                     | 布尔值 | true        |                                                              |
| normalScrollElementTouchThreshold | 整数   | 5           | -                                                            |

- 方法

| 名称                   | 说明                                     |
| ---------------------- | ---------------------------------------- |
| moveSectionUp()        | 向上滚动                                 |
| moveSectionDown()      | 向下滚动                                 |
| moveTo(section, slide) | 滚动到                                   |
| moveSlideRight()       | slide 向右滚动                           |
| moveSlideLeft()        | slide 向左滚动                           |
| setAutoScrolling()     | 设置页面滚动方式，设置为 true 时自动滚动 |
| setAllowScrolling()    | 添加或删除鼠标滚轮/触控板控制            |
| setKeyboardScrolling() | 添加或删除键盘方向键控制                 |
| setScrollingSpeed()    | 定义以毫秒为单位的滚动速度               |

- 回调函数

| 名称           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| afterLoad      | 滚动到某一屏后的回调函数，接收 anchorLink 和 index 两个参数，anchorLink 是锚链接的名称，index 是序号，从1开始计算 |
| onLeave        | 滚动前的回调函数，接收 index、nextIndex 和 direction 3个参数：index 是离开的“页面”的序号，从1开始计算；nextIndex 是滚动到的“页面”的序号，从1开始计算；direction 判断往上滚动还是往下滚动，值是 up 或 down。 |
| afterRender    | 页面结构生成后的回调函数，或者说页面初始化完成后的回调函数   |
| afterSlideLoad | 滚动到某一水平滑块后的回调函数，与 afterLoad 类似，接收 anchorLink、index、slideIndex、direction 4个参数 |
| onSlideLeave   | 某一水平滑块滚动前的回调函数，与 onLeave 类似，接收 anchorLink、index、slideIndex、direction 4个参数 |



```js
$(function(){
    $('#fullpage').fullpage({
		//Navigation
		menu: false,//绑定菜单，设定的相关属性与anchors的值对应后，菜单可以控制滚动，默认为false。
		anchors:['firstPage', 'secondPage'],//anchors定义锚链接，默认为[]
		lockAnchors: false,//是否锁定锚链接，默认为false,设为true后链接地址不会改变
		navigation: false,//是否显示导航，默认为false
		navigationPosition: 'right',//导航小圆点的位置
		navigationTooltips: ['firstSlide', 'secondSlide'],//导航小圆点的提示
		showActiveTooltip: false,//是否显示当前页面的tooltip信息
		slidesNavigation: true,//是否显示横向幻灯片的导航，默认为false
		slidesNavPosition: 'bottom',//横向导航的位置，默认为bottom，可以设置为top或bottom
		 
		//Scrolling
		css3: true,//是否使用CSS3 transforms来实现滚动效果，默认为true
		scrollingSpeed: 700,//设置滚动速度，单位毫秒，默认700
		autoScrolling: true,//是否使用插件的滚动方式，默认为true,若为false则会出现浏览器自带滚动条
		fitToSection: true,//设置是否自适应整个窗口的空间，默认值：true
		scrollBar: false,//是否包含滚动条，默认为false,若为true浏览器自带滚动条出现
		easing: 'easeInOutCubic',//定义页面section滚动的动画方式，若修改此项需引入jquery.easing插件
		easingcss3: 'ease',//定义页面section滚动的过渡效果，若修改此项需引入第三方插件
		loopBottom: false,//滚动到最低部后是否连续滚动到顶部，默认为false
		loopTop: false,//滚动到最顶部后是否连续滚动到底部，默认为false
		loopHorizontal: true,//横向slide幻灯片是否循环滚动，默认为true
		continuousVertical: false,//是否循环滚动，不兼容loopTop和loopBottom选项
		normalScrollElements: '#element1, .element2',//避免自动滚动，滚动时的一些元素，例如百度地图
		scrollOverflow: false,//内容超过满屏后是否显示滚动条，true则显示滚动条，若需滚动查看内容还需要jquery.slimscroll插件的配合
		touchSensitivity: 15,//在移动设备中滑动页面的敏感性，默认为5最高100，越大越难滑动
		normalScrollElementTouchThreshold: 5,
		 
		//Accessibility
		keyboardScrolling: true,//是否可以使用键盘方向键导航，默认为true
		animateAnchor: true,//锚链接是否可以控制滚动动画，默认为true,若为false则锚链接定位失效
		recordHistory: true,//是否记录历史，默认为true,浏览器的前进后退可导航。若autoScrolling:false,那么这个属性将被关闭
		 
		//Design
		controlArrows: true,//定义是否通过箭头来控制slide,默认true
		verticalCentered: true,//定义每一页的内容是否垂直居中，默认true
		resize : false,//字体是否随窗口缩放而缩放，默认false
		sectionsColor : ['#ccc', '#fff'],//为每个section设置background-color属性
		paddingTop: '3em',设置每一个section顶部的padding,默认为0
		paddingBottom: '10px',设置每一个section底部的padding,默认为0
		fixedElements: '#header, .footer',固定元素，默认为null,需要配置一个jquery选择器，在页面滚动时，fixElements设置的元素不滚动
		responsiveWidth: 0,
		responsiveHeight: 0,
		 
		//Custom selectors
		sectionSelector: '.section',//section选择器。默认为.section
		slideSelector: '.slide',//slide选择器，默认为.slide
		 
		//events
		onLeave: function(index, nextIndex, direction){},
		afterLoad: function(anchorLink, index){},
		afterRender: function(){},
		afterResize: function(){},
		afterSlideLoad: function(anchorLink, index, slideAnchor, slideIndex){},
		onSlideLeave: function(anchorLink, index, slideIndex, direction, nextSlideIndex){}
	});
})
```

## 1、基础结构

```js
$(function(){
    $('#fullpage').fullpage({
		//Navigation
		navigation: true,//是否显示导航，默认为false
		navigationPosition: 'right',//导航小圆点的位置
		//Scrolling
		scrollingSpeed: 500,//设置滚动速度，单位毫秒，默认700
        continuousVertical: true,
	});
})
```

```css
/* 改变导航颜色 */
#fp-nav ul li a.active span{
    background-color: white;
}
#fp-nav ul li a span{
    background-color: white;
}
```

## 2、第一屏

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="./css/jquery.fullpage.css" />
    <script src="./js/jquery-1.11.1.min.js"></script>
    <script type="text/javascript" src="./js/jquery.fullpage.js"></script>
    <script src="./js/index.js"></script>
    <link rel="stylesheet" href="./css/index.css">
    <style>
        * {
            margin: 0;
        }
    </style>
</head>

<body>
    <div id="fullpage">
        <div class="section back1">
            <div class="bg"></div>
            <div class="box">
                <div class="top">
                    <p><strong>AISHANG浏览器</strong>Lite for Mac <span class="yi">译</span> <span
                            class="down">AppStore下载</span></p>
                </div>
                <div class="info">

                    <ul>
                        <li><img width="577px" height="359px" src="./img/better_title.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/better.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_1new.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_2new.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_4.png" alt=""></li>
                    </ul>

                </div>

                <!-- <div class="round"><img src="./img/ttround.png" alt=""></div> -->
            </div>
        </div>
        <div class="section back2">
            <div class="box"></div>
        </div>
        <div class="section back3">
            <div class="box"></div>
        </div>
        <div class="section back4">
            <div class="box"></div>
        </div>
        <div class="section back5">
            <div class="box"></div>
        </div>
    </div>
</body>

</html>
```

css

```css
/*第一屏*/

.back1 {
    background-image: linear-gradient(0deg ,#395ecb 0%, #3657be 35%, #242161 75%, #1a0531 100%);
}
/*第2屏*/

.back2 {
    background-image: -webkit-linear-gradient(90deg, #41b93e 0%, #3b9e3c 35%, #274535 75%, #190530 100%);
}
/*第3屏*/

.back3 {
    background-image: -webkit-linear-gradient(90deg, #395ecb 0%, #3657be 35%, #242161 75%, #1a0531 100%);
}
/*第4屏*/

.back4 {
    background-image: -webkit-linear-gradient(90deg, #ff9f42 0%, #ed923d 35%, #6d3816 75%, #2e0b04 100%);
}
/*第5屏*/

.back5 {
    background-image: -webkit-linear-gradient(90deg, #d74d53 0%, #c44655 35%, #4c184c 75%, #110145 100%);
}

/* 改变导航颜色 */
#fp-nav ul li a.active span{
    background-color: white;
}
#fp-nav ul li a span{
    background-color: white;
}
/*  第一屏 */
.back1,.back2,.back3,.back4,.back5 {
    position: relative;
   perspective: 4000px;
}
.back1 .box{
    position: absolute;
    width: 900px;
    height: 600px;
    top:50%;
    left:50%;
    transform: translate(-50%,-50%);
    transform-style: preserve-3d;
}

 .back1 .bg {
    width: 100%;
    height: 100%;
    background: url("../img/page0_glow.png") no-repeat;
    background-size:cover;
    transform: scale(1.1);
    /* // 定位背景 居底部 */
    background-position: 100% -50%;
}
.back1 .top {
    text-align: center;
    margin-top: 25px;
}
.back1 .top p{
    font-size: 24px;
    color: white;
}
.back1 .top p strong{
        font-size: 30px;
        color: white;
        margin-right: 25px;
}
.back1 .top p .yi{
    border:1px white solid;
    padding: 5px 10px 5px 10px;
    border-radius: 5px;
    margin: 0 5px;
    font-size: 20px;
}
.back1 .top p .down{
    border:1px #636086 solid;
    padding: 12px 16px 12px 16px;
    border-radius: 5px;
    margin: 0 5px;
    font-size: 16px;
}
.back1 .box .info {
    position: relative; 
    width: 100%;
    height: 100%;
    margin: 0 auto;
    transform-style: preserve-3d;
    transition: all 10s;
    perspective: 1000px;
    margin-top: 50px;
    transition: all 5s;
    animation: more linear 10s infinite;
}
.back1 .box .info ul {
    transform-style: preserve-3d;
}
/* 测试 */
.back1 .box .info:hover {
    transform: rotateY(180deg);
}
.back1 .box .info ul li{
  
    transform-style: preserve-3d;
    width: 577px;
    height: 359px;
    list-style: none;
    position: absolute;
    top:3%;
    left:50%;
    margin-left: -288.5px;
         /* !!每个li的 背景不可见 */
         backface-visibility: hidden;
}
.back1 .box .info ul li:nth-child(1){
    transform: translateZ(20px) ;
}
.back1 .box .info ul li:nth-child(2){
    transform: translateZ(60px) ;
}
/* 180度  正反面，背面不可见 */
.back1 .box .info ul li:nth-child(3){
    transform: translateZ(60px) rotateY(180deg) ;
}
.back1 .box .info ul li:nth-child(4){
    transform: translateZ(20px) rotateY(180deg) ;
}
.back1 .box .info ul li:nth-child(5){
    transform: translateZ(-20px) rotateY(180deg) ;
}



@keyframes more {
  0%{
      transform: rotateY(0deg);
  }
  100%{
      transform: rotateY(360deg);
  }
}





```

js

```js
$(function(){
    $('#fullpage').fullpage({
		//Navigation
		navigation: true,//是否显示导航，默认为false
		navigationPosition: 'right',//导航小圆点的位置
		//Scrolling
		scrollingSpeed: 500,//设置滚动速度，单位毫秒，默认700
        continuousVertical: true,
    });
    

    // 获取旋转的值
    // console.log($(".info").css("transform"))
})
```

### 绿色光圈

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="jquery.fullpage.css" />
    <script src="jquery-1.11.1.min.js"></script>
    <script type="text/javascript" src="jquery.fullpage.js"></script>
    <script src="index.js"></script>
    <link rel="stylesheet" href="index.css">
    <style>
        * {
            margin: 0;
        }
    </style>
</head>

<body>

    <div id="fullpage">
        <div class="section back1 " >
            <div class="bg"></div>
            <div class="box">
                <div class="top">
                    <p><strong>AISHANG浏览器</strong>Lite for Mac <span class="yi">译</span> <span
                            class="down">AppStore下载</span></p>
                </div>
                <div class="info">

                    <ul>
                        <li><img width="577px" height="359px" src="./img/better_title.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/better.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_1new.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_2new.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_4.png" alt=""></li>
                    </ul>
                   <div>
                    <img src="./img/ttround.png" alt="">
                   </div>
                </div>

            
            </div>
        </div>
        <div class="section back2">第二屏</div>
        <div class="section back3">第三屏 </div>
        <div class="section back4">第四屏 </div>
        <div class="section back5">第5屏 </div>
    </div>
</body>

</html>
```

css

```css
  /* 绿色光圈 */
 .back1 .info div {
    position: absolute;
    width: 879px;
    height: 679px;
    transform:  translateZ(20px) rotateX(-110deg) rotateY(-6deg)  scaleX(2.6) ; 
  }
  .back1 .info  div img {
    animation: z 5s linear 0s infinite;
  }


@keyframes z {
    from {
    }
    to {
      transform: rotateZ(360deg) scale(1) ;
    }
  }
```

### 滚动

html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="jquery.fullpage.css" />
    <script src="jquery-1.11.1.min.js"></script>
    <script type="text/javascript" src="jquery.fullpage.js"></script>
    <script src="index.js"></script>
    <link rel="stylesheet" href="index.css">
    <style>
        * {
            margin: 0;
        }
    </style>
</head>

<body>

    <div id="fullpage">
        <div class="section back1 " >
            <div class="bg"></div>
            <div class="box">
                <div class="top">
                    <p><strong>AISHANG浏览器</strong>Lite for Mac <span class="yi">译</span> <span
                            class="down">AppStore下载</span></p>
                </div>
                <div class="info">

                    <ul>
                        <li><img width="577px" height="359px" src="./img/better_title.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/better.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_1new.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_2new.png" alt=""></li>
                        <li><img width="577px" height="359px" src="./img/page1_main_4.png" alt=""></li>
                    </ul>
                   <div>
                    <img src="./img/ttround.png" alt="">
                   </div>
                </div>

              
            </div>
            <div class="scroll">
                <img src="./img/scroll.png" alt="">
            </div>
        </div>
        <div class="section back2">第二屏</div>
        <div class="section back3">第三屏 </div>
        <div class="section back4">第四屏 </div>
        <div class="section back5">第5屏 </div>
    </div>
</body>

</html>
```

css

```css

  /* 滚动 */
  .back1 .scroll{
    position: absolute;
    bottom: 0;
    left: 50%;
    bottom: 5%;
    transform: translate(-50%);
    animation: g .7s linear infinite alternate;
  }
  @keyframes g {
      from{
        bottom: 4%;
      }
      to{
        bottom: 6%;
      }
  }
```

js

```js
$(function () {
    $('#fullpage').fullpage({
        //Navigation
        navigation: true,//是否显示导航，默认为false
        navigationPosition: 'right',//导航小圆点的位置
        //Scrolling
        scrollingSpeed: 500,//设置滚动速度，单位毫秒，默认700
        continuousVertical: true,
    });

    $('#next').click(function () {
        $.fn.fullpage.moveSectionDown()
    })

    // $(widow).height()获取当前屏幕的高度
    // 获取旋转的值
    // console.log($(".info").css("transform"))
})
```

