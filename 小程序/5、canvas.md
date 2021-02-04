js

```js
// pages/food/food.js
Page({

  /**
   * 页面的初始数据
   */
  data: {

  },
  // 把rpx单位转化为支持canvas的px单位
  rpx2px(rpx) {
    const info = wx.getSystemInfoSync()
    return rpx * info.windowWidth / 750
  },
  // 保存至相册
  save() {
    console.log("点击保存了")
    // 1-把画布转化成临时文件
    wx.canvasToTempFilePath({
      x: 0,
      y: 0,
      width: this.rpx2px(632),    // 画布的宽
      height: this.rpx2px(750),   // 画布的高
      destWidth: 1264,
      destHeight: 1500,
      canvasId: 'canvas',
      success(res) {
        // 2-保存图片至相册
        wx.saveImageToPhotosAlbum({
          filePath: res.tempFilePath,
          success(res2) {
            wx.showToast({ title: '保存至相册成功' })
          },
          fail() {
            wx.showToast({ title: '保存失败，稍后再试', icon: 'none' })
          }
        })
      },
      fail() {
        wx.showToast({ title: '保存失败，稍后再试', icon: 'none' })
      }
    })
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function(options) {
    let ctx = wx.createCanvasContext('canvas')
    // 第1步：绘制画布背景色
    ctx.setFillStyle('#FF7A04')
    ctx.fillRect(0, 0, this.rpx2px(632), this.rpx2px(750))

    // 第2步：绘制标题
    ctx.setFontSize(this.rpx2px(52))
    ctx.setFillStyle('#FDFDFD')
    ctx.setTextAlign('center')
    ctx.fillText('1000元礼品券', this.rpx2px(316), this.rpx2px(90))

    // 第3步：绘制礼券卡片（背景图片+文字）
    ctx.drawImage('../../static/joker.png', this.rpx2px(48), this.rpx2px(142), this.rpx2px(536), this.rpx2px(182))
    ctx.setFontSize(this.rpx2px(30))
    ctx.setFillStyle('#FF7A04')
    ctx.setTextAlign('left')
    ctx.fillText('礼券编码：201905251000', this.rpx2px(72), this.rpx2px(200))
    ctx.fillText('截止日期：2019年10月31日', this.rpx2px(72), this.rpx2px(290))

    // 第4步：绘制礼券的使用说明
    const arr = ['使用说明：', '1.请保存图片，凭图片唯一礼券码尽快联系市场', '经理进行兑奖；', '2.补品券以手机绑定的客户编码为准，一张礼品', '券在有效期内只能使用一次，不可叠加使用；', '3.礼品券不能兑换现金、不找零、不能转赠他人、', '不能为他人代付款，抵扣金额不能开具发票；', '4.2019年9月07日12:00之前使用有效。']

    ctx.setFontSize(this.rpx2px(26))
    ctx.setFillStyle('#FFFFFF')
    ctx.setTextAlign('left')
    for (let i = 0; i < arr.length; i++) {
      ctx.fillText(arr[i], this.rpx2px(60), this.rpx2px(390 + 40 * i), this.rpx2px(518))
    }

    // 执行 draw()方法进行绘制
    ctx.draw()
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function() {

  },

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function() {

  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function() {

  },

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function() {

  },

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function() {

  },

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function() {

  },

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function() {

  }
})
```

```css
/* pages/food/food.wxss */

.layer {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.7);
  z-index: 9999;
}

.layer-bill {
  position: fixed;
  top: 50%;
  left: 0;
  width: 100%;
  height: 1052rpx;
  margin-top: -526rpx;
  overflow: hidden;
}

.layer-bill .content {
  margin: 0 auto;
  height: 930rpx;
  width: 632rpx;
  border-radius: 24rpx;
  background: #ff7a04;
  overflow: hidden;
  position: relative;
}

.canvas {
  position: absolute !important;
  top: 0 !important;
  left: 0 !important;
  display: block !important;
  width: 632rpx !important;
  height: 750rpx !important;
  margin: 0 auto;
  background: #ff7a04;
  z-index: 100;
}

.save {
  position: absolute;
  bottom: 60rpx;
  left: 60rpx;
  right: 60rpx;
  color: #ff7a04;
  height: 90prx;
  line-height: 90rpx;
  text-align: center;
  font-size: 32rpx;
  background: #fff;
  border-radius: 45rpx;
}

```

```html
<!--pages/food/food.wxml-->
<view class="layer">
  <view class="layer-bill">
    <view class="content">
      <!-- 画布，注意是 canvas-id，不是 id -->
      <canvas canvas-id="canvas" class="canvas"></canvas>
      <!-- 保存按钮 -->
      <view class="save" bindtap="save">保存至相册</view>
    </view>
  </view>  
</view>  

```

注意点：  在绘制网络图片时必需先将其保存到本地然后在绘制。当我们在加载一个带有图片的页面时，图片都会被暂存到本地，由此我们可以通过**wx.gerImageInfo**接口访问本地的暂存路径来调用drawImage方法绘制图片

```js
// pages/food/food.js
Page({

  /**
   * 页面的初始数据
   */
  data: {

  },
  // 把rpx单位转化为支持canvas的px单位
  rpx2px(rpx) {
    const info = wx.getSystemInfoSync()
    return rpx * info.windowWidth / 750
  },
  // 保存至相册
  save() {
    console.log("点击保存了")
    // 1-把画布转化成临时文件
    wx.canvasToTempFilePath({
      x: 0,
      y: 0,
      width: this.rpx2px(632), // 画布的宽
      height: this.rpx2px(750), // 画布的高
      destWidth: 1264,
      destHeight: 1500,
      canvasId: 'canvas',
      success(res) {
        // 2-保存图片至相册
        wx.saveImageToPhotosAlbum({
          filePath: res.tempFilePath,
          success(res2) {
            wx.showToast({
              title: '保存至相册成功'
            })
          },
          fail() {
            wx.showToast({
              title: '保存失败，稍后再试',
              icon: 'none'
            })
          }
        })
      },
      fail() {
        wx.showToast({
          title: '保存失败，稍后再试',
          icon: 'none'
        })
      }
    })
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function(options) {





    let ctx = wx.createCanvasContext('canvas')
    // 第1步：绘制画布背景色
    // ctx.setFillStyle('#FF7A04')
    // ctx.fillRect(0, 0, this.rpx2px(632), this.rpx2px(750))

    // 第2步：绘制标题
    ctx.setFontSize(this.rpx2px(52))
    ctx.setFillStyle('#FDFDFD')
    ctx.setTextAlign('center')
    ctx.fillText('1000元礼品券', this.rpx2px(316), this.rpx2px(90))
    var that = this;
    wx.downloadFile({
      url: 'https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2572654905.webp', //网络资源
      success(res) {
        // 成功存到临时目录
        console.log(res.tempFilePath)
        // 第3步：绘制礼券卡片（背景图片+文字）
        ctx.drawImage(res.tempFilePath, 0, 0, that.rpx2px(300), that.rpx2px(582))
        ctx.setFontSize(that.rpx2px(30))
        ctx.setFillStyle('#FF7A04')
        ctx.setTextAlign('left')
        ctx.fillText('礼券编码：201905251000', that.rpx2px(72), that.rpx2px(200))
        ctx.fillText('截止日期：2019年10月31日', that.rpx2px(72), that.rpx2px(290))


        // 第4步：绘制礼券的使用说明
        const arr = ['使用说明：', '1.请保存图片，凭图片唯一礼券码尽快联系市场', '经理进行兑奖；', '2.补品券以手机绑定的客户编码为准，一张礼品', '券在有效期内只能使用一次，不可叠加使用；', '3.礼品券不能兑换现金、不找零、不能转赠他人、', '不能为他人代付款，抵扣金额不能开具发票；', '4.2019年9月07日12:00之前使用有效。']

        ctx.setFontSize(that.rpx2px(26))
        ctx.setFillStyle('#FFFFFF')
        ctx.setTextAlign('left')
        for (let i = 0; i < arr.length; i++) {
          ctx.fillText(arr[i], that.rpx2px(60), that.rpx2px(390 + 40 * i), that.rpx2px(518))
        }

        // 执行 draw()方法进行绘制
        ctx.draw()
      }
    })

  },


})
```

