### 安装 MongoDB 数据库

通过 HomeBrew 安装 MongoDB 数据库：
```bash
brew install mongodb/brew/mongodb-community
```

MongoDB 配置文件路径：`/usr/local/etc/mongod.conf`

### 在前台运行 MongoDB

```bash
mongod --config /usr/local/etc/mongod.conf
```
数据库访问地址：<mongodb://127.0.0.1:27017/?compressors=zlib&readPreference=primary&gssapiServiceName=mongodb&appname=MongoDB%20Compass&ssl=false>

### 在后台运行 MongoDB

```bash
brew services start mongodb/brew/mongodb-community
```

### 停止运行 MongoDB

```bash
brew services stop mongodb-community
```

### mongodb-community 成功运行但是无法连接问题

问题记录：
```bash
$ mongo
MongoDB shell version v4.2.1
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
2019-12-06T13:51:26.558+0800 E  QUERY    [js] Error: couldn't connect to server 127.0.0.1:27017, connection attempt failed: SocketException: Error connecting to 127.0.0.1:27017 :: caused by :: Connection refused :
connect@src/mongo/shell/mongo.js:341:17
@(connect):2:6
2019-12-06T13:51:26.563+0800 F  -        [main] exception: connect failed
2019-12-06T13:51:26.563+0800 E  -        [main] exiting with code 1
```

这个问题通常是因为 Mac 系统的数据卷权限导致的，终端下执行以下命令，更改 MongoDB 数据库目录即可：

```bash
mongod --dbpath /usr/local/var/
```

参考：[MongoDB can't find data directory after upgrading to Mac OS 10.15 (Catalina)](https://stackoverflow.com/questions/58283257/mongodb-cant-find-data-directory-after-upgrading-to-mac-os-10-15-catalina)
