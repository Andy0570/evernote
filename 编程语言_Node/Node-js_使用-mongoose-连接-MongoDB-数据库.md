## MongoDB 数据库

MongoDB 的数据逻辑结构：文档（document）、集合（collection）、数据库（database）。

* 文档（document）：由键值对构成，相当于关系数据库中的一行记录。
* 集合（collection）：多个文档组成一个集合，相当于关系数据库的表。
* 数据库（database）：多个集合逻辑上组织在一起，就是数据库。

一个 MongoDB 实例支持多个数据库（database）。

![MongoDB 数据库结构](http://upload-images.jianshu.io/upload_images/2648731-9e1be93cdc76d687.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 使用 [mongoose](https://github.com/Automattic/mongoose) 连接 MongoDB

MongoDB 数据库基础：


*  mongoose 中的一切由 **schema**（ˈskēmə） 开始。 **schema** 是一种以文件形式存储的数据库模型骨架，并不具备数据库的操作能力。 
*  **Schema** 定义了 **model** 中的所有属性，而 **model** 则是对应一个 MongoDB 中的 **collection**。



> **Schema** 生成 **Model**，**Model** 创造 **Entity**，**Model** 和 **Entity** 都可对数据库操作造成影响，但 **Model** 比 **Entity** 更具操作性。



### 连接数据库

连接数据库，实现增、删、改、查操作

```javascript
// 引入 mongoose 模块
const mongoose = require('mongoose');

// 定义数据库地址
// uri:mongodb://+[用户名:密码@]+数据库地址[:端口]+数据库名
// uri：mongodb://user:pass@localhost:port/database
const uri = 'mongodb://user:pass@localhost:port/database?authSource=admin';

// 连接 mongoDB 数据库
mongoose.connect(uri, {useNewUrlParser: true});
const db = mongoose.connection;

// 将连接绑定到错误事件
db.on('error', console.error.bind(console, 'MongoDB connection error'));

// 错误事件，同上
db.on('error', function(error) {
  console.error.bind(console, '数据库连接失败：' + error);
});

// 一次打开事件
db.once('open', function() {
    console.log('一次打开记录');
});

// 数据库连接成功
db.on('open', function() {
  console.log('数据库连接成功');
});

// 数据库连接失败
db.on('disconnected', function() {
  console.log('数据库连接断开');
});

// 断开连接
mongoose.disconnect();
```



### Schema

```javascript
// 定义 Schema
const Schema = mongoose.Schema;
// 使用 Schema 构造函数，创建一个新的 Schema 实例
// 实例化 mongoose.Schema 对象并定义 Model 的属性
const ArticleScheme = new Schema({
    title: String,
    author: String,
    content: String,
    publishTime: Date
});
// 将 Schema 注册成 Model
mongoose.model('Article', ArticleScheme);

// 如果 Model成功注册，就可以通过如下方式访问模型
const ArticleModel = mongoose.model('Article');

// 通过 Model 定义 Entity
var ArticleEntity = new ArticleModel({
    title: 'node.js',
    author: 'node',
    content: 'node.js is great!',
    publishTime: new Date()
});

// 【将文档插入到集合中】
ArticleEntity.save(function(err) {
    if (err) {
        console.log('save failed ' + err);
    }else {
        console.log('save successed');
    }
});

// 【查询】
// find() 方法，查询所有符合要求的数据
// findOne() 方法，查询符号要求的第一条数据
ArticleModel.find({title:'node.js'}, function(err, docs) {
    if (err) {
        console.log('error');
        return;
    }
    console.log('result: ' + docs); // 打印实体
    
    // 【查询纪录后，修改记录的值】
    docs[0].title = 'javascript'; // 修改数据
    docs[0].save(); // 保存修改后的数据

    // 【删除数据】
    if (docs) {
        // 遍历文档，逐条删除
        docs.forEach(function(ele) {
            // 只有单个文档可以使用 remove() 方法
            ele.remove();
        })
    }
})
```



可以将 Model 提取成单独的一个类：

> 💡
>
> 强烈建议在每个模型模块（文件）中，定义每个模型纲要，导出方法以创建模型。

#### article.js

```javascript
// 导入 mongoose 模块
const mongoose = require('mongoose');

// 定义 Schema
const Schema = mongoose.Schema;
const ObjectId = Schema.ObjectId; // 主键

const ArticleSchema = new Schema({
    id: ObjectId,
    title: {type: String, required: true, max: 100},
    name: {type: String, required: true, max: 100},
    date: {type: date}
});

// 导出模型
module.exports = mongoose.model('Article', ArticleSchema);
```

#### farmers.js

```javascript
const mongoose = require('mongoose');

// 定义 Schema
const Schema = mongoose.Schema;
const FarmerSchema = new Schema({
  uuid: { type: String, required: true },
  name: { type: String, required: true },
  id_number: { type: String, required: true },
  phone: { type: String, required: true },
  public_key: { type: String, required: true },
  private_key: { type: String, required: true },
});

// 导出模型
module.exports = mongoose.model('Farmer', FarmerSchema);

// --------
// 引入并使用
const FarmerModel = require('../model/farmers');
```



### 增（Create）

通常所说的 CRUD 就是数据库的增（Create）删（Delete）查（Read）改（Update）。

> `Model.create()`

```javascript
const doc = ({
  uuid: '1234567890',
  name: '张三',
  id_number: '320321201810104321',
  phone: '15812345678',
  public_key: 'publickey',
  private_key: 'privatekey',
});

FarmerModel.create(doc, function(err, docs) {
  if (err) {
    console.log('create error: ' + err);
    return;
  }
  console.log('create success:\n ' + docs);
});
```



> Entity.save()

```javascript
// 通过 Model 定义 Entity
const FarmerEntity = new FarmerModel({
  uuid: '2234567890',
  name: '李四',
  id_number: '320321201810104322',
  phone: '15912345678',
  public_key: 'publickey_1',
  private_key: 'privatekey_1',
});

// 将 Entity 保存到数据库
FarmerEntity.save(function(err, docs) {
  if (err) {
    console.log('create error: ' + err);
  }
  console.log('create success:\n ' + docs);
});
```



> `Model.insertMany()`，一次插入多个值

```javascript
// insertMany()
FarmerModel.insertMany([{
  uuid: '12345',
  name: '王五',
  id_number: '123456',
  phone: '123456',
  public_key: '12345',
  private_key: '12345',
}, {
  uuid: '12345',
  name: '赵六',
  id_number: '123456',
  phone: '123456',
  public_key: '12345',
  private_key: '12345',
}], function(err, docs) {
  if (err) {
    console.log('create error: ' + err);
  }
  console.log('create success:\n ' + docs);
});
```



### 查（Read）

> `Model.find()`，查询所有符合要求的数据

```javascript
FarmerModel.find({ name: '张三' }, function(err, docs) {
  if (err) {
    console.log('查询错误' + err);
    return;
  }
  console.log('打印实体：result: ' + docs);
});

// 从集合/表中按照 uuid 键值对查找文档（document）
db.getCollection('NaturalPersonInfo').find({uuid:'8a033c7f20ea2bdea3c4b9d60d42f11e'})
```

> `Model.findOne()`，查询符号要求的第一条数据



> `Model.findById()`



### 改（Update）

> `Model.update()`

```javascript
// Model.update()
// 查询 uuid 为 2234567890 的实体，将他的名字改为张团团，允许更新多条查询记录
FarmerModel.update({ uuid: '2234567890' }, { name: '张团团' }, { multi: true }, function(err, docs) {
  if (err) {
    console.log('更新错误' + err);
    return;
  }
  console.log('更新后的实体：result: ' + docs);
});
```



> `Model.updateMany()`，一次更新多条数据。
>
> `Model.updateOne()`，一次更新一条数据。
>
> `Model.findByIdAndyUpdate()`，
>
> `Model.findOneAndUpdate()`



### 删（Delete）

> `Model.remove()`，删除

```javascript
FarmerModel.remove({ uuid: '1234567890' }, function(err, docs) {
  if (err) {
    console.log('查询错误' + err);
    return;
  }
  console.log('删除成功');
});
```

> `Model.findByIdAndRemove()`
>
> `Model.findOneAndRemove()`



## 常见错误

### 1. 连接 MongdDB 数据库时提示字符串参数失效

示例代码：

```javascript
// 连接 mongoDB 数据库
mongoose.connect(uri, function(err) {
    if (err) {
        console.log('connect failed' + err);
        return;
    }
    console.log('connect success');
});
```

错误提示内容：

```shell
DeprecationWarning: current URL string parser is deprecated, and will be removed in a future version. To use the new parser, pass option { useNewUrlParser: true } to MongoClient.connect.
```

解决方法：连接时传递参数：

```javascript
// 连接 mongoDB 数据库
mongoose.connect(uri, {useNewUrlParser: true}); // <——
const db = mongoose.connection;
// 将连接绑定到错误事件
db.on('error', console.error.bind(console, 'MongoDB connection error'));
```



### 2. 连接 MongoDB 数据库时授权失败，提示：**errmsg: 'Authentication failed.',  code: 18**

```javascript
// uri 后面添加 authDatabase 参数
mongoose.connect('mongodb://user:password@host:port/dbname?authSource=admin')
```

解决参考：[Authentication in mongoose using SCRAM-SHA-1](https://stackoverflow.com/questions/31398367/authentication-in-mongoose-using-scram-sha-1)



## 参考

* 开源框架：[node-mongode-native](https://mongodb.github.io/node-mongodb-native/)
* 开源框架：[mongoose](https://github.com/Automattic/mongoose)
* 开源框架：[egg-mongoose](https://github.com/eggjs/egg-mongoose)
* [mongoose 官网|Doc](https://mongoosejs.com/)
* [Express 教程 3: 使用数据库 (Mongoose)](https://developer.mozilla.org/zh-CN/docs/learn/Server-side/Express_Nodejs/mongoose)
* [CNode：Mongoose 学习参考文档——基础篇](https://cnodejs.org/topic/504b4924e2b84515770103dd) ⭐️⭐️⭐️
* [Mongoose 增查改删学习笔记](https://segmentfault.com/a/1190000008245062#articleHeader13) ⭐️⭐️⭐️
* [Node 学习记录: mongodb](https://segmentfault.com/a/1190000009492647)