### 简介

#### API

| 方法                                                         | 说明                                  |
| ------------------------------------------------------------ | ------------------------------------- |
| [createCanvasContext](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fcreate-canvas-context.html) | 创建 canvas 绘图上下文(指定 canvasId) |
| [createContext](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fcreate-context.html)(不推荐使用) | 创建 canvas 绘图上下文                |
| [drawCanvas](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fdraw-canvas.html)(不推荐使用) | 进行绘图                              |
| [canvasToTempFilePath](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Ftemp-file.html) | 导出图片                              |

#### context 对象的方法列表

颜色，样式，阴影

| 方法                                                         | 说明         |
| ------------------------------------------------------------ | ------------ |
| [setFillStyle](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-fill-style.html) | 设置填充样式 |
| [setStrokeStyle](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-stroke-style.html) | 设置线条样式 |
| [setShadow](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-shadow.html) | 设置阴影     |

#### 渐变

| 方法                                                         | 说明                             |
| ------------------------------------------------------------ | -------------------------------- |
| [createLinearGradient](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fcreate-linear-gradient.html) | 创建一个线性渐变                 |
| [createCircularGradient](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fcreate-circular-gradient.html) | 创建一个圆形渐变                 |
| [addColorStop](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fadd-color-stop.html) | 在渐变中的某一点添加一个颜色变化 |

#### 线条样式

| 方法                                                         | 说明                 |
| ------------------------------------------------------------ | -------------------- |
| [setLineWidth](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-line-width.html) | 设置线条宽度         |
| [setLineCap](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-line-cap.html) | 设置线条端点的样式   |
| [setLineJoin](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-line-join.html) | 设置两线相交处的样式 |
| [setMiterLimit](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-miter-limit.html) | 设置最大倾斜         |

#### 矩形

| 方法                                                         | 说明                                 |
| ------------------------------------------------------------ | ------------------------------------ |
| [rect](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Frect.html) | 创建一个矩形                         |
| [fillRect](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Ffill-rect.html) | 填充一个矩形                         |
| [strokeRect](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fstroke-rect.html) | 画一个矩形（不填充）                 |
| [clearRect](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fclear-rect.html) | 在给定的矩形区域内，清除画布上的像素 |

#### 路径

| 方法                                                         | 说明                                                     |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| [fill](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Ffill.html) | 对当前路径进行填充                                       |
| [stroke](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fstroke.html) | 对当前路径进行描边                                       |
| [beginPath](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fbegin-path.html) | 开始一个路径                                             |
| [closePath](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fclose-path.html) | 关闭一个路径                                             |
| [moveTo](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fmove-to.html) | 把路径移动到画布中的指定点，但不创建线条。               |
| [lineTo](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fline-to.html) | 添加一个新点，然后在画布中创建从该点到最后指定点的线条。 |
| [arc](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Farc.html) | 添加一个弧形路径到当前路径，顺时针绘制。                 |
| [quadraticCurveTo](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fquadratic-curve-to.html) | 创建二次方贝塞尔曲线                                     |
| [bezierCurveTo](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fbezier-curve-to.html) | 创建三次方贝塞尔曲线                                     |

#### 变形

| 方法                                                         | 说明                   |
| ------------------------------------------------------------ | ---------------------- |
| [scale](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fscale.html) | 对横纵坐标进行缩放     |
| [rotate](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Frotate.html) | 对坐标轴进行顺时针旋转 |
| [translate](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Ftranslate.html) | 对坐标原点进行缩放     |

#### 文字

| 方法                                                         | 说明                     |
| ------------------------------------------------------------ | ------------------------ |
| [fillText](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Ffill-text.html) | 在画布上绘制被填充的文本 |
| [setFontSize](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-font-size.html) | 设置字体大小             |
| [setTextBaseline](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-text-baseline.html) | 设置字体基准线           |
| [setTextAlign](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-text-align.html) | 设置字体对齐方式         |

![img](/Users/liujiang/Documents/Typora/imgs/15570424-4CD9-474E-A96B-AD0CB457036E.png)

#### 图片

| 方法                                                         | 说明             |
| ------------------------------------------------------------ | ---------------- |
| [drawImage](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fdraw-image.html) | 在画布上绘制图像 |

#### 混合

| 方法                                                         | 说明               |
| ------------------------------------------------------------ | ------------------ |
| [setGlobalAlpha](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fset-global-alpha.html) | 设置全局画笔透明度 |

#### 其他

