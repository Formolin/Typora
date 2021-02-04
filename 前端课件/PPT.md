```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>爱尚实训-哈尔滨</title>
    <link rel="stylesheet" href="https://cdn.bootcss.com/fullPage.js/3.0.8/fullpage.min.css">
    <script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script>

    <script src="https://cdn.bootcss.com/fullPage.js/3.0.8/fullpage.min.js"></script>
    <script src="https://cdn.bootcss.com/echarts/4.6.0/echarts.min.js"></script>
    <!-- <script src="https://cdn.bootcss.com/fullPage.js/3.0.8/vendors/scrolloverflow.min.js"></script> -->
    <style>
        .section {
            text-align: center;
            font: 50px "Microsoft Yahei";
            color: #fff;
        }

        .section p {
            font: 30px "Microsoft Yahei";
        }

        /* 第一屏 首页 */
        #fullPage .back1 {
            position: relative;
            background-image: url('./img/图片2.png');
        }

        #fullPage .back1 .mask {
            width: 95%;
            height: calc(100vh - 100px);
            background-color: red;
            opacity: .4;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            z-index: 10;
        }

        #fullPage .back1 .logo {
            position: absolute;
            width: 600px;
            height: 300px;
            background-color: white;
            z-index: 11;
            top: 2%;
            left: 50%;
            transform: translateX(-50%);
            box-shadow: 0 0 10px 4px white;
        }

        #fullPage .back1 .logo img {
            position: absolute;
            width: 400px;
            left: 80px;
            top: 20%;
        }

        #fullPage .back1 .logo p {
            width: 500px;
            position: absolute;
            top: 40%;
            color: red;
            left: 50%;
            transform: translateX(-50%);
            font-size: 42px;
            font-weight: 500;
            letter-spacing: 2px;
        }

        #fullPage .back1 .content {
            position: absolute;
            z-index: 11;
            width: 1000px;
            height: 120px;
            background-color: white;
            left: 50%;
            transform: translateX(-50%);
            color: black;
            line-height: 120px;
            border-top-left-radius: 100px;
            border-bottom-right-radius: 100px;
            border-top-right-radius: 160px;
            border-bottom-left-radius: 160px;
        }

        #fullPage .back1 .content span {
            background: linear-gradient(to right, red, black);
            -webkit-background-clip: text;
            color: transparent;
        }

        #fullPage .back1 .author {
            position: absolute;
            bottom: 15%;
            color: white;
            font-weight: 700;
            z-index: 12;
            right: 22%;
            text-shadow: 2px 2px 10px white;
        }

        /* 第二屏 */
        #fullPage .back2 .left {
            position: relative;
            float: left;
            width: 25%;
            height: calc(100vh);
            background-color: black;
        }

        #fullPage .back2 .right {
            width: 75%;
            float: left;
            height: calc(100vh);
            background-color: white;
        }

        #fullPage .back2 .left img {
            margin-top: 50px;
        }

        #fullPage .back2 .left .my {
            position: absolute;
            left: 50%;
            width: 245px;
            height: 245px;
            transform: translateX(-50%);
            border-radius: 50%;
            background: url('./img/my.jpg') center center;
        }

        #fullPage .back2 .left .teacher {
            color: white;
            position: absolute;
            top: 45%;
            left: 50%;
            transform: translateX(-50%);
            font-size: 500;
        }

        #fullPage .back2 .left .info {
            width: 340px;
            color: white;
            position: absolute;
            top: 55%;
            margin-left: 40px;
            font-size: 20px;
            line-height: 40px;
            text-align: left;
            text-indent: 2em;
        }

        /* 大纲 二维码 */
        #fullPage .back2 .right .dagang {
            float: left;
            width: 60%;
            height: calc(100vh);
            color: black;
            text-align: left;
            margin-left: 30px;
        }

        #fullPage .back2 .right .dagang h6 {
            margin-bottom: 15px;
        }

        #fullPage .back2 .right .dagang ul {
            margin-top: 0;
        }

        #fullPage .back2 .right .dagang li {
            font-size: 30px;
            margin-left: 30px;
            color: darkcyan;
        }

        #fullPage .back2 .right .dagang .end {
            list-style: none;
        }

        #fullPage .back2 .right .dagang li .img {
            margin-top: 15px;
            width: 650px;
            height: 400px;
        }

        #fullPage .back2 .right .erweima {
            padding-top: 100px;
            float: left;
            width: 35%;
            height: calc(100vh);
        }

        #fullPage .back2 .right .erweima img {
            width: 300px;
            height: 300px;
            margin: 20px 20px;
        }

        #fullPage .back2 .right .erweima .wx {
            margin-top: -40px;
            font-size: 26px;
            color: red;
        }

        #fullPage .back2 .right .erweima .info {
            width: 320px;
            margin-left: 60px;
            color: red;
            font-size: 28px;
        }

        /* 第三屏 */
        /* 起始动画位置 */
        #fullPage .back3 .one {
            transform: translateY(-300px);
            letter-spacing: 5px;
        }

        /* 动画结束的位置 */
        #fullPage .back3 .one.tt {
            transform: translateY(-50px) rotate(-20deg);
            transition: all 2s;
        }

        /* 为什么学前端 */
        #fullPage .back3 .four .top {
            position: absolute;
            top: 12%;
            left: 5350px;
            /* transform: translateX(-50%); */
        }

        #fullPage .back3 .four .top img {
            /* float: left; */
            width: 340px;
            height: 120px;
        }


        #fullPage .back3 .four .bottom .item {
            position: absolute;
            top: 32%;
            left: 5350px;
            width: 1000px;
            height: 500px;
        }

        #fullPage .back3 .four .bottom .item div {
            display: none;
        }

        #fullPage .back3 .four .bottom .item div img {
            width: 1050px;
        }

        #fullPage .back3 .four .bottom .item .active {
            display: block;
        }

        /* 第四个页面 */
        #fullPage .back4 .box #main {
            width: 1600px;
            height: calc(100vh)
        }
    </style>
</head>

<body>
    <div id="fullPage">
        <div class="section back1">
            <div class="mask"></div>
            <div class="logo">
                <img src="./img/图片1.png" alt="">
                <p>做品质教育 <span></span> 用事实说话 </p>
            </div>
            <div class="content">
                <span>从懵逼到大神系列之前端CSS3</span>
            </div>
            <div class="author">主讲：Tony</div>
        </div>
        <div class="section back2">
            <div class="left">
                <img src="./img/it.png" alt="">
                <div class="my">
                </div>
                <p class="teacher">Tony老师</p>
                <p class="info">
                    互联网全栈工程师，10余年开发经验，擅长领域:web前端
                    、java、大数据、分布式、消息中间件、小程序、公众号等。
                    注重实践，以项目驱动教学。
                </p>
            </div>
            <div class="right">
                <div class="dagang">
                    <h6>本节内容</h6>
                    <ol>
                        <li>背景
                            <ul>
                                <li>background-image</li>
                                <li>background-repeat</li>
                                <li>background-size</li>
                                <li>background-position</li>
                            </ul>
                        </li>
                        <li>transform转换
                            <ul>
                                <li>translate</li>
                                <li>rotate</li>
                            </ul>
                        </li>
                        <li>C3动画
                            <ul>
                                <li>animate</li>
                            </ul>
                        </li>
                        <li>20分钟搞定360浏览器主页</li>
                        <!-- <li class="end"><img class="img" src="./img/002.png" alt=""></li> -->
                    </ol>
                </div>
                <div class="erweima">
                    <img src="./img/erweima.png" alt="">
                    <p class="wx">微信号：aishanghrb</p>
                    <p class="info">更多资料、源码、视频等扫描上方二维码，添加微信即可获取！！！</p>
                </div>
            </div>
        </div>
        <div class="section back3">
            <div class="slide one">
                <h3>什么是前端 ？？？</h3>
            </div>
            <div class="slide two">
                <!-- 谷歌浏览器禁用了自动播放功能，如果想自动播放，需要添加 muted 属性 -->
                <video controls="controls" autoplay muted loop poster="./media/pig.jpg">
                    <source src="./img/vv.mp4" type="video/mp4">
                </video>
            </div>
            <div class="slide three">
                <h3>为什么学前端 ？？？</h3>
            </div>
            <div class="slide four">
                <div class="top">
                    <img src="./img/w1.png" alt="">
                    <img src="./img/w2.png" alt="">
                    <img src="./img/w3.png" alt="">
                </div>
                <div class="bottom">
                    <div class="item">
                        <div class="active">
                            <img src="./img/tab1_b.png" alt="">
                        </div>
                        <div>
                            <img src="./img/tab2_b.png" alt="">
                        </div>
                        <div>
                            <img src="./img/tab3_b.png" alt="">
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="section back4">
            <div class="box">
                <div id="main"></div>
            </div>
        </div>
    </div>

    <script>
        $(function () {
            $('#fullPage').fullpage({
                sectionsColor: ['#7BAABE', '#f90', '#4BBFC3', 'white'],
                loopTop: true,
                loopBottom: true,
                scrollOverflow: true,
                onLeave: function (index, nextIndex, direction) {

                    if (nextIndex.index == 2) {
                        console.log(nextIndex.index);
                        $('.one').addClass('tt')
                    }
                }
            });
        });
        // 第四个页面拖放功能
        // var box = document.querySelector('.box')
        // box.addEventListener('mousedown', function (e) {


        //     //当我们鼠标按下， 就获得鼠标在盒子内的坐标
        //     var x = e.pageX - this.offsetLeft;
        //     var y = e.pageY - this.offsetTop;
        //     console.log(x + "--" + y);
        //     // 鼠标移动的时候，把鼠标在页面中的坐标，减去 鼠标在盒子内的坐标就是模态框的left和top值
        //     document.addEventListener('mousemove', move)
        //     function move(e) {
        //         box.style.left = e.pageX - x + "px"
        //         box.style.top = e.pageY - y + "px"
        //     }
        //     // 鼠标弹起，就让鼠标移动事件移除
        //     document.addEventListener('mouseup', function () {
        //         document.removeEventListener('mousemove', move);
        //     })
        // })

        // echarts 思维导图
        // 基于准备好的dom，初始化echarts实例
        var myChart = echarts.init(document.getElementById('main'));
        myChart.showLoading();
        $.get('./flare.json', function (data) {
            myChart.hideLoading();
            // index%1或者2  控制初始化显示的节点是否展开
            echarts.util.each(data.children, function (datum, index) {
                index % 1 === 0 && (datum.collapsed = true);
            });

            myChart.setOption(option = {
                tooltip: {
                    trigger: 'item',
                    triggerOn: 'mousemove'
                },

                series: [
                    {
                        type: 'tree',
                        data: [data],
                        top: '1%',
                        left: '20%',
                        right: '25%',
                        symbolSize: 16,

                        label: {

                            position: 'left',
                            verticalAlign: 'middle',
                            align: 'right',
                            fontSize: 16
                        },

                        leaves: {
                            label: {
                                position: 'right',
                                verticalAlign: 'middle',
                                align: 'left'
                            }
                        },

                        expandAndCollapse: true,
                        animationDuration: 550,
                        animationDurationUpdate: 750
                    }
                ]
            });
        });

        // 只能展开一个节点

        // let current_dep = 0;
        // myChart.on('click', function (params) {
        //     dfs(option.series[0].data, params.name, 0);
        //     solve(option.series[0].data, params.name, current_dep);
        //     myChart.clear();
        //     myChart.setOption(option);
        // });
        // function dfs(array, id, dep) {
        //     array.forEach((obj, i) => {
        //         obj.dep = dep;
        //         if (obj.name === id) {
        //             current_dep = dep;
        //             console.log("current: " + current_dep);
        //         } else if (obj.children !== undefined && obj.children.length > 0) {
        //             return dfs(obj.children, id, dep + 1);
        //         }
        //     })
        // }
        // function solve(array, id, current_dep) {
        //     array.forEach((obj, i) => {
        //         if (obj.dep < current_dep) {
        //             obj.collapsed = false;
        //             if (obj.children && obj.children.length > 0) {
        //                 solve(obj.children, id, current_dep);
        //             }
        //         } else if (obj.dep === current_dep) {
        //             if (obj.name === id) {
        //                 obj.collapsed = false;
        //             } else {
        //                 obj.collapsed = true;
        //             }
        //         }
        //     })
        // }

    </script>
</body>

</html>
```

