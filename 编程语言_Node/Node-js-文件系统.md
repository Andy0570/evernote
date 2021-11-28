Node.js 使用 fs（File System）模块来操作文件。文件系统模块有两种不同的方法：同步方法/异步方法。

## 同步和异步

* 异步方法的最后一个参数总是一个完整的回调函数，该回调函数的第一个参数永远是异常（err）。
* 异步的方法不能保证执行顺序，因此注意**文件操作的顺序**很重要。

异步方法示例：

```javascript
const fs = require('fs');

// 异步操作读取文件
const path = '/Users/huqilin/Desktop/node/test/tmp/hello.html';
fs.unlink(path, (err) => {
  if (err) throw err;
  console.log('成功删除 /tmp/hello');
});
```

* 当使用同步操作时，任何异常都会被立即抛出，可以使用 `try`/`catch` 来处理异常，或让异常向上冒泡。

同步方法示例：

```javascript
const fs = require('fs');

// 同步操作读取文件
try {
    const path = '/Users/huqilin/Desktop/node/test/tmp/hello.html';
    fs.unlinkSync(path);
    console.log('成功删除 /tmp/hello');
} catch (err) {
    // handle error
}
```

> 💡💡💡
> * 在一个大型的系统中，建议使用**异步方法**，同步方法将会导致进程被锁死。
> * 和同步方法相比，异步方法性能更高，速度更快，而且阻塞更少。
> * 不建议省略异步函数的回调函数，未来的版本可能会导致抛出错误。

## fs 模块中的类和文件的基本信息

![fs 类](http://upload-images.jianshu.io/upload_images/2648731-541386dac60a73d3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



使用 `fs.stat()` 方法返回文件基本信息：

```javascript
const fs = require('fs');

const path = '/Users/huqilin/Desktop/node/test/tmp/hello.html';
fs.stat(path, function(err, stats) {
    console.log(stats);
});

/*
Stats {
  dev: 16777220,
  mode: 33188,
  nlink: 1,
  uid: 501,
  gid: 20,
  rdev: 0,
  blksize: 4096,
  ino: 8610556045,
  size: 369,
  blocks: 8,
  atimeMs: 1539221692470.0618,
  mtimeMs: 1539221691421.845,
  ctimeMs: 1539221691421.845,
  birthtimeMs: 1539221685682.8408,
  atime: 2018-10-11T01:34:52.470Z,
  mtime: 2018-10-11T01:34:51.422Z,
  ctime: 2018-10-11T01:34:51.422Z,
  birthtime: 2018-10-11T01:34:45.683Z }
*/
```



## 文件路径

可以使用 path 模块的相关功能来修改、解析路径，还可以将路径进行规范化转换。

```javascript
const path = require('path');

// 使用 path.normalize() 方法规范化路径字符串
path.normalize('/foo/bar//baz/asdf/guux/...'); // /foo/bar/baz/asdf/guux/...

// path.join() 方法可以连接任意多个字符串
path.join('/foo', 'bar', 'baz/asdf', 'quux', '...'); // /foo/bar/baz/asdf/quux/...

// path.relative() 方法可以找出一个绝对路径到另一个绝对路径的相对关系
path.relative('/Users/liuht/code/itbilu/demo', '/Users/libut/code/zszsqc/lib'); // ../../zszsgc/lib
```



## 基本文件操作

* Node.js 使用流（stream）的方式来处理文件；



### 打开文件&关闭文件

异步打开文件语法:`fs.open(path, flags[, mode], callback)`

关闭文件语法：`fs.close`、`fs.closeSync`

```javascript
const fs = require('fs');

console.log('准备打开文件！')
// 打开文件
const path = '/Users/huqilin/Desktop/node/test/tmp/hello.html';
fs.open(path, 'r+', function(err, fd) {
    if (err) {
        return console.log(err);
    }
    console.log('成功打开文件！')

    // 关闭文件
    fs.close(fd, function(err) {
        if(err) {
            console.log(err);
        }
        console.log(‘文件已关闭’);
    })
});
```

在使用 `fs.readFile()`、``fs.writeFile()``、`fs.append` 之后，它们并不返回任何 fd，Node.js 将在文件操作之后进行判断并自动关闭文件。

```javascript
const fs = require('fs');

// 在 /home/text.txt 中写入字符串 abc
fs.writeFile('/home/text.txt', 'abc');
```



### 读取文件

* 如果想读取 GBK 或 GB2312 格式的文件，需要使用第三方模块（**iconv 模块**、**iconv-lite 模块**）。
* 可以使用 `fs.read()` 方法读取文件。
* `fs.readFile()`方法是 `fs.read()` 方法的封装，只能读取文件的全部内容。

```javascript
fs.read(fd, buffer, offset, length, position, callback)

/*
* 参数说明：
* fd: fs.open() 返回的文件描述符；
* buffer:数据写入的缓冲区；
* offset:缓冲区写入的写入偏移量；
* length:要从文件中读取的字节数；
* position:文件读取的起始位置；
* callback:回调函数；
*/
```

示例代码：

```javascript

const fs = require('fs');

const path = '/Users/huqilin/Desktop/node/test/tmp/hello.html';
// 使用 fs.open() 打开文件
fs.open(path, 'r', function (err, fd) {
    
    var readBuffer = new Buffer(1024),
        offset = 0,
        len = readBuffer.length,
        filePosition = 100;
    // 从第100个字节开始，读取最后面的1024个字节的数据。
    fs.read(fd, readBuffer, offset, len, filePosition, function(err, readByte) {
        
    })
})
```

> 💡💡💡
> js 文件必须保存为 UTF-8 的格式。使用 Node.js 开发时，无论是代码还是要读写的文件，建议使用 UTF8 编码格式保存，这样可以无需额外的模块支持。



### 写入文件

* 写入文件方法：`fs.writeFile()`、`fs.appendFile()`。

```javascript
fs.writeFile(file, data[, options], callback)

/*
 * 参数说明
 * path: 文件路径；
 * data：写入文件的数据。可以是string或者Buffer对象；
 * options：该参数是一个对象，包含{encoding, mode, flag}：默认编码为 utf-8、模式为 066、flag 为 'w'
 * callbac：回调函数。
 */

fs.appendFile(path, data[, options], callback)
```



将字符串（string）和流（Buffer）作为数据源写入一个文件中：

```javascript
const fs = require('fs');

// 使用 string 写入文件
fs.appendFile('message.txt', 'data to append', 'utf8', callback);

// 使用 Buffer 写入文件
fs.appendFile('message.txt', 'data to append', (err) => {
    if (err) {
        console.log('The '
            data to append ' was appended to file');
    }
})
```



## 其它文件操作

在实际编程中，我们需要操作需多种不同类型的数据，如 CSV、XML 和 JSON 文件。

Node.js 除了提供官方的 API 对文件操作进行支持，也可以通过 NPM 安装第三方模块来进行文件的操作。

如，使用 [CSV](https://csv.js.org/) 操作 CSV 文件。


