```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body {
            background-color: #eee;
        }
        ul {
            padding: 0;
        }
        li {
            list-style: none;
        }
        .box {
            margin: 0 auto;
            width: 1245px;
            height: 2000px;
        }

        .box .head {
            height: 100px;
        }

        .box .head .left {
            float: left;
            margin-top: 18px;
        }

        .box .head .right {
            float: right;
            margin-top: 18px;
        }

        .box .head .center {
            float: left;
            font-size: 26px;
            margin-left: 210px;
            letter-spacing: 20px;
        }

        .box .nav {
            height: 50px;
            background-color: red;
        }

        .box .nav ul {
            padding: 0;
        }

        .box .nav li {
            list-style: none;
            float: left;
            width: 100px;
            margin: 0 12px;
            text-align: center;
            height: 50px;
            line-height: 50px;
            color: white;
            cursor: pointer;
        }

        .box .nav li:hover {
            background-color: black;
        }

        .banner {
            width: 100%;
        }

        .banner img {
            width: 100%;
        }

        .list li {
            float: left;
            border: 1px gray solid;
            margin: 0 35px;
        }
        .listactive {
            background-color: white;
        }

        .content {
            height: 235px;
            margin-top: 140px;
        }
        .content .left {
            background-color: white;
            width: 600px;
            float: left;
            margin-left: 10px;
            border: 1px solid black;
        }
        .content .right {
            background-color: white;
            width: 600px;
            float: right;
            margin-right: 10px;
            border: 1px solid black;
        }
        .content .top {
            height:40px;
            border-bottom: 1px black solid;
            line-height: 40px;
        }
        .content .top .gg{
            float: left;
            margin-left: 10px;
            color: red;
        }
        .content .top .more{
            float: right;
            margin-right: 10px;
            color:gray;

        }
        .content .bottom .img{
            float: left;
            margin: 20px 20px;
        }
        .content .bottom .lis{
            float: left;
            margin: 20px 20px;
        }
        .content .bottom .lis li{
            list-style-image: url('./04 任务：DIVCSS实现页面布局/任务素材/li2.gif');
            margin: 5px 0;
        }

        .jiaoxue {
            background-color: white;
            height: 200px;
            margin-top: 50px;
            border:1px black solid ;
        }
        .jiaoxue .top {
            height: 40px;
            border-bottom: 1px black solid;
        }

        .jiaoxue .top .gg{
            float: left;
            margin-left: 10px;
            color: red;
            line-height: 40px;
        }
        .jiaoxue .top .more{
            float: right;
            margin-right: 10px;
            color:gray;
            line-height: 40px;
        }
        .jiaoxue .bottom li {
            margin: 0 13px;
            float: left;
        }
        .jiaoxue .bottom li img {
            width: 180px;
            height: 120px;
        }
    </style>
</head>

<body>
    <div class="box">
        <div class="head">
            <div class="left">
                <img src="./04 任务：DIVCSS实现页面布局/任务素材/logo.png" alt="">
            </div>
            <div class="center">
                <p>做品质教育 <span></span>用事实说话</p>
            </div>
            <div class="right">
                <img src="./04 任务：DIVCSS实现页面布局/任务素材/tel.jpg" alt="">
            </div>
        </div>
        <div class="nav">
            <ul>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
                <li>网站建设</li>
            </ul>
        </div>
        <div class="banner">
            <img src="./04 任务：DIVCSS实现页面布局/任务素材/bigpic.png" alt="">
        </div>
        <div class="list">
            <ul>
                <li ><img src="./04 任务：DIVCSS实现页面布局/任务素材/1-01.png" alt=""></li class="listactive">
                <li class="listactive"><img src="./04 任务：DIVCSS实现页面布局/任务素材/1-01.png" alt=""></li class="listactive">
                <li class="listactive"><img src="./04 任务：DIVCSS实现页面布局/任务素材/1-01.png" alt=""></li class="listactive">
                <li class="listactive"><img src="./04 任务：DIVCSS实现页面布局/任务素材/1-01.png" alt=""></li class="listactive">
            </ul>
        </div>

        <div class="content">
            <div class="left">
                <div class="top">
                    <span class="gg">通知公告</span>
                    <a href="#" class="more">更多</a>
                </div>
                <div class="bottom">
                    <div class="img">
                        <img src="./04 任务：DIVCSS实现页面布局/任务素材/tongzhi.png" alt="">
                    </div>
                    <div class="lis">
                        <ul>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                        </ul>
                    </div>
                </div>
            </div>
            <div class="right">
                <div class="top">
                    <span class="gg">通知公告</span>
                    <a href="#" class="more">更多</a>
                </div>
                <div class="bottom">
                    <div class="img">
                        <img src="./04 任务：DIVCSS实现页面布局/任务素材/tongzhi.png" alt="">
                    </div>
                    <div class="lis">
                        <ul>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                            <li>aaaaaa</li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>

        <div class="jiaoxue">
            <div class="top">
                <span class="gg">通知公告</span>
                <a href="#" class="more">更多</a>
            </div>
            <div class="bottom">
                <ul>
                    <li><img src="./04 任务：DIVCSS实现页面布局/任务素材/20130204031606.jpg" alt=""></li>
                    <li><img src="./04 任务：DIVCSS实现页面布局/任务素材/20130204031606.jpg" alt=""></li>
                    <li><img src="./04 任务：DIVCSS实现页面布局/任务素材/20130204031606.jpg" alt=""></li>
                    <li><img src="./04 任务：DIVCSS实现页面布局/任务素材/20130204031606.jpg" alt=""></li>
                    <li><img src="./04 任务：DIVCSS实现页面布局/任务素材/20130204031606.jpg" alt=""></li>
                    <li><img src="./04 任务：DIVCSS实现页面布局/任务素材/20130204031606.jpg" alt=""></li>
                </ul>
            </div>
        </div>
    </div>
</body>

</html>
```