```json
{
    "name": "前端课程体系",
    "children": [
        {
            "name": "第一阶段-前端核心技术",
            "children": [
                {
                    "name": "1、HTML",
                    "children": [
                        {
                            "name": "介绍"
                        },
                        {
                            "name": "标签"
                        },
                        {
                            "name": "表格"
                        },
                        {
                            "name": "表单"
                        },
                        {
                            "name": "框架页"
                        }
                    ]
                },
                {
                    "name": "2、CSS",
                    "children": [
                        {
                            "name": "引入方式"
                        },
                        {
                            "name": "常用样式"
                        },
                        {
                            "name": "基础选择器"
                        },
                        {
                            "name": "行内元素和块级元素"
                        },
                        {
                            "name": "子类、后代、伪类选择器"
                        },
                        {
                            "name": "行高、垂直对齐、盒模型"
                        },
                        {
                            "name": "浮动、清除浮动、透明"
                        },
                        {
                            "name": "三大特性、文本标签"
                        },
                        {
                            "name": "背景、定位、禁止拖拽"
                        },
                        {
                            "name": "精灵图、文本溢出"
                        }
                    ]
                },
                {
                    "name": "3、项目实战：爱尚购物平台"
                },
                {
                    "name": "4、javascript",
                    "children": [
                        {
                            "name": "基础",
                            "children": [
                                {
                                    "name": "变量"
                                },
                                {
                                    "name": "数据类型"
                                },
                                {
                                    "name": "注释"
                                },
                                {
                                    "name": "数据转换"
                                },
                                {
                                    "name": "运算符"
                                },
                                {
                                    "name": "流程控制"
                                },
                                {
                                    "name": "分支结构"
                                }
                            ]
                        },
                        {
                            "name": "循环",
                            "children": [
                                {
                                    "name": "while"
                                },
                                {
                                    "name": "do while"
                                },
                                {
                                    "name": "for"
                                },
                                {
                                    "name": "continue"
                                },
                                {
                                    "name": "break"
                                }
                            ]
                        },
                        {
                            "name": "数组",
                            "children": [
                                {
                                    "name": "定义数组"
                                },
                                {
                                    "name": "遍历"
                                }
                            ]
                        },
                        {
                            "name": "函数",
                            "children": [
                                {
                                    "name": "定义函数"
                                },
                                {
                                    "name": "返回值"
                                },
                                {
                                    "name": "参数"
                                },
                                {
                                    "name": "作用域"
                                },
                                {
                                    "name": "预解析"
                                }
                            ]
                        },
                        {
                            "name": "对象",
                            "children": [
                                {
                                    "name": "创建对象的方式"
                                },
                                {
                                    "name": "属性"
                                },
                                {
                                    "name": "方法"
                                },
                                {
                                    "name": "this关键词"
                                },
                                {
                                    "name": "内置对象"
                                }
                            ]
                        },
                        {
                            "name": "网页特效",
                            "children": [
                                {
                                    "name": "DOM操作"
                                },
                                {
                                    "name": "事件"
                                },
                                {
                                    "name": "克隆"
                                },
                                {
                                    "name": "事件监听"
                                },
                                {
                                    "name": "事件冒泡"
                                },
                                {
                                    "name": "事件捕获"
                                },
                                {
                                    "name": "事件对象"
                                },
                                {
                                    "name": "键盘事件"
                                },
                                {
                                    "name": "定时器"
                                },
                                {
                                    "name": "BOM操作"
                                },
                                {
                                    "name": "offset、client、scroll"
                                },
                                {
                                    "name": "动画"
                                },
                                {
                                    "name": "特效案例"
                                }
                            ]
                        }
                    ]
                },
                {
                    "name": "项目实战：网页特效"
                },
                {
                    "name": "5、JQuery",
                    "children": [
                        {
                            "name": "选择器"
                        },
                        {
                            "name": "事件"
                        },
                        {
                            "name": "事件绑定、解绑"
                        },
                        {
                            "name": "插件使用"
                        }
                    ]
                },
                {
                    "name": "项目实战：QQTIM主页"
                },
                {
                    "name": "6、javascript高级",
                    "children": [
                        {
                            "name": "ES6语法"
                        },
                        {
                            "name": "面向对象",
                            "children": [
                                {
                                    "name": "构造函数"
                                },
                                {
                                    "name": "原型"
                                },
                                {
                                    "name": "继承"
                                },
                                {
                                    "name": "super"
                                },
                                {
                                    "name": "call方法"
                                },
                                {
                                    "name": "数组、字符串新增方法"
                                }
                            ]
                        },
                        {
                            "name": "函数进阶",
                            "children": [
                                {
                                    "name": "this指向"
                                },
                                {
                                    "name": "apply"
                                },
                                {
                                    "name": "bind"
                                },
                                {
                                    "name": "高阶函数"
                                },
                                {
                                    "name": "闭包"
                                },
                                {
                                    "name": "递归"
                                },
                                {
                                    "name": "深拷贝、浅拷贝"
                                }
                            ]
                        },
                        {
                            "name": "像素鸟游戏"
                        },
                        {
                            "name": "正则表达式"
                        }
                    ]
                },
                {
                    "name": "项目实战：游戏"
                }
            ]
        },
        {
            "name": "第二阶段-H5C3",
            "children": [
                {
                    "name": "HTML5",
                    "children": [
                        {
                            "name": "标签"
                        },
                        {
                            "name": "属性"
                        }
                    ]
                },
                {
                    "name": "CSS3",
                    "children": [
                        {
                            "name": "选择器"
                        },
                        {
                            "name": "过渡"
                        },
                        {
                            "name": "转换"
                        },
                        {
                            "name": "旋转"
                        },
                        {
                            "name": "缩放"
                        },
                        {
                            "name": "动画"
                        }
                    ]
                },{
                    "name": "项目实战：360浏览器项目"
                }
            ]
        },
        
        {
            "name": "第三阶段-移动端开发",
            "children": [
                {
                    "name": "flex布局"
                },
                {
                    "name": "rem布局"
                },
                {
                    "name": "less"
                },
                {
                    "name": "bootstrap"
                },
                {
                    "name": "项目实战：响应式布局页面"
                }
            ]
        },
        {
            "name": "第四阶段-前后端交互",
            "children": [
                {
                    "name": "NodeJS"
                },
                {
                    "name": "MongoDB"
                },
                {
                    "name": "Express"
                },
                {
                    "name": "GIT"
                },
                {
                    "name": "Github"
                },
                {
                    "name": "项目实战：博客系统"
                }
            ]
        },
        {
            "name": "第五阶段-框架",
            "children": [
                {
                    "name": "Vue"
                },
                {
                    "name": "项目实战：电商后台管理系统"
                },
                {
                    "name": "小程序"
                },
                {
                    "name": "项目实战：爱尚购物小程序"
                },
                {
                    "name": "公众号开发"
                }
            ]
        }
    ]
}
```

