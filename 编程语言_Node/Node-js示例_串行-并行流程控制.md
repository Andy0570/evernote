> 本文内容：
> 用简单的示例程序描述 Node.js 串行和并行化流程控制的底层机制。
> 实际应用开发中以社区中的工具为主。比较流行的有 [Async](https://caolan.github.io/async/index.html)、Step、Seq 等。

# 一、串行化流程控制示例程序

* 参考：《Node.js 实战（第2版）》
* 示例程序功能：从一个随机选择的 RSS 预定源中获取一篇文章的标题和 URL，并显示出来。
* 示例程序的目的：用串行化流程控制让几个异步任务按顺序执行。

## 初始化项目

```shell
mkdir listing_217
cd listing_217
npm init -y
npm i request --save
npm i htmlparser --save
```

* [request](https://github.com/request/request#readme) 模块是一个经过简化的 HTTP 客户端，这里用它来获取 RSS 数据。
* [htmlparser](https://github.com/tautologistics/node-htmlparser#readme) 模块能把原始的 RSS 数据转换成 JavaScript 数据结构。

## index.js 开发

实现方式及原理：把一组任务按预期的执行顺序放到一个数组中。这个数组起到队列的作用：完成一个任务后按顺序从数组中取出下一个。
数组中的每个任务都是一个函数。任务完成后应该调用一个处理器函数，告诉它错误状态和结果。在这一实现中，如果有错误，处理器函数会终止执行；如果没有错误，处理器就从队列中取出下一个任务执行它。

```javascript
'use strict';

const fs = require('fs');
const path = require('path');
const request = require('request');
const htmlparser = require('htmlparser');
const configFilename = path.join(__dirname, './rss_feeds.txt');

// 【任务一】确保包含 RSS 预定源 URL 列表的文件存在
function checkForRSSFile() {
  fs.exists(configFilename, (exists) => {
    if (!exists)
      // 只要有错误就尽早返回
      return next(new Error(`Missing RSS file: ${configFilename}`));
    next(null, configFilename);
  })
}

// 【任务二】读取并解析包含预定源 URL 的文件
function readRSSFile(configFilename) {
  fs.readFile(configFilename, (err, feedList) => {
    if (err) return next(err);
    // 将预定源 URL 列表转换成字符串，然后分隔成一个数组
    feedList = feedList
      .toString()
      .replace(/^\s+|\s+$/g, '') // 字符串替换
      .split('\n'); // split() 方法用于把一个字符串分割成字符串数组。按（\n）分割字符串
    // 从预定源 URL 数组中随机选择一个预定源 URL
    const random = Math.floor(Math.random() * feedList.length);
    next(null, feedList[random]);
  })
}

// 【任务三】向选定的预定源发送 HTTP 请求以获取数据
function downloadRSSFeed(feedUrl) {
  request({ uri: feedUrl }, (err, res, body) => {
    if (err) return next(err);
    if (res.statusCode !== 200)
      return next(new Error('Abnormal response status code'));
    next(null, body);
  })
}

// 【任务四】将预定源数据解析到一个条目数组中
function parseRSSFeed(rss) {
  const handler = new htmlparser.RssHandler();
  const parser = new htmlparser.Parser(handler);
  parser.parseComplete(rss);
  if (!handler.dom.items.length)
    return next(new Error('No RSS items found'));
  // 如果有数据，显示第一个预定源条目的标题和 URL
  const item = handler.dom.items.shift();
  console.log(item.title);
  console.log(item.link);
}

// 把所有要做的任务按执行顺序添加到一个数组中
const tasks = [
  checkForRSSFile,
  readRSSFile,
  downloadRSSFeed,
  parseRSSFeed,
];

// 负责执行任务的 next 函数
function next(err, result) {
  if (err) throw err; // 如果任务出错，则中断程序，抛出异常。
  // shift()：把数组的第一个元素从其中删除，并返回第一个元素的值。
  const  currentTask = tasks.shift(); // 从任务数组中取出下一个任务。
  if (currentTask) {
    currentTask(result); // 执行当前任务
  }
}

next(); // 开始任务的串行化执行。
```

串行化流程控制的本质：在需要的时候让回调进场，而不是简单地把它们嵌套起来。

## 订阅源文件 rss_feeds.txt

```txt
http://blog.nodejs.org/feed/
http://blog.npmjs.org/rss
```

## 运行实例效果：

``` console
[Running] node "/Users/andy/Desktop/application/listing_217/app/index.js"
![CDATA[Node v11.1.0 (Current)]]
https://nodejs.org/en/blog/release/v11.1.0
```

# 二、并行化流程控制示例程序

为了让异步任务并行执行，仍然是要把任务放到数组中，但任务的存放顺序无关紧要。每个任务都应该调用处理器函数增加已完成任务的计数值。当所有任务都完成后，处理器函数应该执行后续的逻辑。

* 参考《Node.js 实战（第2版）》
* 示例程序功能：读取几个文本文件的内容，并输出单词在整个文件中出现的次数。
* 示例程序目的：让异步任务并行执行

## 初始化项目

```shell
mkdir listing_218
cd listing_218
mkdir text
npm init -y
```

## word_count.js 开发

```javascript
'use strict';

const fs = require('fs');
const path = require('path');
const tasks = [];
const wordCounts = {};
const filesDir = './text';
let completedTasks = 0;

// 检查并行任务是否完成
function checkIfComplete() {
  completedTasks++;
  if (completedTasks === tasks.length) {
    // 当所有任务全部完成后，列出文件中用到的每个单词以及用了多少次。
    for (const index in wordCounts) {
      console.log(`${index}: ${wordCounts[index]}`);
    }
  }
}

// 添加单次计数次数
function addWordCount(word) {
  wordCounts[word] = (wordCounts[word]) ? wordCounts[word] + 1 : 1;
}

function countWordsInText(text) {
  const words = text
    .toString()
    .toLowerCase()
    .split(/\w+/)
    .sort();
  
  words
    .filter(word => word)
    .forEach(word => addWordCount(word)); // 对文本中出现的单次计数

}

fs.readdir(filesDir, (err, files) => { //得出 text 目录中的文件列表
  if (err) throw err;

  files.forEach(file => {
    // 定义处理每个文件的任务。
    // 每个任务中都会调用一个异步读取文件的函数并对文件中使用的单次计数。
    const task = (file => {
      return () => {
        fs.readFile(file, (err, text) => {
          if (err) throw err;
          countWordsInText(text);
          checkIfComplete();
        });
      };
    })(`${filesDir}/${file}`);
    task.push(task); // 把所有任务都添加到函数调用数组中。
  });
  
  tasks.forEach(task => task()); // 开始并行执行所有任务。
});
```

