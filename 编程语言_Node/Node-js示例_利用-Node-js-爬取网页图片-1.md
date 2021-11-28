主要内容：利用 Node.js 爬取一个网页，通过第三方模块 cheerio.js 分析这个网页的内容，最后将这个网页的图片保存在本地。

### 整体思路

 * 通过第三方模块 request 请求网页地址，从而得到整个网页的DOM结构。
 * 根据DOM结构利用 cheerio 模块分析出图片文件的地址，再次请求这个地址，最后将得到的图片数据储存在本地。


### 项目目录结构

![image](http://upload-images.jianshu.io/upload_images/2648731-acbea90993b7be2d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* img 文件夹用来存储图片文件。
* node_modules 文件夹是模块默认的保存位置。
* index.js 文件是整个项目的入口地址。
* config.js 文件是配置文件。
* analyze.js 文件用来存储分析 DOM 的方法。



### config.js 文件

配置网页地址及图片存放路径

```javascript
// 网页地址
const url = 'https://unsplash.com/photos/RDDYS5DFo08';

// 图片文件夹路径
const path = require('path');
const imgDir = path.join(__dirname, 'img');

module.exports.url = url;
module.exports.imgDir = imgDir;
```



### analyze.js 文件

```javascript
const cheerio = require('cheerio');

/**
 * 解析 DOM 得到图片地址
 *
 * @param {*} dom
 * @param {*} callback
 */
function findImg(dom, callback) {
  let $ = cheerio.load(dom);
  $('img').each(function(i, elem) {
    let imgSrc = $(this).attr('src');
    callback(imgSrc, i);
  });
};

module.exports.findImg = findImg;
```



### index.js 文件

```javascript
const request = require('request');
const path = require('path');
const fs = require('fs');
const config = require('./config');
const analyze = require('./analyze');


/**
 * 请求图片地址
 */
function start() {
  // 发起请求获取 DOM
  request(config.url, function(err, res, body) {
    console.log('start');
    if (!err && res) {
      console.log('start');
      // 将 downLoad 函数作为参数传递给 analyze 模块的 findImg 方法
      analyze.findImg(body, downLoad);
    }
  });
}


/**
 * 获取到 findImg 函数返回的图片地址后，利用 request 再次发起请求，将数据写入本地。
 *
 * @param {*} imgUrl
 * @param {*} i
 */
function downLoad(imgUrl, i) {
  let ext = imgUrl.split('.').pop();

  // 再次发起请求，写文件
  request(imgUrl).pipe(fs.createWriteStream(path.join(config.imgDir, i + '.' + ext), {
    'encoding': 'utf8',
  }));
  console.log(i);
}

start();
```


