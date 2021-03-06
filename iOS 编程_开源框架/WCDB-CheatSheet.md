[WCDB](https://github.com/Tencent/wcdb) 是一个高效、完整、易用的移动数据库框架，基于 [SQLCipher](https://github.com/sqlcipher/sqlcipher)，支持 iOS, macOS 和 Android。

**本文简单记录 WCDB 在 Objective-C 项目下的使用代码**。

# 类字段绑定（ORM）

### `Message.h`

```objectivec
#import <Foundation/Foundation.h>

@interface Message : NSObject

@property (nonatomic, assign) int localID;
@property (nonatomic, copy) NSString *content;
@property (nonatomic, retain) NSDate *createTime;
@property (nonatomic, retain) NSDate *modifiedTime;
@property (nonatomic, assign) int unused; // You can only define the properties you need

@end
```

### `Message.mm`

```objectivec
#import "Message.h"
#import <WCDB/WCDB.h>

@implementation Message

// 使用 WCDB_IMPLEMENTATION 宏在「类文件」定义绑定到数据库表的类
WCDB_IMPLEMENTATION(Message)

// 绑定字段
// 使用 WCDB_SYNTHESIZE 宏在类文件定义需要绑定到数据库表的字段
WCDB_SYNTHESIZE(Message, localID)
WCDB_SYNTHESIZE(Message, content)
WCDB_SYNTHESIZE(Message, createTime)
WCDB_SYNTHESIZE(Message, modifiedTime)

// 将数据库表的字段与类的属性名进行映射
// WCDB_SYNTHESIZE_COLUMN(Message, localID, "db_id")
// WCDB_SYNTHESIZE_COLUMN(Message, content, "db_content")

// WCDB_PRIMARY 定义主键
WCDB_PRIMARY(Message, localID)

// WCDB_PRIMARY_AUTO_INCREMENT 定义主键且自增
// Auto-increment 会在新记录插入表中时生成一个唯一的数字。
//WCDB_PRIMARY_AUTO_INCREMENT(Message, localID)

// WCDB_INDEX 定义索引
// 索引：CREATE INDEX message_index ON message(createTime)
WCDB_INDEX(Message, "_index", createTime)

// WCDB_UNIQUE 定义唯一约束
// WCDB_UNIQUE(<#className#>, <#propertyName#>)

// WCDB_NOT_NULL 定义非空约束
// WCDB_NOT_NULL(<#className#>, <#propertyName#>)

@end
```

### `Message+WCTTableCoding.h`

```objectivec
#import "Message.h"
#import <WCDB/WCDB.h>

@interface Message (WCTTableCoding) <WCTTableCoding>

// 使用 WCDB_PROPERTY 宏在「头文件」中声明需要绑定到数据库表的字段
WCDB_PROPERTY(localID)
WCDB_PROPERTY(content)
WCDB_PROPERTY(createTime)
WCDB_PROPERTY(modifiedTime)

@end
```



# 创建表和索引

```objectivec
- (void)setupDatabase {
    NSURL *databaseURL = [self p_userDatabaseURL];
    self.database = [[WCTDatabase alloc] initWithPath:databaseURL.path];
    BOOL result = [self.database createTableAndIndexesOfName:kTableName withClass:IMUser.class];
    NSAssert(result, @"Failed to Create users Database.");
}

// ./Documents/User/%ld/Database/common/user.sqlite
- (NSURL *)p_userDatabaseURL {
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSURL *documentsURL = [fileManager URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask].firstObject;
    NSString *directory = [NSString stringWithFormat:@"User/%ld/Database/common/", (long)_userId];
    NSURL *directoryURL = [documentsURL URLByAppendingPathComponent:directory];
    
    if (![fileManager fileExistsAtPath:directoryURL.path]) {
        BOOL result = [fileManager createDirectoryAtPath:directoryURL.path withIntermediateDirectories:YES attributes:nil error:nil];
        NSAssert(result, @"Failed to Create Directory: %@.",directoryURL.path);
    }
    return [directoryURL URLByAppendingPathComponent:@"user.sqlite"];
}
```



# 增删查改

## 插入

* `insertObject:into:` 和 `insertObjects:into:`，插入单个或多个对象
* `insertOrReplaceObject:into` 和 `insertOrReplaceObjects:into`，插入单个或多个对象。当对象的主键在数据库内已经存在时，更新数据；否则插入对象。
* `insertObject:onProperties:into:` 和 `insertObjects:onProperties:into:`，插入单个或多个对象的部分属性
* `insertOrReplaceObject:onProperties:into` 和 `insertOrReplaceObjects:onProperties:into`，插入单个或多个对象的部分属性。当对象的主键在数据库内已经存在时，更新数据；否则插入对象。

```objectivec
Message *message = [[Message alloc] init];
message.localID = 1;
message.content = @"Hello WCDB!";
message.createTime = [NSDate date];
message.modifiedTime = [NSDate date];

/**
 INSERT INTO message(localID, content, createTime, modifiedTime)
 VALUES(1, "Hello  WCDB!", 1496396165, 1496396165);
 */
BOOL result = [database insertObject:message 
                                into:@"message"];

// INSERT INTO message(localID, content) VALUES(?,?);
BOOL result = [database insertObject:message
                        onProperties:{Message.localID, Message.content}
                                into:@"message"];
```



```objectivec
//Insert one object
{
    WCTSampleConvenient *object = [[WCTSampleConvenient alloc] init];
    object.intValue = 1;
    object.stringValue = @"Insert one object";
    [database insertObject:object
                      into:tableName];
}
//Insert objects
{
    NSMutableArray *objects = [[NSMutableArray alloc] init];
    WCTSampleConvenient *object1 = [[WCTSampleConvenient alloc] init];
    object1.intValue = 2;
    object1.stringValue = @"Insert objects";
    [objects addObject:object1];
    WCTSampleConvenient *object2 = [[WCTSampleConvenient alloc] init];
    object2.intValue = 3;
    object2.stringValue = @"Insert objects";
    [objects addObject:object2];
    [database insertObjects:objects
                       into:tableName];
}
//Insert or replace one object
{
    WCTSampleConvenient *object = [[WCTSampleConvenient alloc] init];
    object.intValue = 1;
    object.stringValue = @"Insert or replace one object";
    [database insertOrReplaceObject:object
                               into:tableName];
}
//Insert or replace objects
{
    NSMutableArray *objects = [[NSMutableArray alloc] init];
    WCTSampleConvenient *object1 = [[WCTSampleConvenient alloc] init];
    object1.intValue = 2;
    object1.stringValue = @"Insert or replace objects";
    [objects addObject:object1];
    WCTSampleConvenient *object2 = [[WCTSampleConvenient alloc] init];
    object2.intValue = 3;
    object2.stringValue = @"Insert or replace objects";
    [objects addObject:object2];
    [database insertOrReplaceObjects:objects
                                into:tableName];
}
//Insert auto increment
{
    WCTSampleConvenient *object = [[WCTSampleConvenient alloc] init];
    object.isAutoIncrement = YES;
    object.stringValue = @"Insert auto increment";
    [database insertObject:object
                      into:tableName];
}
```



## 删除

* `deleteAllObjectsFromTable:` 删除表内的所有数据
* `deleteObjectsFromTable:` 后可组合接 `where`、`orderBy`、`limit`、`offset` 以删除部分数据

```objectivec
// DELETE FROM message WHERE localID>0;
BOOL result = [database deleteObjectsFromTable:@"message"
                                         where:Message.localID > 0];

// DELETE FROM message WHERE localID>0 AND content IS NULL LIMIT 1;
BOOL result = [database deleteObjectsFromTable:@"message"
                                         where:Message.localID>0 && Message.content != nil
                                         limit:1];
```

```objectivec
//Delete
{
    BOOL ret = [database deleteAllObjectsFromTable:tableName];
}
//Delete with condition/order/offset/limit
{
    BOOL ret = [database deleteObjectsFromTable:tableName
                                          where:WCTSampleConvenient.intValue.in({1, 2, 3})];
}
//Delete with condition/order/offset/limit
{
    BOOL ret = [database deleteObjectsFromTable:tableName
                                          where:WCTSampleConvenient.intValue.in(@[ @(1), @(2), @(3) ])];
}
```



## 更新

* `updateAllRowsInTable:onProperties:withObject:`，通过 object 更新数据库中所有指定列的数据
* `updateRowsInTable:onProperties:withObject:` 后可组合接 `where`、`orderBy`、`limit`、`offset` 以通过 object 更新指定列的部分数据
* `updateAllRowsInTable:onProperty:withObject:`，通过 object 更新数据库某一列的数据
* `updateRowsInTable:onProperty:withObject:` 后可组合接 `where`、`orderBy`、`limit`、`offset` 以通过 object 更新某一列的部分数据
* `updateAllRowsInTable:onProperties:withRow:`，通过数组更新数据库中的所有指定列的数据
* `updateRowsInTable:onProperties:withRow:` 后可组合接 `where`、`orderBy`、`limit`、`offset` 以通过数组更新指定列的部分数据
* `updateAllRowsInTable:onProperty:withRow:`，通过数组更新数据库某一列的数据
* `updateRowsInTable:onProperty:withRow:` 后可组合接 `where`、`orderBy`、`limit`、`offset` 以通过数组更新某一列的部分数

```objectivec
// UPDATE message SET content="Hello, Wechat!";
Message *message = [[Message alloc] init];
message.content = @"Hello, Wechat";
BOOL result = [database updateAllRowsInTable:@"message"
                                onProperties:Message.content
                                  withObject:message];

// UPDATE message SET modifiedTime=? WHERE localID==1;
BOOL result = [database updateRowsInTable:@"message"
                               onProperty:Message.modifiedTime
                                withValue:[NSDate date]
                                    where:Message.localID==1];
```

```objectivec
//Update by object
{
    WCTSampleConvenient *object = [[WCTSampleConvenient alloc] init];
    object.stringValue = @"Update by object";
    [database updateAllRowsInTable:tableName
                      onProperties:WCTSampleConvenient.stringValue
                        withObject:object];
}
//Update by value
{
    NSArray *row = [NSArray arrayWithObject:@"Update by value"];
    [database updateAllRowsInTable:tableName
                      onProperties:WCTSampleConvenient.stringValue
                           withRow:row];
}
//Update with condition/order/offset/limit
{
    WCTSampleConvenient *object = [[WCTSampleConvenient alloc] init];
    object.stringValue = @"Update with condition/order/offset/limit";
    [database updateRowsInTable:tableName
                   onProperties:WCTSampleConvenient.stringValue
                     withObject:object
                          where:WCTSampleConvenient.intValue > 0];
}
```



## 查询

* `getOneObjectOfClass:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一行数据并组合成 object
* `getOneObjectOnResults:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一行数据的部分列并组合成 object
* `getOneRowOnResults:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一行数据的部分列并组合成数组
* `getOneColumnOnResult:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一列数据并组合成数组
* `getOneDistinctColumnOnResult:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一列数据，并取 distinct 后组合成数组。
* `getOneValueOnResult:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一行数据的某一列
* `getAllObjectsOfClass:fromTable:`，取出所有数据，并组合成 object
* `getObjectsOfClass:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一部分数据，并组合成 object
* `getAllObjectsOnResults:fromTable:`，取出所有数据的指定列，并组合成 object
* `getObjectsOnResults:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一部分数据的指定列，并组合成 object
* `getAllRowsOnResults:fromTable:`，取出所有数据的指定列，并组合成数组
* `getRowsOnResults:fromTable:` 后可接 `where`、`orderBy`、`limit`、`offset` 以从数据库中取出一部分数据的指定列，并组合成数组


```objectivec
// MARK: 查询
// SELECT * FROM message;
NSArray<Message *> *messages = [database getAllObjectsOfClass:Message.class
                                                    fromTable:@"message"];

// MARK: 查询并排序
// SELECT * FROM message ORDER BY localID;
NSArray<Message *> *messages2 = [database getObjectsOfClass:Message.class
                                                  fromTable:@"message"
                                                    orderBy:Message.localID.order()];
```

```objectivec
//Select One Object
{
    WCTSampleConvenient *object = [database getOneObjectOfClass:WCTSampleConvenient.class
                                                      fromTable:tableName];
}
//Select Objects
{
    NSArray<WCTSampleConvenient *> *objects = [database getAllObjectsOfClass:WCTSampleConvenient.class
                                                                   fromTable:tableName];
}
//Select Objects with condition/order/offset/limit
{
    NSArray<WCTSampleConvenient *> *objects = [database getObjectsOfClass:WCTSampleConvenient.class
                                                                fromTable:tableName
                                                                  orderBy:WCTSampleConvenient.intValue.order(WCTOrderedAscending)
                                                                    limit:1
                                                                   offset:2];
}
//Select Part of Objects
{
    NSArray<WCTSampleConvenient *> *objects =
        [database getAllObjectsOnResults:WCTSampleConvenient.stringValue
                               fromTable:tableName];
}
//Select column
{
    WCTOneColumn *objects = [database getOneColumnOnResult:WCTSampleConvenient.stringValue
                                                 fromTable:tableName];
    for (NSString *string : objects) {
        //do sth
    }
}
//Select row
{
    WCTOneRow *row = [database getOneRowOnResults:{
                                                      WCTSampleConvenient.intValue,
                                                      WCTSampleConvenient.stringValue}
                                        fromTable:tableName];
    NSNumber *intValue = (NSNumber *) row[0];
    NSString *stringValue = (NSString *) row[1];
}
//Select one value
{
    NSNumber *count = [database getOneValueOnResult:WCTSampleConvenient.AnyProperty.count()
                                          fromTable:tableName];
}
//Select aggregation 聚合查询
{
    WCTOneRow *row = [database getOneRowOnResults:{
                                                      WCTSampleConvenient.intValue.avg(),
                                                      WCTSampleConvenient.stringValue.count(),
                                                  }
                                        fromTable:tableName];
}
//Select distinct aggregation
//取出所有数据的指定列，并组合成 object
{
    NSArray *objects = [database getAllObjectsOnResults:WCTSampleConvenient.stringValue.count(true)
                                              fromTable:tableName];
}
//Select distinct result
//从数据库中取出一列数据，并取 distinct 去重后组合成数组
{
    NSNumber *distinctCount = [database getOneDistinctValueOnResult:WCTSampleConvenient.intValue fromTable:tableName];
}
```



### WCTTable

```objectivec
// WCTTable 相当于预设了表名和类名的 WCTDatabase 对象，接口和 WCTDatabase 基本一致
WCTTable *table = [database getTableOfName:@"message" withClass:Message.class];

// MARK: 通过 WCTTable 查询
// SELECT * FROM message ORDER BY localID;
NSArray<Message *> *queryResult = [table getObjectsOrderBy:Message.localID.order()];
```




# 事务（Transaction）

## 1. 通过 `runTransaction` 接口执行事务

```objectivec
BOOL commited = [database runTransaction:^BOOL{
    [database insertObject:message into:@"message"];
    return YES; // 返回 YES 以 commit 事务，返回 NO 以 rollback 事务。
}];
```

这种方式要求数据库操作在一个 Block 块内完成，简单易用。

## 2. 获取 `WCTTransaction` 对象执行事务

```objectivec
WCTTransaction *transaction = [database getTransaction];
BOOL result = [transaction begin];
[transaction insertObject:message into:@"message"];
result = [transaction commit];
if (!result) {
    // 如果事务执行失败，则执行 rollback 回滚
    [transaction rollback];
    NSLog(@"%@",transaction.error);
}
```



# WINQ（WCDB 语言集成查询）

WINQ 将自然查询的 SQL 语言集成到了 C++ 中，可以通过类似函数调用的方式来写 SQL 查询。

WINQ 接口语法：

* 一元操作符：+、-、! 等
* 二元操作符：||、&&、+、-、*、/、|、&、<<、>>、<、<=、==、!=、>、>= 等
* 范围比较：IN、BETWEEN 等
* 字符串匹配：LIKE、GLOB、MATCH、REGEXP 等
* 聚合函数：AVG、COUNT、MAX、MIN、SUM 等
* ...



以下是 SQL 和 WINQ 之间转换的一些例子：

| 类型                | SQL 示例                                     | WINQ 示例                                                    |
| :------------------ | :------------------------------------------- | :----------------------------------------------------------- |
| 排序                | `ORDER BY localID ASC`                       | `Message.localID.order(WCTOrderedAscending)`                 |
| 多字段排序          | `ORDER BY localID ASC, content DESC`         | `{Message.localID.order(WCTOrderedAscending), Message.content.order(WCTOrderedDescending)}` |
| 聚合函数            | `MAX(localID)`                               | `Message.localID.max()`                                      |
| 条件语句            | `localID == 2 AND content IS NOT NULL`       | `Message.localID == 2 && Message.content.isNotNull()`        |
| 多个字段组合        | `localID, content`                           | `{Message.localID, Message.content}`                         |
| `*`                 | `COUNT(*)`                                   | `Message.AnyProperty.count()`                                |
| 所有 ORM 定义的字段 | `localID, content, createTime, modifiedTime` | `Message.AllProperties`                                      |
| 指定 table          | `myTable.localID`                            | `Message.localID.inTable("myTable")`                         |

* 注：``Max()`` 函数返回一列中的最大值。



## 字段映射与运算符

通过 `className.propertyName` 的方式，获得数据库内字段的映射，以此书写 SQL 的条件、排序、过滤等等所有语句。

```objectivec
/**
 SELECT MAX(createTime), MIN(createTime)
 FROM message
 WHERE localID>0 AND content IS NOT NULL
 */
[database getObjectsOnResults:{Message.createTime.max(), Message.createTime.min()}
                    fromTable:@"message"
                        where:Message.localID > 0 && Message.content.isNotNull()];
```



```objectivec
/**
 SELECT DISTINCT localID
 FROM message
 ORDER BY modifiedTime ASC
 LIMIT 10
 */
[database getObjectsOnResults:Message.localID.distinct()
                    fromTable:@"message"
                      orderBy:Message.modifiedTime.order(WCTOrderedAscending)
                        limit:10];
```



```objectivec
/**
 DELETE FROM message
 WHERE localID BETWEEN 10 AND 20 OR content LIKE 'Hello%'
 */
[database deleteObjectsFromTable:@"message"
                           where:Message.localID.between(10, 20) || Message.content.like("Hello%")];
```



### 字段组合

多个字段映射可以通过大括号 `{}` 进行组合。

```objectivec
/**
 SELECT localID, content FROM message;
 */
[database getAllObjectsOnResults:{Message.localID, Message.content}
                       fromTable:@"message"];
```

```objectivec
/**
 SELECT localID, createTime
 FROM message
 WHERE localID>=1 OR modified!=createTime;
 */
[database getObjectsOnResults:{Message.localID, Message.createTime}
                    fromTable:@"message"
                        where:Message.localID>=1||Message.modifiedTime!=Message.createTime];
```

```objectivec
/**
 SELECT *
 FROM message
 ORDER BY createTime ASC, localID DESC;
 */
[database getObjectsOfClass:Message.class
                  fromTable:@"message"
                    orderBy:{Message.createTime.order(WCTOrderedAscending), Message.localID.order(WCTOrderedDescending)}];
```



### AllProperties

**className.AllProperties** 用于获取类定义的所有字段映射的列表。

```objectivec
/**
 SELECT localID, content, createTime, modifiedTime FROM message;
 */
[database getAllObjectsOnResults:Message.AllProperties
                       fromTable:@"message"];
```



### AnyProperty

**className.AnyProperty** 用于指代 SQL 中的 `*`。

```objectivec
/**
 SELECT count(*) FROM message;
 */
NSNumber *count = [database getOneValueOnResult:Message.AnyProperty.count()
                   		                fromTable:@"message"];
```



### 链式接口

链式调用是指对象的接口返回一个对象，从而允许在单个语句中将调用链接在一起，而不需要变量来存储中间结果。

WCDB 对于增删改查操作，都提供了对应的类以实现链式调用

* `WCTInsert`
* `WCTDelete`
* `WCTUpdate`
* `WCTSelect`
* `WCTRowSelect`
* `WCTMultiSelect`

```objectivec
WCTSelect *select = [database prepareSelectObjectsOnResults:Message.localID.max()
                                                  fromTable:@"message"];

NSArray<Message *> *objects= [[[[select where:Message.localID > 0]
                                groupBy:{Message.content}]
                               orderBy:Message.createTime.order()]
                              limit:10].allObjects;
```



### as 重定向

基于 ORM 的支持，我们可以从数据库直接取出一个 Object。然而，有时候需要取出并非是某个字段，而是有一些组合。

从数据库中取出了消息的最新的修改时间，并以此将此时间作为消息的创建时间，新建了一个 message。

```objectivec
NSNumber *maxModifiedTime = [database getOneValueOnResult:Message.modifiedTime.max()
                                                fromTable:@"message"];
Message *lastMessage = [[Message alloc] init];
lastMessage.createTime = [NSDate dateWithTimeIntervalSince1970:maxModifiedTime.doubleValue];
```

as 重定向，它可以将一个查询结果重定向到某一个字段，如下：

```objectivec
// as(Message.createTime) 语法，将查询结果重新指向 createTime
Message *lastMessage = [database getOneObjectOnResults:Message.modifiedTime.max().as(Message.createTime)
                                              fromTable:@"message"];
```



### 多表查询

通过 `WCTMultiSelect `的链式接口，可以同时从表中取出多个类的对象。

```objectivec
/*
 SELECT contact.nickname, contact_ext.headImg
 FROM contact, contact_ext
 WHERE contact.name==contact_ext.name
 */
WCTMultiSelect *multiSelect = [[database prepareSelectMultiObjectsOnResults:{
    Contact.nickname.inTable(@"contact"),
	  ContactExt.nickname.inTable(@"contact_ext")
} fromTables:@[ @"contact", @"contact_ext" ]] where:Contact.name.inTable(@"contact") == ContactExt.name.inTable(@"contact_ext")];

while ((multiObject = [multiSelect nextMultiObject])) {
    Contact *contact = (Contact *) [multiObject objectForKey:@"contact"];
    ContactExt *contactExt = (ContactExt *) [multiObject objectForKey:@"contact_ext"];
    //...
}
```



# 加密

WCDB 提供基于 SQLCipher 的数据库加密功能，如下：

```objectivec
WCTDatabase *database = [[WCTDatabase alloc] initWithPath:path];
NSData *password = [@"MyPassword" dataUsingEncoding:NSASCIIStringEncoding];
[database setCipherKey:password];
```



# 全局监控

WCDB 内提供统计的接口注册获取数据库操作的 SQL、性能、错误等，开发者可以将这些信息打印到日志或上报到后台，以调试或统计。

```objectivec
//Error Monitor
[WCTStatistics SetGlobalErrorReport:^(WCTError *error) {
    NSLog(@"[WCDB]%@", error);
}];

//Performance Monitor
[WCTStatistics SetGlobalPerformanceTrace:^(WCTTag tag, NSDictionary<NSString *, NSNumber *> *sqls, NSInteger cost) {
    NSLog(@"Database with tag:%d", tag);
    NSLog(@"Run :");
    [sqls enumerateKeysAndObjectsUsingBlock:^(NSString *sqls, NSNumber *count, BOOL *) {
        NSLog(@"SQL %@ %@ times", sqls, count);
    }];
    NSLog(@"Total cost %ld nanoseconds", (long)cost);
}];

//SQL Execution Monitor
[WCTStatistics SetGlobalSQLTrace:^(NSString *sql) {
    NSLog(@"SQL: %@", sql);
}];
```




# 参考

* [iOS/macOS 使用教程](https://github.com/Tencent/wcdb/wiki/iOS+macOS%e4%bd%bf%e7%94%a8%e6%95%99%e7%a8%8b)
* [ORM 使用教程](https://github.com/Tencent/wcdb/wiki/ORM%e4%bd%bf%e7%94%a8%e6%95%99%e7%a8%8b)
* [基础类、CRUD与Transaction](https://github.com/Tencent/wcdb/wiki/%e5%9f%ba%e7%a1%80%e7%b1%bb%e3%80%81CRUD%e4%b8%8eTransaction)
* [全局监控与错误处理](https://github.com/Tencent/wcdb/wiki/%e5%85%a8%e5%b1%80%e7%9b%91%e6%8e%a7%e4%b8%8e%e9%94%99%e8%af%af%e5%a4%84%e7%90%86)
* [从 FMDB 迁移到 WCDB](https://github.com/Tencent/wcdb/wiki/%e4%bb%8eFMDB%e8%bf%81%e7%a7%bb%e5%88%b0WCDB)


