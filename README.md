# screen-capture

一个简单的基于 `html2canvas` 的截屏组件。

## How

使用 `html2canvas` 将页面 `DOM` 转换为 `canvas`，再使用 `canvas.toDataURL` 将 canvas 转成 `base64串`，鼠标点击截图将取到截图区域的坐标，利用 `canvas.drawImage` 截取图片生成新的 `canvas`，最后使用 `canvas.toDataURL` 得到最终的截图 `base64串`。

### Usage

TODO，待 publish。

```vue
<ScreenCapture @captured="onCaptured({ error, imageData })" :z-index="10000"><WhatEverYouWantToReplaceDefaultPlaceholder /></ScreenCapture>
```

### Preview

```sh
$ npm install
$ npm run serve
```
