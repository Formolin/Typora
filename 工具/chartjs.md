# 入门

让我们开始使用Chart.js！

首先，我们需要在页面中有一个画布。

```html
<canvas id="myChart"></canvas>
```

现在我们有了一个可以使用的画布，我们需要在页面中包含Chart.js。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.4.0/Chart.min.js"></script>
```

现在，我们可以创建一个图表。我们在页面中添加了一个脚本：

```javascript
var ctx = document.getElementById('myChart').getContext('2d');
var chart = new Chart(ctx, {
    // The type of chart we want to create
    type: 'line',

    // The data for our dataset
    data: {
        labels: ["January", "February", "March", "April", "May", "June", "July"],
        datasets: [{
            label: "My First dataset",
            backgroundColor: 'rgb(255, 99, 132)',
            borderColor: 'rgb(255, 99, 132)',
            data: [0, 10, 5, 2, 20, 30, 45],
        }]
    },

    // Configuration options go here
    options: {}
});
```

使用Chart.js很容易上手！从这里，您可以探索许多选项，可以帮助您使用比例，工具提示，标签，颜色，自定义操作等自定义图表。

# 用法

## 一般配置

这些部分描述了可以在文档中的其他位置应用的常规配置选项。

- [响应式](https://www.chartjs.org/docs/latest/general/responsive.html)定义适用于所有图表的响应式图表选项。
- [设备像素比率](https://www.chartjs.org/docs/latest/general/device-pixel-ratio.html)定义显示像素和渲染像素之间的比率。
- [交互](https://www.chartjs.org/docs/latest/general/interactions/)定义了反映悬停图表元素如何工作的选项。
- [选项](https://www.chartjs.org/docs/latest/general/options.html)可编写脚本和可索引选项语法。
- [颜色](https://www.chartjs.org/docs/latest/general/colors.html)定义可接受的颜色值。
- [Font](https://www.chartjs.org/docs/latest/general/fonts.html)定义了各种字体选项。