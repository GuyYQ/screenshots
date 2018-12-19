# screen-capture
##  工具：html2canvas、canvas2image、vue
思路：用html2canvas将整个页面的dom转换为canvas，再用canvas2image将canvas转成图片，鼠标点击截图将取到截图区域的坐标，利用canvas的drawImage方法，截取图片生成新的canvas，再用canvas2image将新生成的canvas转成图片
### 1.安装html2canvas
npm install html2canvas  --save
### 2.引用html2canvas、canvas2image
### 3.点击截图按钮时，用html2canvas将整个页面的dom转换为canvas
### 4.按住鼠标左键选区截图区域，获取截图区域坐标，利用canvas的drawImage方法，截取图片生成新的canvas，再用canvas2image将新生成的canvas转成图片

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


