# nodejs图片处理工具gm

## 安装

首先要安装 [GraphicsMagick](http://www.graphicsmagick.org/)或者[ImageMagick](http://www.imagemagick.org/)

```sh
brew install imagemagick
```

ImageMagick取决于Ghostscript字体。要安装它们，请键入：

```js
brew install ghostscript
```

之后

```
npm install gm -S
```

## 测试

```js
var gm = require('gm');
let imageMagick = gm.subClass({ imageMagick: true });
imageMagick("./1/1-0.jpg").append("./1/1-1.jpg", "./1/1-2.jpg", true)
    .write("out.jpg", function (err) {
    if (!err) console.log('done');
    console.log(err);
});
```

运行 node gm.js

## 图片拼接(append)

gm中使用append也可以实现图片的拼接，这里的拼接应该是不能覆盖的。缺省

参数ltr表示拼接方向，布尔变量，true表示从左到右，false表示从上到下，默认false。

```js
gm("img.png").append(img [, img, ltr])
gm("img.png").append("another.jpg", "third.gif")//从上到下拼接
gm("img.png").append("another.jpg", "third.gif", true)//从左到右拼接
```

## 图片伸缩

可以只依据宽、高或者同时将宽高都放缩。

```js
gm("img.png").resize(width)//保持宽高比
gm("img.png").resize(null, height)//保持宽高比
gm("img.png").resize(width, height, '!')//参数'!'用于忽略宽高比
```

```js
var gm = require('gm');
let imageMagick = gm.subClass({ imageMagick: true });
imageMagick()
    .append("./1/1-0.jpg","./1/1-1.jpg", "./1/1-2.jpg")
    .write("1200.jpg", function (err) {
        if (!err) console.log('done');
    });

```

```js
var gm = require('gm');
let imageMagick = gm.subClass({ imageMagick: true });
// 获取文件夹所有的图片路径
var arr= ["./1/1-0.jpg","./1/1-1.jpg","./1/1-2.jpg"]

imageMagick()
    .append(...arr)
    .write("1200.jpg", function (err) {
        if (!err) console.log('done');
    });

```