| 方法                                                         | 说明                              |
| ------------------------------------------------------------ | --------------------------------- |
| [save](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fsave-restore.html%23save) | 保存当前绘图上下文                |
| [restore](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fsave-restore.html%23restore) | 恢复之前保过的绘图上下文          |
| [draw](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fdraw.html) | 进行绘图                          |
| [getActions](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fget-actions.html)(不推荐使用) | 获取当前`context`上存储的绘图动作 |
| [clearActions](https://link.jianshu.com?t=https%3A%2F%2Fmp.weixin.qq.com%2Fdebug%2Fwxadoc%2Fdev%2Fapi%2Fcanvas%2Fclear-actions.html)(不推荐使用) | 清空当前的存储绘图动作            |

### intro

WXML：

```objectivec
<canvas canvas-id="myCanvas" style="border:1px solid">
```

JS：（我们在接下来的例子中会将 JS 放在 onLoad 中）

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setFillStyle('red')
ctx.fillRect(10, 10, 150, 75)
ctx.draw()
```

步骤：

```csharp
第一步：创建一个 Canvas 绘图上下文
const ctx = wx.createCanvasContext('myCanvas')

第二步：使用 Canvas 绘图上下文进行绘图描述
//设置绘图上下文的填充色为红色：
ctx.setFillStyle('red')
//用 fillRect(x, y, width, height) 方法画一个矩形，填充为刚刚设置的红色：
ctx.fillRect(10, 10, 150, 75)

第三步：画图
ctx.draw()
```

### coordinates

canvas 是在一个二维的网格当中。
 左上角的坐标为(0, 0)。
 方法 fillRect(0, 0, 150, 75)。
 它的含义为：从左上角(0, 0)开始，画一个150 x 75px 的矩形。

demo:
 把手指放到 canvas 中，就会在下边显示出触碰点的坐标：

```jsx
<canvas canvas-id="myCanvas"
  style="margin: 5px; border:1px solid #d3d3d3;"
  bindtouchstart="start"
  bindtouchmove="move"
  bindtouchend="end"/>

<view hidden="{{hidden}}">
  Coordinates: ({{x}}, {{y}})
</view>

Page({
  data: {
    x: 0,
    y: 0,
    hidden: true
  },
  start: function(e) {
    this.setData({
      hidden: false,
      x: e.touches[0].x,
      y: e.touches[0].y
    })
  },
  move: function(e) {
    this.setData({
      x: e.touches[0].x,
      y: e.touches[0].y
    })
  },
  end: function(e) {
    this.setData({
      hidden: true
    })
  }
})
```

### gradient

渐变
 渐变能用于填充一个矩形，圆，线，文字等。填充色可以不固定位固定的一种颜色。
 两种颜色渐变的方式：
 •   createLinearGradient(x, y, x1, y1) - 创建一个线性的渐变
 •   createCircularGradient(x, y, r) - 创建一个从圆心开始的渐变
 一旦我们创建了一个渐变对象，我们必须添加两个颜色渐变点。
 addColorStop(position, color) 方法用于指定颜色渐变点的位置和颜色，位置必须位于0到1之间。
 可以用setFillStyle() 和 setStrokeStyle() 方法设置渐变，然后进行画图描述。

使用 createLinearGradient()

```csharp
const ctx = wx.createCanvasContext('myCanvas')
const grd = ctx.createLinearGradient(0,0,200,0)
grd.addColorStop(0,'red')
grd.addColorStop(1,'white')

ctx.setFillStyle(grd)
ctx.fillRect(10,10,150,80)
ctx.draw()
```

使用 createCircularGradient()

```csharp
const ctx = wx.createCanvasContext('myCanvas')
const grd = ctx.createCircularGradient(75,50,50)
grd.addColorStop(0,'red')
grd.addColorStop(1,'white')

ctx.setFillStyle(grd)
ctx.fillRect(10,10,150,80)
ctx.draw()
```

### reference

#### API接口

| 方法                 | 说明                                  |
| -------------------- | ------------------------------------- |
| createCanvasContext  | 创建 canvas 绘图上下文(指定 canvasId) |
| canvasToTempFilePath | 导出图片                              |

#### context 对象的方法列表:颜色，样式，阴影

| 方法           | 说明         |
| -------------- | ------------ |
| setFillStyle   | 设置填充样式 |
| setStrokeStyle | 设置线条样式 |
| setShadow      | 设置阴影     |

#### 渐变

| 方法                   | 说明                             |
| ---------------------- | -------------------------------- |
| createLinearGradient   | 创建一个线性渐变                 |
| createCircularGradient | 创建一个圆形渐变                 |
| addColorStop           | 在渐变中的某一点添加一个颜色变化 |

#### 线条样式

| 方法          | 说明                 |
| ------------- | -------------------- |
| setLineWidth  | 设置线条宽度         |
| setLineCap    | 设置线条端点的样式   |
| setLineJoin   | 设置两线相交处的样式 |
| setMiterLimit | 设置最大倾斜         |

#### 矩形

| 方法       | 说明                                 |
| ---------- | ------------------------------------ |
| rect       | 创建一个矩形                         |
| fillRect   | 填充一个矩形                         |
| strokeRect | 画一个矩形（不填充）                 |
| clearRect  | 在给定的矩形区域内，清除画布上的像素 |

#### 路径

| 方法             | 说明                                                     |
| ---------------- | -------------------------------------------------------- |
| fill             | 对当前路径进行填充                                       |
| stroke           | 对当前路径进行描边                                       |
| beginPath        | 开始一个路径                                             |
| closePath        | 关闭一个路径                                             |
| moveTo           | 把路径移动到画布中的指定点，但不创建线条。               |
| lineTo           | 添加一个新点，然后在画布中创建从该点到最后指定点的线条。 |
| arc              | 添加一个弧形路径到当前路径，顺时针绘制。                 |
| quadraticCurveTo | 创建二次方贝塞尔曲线                                     |
| bezierCurveTo    | 创建三次方贝塞尔曲线                                     |

#### 变形

| 方法      | 说明                   |
| --------- | ---------------------- |
| scale     | 对横纵坐标进行缩放     |
| rotate    | 对坐标轴进行顺时针旋转 |
| translate | 对坐标原点进行缩放     |

#### 文字

| 方法            | 说明                     |
| --------------- | ------------------------ |
| fillText        | 在画布上绘制被填充的文本 |
| setFontSize     | 设置字体大小             |
| setTextBaseline | 设置字体基准线           |
| setTextAlign    | 设置字体对齐方式         |

#### 图片

| 方法      | 说明             |
| --------- | ---------------- |
| drawImage | 在画布上绘制图像 |

#### 混合

| 方法           | 说明               |
| -------------- | ------------------ |
| setGlobalAlpha | 设置全局画笔透明度 |

#### 其它

| 方法                     | 说明                            |
| ------------------------ | ------------------------------- |
| save                     | 保存当前绘图上下文              |
| restore                  | 恢复之前保过的绘图上下文        |
| draw                     | 进行绘图                        |
| getActions(不推荐使用)   | 获取当前context上存储的绘图动作 |
| clearActions(不推荐使用) | 清空当前的存储绘图动作          |

### color

用以下几种方式来表示 canvas 中使用的颜色：
 •   RGB 颜色： 如 'rgb(255, 0, 0)'
 •   RGBA 颜色：如 'rgba(255, 0, 0, 0.3)'
 •   16 进制颜色： 如 '#FF0000'
 •   预定义的颜色： 如 'red'

### wx.canvasToTempFilePath(OBJECT, this)

把当前画布指定区域的内容导出生成指定大小的图片，并返回文件路径;

| 参数       | 类型     | 必填 | 说明                                                   | 最低版本 |
| ---------- | -------- | ---- | ------------------------------------------------------ | -------- |
| x          | Number   | 否   | 画布x轴起点（默认0）                                   | 1.2.0    |
| y          | Number   | 否   | 画布y轴起点（默认0）                                   | 1.2.0    |
| width      | Number   | 否   | 画布宽度（默认为canvas宽度-x）                         | 1.2.0    |
| height     | Number   | 否   | 画布高度（默认为canvas高度-y）                         | 1.2.0    |
| destWidth  | Number   | 否   | 输出图片宽度（默认为width）                            | 1.2.0    |
| destHeight | Number   | 否   | 输出图片高度（默认为height）                           | 1.2.0    |
| canvasId   | String   | 是   | 画布标识，传入 <canvas/> 的 canvas-id                  |          |
| fileType   | String   | 否   | 目标文件的类型，只支持 'jpg' 或 'png'。默认为 'png'    | 1.7.0    |
| quality    | Number   | 否   | 图片的质量，取值范围为 (0, 1]，不在范围内时当作1.0处理 | 1.7.0    |
| success    | Function | 否   | 接口调用成功的回调函数                                 |          |
| fail       | Function | 否   | 接口调用失败的回调函数                                 |          |
| complete   | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）       |          |

```css
wx.canvasToTempFilePath({
  x: 100,
  y: 200,
  width: 50,
  height: 50,
  destWidth: 100,
  destHeight: 100,
  canvasId: 'myCanvas',
  success: function(res) {
    console.log(res.tempFilePath)
  } 
```

### wx.canvasGetImageData(OBJECT)

返回一个数组，用来描述 canvas 区域隐含的像素数据
 OBJECT参数说明：

| 参数     | 类型     | 必填 | 说明                                             |
| -------- | -------- | ---- | ------------------------------------------------ |
| canvasId | String   | 是   | 画布标识，传入 <canvas /> 的 canvas-id           |
| x        | Number   | 是   | 将要被提取的图像数据矩形区域的左上角 x 坐标      |
| y        | Number   | 是   | 将要被提取的图像数据矩形区域的左上角 y 坐标      |
| width    | Number   | 是   | 将要被提取的图像数据矩形区域的宽度               |
| height   | Number   | 是   | 将要被提取的图像数据矩形区域的高度               |
| success  | Function | 否   | 接口调用成功的回调函数                           |
| fail     | Function | 否   | 接口调用失败的回调函数                           |
| complete | Function | 否   | 接口调用结束的回调函数（调用成功、失败都会执行） |

示例代码：

```css
wx.canvasGetImageData({
  canvasId: 'myCanvas',
  x: 0,
  y: 0,
  width: 100,
  height: 100,
  success(res) {
    console.log(res.width) // 100
    console.log(res.height) // 100
    console.log(res.data instanceof Uint8ClampedArray) // true
    console.log(res.data.length) // 100 * 100 * 4
  }
})
```

### wx.canvasPutImageData(OBJECT)

将像素数据绘制到画布的方法

| 参数     | 类型              | 必填 | 说明                                                   |
| -------- | ----------------- | ---- | ------------------------------------------------------ |
| canvasId | String            | 是   | 画布标识，传入 <canvas /> 的 canvas-id                 |
| data     | Uint8ClampedArray | 是   | 图像像素点数据，一维数组，每四项表示一个像素点的rgba   |
| x        | Number            | 是   | 源图像数据在目标画布中的位置偏移量（x 轴方向的偏移量） |
| y        | Number            | 是   | 源图像数据在目标画布中的位置偏移量（y 轴方向的偏移量） |
| width    | Number            | 是   | 源图像数据矩形区域的宽度                               |
| height   | Number            | 否   | 源图像数据矩形区域的高度                               |
| success  | Function          | 否   | 接口调用成功的回调函数                                 |
| fail     | Function          | 否   | 接口调用失败的回调函数                                 |
| complete | Function          | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）       |

示例代码：

```kotlin
const data = new Uint8ClampedArray([255, 0, 0, 1])
wx.canvasPutImageData({
  canvasId: 'myCanvas',
  x: 0,
  y: 0,
  width: 1,
  data: data,
  success(res) {}
})
```

### canvasContext.setFillStyle

设置填充色。

如果没有设置 fillStyle，默认颜色为 black;

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setFillStyle('red')
ctx.fillRect(10, 10, 150, 75)
ctx.draw()
```

### canvasContext.setStrokeStyle

设置边框颜色。
 Tip: 如果没有设置 fillStyle，默认颜色为 black。

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setStrokeStyle('red')
ctx.strokeRect(10, 10, 150, 75)
ctx.draw()
```

### canvasContext.setShadow

设置阴影样式。
 Tip: 如果没有设置，offsetX 默认值为0， offsetY 默认值为0， blur 默认值为0，color 默认值为 black。

| 参数    | 类型   | 范围                           | 定义                           |
| ------- | ------ | ------------------------------ | ------------------------------ |
| offsetX | Number | 阴影相对于形状在水平方向的偏移 |                                |
| offsetY | Number | 阴影相对于形状在竖直方向的偏移 |                                |
| blur    | Number | 0~100                          | 阴影的模糊级别，数值越大越模糊 |
| color   | Color  |                                | 阴影的颜色                     |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setFillStyle('red')
ctx.setShadow(10, 50, 50, 'blue')
ctx.fillRect(10, 10, 150, 75)
ctx.draw()
```

canvasContext.shadowBlur
 设置阴影的模糊级别

```csharp
canvasContext.shadowBlur = value
```

canvasContext.shadowColor
 设置阴影的颜色

```csharp
canvasContext.shadowColor = value
```

canvasContext.shadowOffsetX
 设置阴影相对于形状在水平方向的偏移

```csharp
canvasContext.shadowOffsetX = value
```

canvasContext.shadowOffsetY
 设置阴影相对于形状在竖直方向的偏移

```csharp
canvasContext.shadowOffsetY = value
```

### canvasContext.createLinearGradient

创建一个线性的渐变颜色。
 Tip: 需要使用 addColorStop() 来指定渐变点，至少要两个。

| 参数 | 类型   | 定义        |
| ---- | ------ | ----------- |
| x0   | Number | 起点的x坐标 |
| y0   | Number | 起点的y坐标 |
| x1   | Number | 终点的x坐标 |
| y1   | Number | 终点的y坐标 |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

// Create linear gradient
const grd = ctx.createLinearGradient(0, 0, 200, 0)
grd.addColorStop(0, 'red')
grd.addColorStop(1, 'white')

// Fill with gradient
ctx.setFillStyle(grd)
ctx.fillRect(10, 10, 150, 80)
ctx.draw()
```

### canvasContext.createCircularGradient

创建一个圆形的渐变颜色。
 Tip: 起点在圆心，终点在圆环。
 Tip: 需要使用 addColorStop() 来指定渐变点，至少要两个。

| 参数 | 类型   | 定义        |
| ---- | ------ | ----------- |
| x    | Number | 圆心的x坐标 |
| y    | Number | 圆心的y坐标 |
| r    | Number | 圆的半径    |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

// Create circular gradient
const grd = ctx.createCircularGradient(75, 50, 50)
grd.addColorStop(0, 'red')
grd.addColorStop(1, 'white')

// Fill with gradient
ctx.setFillStyle(grd)
ctx.fillRect(10, 10, 150, 80)
ctx.draw()
```

### canvasContext.addColorStop

定义
 创建一个颜色的渐变点。
 Tip: 小于最小 stop 的部分会按最小 stop 的 color 来渲染，大于最大 stop 的部分会按最大 stop 的 color 来渲染。
 Tip: 需要使用 addColorStop() 来指定渐变点，至少要两个。

eg:多彩

```csharp
const ctx = wx.createCanvasContext('myCanvas')

// Create circular gradient
const grd = ctx.createLinearGradient(30, 10, 120, 10)
grd.addColorStop(0, 'red')
grd.addColorStop(0.16, 'orange')
grd.addColorStop(0.33, 'yellow')
grd.addColorStop(0.5, 'green')
grd.addColorStop(0.66, 'cyan')
grd.addColorStop(0.83, 'blue')
grd.addColorStop(1, 'purple')

// Fill with gradient
ctx.setFillStyle(grd)
ctx.fillRect(10, 10, 150, 80)
ctx.draw()
```

### canvasContext.setLineWidth

设置线条的宽度。

eg:不同宽度的线条

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.beginPath()
ctx.moveTo(10, 10)
ctx.lineTo(150, 10)
ctx.stroke()

ctx.beginPath()
ctx.setLineWidth(5)
ctx.moveTo(10, 30)
ctx.lineTo(150, 30)
ctx.stroke()

ctx.beginPath()
ctx.setLineWidth(10)
ctx.moveTo(10, 50)
ctx.lineTo(150, 50)
ctx.stroke()

ctx.beginPath()
ctx.setLineWidth(15)
ctx.moveTo(10, 70)
ctx.lineTo(150, 70)
ctx.stroke()

ctx.draw()
```

### canvasContext.setLineCap

定义
 设置线条的端点样式。

| 参数    | 类型   | 范围                      | 说明               |
| ------- | ------ | ------------------------- | ------------------ |
| lineCap | String | 'butt'、'round'、'square' | 线条的结束端点样式 |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.beginPath()
ctx.moveTo(10, 10)
ctx.lineTo(150, 10)
ctx.stroke()

ctx.beginPath()
ctx.setLineCap('butt')
ctx.setLineWidth(10)
ctx.moveTo(10, 30)
ctx.lineTo(150, 30)
ctx.stroke()

ctx.beginPath()
ctx.setLineCap('round')
ctx.setLineWidth(10)
ctx.moveTo(10, 50)
ctx.lineTo(150, 50)
ctx.stroke()

ctx.beginPath()
ctx.setLineCap('square')
ctx.setLineWidth(10)
ctx.moveTo(10, 70)
ctx.lineTo(150, 70)
ctx.stroke()

ctx.draw()
```

### canvasContext.setLineJoin

定义
 设置线条的交点样式。

| 参数     | 类型   | 范围                      | 说明               |
| -------- | ------ | ------------------------- | ------------------ |
| lineJoin | String | 'bevel'、'round'、'miter' | 线条的结束交点样式 |

语法：

```css
canvasContext.setLineJoin(lineJoin)
```

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.beginPath()
ctx.moveTo(10, 10)
ctx.lineTo(100, 50)
ctx.lineTo(10, 90)
ctx.stroke()

ctx.beginPath()
ctx.setLineJoin('bevel')
ctx.setLineWidth(10)
ctx.moveTo(50, 10)
ctx.lineTo(140, 50)
ctx.lineTo(50, 90)
ctx.stroke()

ctx.beginPath()
ctx.setLineJoin('round')
ctx.setLineWidth(10)
ctx.moveTo(90, 10)
ctx.lineTo(180, 50)
ctx.lineTo(90, 90)
ctx.stroke()

ctx.beginPath()
ctx.setLineJoin('miter')
ctx.setLineWidth(10)
ctx.moveTo(130, 10)
ctx.lineTo(220, 50)
ctx.lineTo(130, 90)
ctx.stroke()

ctx.draw()
```

### canvasContext.setLineDash

设置线条的宽度。

| 参数    | 类型   | 说明                                                 |
| ------- | ------ | ---------------------------------------------------- |
| pattern | Array  | 一组描述交替绘制线段和间距（坐标空间单位）长度的数字 |
| offset  | Number | 虚线偏移量                                           |

eg:虚线

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setLineDash([10, 20], 5);

ctx.beginPath();
ctx.moveTo(0,100);
ctx.lineTo(400, 100);
ctx.stroke();

ctx.draw()
```

### canvasContext.setMiterLimit

定义
 设置最大斜接长度，斜接长度指的是在两条线交汇处内角和外角之间的距离。 当 setLineJoin() 为 miter 时才有效。超过最大倾斜长度的，连接处将以 lineJoin 为 bevel 来显示
 语法

```css
canvasContext.setMiterLimit(miterLimit)
```

| 参数       | 类型   | 说明         |
| ---------- | ------ | ------------ |
| miterLimit | Number | 最大斜接长度 |

eg:线的倾斜角度

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.beginPath()
ctx.setLineWidth(10)
ctx.setLineJoin('miter')
ctx.setMiterLimit(1)
ctx.moveTo(10, 10)
ctx.lineTo(100, 50)
ctx.lineTo(10, 90)
ctx.stroke()

ctx.beginPath()
ctx.setLineWidth(10)
ctx.setLineJoin('miter')
ctx.setMiterLimit(2)
ctx.moveTo(50, 10)
ctx.lineTo(140, 50)
ctx.lineTo(50, 90)
ctx.stroke()

ctx.beginPath()
ctx.setLineWidth(10)
ctx.setLineJoin('miter')
ctx.setMiterLimit(3)
ctx.moveTo(90, 10)
ctx.lineTo(180, 50)
ctx.lineTo(90, 90)
ctx.stroke()

ctx.beginPath()
ctx.setLineWidth(10)
ctx.setLineJoin('miter')
ctx.setMiterLimit(4)
ctx.moveTo(130, 10)
ctx.lineTo(220, 50)
ctx.lineTo(130, 90)
ctx.stroke()

ctx.draw()
```

### canvasContext.rect

定义
 创建一个矩形。
 Tip: 用 fill() 或者 stroke() 方法将矩形真正的画到 canvas 中。

| 参数   | 类型   | 说明                  |
| ------ | ------ | --------------------- |
| x      | Number | 矩形路径左上角的x坐标 |
| y      | Number | 矩形路径左上角的y坐标 |
| width  | Number | 矩形路径的宽度        |
| height | Number | 矩形路径的高度        |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.rect(10, 10, 150, 75)
ctx.setFillStyle('red')
ctx.fill()
ctx.draw()
```

### canvasContext.fillRect

定义
 填充一个矩形。
 Tip: 用 setFillStyle() 设置矩形的填充色，如果没设置默认是黑色

| 参数   | 类型   | 说明                  |
| ------ | ------ | --------------------- |
| x      | Number | 矩形路径左上角的x坐标 |
| y      | Number | 矩形路径左上角的y坐标 |
| width  | Number | 矩形路径的宽度        |
| height | Number | 矩形路径的高度        |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setFillStyle('red')
ctx.fillRect(10, 10, 150, 75)
ctx.draw()
```

### canvasContext.strokeRect

定义
 画一个矩形(非填充)。
 Tip: 用 setFillStroke() 设置矩形线条的颜色，如果没设置默认是黑色。

| 参数   | 类型   | 说明                  |
| ------ | ------ | --------------------- |
| x      | Number | 矩形路径左上角的x坐标 |
| y      | Number | 矩形路径左上角的y坐标 |
| width  | Number | 矩形路径的宽度        |
| height | Number | 矩形路径的高度        |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setStrokeStyle('red')
ctx.strokeRect(10, 10, 150, 75)
ctx.draw()
```

### canvasContext.clearRect

定义
 清除画布上在该矩形区域内的内容。

Tip: clearRect 并非画一个白色的矩形在地址区域，而是清空

| 参数   | 类型   | 说明                  |
| ------ | ------ | --------------------- |
| x      | Number | 矩形路径左上角的x坐标 |
| y      | Number | 矩形路径左上角的y坐标 |
| width  | Number | 矩形路径的宽度        |
| height | Number | 矩形路径的高度        |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.setFillStyle('red')
ctx.fillRect(0, 0, 150, 200)
ctx.setFillStyle('blue')
ctx.fillRect(150, 0, 150, 200)
ctx.clearRect(10, 10, 150, 75)
ctx.draw()
```

### canvasContext.fill

定义
 对当前路径中的内容进行填充。默认的填充色为黑色。
 Tip: 如果当前路径没有闭合，fill() 方法会将起点和终点进行连接，然后填充，详情见例一。
 Tip: fill() 填充的的路径是从 beginPath() 开始计算，但是不会将 fillRect() 包含进去。

eg:填充三角形

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.moveTo(10, 10)
ctx.lineTo(100, 10)
ctx.lineTo(100, 100)
ctx.fill()
ctx.draw()
```

eg:填充矩形

```csharp
const ctx = wx.createCanvasContext('myCanvas')
// begin path
ctx.rect(10, 10, 100, 30)
ctx.setFillStyle('yellow')
ctx.fill()

// begin another path
ctx.beginPath()
ctx.rect(10, 40, 100, 30)

// only fill this rect, not in current path
ctx.setFillStyle('blue')
ctx.fillRect(10, 70, 100, 30)

ctx.rect(10, 100, 100, 30)

// it will fill current path
ctx.setFillStyle('red')
ctx.fill()
ctx.draw()
```

### canvasContext.stroke

定义
 画出当前路径的边框。默认颜色色为黑色。
 Tip: stroke() 描绘的的路径是从 beginPath() 开始计算，但是不会将 strokeRect() 包含进去

eg:画路径（线条）

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.moveTo(10, 10)
ctx.lineTo(100, 10)
ctx.lineTo(100, 100)
ctx.stroke()
ctx.draw()
```

eg2:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
// begin path
ctx.rect(10, 10, 100, 30)
ctx.setStrokeStyle('yellow')
ctx.stroke()

// begin another path
ctx.beginPath()
ctx.rect(10, 40, 100, 30)

// only stoke this rect, not in current path
ctx.setStrokeStyle('blue')
ctx.strokeRect(10, 70, 100, 30)

ctx.rect(10, 100, 100, 30)

// it will stroke current path
ctx.setStrokeStyle('red')
ctx.stroke()
ctx.draw()
```

### canvasContext.beginPath

定义
 开始创建一个路径，需要调用fill或者stroke才会使用路径进行填充或描边。
 Tip: 在最开始的时候相当于调用了一次 beginPath()。
 Tip: 同一个路径内的多次setFillStyle()、setStrokeStyle()、setLineWidth()等设置，以最后一次设置为准

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
// begin path
ctx.rect(10, 10, 100, 30)
ctx.setFillStyle('yellow')
ctx.fill()

// begin another path
ctx.beginPath()
ctx.rect(10, 40, 100, 30)

// only fill this rect, not in current path
ctx.setFillStyle('blue')
ctx.fillRect(10, 70, 100, 30)

ctx.rect(10, 100, 100, 30)

// it will fill current path
ctx.setFillStyle('red')
ctx.fill()
ctx.draw()
```

### canvasContext.closePath

定义
 关闭一个路径
 Tip: 关闭路径会连接起点和终点。
 Tip: 如果关闭路径后没有调用 fill() 或者 stroke() 并开启了新的路径，那之前的路径将不会被渲染;

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.moveTo(10, 10)
ctx.lineTo(100, 10)
ctx.lineTo(100, 100)
ctx.closePath()
ctx.stroke()
ctx.draw()
```

eg2:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
// begin path
ctx.rect(10, 10, 100, 30)
ctx.closePath()

// begin another path
ctx.beginPath()
ctx.rect(10, 40, 100, 30)

// only fill this rect, not in current path
ctx.setFillStyle('blue')
ctx.fillRect(10, 70, 100, 30)

ctx.rect(10, 100, 100, 30)

// it will fill current path
ctx.setFillStyle('red')
ctx.fill()
ctx.draw()
```

### canvasContext.moveTo

定义
 把路径移动到画布中的指定点，不创建线条。
 Tip: 用 stroke() 方法来画线条

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.moveTo(10, 10)
ctx.lineTo(100, 10)

ctx.moveTo(10, 50)
ctx.lineTo(100, 50)
ctx.stroke()
ctx.draw()
```

### canvasContext.lineTo

定义
 lineTo 方法增加一个新点，然后创建一条从上次指定点到目标点的线。
 Tip: 用 stroke() 方法来画线条

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
ctx.moveTo(10, 10)
ctx.rect(10, 10, 100, 50)
ctx.lineTo(110, 60)
ctx.stroke()
ctx.draw()
```

### canvasContext.arc

定义
 画一条弧线。
 Tip: 创建一个圆可以用 arc() 方法指定其实弧度为0，终止弧度为 2 * Math.PI。
 Tip: 用 stroke() 或者 fill() 方法来在 canvas 中画弧线。

| 参数             | 类型    | 说明                                                         |
| ---------------- | ------- | ------------------------------------------------------------ |
| x                | Number  | 圆的x坐标                                                    |
| y                | Number  | 圆的y坐标                                                    |
| r                | Number  | 圆的半径                                                     |
| sAngle           | Number  | 起始弧度，单位弧度（在3点钟方向）                            |
| eAngle           | Number  | 终止弧度                                                     |
| counterclockwise | Boolean | 可选。指定弧度的方向是逆时针还是顺时针。默认是false，即顺时针。 |

eg:圆

```jsx
const ctx = wx.createCanvasContext('myCanvas')

// Draw coordinates
ctx.arc(100, 75, 50, 0, 2 * Math.PI)
ctx.setFillStyle('#EEEEEE')
ctx.fill()

ctx.beginPath()
ctx.moveTo(40, 75)
ctx.lineTo(160, 75)
ctx.moveTo(100, 15)
ctx.lineTo(100, 135)
ctx.setStrokeStyle('#AAAAAA')
ctx.stroke()

ctx.setFontSize(12)
ctx.setFillStyle('black')
ctx.fillText('0', 165, 78)
ctx.fillText('0.5*PI', 83, 145)
ctx.fillText('1*PI', 15, 78)
ctx.fillText('1.5*PI', 83, 10)

// Draw points
ctx.beginPath()
ctx.arc(100, 75, 2, 0, 2 * Math.PI)
ctx.setFillStyle('lightgreen')
ctx.fill()

ctx.beginPath()
ctx.arc(100, 25, 2, 0, 2 * Math.PI)
ctx.setFillStyle('blue')
ctx.fill()

ctx.beginPath()
ctx.arc(150, 75, 2, 0, 2 * Math.PI)
ctx.setFillStyle('red')
ctx.fill()

// Draw arc
ctx.beginPath()
ctx.arc(100, 75, 50, 0, 1.5 * Math.PI)
ctx.setStrokeStyle('#333333')
ctx.stroke()

ctx.draw()
```

### canvasContext.bezierCurveTo

定义
 创建三次方贝塞尔曲线路径。
 Tip: 曲线的起始点为路径中前一个点。

| 参数 | 类型   | 说明                        |
| ---- | ------ | --------------------------- |
| cp1x | Number | 第一个贝塞尔控制点的 x 坐标 |
| cp1y | Number | 第一个贝塞尔控制点的 y 坐标 |
| cp2x | Number | 第二个贝塞尔控制点的 x 坐标 |
| cp2y | Number | 第二个贝塞尔控制点的 y 坐标 |
| x    | Number | 结束点的 x 坐标             |
| y    | Number | 结束点的 y 坐标             |

eg:不规则曲线

```jsx
const ctx = wx.createCanvasContext('myCanvas')

// Draw points
ctx.beginPath()
ctx.arc(20, 20, 2, 0, 2 * Math.PI)
ctx.setFillStyle('red')
ctx.fill()

ctx.beginPath()
ctx.arc(200, 20, 2, 0, 2 * Math.PI)
ctx.setFillStyle('lightgreen')
ctx.fill()

ctx.beginPath()
ctx.arc(20, 100, 2, 0, 2 * Math.PI)
ctx.arc(200, 100, 2, 0, 2 * Math.PI)
ctx.setFillStyle('blue')
ctx.fill()

ctx.setFillStyle('black')
ctx.setFontSize(12)

// Draw guides
ctx.beginPath()
ctx.moveTo(20, 20)
ctx.lineTo(20, 100)
ctx.lineTo(150, 75)

ctx.moveTo(200, 20)
ctx.lineTo(200, 100)
ctx.lineTo(70, 75)
ctx.setStrokeStyle('#AAAAAA')
ctx.stroke()

// Draw quadratic curve
ctx.beginPath()
ctx.moveTo(20, 20)
ctx.bezierCurveTo(20, 100, 200, 100, 200, 20)
ctx.setStrokeStyle('black')
ctx.stroke()

ctx.draw()
```

### canvasContext.quadraticCurveTo

定义
 创建二次贝塞尔曲线路径。
 Tip: 曲线的起始点为路径中前一个点。

| 参数 | 类型   | 说明                |
| ---- | ------ | ------------------- |
| cpx  | Number | 贝塞尔控制点的x坐标 |
| cpy  | Number | 贝塞尔控制点的y坐标 |
| x    | Number | 结束点的x坐标       |
| y    | Number | 结束点的y坐标       |

eg:

```jsx
const ctx = wx.createCanvasContext('myCanvas')

// Draw points
ctx.beginPath()
ctx.arc(20, 20, 2, 0, 2 * Math.PI)
ctx.setFillStyle('red')
ctx.fill()

ctx.beginPath()
ctx.arc(200, 20, 2, 0, 2 * Math.PI)
ctx.setFillStyle('lightgreen')
ctx.fill()

ctx.beginPath()
ctx.arc(20, 100, 2, 0, 2 * Math.PI)
ctx.setFillStyle('blue')
ctx.fill()

ctx.setFillStyle('black')
ctx.setFontSize(12)

// Draw guides
ctx.beginPath()
ctx.moveTo(20, 20)
ctx.lineTo(20, 100)
ctx.lineTo(200, 20)
ctx.setStrokeStyle('#AAAAAA')
ctx.stroke()

// Draw quadratic curve
ctx.beginPath()
ctx.moveTo(20, 20)
ctx.quadraticCurveTo(20, 100, 200, 20)
ctx.setStrokeStyle('black')
ctx.stroke()

ctx.draw()
```

### canvasContext.scale

定义
 在调用scale方法后，之后创建的路径其横纵坐标会被缩放。多次调用scale，倍数会相乘。

| 参数        | 类型   | 说明                                               |
| ----------- | ------ | -------------------------------------------------- |
| scaleWidth  | Number | 横坐标缩放的倍数 (1 = 100%，0.5 = 50%，2 = 200%)   |
| scaleHeight | Number | 纵坐标轴缩放的倍数 (1 = 100%，0.5 = 50%，2 = 200%) |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.strokeRect(10, 10, 25, 15)
ctx.scale(2, 2)
ctx.strokeRect(10, 10, 25, 15)
ctx.scale(2, 2)
ctx.strokeRect(10, 10, 25, 15)

ctx.draw()
```

### canvasContext.rotate

定义
 以原点为中心，原点可以用 translate方法修改。顺时针旋转当前坐标轴。多次调用rotate，旋转的角度会叠加。

| 参数   | 类型   | 说明                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| rotate | Number | 旋转角度，以弧度计(degrees * Math.PI/180；degrees范围为0~360) |

eg:

```jsx
const ctx = wx.createCanvasContext('myCanvas')

ctx.strokeRect(100, 10, 150, 100)
ctx.rotate(20 * Math.PI / 180)
ctx.strokeRect(100, 10, 150, 100)
ctx.rotate(20 * Math.PI / 180)
ctx.strokeRect(100, 10, 150, 100)

ctx.draw()
```

### canvasContext.translate

定义
 对当前坐标系的原点(0, 0)进行变换，默认的坐标系原点为页面左上角。

| 参数 | 类型   | 说明           |
| ---- | ------ | -------------- |
| x    | Number | 水平坐标平移量 |
| y    | Number | 竖直坐标平移量 |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.strokeRect(10, 10, 150, 100)
ctx.translate(20, 20)
ctx.strokeRect(10, 10, 150, 100)
ctx.translate(20, 20)
ctx.strokeRect(10, 10, 150, 100)

ctx.draw()
```

### canvasContext.clip

clip() 方法从原始画布中剪切任意形状和尺寸。一旦剪切了某个区域，则所有之后的绘图都会被限制在被剪切的区域内（不能访问画布上的其他区域）。可以在使用 clip() 方法前通过使用 save() 方法对当前画布区域进行保存，并在以后的任意时间对其进行恢复（通过 restore() 方法）。

eg:裁剪图片

```jsx
const ctx = wx.createCanvasContext('myCanvas')

wx.downloadFile({
  url: 'http://is5.mzstatic.com/image/thumb/Purple128/v4/75/3b/90/753b907c-b7fb-5877-215a-759bd73691a4/source/50x50bb.jpg',
  success: function(res) {
      ctx.save()
      ctx.beginPath()
      ctx.arc(50, 50, 25, 0, 2*Math.PI)
      ctx.clip()
      ctx.drawImage(res.tempFilePath, 25, 25)
      ctx.restore()
      ctx.draw()
  }
})
```

### canvasContext.setFontSize

定义
 设置字体的字号。

| 参数     | 类型   | 说明       |
| -------- | ------ | ---------- |
| fontSize | Number | 字体的字号 |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setFontSize(20)
ctx.fillText('20', 20, 20)
ctx.setFontSize(30)
ctx.fillText('30', 40, 40)
ctx.setFontSize(40)
ctx.fillText('40', 60, 60)
ctx.setFontSize(50)
ctx.fillText('50', 90, 90)

ctx.draw()
```

### canvasContext.fillText

定义
 在画布上绘制被填充的文本。

| 参数     | 类型   | 说明                      |
| -------- | ------ | ------------------------- |
| text     | String | 在画布上输出的文本        |
| x        | Number | 绘制文本的左上角x坐标位置 |
| y        | Number | 绘制文本的左上角y坐标位置 |
| maxWidth | Number | 需要绘制的最大宽度，可选  |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setFontSize(20)
ctx.fillText('Hello', 20, 20)
ctx.fillText('MINA', 100, 100)

ctx.draw()
```

### canvasContext.setTextAlign

用于设置文字的对齐

canvasContext.setTextAlign(align)

| 参数  | 类型   | 说明                             |
| ----- | ------ | -------------------------------- |
| align | String | 可选值 'left'、'center'、'right' |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setStrokeStyle('red')
ctx.moveTo(150, 20)
ctx.lineTo(150, 170)
ctx.stroke()

ctx.setFontSize(15)
ctx.setTextAlign('left')
ctx.fillText('textAlign=left', 150, 60)

ctx.setTextAlign('center')
ctx.fillText('textAlign=center', 150, 80)

ctx.setTextAlign('right')
ctx.fillText('textAlign=right', 150, 100)

ctx.draw()
```

### canvasContext.setTextBaseline

用于设置文字的水平对齐
 canvasContext.setTextBaseline(textBaseline)

| 参数         | 类型   | 说明                                       |
| ------------ | ------ | ------------------------------------------ |
| textBaseline | String | 可选值 'top'、'bottom'、'middle'、'normal' |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setStrokeStyle('red')
ctx.moveTo(5, 75)
ctx.lineTo(295, 75)
ctx.stroke()

ctx.setFontSize(20)

ctx.setTextBaseline('top')
ctx.fillText('top', 5, 75)

ctx.setTextBaseline('middle')
ctx.fillText('middle', 50, 75)

ctx.setTextBaseline('bottom')
ctx.fillText('bottom', 120, 75)

ctx.setTextBaseline('normal')
ctx.fillText('normal', 200, 75)

ctx.draw()
```

### canvasContext.drawImage

定义
 绘制图像到画布。

| 参数          | 类型   | 说明                                                 |
| ------------- | ------ | ---------------------------------------------------- |
| imageResource | String | 所要绘制的图片资源                                   |
| dx            | Number | 图像的左上角在目标canvas上 X 轴的位置                |
| dy            | Number | 图像的左上角在目标canvas上 Y 轴的位置                |
| dWidth        | Number | 在目标画布上绘制图像的宽度，允许对绘制的图像进行缩放 |
| dHeigt        | Number | 在目标画布上绘制图像的高度，允许对绘制的图像进行缩放 |
| sx            | Number | 源图像的矩形选择框的左上角 X 坐标                    |
| sy            | Number | 源图像的矩形选择框的左上角 Y 坐标                    |
| sWidth        | Number | 源图像的矩形选择框的高度                             |
| sHeight       | Number | 源图像的矩形选择框的高度                             |

eg:

```jsx
const ctx = wx.createCanvasContext('myCanvas')

wx.chooseImage({
  success: function(res){
    ctx.drawImage(res.tempFilePaths[0], 0, 0, 150, 100)
    ctx.draw()
  }
})
```

### canvasContext.setGlobalAlpha

设置全局画笔透明度。

canvasContext.setGlobalAlpha(alpha)

| 参数  | 类型   | 范围 | 说明                                     |
| ----- | ------ | ---- | ---------------------------------------- |
| alpha | Number | 0~1  | 透明度，0 表示完全透明，1 表示完全不透明 |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setFillStyle('red')
ctx.fillRect(10, 10, 150, 100)
ctx.setGlobalAlpha(0.2)
ctx.setFillStyle('blue')
ctx.fillRect(50, 50, 150, 100)
ctx.setFillStyle('yellow')
ctx.fillRect(100, 100, 150, 100)

ctx.draw()
```

### canvasContext.save

定义
 保存当前的绘图上下文。

restore
 定义
 恢复之前保存的绘图上下文。

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

// save the default fill style
ctx.save() 
ctx.setFillStyle('red')
ctx.fillRect(10, 10, 150, 100)

// restore to the previous saved state
ctx.restore()
ctx.fillRect(50, 50, 150, 100)

ctx.draw()
```

### canvasContext.draw

定义
 将之前在绘图上下文中的描述（路径、变形、样式）画到 canvas 中。
 Tip: 绘图上下文需要由 wx.createCanvasContext(canvasId) 来创建。

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')

ctx.setFillStyle('red')
ctx.fillRect(10, 10, 150, 100)
ctx.draw()
ctx.fillRect(50, 50, 150, 100)
ctx.draw()
```

### canvasContext.measureText

测量文本尺寸信息，目前仅返回文本宽度。同步接口。

参数:
 text String 要测量的文本

返回 TextMetrics 对象:
 width Number 文本的宽度

eg:

```jsx
const ctx = wx.createCanvasContext('myCanvas')
ctx.font = 'italic bold 20px cursive'
const metrics = ctx.measureText('Hello World')
console.log(metrics.width)
```

### canvasContext.globalCompositeOperation

该属性是设置要在绘制新形状时应用的合成操作的类型。

语法：
 canvasContext.globalCompositeOperation = type

参数：
 type String 标识要使用哪种合成或混合模式操作

### canvasContext.arcTo

根据控制点和半径绘制圆弧路径。

语法
 canvasContext.arcTo(x1, y1, x2, y2, radius)

参数

| 属性值 | 类型   | 说明                    |
| ------ | ------ | ----------------------- |
| x1     | Number | 第一个控制点的 x 轴坐标 |
| y1     | Number | 第一个控制点的 y 轴坐标 |
| x2     | Number | 第二个控制点的 x 轴坐标 |
| y2     | Number | 第二个控制点的 y 轴坐标 |
| radius | Number | 圆弧的半径              |

### canvasContext.strokeText

给定的 (x, y) 位置绘制文本描边的方法

语法
 canvasContext.strokeText(text, x, y, maxWidth)

| 属性值   | 类型   | 说明                     |
| -------- | ------ | ------------------------ |
| text     | String | 要绘制的文本             |
| x        | Number | 文本起始点的 x 轴坐标    |
| y        | Number | 文本起始点的 y 轴坐标    |
| maxWidth | Number | 需要绘制的最大宽度，可选 |

### canvasContext.lineDashOffset

设置虚线偏移量的属性
 语法
 canvasContext.lineDashOffset = value

| 属性值 | 类型   | 说明               |
| ------ | ------ | ------------------ |
| value  | Number | 偏移量，初始值为 0 |

### canvasContext.createPattern

对指定的图像创建模式的方法，可在指定的方向上重复元图像
 语法
 canvasContext.createPattern(image, repetition)

| 属性值     | 类型   | 说明                                                         |
| ---------- | ------ | ------------------------------------------------------------ |
| image      | String | 重复的图像源，仅支持包内路径和临时路径                       |
| repetition | String | 指定如何重复图像，有效值有: repeat, repeat-x, repeat-y, no-repeat |

eg:

```csharp
const ctx = wx.createCanvasContext('myCanvas')
const pattern = ctx.createPattern('/path/to/image', 'repeat-x')
ctx.fillStyle = pattern
ctx.fillRect(0, 0, 300, 150)
ctx.draw()
```

### canvasContext.setTransform

使用矩阵重新设置（覆盖）当前变换的方法
 语法
 canvasContext.setTransform(scaleX, skewX, skewY, scaleY, translateX, translateY)

| 属性值     | 类型   | 说明     |
| ---------- | ------ | -------- |
| scaleX     | Number | 水平缩放 |
| skewX      | Number | 水平倾斜 |
| skewY      | Number | 垂直倾斜 |
| scaleY     | Number | 垂直缩放 |
| translateX | Number | 水平移动 |
| translateY | Number | 垂直移动 |

# 效果

```js
// pages/food/food.js
Page({

  /**
   * 页面的初始数据
   */
  data: {

  },
  // 把rpx单位转化为支持canvas的px单位
  rpx2px(rpx) {
    const info = wx.getSystemInfoSync()
    return rpx * info.windowWidth / 750
  },
  // 保存至相册
  save() {

    // 1-把画布转化成临时文件
    wx.canvasToTempFilePath({
      x: 0,
      y: 0,
      width: this.rpx2px(632), // 画布的宽
      height: this.rpx2px(930), // 画布的高
      destWidth: 1080,
      destHeight: 1600,
      canvasId: 'canvas',
      success(res) {
        // 2-保存图片至相册
        wx.saveImageToPhotosAlbum({
          filePath: res.tempFilePath,
          success(res2) {
            wx.showToast({
              title: '保存至相册成功'
            })
          },
          fail() {
            wx.showToast({
              title: '保存失败，稍后再试',
              icon: 'none'
            })
          }
        })
      },
      fail() {
        wx.showToast({
          title: '保存失败，稍后再试',
          icon: 'none'
        })
      }
    })
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function(options) {





    let ctx = wx.createCanvasContext('canvas')
    // 第1步：绘制画布背景色
    // ctx.setFillStyle('#FF7A04')
    // ctx.fillRect(0, 0, this.rpx2px(632), this.rpx2px(750))

    // 第2步：绘制标题
    ctx.setFontSize(this.rpx2px(52))
    ctx.setFillStyle('#FDFDFD')
    ctx.setTextAlign('center')
    ctx.fillText('1000元礼品券', this.rpx2px(316), this.rpx2px(90))
    var that = this;
    wx.downloadFile({
      // url: 'https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2572654905.webp', //网络资源
      // url: 'https://img3.doubanio.com/view/photo/l/public/p2567248684.webp?'+new Date(), //网络资源
      url: 'https://img3.doubanio.com/view/photo/l/public/p2567248684.webp?' + new Date(),
      success(res) {
        // 成功存到临时目录  res.tempFilePath
        console.log('下载图片的地址'+res.tempFilePath)
        // 第3步：绘制礼券卡片（背景图片+文字）
        ctx.drawImage(res.tempFilePath, 0, 0, that.rpx2px(632), that.rpx2px(930))
        ctx.setFontSize(that.rpx2px(60))
        ctx.setFillStyle('#fff')
        ctx.setTextAlign('left')
        var time = new Date();
        var mon = time.getMonth()//月
        var day = time.getDate()//日
        var year = time.getFullYear()//年
        ctx.fillText(year, that.rpx2px(450), that.rpx2px(100))
        ctx.setFontSize(that.rpx2px(40))
        ctx.setFillStyle('#fff')
        ctx.setTextAlign('left')
        ctx.fillText(mon+"/"+day, that.rpx2px(460), that.rpx2px(140))
      // 二维码
        ctx.drawImage('../../static/logo.png', that.rpx2px(460), that.rpx2px(150), that.rpx2px(100), that.rpx2px(100))



        // 执行 draw()方法进行绘制
        ctx.draw()
      }
    })

  },


})
```

css

```css
/* pages/food/food.wxss */

.layer {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.7);
  /* z-index: 9999; */
}

.layer-bill {
  position: fixed;
  top: 50%;
  left: 0;
  width: 100%;
  height: 1052rpx;
  margin-top: -526rpx;
  overflow: hidden;
}

.layer-bill .content {
  margin: 0 auto;
  height: 930rpx;
  width: 632rpx;
  border-radius: 24rpx;
  position: relative;
}

.canvas {
  position: absolute ;
  top: 0 ;
  left: 0 ;
  display: block !important;
  width: 632rpx !important;
  height: 930rpx !important;

  margin: 0 auto;
  /* background:url('https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2572654905.webp'); */
  z-index: 100;
}

.save {
  position: absolute;
  bottom: -100rpx;
  left: 60rpx;
  right: 60rpx;
  color: #ff7a04;
  height: 90prx;
  line-height: 90rpx;
  text-align: center;
  font-size: 32rpx;
  background: #fff;
  border-radius: 45rpx;
  z-index: 101;
}

```

