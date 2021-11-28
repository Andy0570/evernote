### 介绍

OpenAPI 规范（OAS）定义了一个标准的、语言无关的 RESTful API 接口规范，它可以同时允许开发人员和操作系统查看并理解某个服务的功能，而无需访问源代码，文档或网络流量检查（既方便人类学习和阅读，也方便机器阅读）。正确定义 OAS 后，开发者可以使用最少的实现逻辑来理解远程服务并与之交互。

此外，文档生成工具可以使用 OpenAPI 规范来生成 API 文档，代码生成工具可以生成各种编程语言下的服务端和客户端代码，测试代码和其他用例。


### 数据类型

OAS 使用几种已知的 `format` 格式来详细定义所使用的 `type` 数据类型。

`format` 属性是开放的字符串值，可以是自定义的任意类型值，比如：`email`、`uuid`。

OAS 定义的 `formats` 类型如下：

| 通用名称 | 数据类型 | 数据格式 | 描述 |
| --- | --- | --- | --- |
| integer | integer | int32 | signed 32 bits，32位有符号数 |
| long | integer | int64 | signed 64 bits，64位有符号数 |
| float | number | float |  |
| double | number | double |  |
| string | string |  |  |
| byte | string | byte | base64 encoded characters，base64 编码字符 |
| binary | string | binary | any sequence of octets |
| boolean | boolean |  |  |
| date | string | date | 参考 [RFC3339](https://xml2rfc.tools.ietf.org/public/rfc/html/rfc3339.html#anchor14) - `full-date` |
| dateTime | string | date-time | 参考 [RFC3339](https://xml2rfc.tools.ietf.org/public/rfc/html/rfc3339.html#anchor14) - `date-time` |
| password | string | password | UI 提示隐藏输入 |


### OpenAPI 根对象

这是 OpenAPI 的根文档对象。

```yaml
# OpenAPI 规范版本号
openapi: 3.0.0

# API 元数据信息
info:

# 服务器连接信息
servers:

# API 的分组标签
tags: 

# 对所提供的 API 有效的路径和操作
paths:

# 一个包含多种纲要的元素，可重复使用组件
components:

# 声明 API 使用的安全机制
security:

# 附加文档
externalDocs:
```

### Info 对象

Info 对象描述 API 的元数据信息。

```yaml
# API 元数据信息
info:
  title:  xx开放平台接口文档                    # 应用的名称
  description: |                          
    简短的描述信息，支持 markdown 语法。 | 表示换行，< 表示忽略换行。
  version: "1.0.0"                            # API 文档的版本信息
  termsOfService: 'http://swagger.io/terms/'  # 指向服务条款的 URL 地址
  contact:                                    # 所开放的 API 的联系人信息
    name: API Support                           # 人或组织的名称
    url: http://www.example.com/support         # 指向联系人信息的 URL 地址
    email: apiteam@swagger.io                   # 人或组织的 email 地址
  license:                                    # 所开放的 API 的证书信息。
    name: Apache 2.0
    url: 'http://www.apache.org/licenses/LICENSE-2.0.html'
```

### Server 对象

所有的 API 端点都是相对于基本 URL 的。例如，假设 <https://api.example.com/v1> 的基本 URL 中，`/users` 端点指的是 <https://api.example.com/v1/users>。

```
https://api.example.com/v1/users?role=admin&status=active
\ __________________ / \__/ \ ______________________ /
         服务器URL      端点路径        查询参数
```

Server 表示一个服务器的对象。这里通常填写测试服务器或者生产服务器的 IP 地址、端口版本号等信息（指定基本 URL）。

```yaml
# 服务器连接信息
servers:
  - url: https://development.gigantic-server.com/v1
    description: 开发服务器
  - url: https://staging.gigantic-server.com/v1
    description: 测试服务器
  - url: https://api.gigantic-server.com/v1
    description: 生产服务器
```

### Tag 对象

Tag 对象用于对 path 对象中的 API 进行分组，可以更美观的生成文档。

```yaml
# API 的分组标签
tags: 
  - name: pet
    description: 与宠物相关的接口
    externalDocs:
      description: 外部文档
      url: 'http://swagger.io'
  - name: store
    description: 宠物商店
  - name: user
    description: 用户操作相关
    externalDocs:
      description: 外部文档
      url: 'http://swagger.io'
```


### External Documentation 对象

允许引用外部资源来扩展文档。

```yaml
# 附加文档
externalDocs:
  description: Find out more about Swagger
  url: 'http://swagger.io'
```

### Components 对象

`components` 对象包含开放 API 规范规定的各种**可重用组件**。当没有被其他对象引用时，在这里定义的组件不会产生任何效果。

```yaml
# 一个包含多种纲要的元素，可重复使用组件
components:
  schemas:
 
  responses:
  
  parameters:
  
  examples:
    
  requestBodies:
  
  headers:
    
  securitySchemes:
  
  links:
  
  callbacks:
```


### Path 对象

定义各个端点和操作的相对路径。

```yaml
# 对所提供的 API 有效的路径和操作
paths:
  /pet/{petId}:
    get:
      tags:
        - pet
      summary: 简要总结，描述此路径内包含的所有操作。
      description: 详细说明，用于描述此路径包含的所有操作。
      operationId: getPetById      # 此操作的唯一标识符
      parameters:                 # 参数列表
        - name: petId
          in: path
          description: 路径参数，宠物 ID。
          required: true
          schema:
            type: integer
            format: int64
      responses:                  # 接口响应内容
        '200':
          description: 操作成功
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: integer
                    format: int64
                  category:
                    type: object
                    properties:
                      id:
                        type: integer
                        format: int64
                      name:
                        type: string
                  name:
                    type: string
                    example: doggie
                  photoUrls:
                    type: array
                    items:
                      type: string
                  tags:
                    type: array
                    items:
                      type: object
                      properties:
                        id:
                          type: integer
                          format: int64
                        name:
                          type: string
                  status:
                    type: string
                    description: 宠物在商店的状态
                    enum:
                      - available
                      - pending
                      - sold
        '400':
          description: 无效的 id
        '404':
          description: 找不到指定的资源
      security:             # 作用于此操作的安全机制
        - api_key: []         # 可以声明一个空数组来变相的移除顶层的安全声明
```

返回的 Pet 对象详解：
![返回的 Pet 对象详解](https://upload-images.jianshu.io/upload_images/2648731-3ca625a560103380.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用 `components` 对象进行优化：

```yaml
# 对所提供的 API 有效的路径和操作
paths:
  '/pet/{petId}':
    get:
      tags:
        - pet
      summary: 简要总结，描述此路径内包含的所有操作
      description: 详细说明，用于描述此路径包含的所有操作
      operationId: getPetById      # 此操作的唯一标识符
      parameters:                  # 参数列表
        - name: petId
          in: path
          description: pet ID
          required: true
          schema:
            type: integer
            format: int64
      responses:                  # 响应列表
        '200':
          description: 操作成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '400':
          description: 无效的 id
        '404':
          description: 找不到指定的资源
      security:             # 作用于此操作的安全机制
        - api_key: []         # 可以声明一个空数组来变相的移除顶层的安全声明

components:
  schemas:
    Category:
      type: object
      properties:
        id:
          type: integer
          format: int64
        name:
          type: string
    Tag:
      type: object
      properties:
        id:
          type: integer
          format: int64
        name:
          type: string
    Pet:
      type: object
      properties:
        id:
          type: integer
          format: int64
        category:
          $ref: '#/components/schemas/Category'
        name:
          type: string
          example: doggie
        photoUrls:
          type: array
          items:
            type: string
        tags:
          type: array
          items:
            $ref: '#/components/schemas/Tag'
        status:
          type: string
          description: 宠物在商店的状态
          enum:
            - available
            - pending
            - sold
```

通过 `components` 对象封装可重用对象，然后通过 `$ref` 标签进行引用：
![components 对象封装可重用对象](https://upload-images.jianshu.io/upload_images/2648731-5354a90f7414cc8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Parameter 参数对象

描述一个操作参数。

参数位置（`in`）的值：

| 参数位置 | 描述 | 示例 |
| --- | --- | --- |
| path | 参数的值是 URL 操作路径的一部分 | `/items/{itemId}`，路径参数是`{itemId}`  |
| query | 追加在 URL 地址之后的参数 | `/items?id=###`，查询参数是 `id` | 
| header | 请求中使用的自定义请求头 |  |
| cookie | 用于传递特定的 cookie 值 |  |


一个值数组，数组元素为 64 位整数值的请求头参数：

```yaml
name: token
in: header
description: 需要作为 HTTP header 请求头传递的 token 参数
required: true
schema:
  type: array
  items:
    type: integer
    format: int64
style: simple
```

路径 `/pet/{petId}` 下的 `petId` 路径参数：

```yaml
parameters:               # 参数列表（以“数组”格式描述参数）
  - name: petId             # 参数名称
    in: path                # 参数的位置 path/query/header/cookie
    description: URL 路径参数，宠物 ID。
    required: true          # 是否是必选参数，path 路径下的参数必须为 true。
    deprecated: false       # 参数是否被弃用
    allowEmptyValue: false  # 是否允许传递空参数，仅在路径为 query 下有效。
    schema:                 # 描述参数的结构
      type: integer
      format: int64
      example: 123456
```

一个值类型为字符串的路径参数：
```yaml
parameters:               # 参数列表（以“数组”格式描述参数）
  - name: username       # 一个值类型为字符串的路径参数
    in: path
    description: URL 路径参数，用户名。
    required: true
    schema:
      type: string
      pattern: "[a-z0-9]{8,64}" # 正则表达式
      minLength: 8              # 字符串的最小长度
      maxLength: 64             # 字符串的最大长度
```

一个值类型为字符串的可选查询参数，允许通过通过重复参数来传递多个值：

```yaml
parameters: # 参数列表
  - name: id
    in: query
    description: 通过 id 查询对象。
    required: false   # 可选的查询参数
    schema:           # schema - 参数的结构
      type: array
      items:            # 只有 array 类型 才有 items 项，描述数组项的数据类型
        type: string
    style: form       # style - 描述如何序列化参数？
    explode: true     # 生成带分隔符的参数值
```

通过状态值（数组类型）查询，可以传入多个值：
`/？status="["available","pending"]"`
```yaml
parameters:
  - name: status
    in: query
    description: Status values that need to be considered for filter
    required: true
    explode: true
    schema:
      type: array
      items:
        type: string
        enum: # 该参数是一个枚举类型
          - available
          - pending
          - sold
        default: available  # 默认枚举类型
```

URL 请求路径中添加查询参数，实现分页输出：

`GET https://example.com/v1/resources?pageSize=10&pageNumber=1`

```yaml
parameters:
  - name: pageSize
    in: query
    description: 每页返回的数量
    type: integer
    format: int32
    minimum: 0             # 最小值
    exclusiveMinimum: true  # 数值必须 > 最小值
    maximum: 100           # 最大值
    exclusiveMaximum: false # 数值必须 < 最大值
    multipleOf: 10         # 数值必须是 multipleOf 的整数倍
  - name: pageNumber
    in: query
    description: 当前页码
    type: integer
```


### Request Body 请求体对象

定义请求体。

```yaml
paths:
  /pet:
    post:
      tags:
        - pet
      summary: 向商店中添加新的宠物
      operationId: addPet
      responses:
        '405':
          description: 非法的操作
      requestBody:    # 请求体
        description: 需要被添加进商店的 Pet 对象
        required: true # 请求体是否被包含在请求中，默认值 false
        content:      # 请求体的内容
          application/json:
            schema:
              $ref: '#/components/schemas/Pet'
```

为了方便重用，`requestBody` 的内容也可以放到 `components` 对象的 `requestBodies` 中：
```yaml
# 对所提供的 API 有效的路径和操作
paths:
  /pet:
    post:
      tags:
        - pet
      summary: 向商店中添加新的宠物
      operationId: addPet
      responses:
        '405':
          description: 非法的操作
      requestBody:    # 请求体
        $ref: '#/components/requestBodies/Pet'

components:
  schemas:
    Category:
      type: object
      properties:
        id:
          type: integer
          format: int64
        name:
          type: string
    Tag:
      type: object
      properties:
        id:
          type: integer
          format: int64
        name:
          type: string
    Pet:
      type: object
      required:       # 必填字段
        - name
        - photoUrls
      properties:
        id:
          type: integer
          format: int64
        category:
          $ref: '#/components/schemas/Category'
        name:
          type: string
          example: doggie
        photoUrls:
          type: array
          items:
            type: string
        tags:
          type: array
          items:
            $ref: '#/components/schemas/Tag'
        status:
          type: string
          description: 宠物在商店的状态
          enum:
            - available
            - pending
            - sold
  requestBodies:
    Pet:
      description: 需要被添加进商店的 Pet 对象
      required: true          # 请求体是否被包含在请求中，默认值 false
      content:                # 请求体的内容
        application/json:
          schema:
            $ref: '#/components/schemas/Pet'
```

### Media Type 类型

#### 文件上传和下载

```yaml
# 使用 base64 编码传输的内容
schema:
  type: string
  format: base64
  
# 以二进制（octet-stream，八位字节流）传输的内容
schema:
  type: string
  format: binary
```

一个使用 `POST` 操作提交文件的 `requestBody` 看起来像下面这样：

```yaml
requestBody:
  content:
    application/octet-stream:
      # any media type is accepted, functionally equivalent to `*/*`
      # 任何媒体类型都被接受，功能上等同于 `* / *`
      schema:
        # a binary file of any type
        # 任何类型的二进制文件
        type: string
        format: binary
```

此外，可以指定明确的媒体类型：

```yaml
# 可以指定多个特定媒体类型
requestBody:
  content:
      # png 或 jpeg 类型的二进制文件
    'image/jpeg':
      schema:
        type: string
        format: binary
    'image/png':
      schema:
        type: string
        format: binary
```

为了同时上传多个文件，必须指定 `multipart` 媒体类型。

```yaml
requestBody:
  content:
    multipart/form-data:
      schema:
        properties:
          # 属性名称 'file' 将用于所有文件
          file:
            type: array
            items:
              type: string
              format: binary
```

使用 `multipart/form-data` 作为 `Content-Type` 来传送请求体是很常见的做法。

当定义 `multipart` 内容的输入参数时必须指定 `schema` 属性。这不但支持复杂的结构而且支持多文件上传机制。

```yaml
requestBody:
  content:
    multipart/form-data:
      schema:
        type: object
        properties:
          id:
            type: string
            format: uuid
          address:
            # default Content-Type for objects is `application/json`
            # 如果属性是复杂对象或者复杂对象的数组，那么默认的 Content-Type 是 application/json
            type: object
            properties: {}
          profileImage:
            # default Content-Type for string/binary is `application/octet-stream`
            # 如果属性是 type: string 与 format: binary 或 format: base64(也就是文件对象) 的组合，那么默认的 Content-Type 是 application/octet-stream
            type: string
            format: binary
          children:
            # default Content-Type for arrays is based on the `inner` type (text/plain here)
            # 如果属性是一个原始值或者是一个原始值的数组，那么默认的 Content-Type 是 text/plain
            type: array
            items:
              type: string
          addresses:
            # default Content-Type for arrays is based on the `inner` type (object shown, so `application/json` in this example)
            type: array
            items:
              type: '#/components/schemas/Address'
```

### Encoding 对象

对具体属性的 `Content-Type` 的编码。默认值取决于属性的类型：

* `application/octet-stream` 编码适用于 `binary` 格式的 `string`；
* `text/plain` 适用于其他原始值；
* `application/json` 适用于 `object`；
* 对于 `array` 值类型的默认值取决于数组内元素的类型，默认值可以是明确的媒体类型 (比如 `application/json`), 或者通配符类型的媒体类型 (比如 `image/*`), 又或者是用分号分隔的两种媒体类型。

Encoding 对象示例：

```yaml
requestBody:
  content:
    multipart/mixed:
      schema:
        type: object
        properties:
          id:
            # default is text/plain
            type: string
            format: uuid
          address:
            # default is application/json
            type: object
            properties: {}
          historyMetadata:
            # need to declare XML format!
            description: metadata in XML format
            type: object
            properties: {}
          profileImage:
            # default is application/octet-stream, need to declare an image type only!
            type: string
            format: binary
      encoding:
        historyMetadata:
          # require XML Content-Type in utf-8 encoding
          contentType: application/xml; charset=utf-8
        profileImage:
          # only accept png/jpeg
          contentType: image/png, image/jpeg
          headers:
            X-Rate-Limit-Limit:
              description: The number of allowed requests in the current period
              schema:
                type: integer
```

### Responses 对象

描述一个操作可能发生响应的响应码与响应包含的响应体的对象。

一份 API 文档不必包含所有可能响应码，因为有些状态码无法提前预知。尽管如此，一份文档还是应当包含所有成功的响应和任何已知的错误响应。

`default` 字段可以用来标记一个响应适用于其他未被规范明确定义的 HTTP 响应码的默认响应。
一个 `Responses` 对象必须至少包含一个响应码，而且是成功的响应。


一个代表成功操作的 200 响应和一个代表其他操作状态的默认响应（暗示是一个错误）：

```yaml
'200':
  description: 操作成功返回的对象
  content:
    application/json:
      schema:
        $ref: '#/components/schemas/Pet'
default:
  description: Unexpected error，未知的错误
  content:
    application/json:
      schema:
        $ref: '#/components/schemas/ErrorModel'
```

带 HTTP 头的普通文本类型的响应：

```yaml
description: 响应简单的字符串
content:
  text/plain:
    schema:
      type: string
    example: 'whoa!'
headers:
  X-Rate-Limit-Limit:
    description: 当前期间允许的请求数目
    schema:
      type: integer
  X-Rate-Limit-Remaining:
    description: 当前期间的剩余请求数
    schema:
      type: integer
  X-Rate-Limit-Reset:
    description: 当前期间剩余的秒数
    schema:
      type: integer
```

### Response 对象

单个 API 操作的响应对象。

一个包含复杂类型的数组格式的响应：
```yaml
responses:
  '200':
    description: successful operation
    content:
      application/json:
        schema:
          type: array  # 返回一个数组，数组中每个值都是 Pet 对象。
          items:
            $ref: '#/components/schemas/Pet'
```

字符串响应：
```yaml
description: 响应一个字符串
content:
  text/plain:
    schema:
      type: string
```

带 HTTP 头的普通文本类型的响应：
```yaml
description: 响应一个字符串
content:
  text/plain:
    schema:
      type: string
    example: 'whoa!'
headers:
  X-Rate-Limit-Limit:
    description: 当前期间允许的请求数
    schema:
      type: integer
  X-Rate-Limit-Remaining:
    description: 当前期间剩余请求数
    schema:
      type: integer
  X-Rate-Limit-Reset:
    description: 当前时段剩余的秒数
    schema:
      type: integer
```

没有返回值的响应：

```yaml
responses:
  '400':
    description: Invalid ID supplied
  '404':
    description: Pet not found
  '405':
    description: Validation exception
```


### Header 对象

`Header` 对象的结构与 `Parameter` 对象的结构相似，并添加以下规则：

1. `name` 属性不必指定！
2. `in` 属性不必指定！
3. 受 loaction 影响的所有特征必须适用于 `header` 位置，例如 `style` 属性。

数据类型为 `integer` 的 header 对象示例：

```yaml
headers:
  X-Rate-Limit-Limit:
    description: 当前期间允许的请求数
    schema:
      type: integer
  X-Rate-Limit-Remaining:
    description: 当前期间剩余请求数
    schema:
      type: integer
  X-Rate-Limit-Reset:
    description: 当前时段剩余的秒数
    schema:
      type: integer
```


### Reference 对象

一个允许引用规范内部的其他部分或外部规范的对象。

示例：
```yaml
$ref: '#/components/schemas/Pet'
```





### Schema 对象

Schema 对象用于定义输入和输出的数据类型。这些类型可以是对象，也可以是原始值和数组。

字符串示例：

```yaml
type: string
format: email
```

模型示例：

```yaml
type: object
required:
- name
properties:
  name:
    type: string
  address:
    $ref: '#/components/schemas/Address'
  age:
    type: integer
    format: int32
    minimum: 0
```


### Security Schema 对象

`securitySchemes` 和 `security` 关键字用于描述 API 中使用的身份验证方法。


`components` 对象下的 `securitySchemas` 对象示例：

```
# 一个包含多种纲要的元素，可重复使用组件
components:
  securitySchemes:
    petstore_auth:
      type: oauth2
      flows:
        implicit:
          authorizationUrl: 'http://petstore.swagger.io/oauth/dialog'
          scopes:
            'write:pets': modify pets in your account
            'read:pets': read your pets
    api_key:
      type: apiKey
      name: api_key
      in: header
```

#### Basic Authentication Sample

基础（basic）鉴权。

```yaml
components:
  securitySchemes:
    BasicAuth:
      type: http
      scheme: basic
```

`type` 字段用于描述 security scheme 的类型。有效值包括 `apiKey`、`http`、`oauth2`、`openIdConnect`。

#### API Key Sample

API 秘钥鉴权。

```yaml
securitySchemes:
  api_key:
    type: apiKey
    name: api_key  # name 用于 header、 query 或 cookie 的参数名字
    in: header     # 指定 API 密钥的位置。有效值包括：query、header、cookie
```

通过关键字 `in` 指示 API 秘钥所在位置。通常 API 秘钥会放在消息头、请求参数或者消息体中。


#### JWT Bearer Sample

```yaml
type: http
scheme: bearer
bearerFormat: JWT
```

`bearerFormat` 用于提示客户端所使用的 bearer token 的格式。Bearer token 通常通过授权服务器生成，所以这个字段最主要的目的是用来记录这个信息。

#### 隐含的 OAuth2 示例

OAuth2 鉴权。

当我们定义 OAuth2 类型的安全项时，我们通常会定义 OAuth2 的流程（flow）和并根据选定的流程配置相应的鉴权地址（`authorizationUrl`）和/或令牌地址（`tokenUrl`）。

OAuth2 的流程（Flow）有如下四个：

| 流程 | 所需要的 URL |
| --- | ---------- |
| implicit	| authorizationUrl（鉴权地址）| 
| password	| tokenUrl（令牌地址）| 
| application	| tokenUrl | 
| accessCode	| authorizationUrl and tokenUrl | 

示例：

```yaml
securitySchemes:
  petstore_auth:
    type: oauth2
    description: 对 security scheme 的简短描述。
    flows: # 一个包含所支持的 flow types 的配置信息的对象。
      implicit: # OAuth 隐式流的配置
        authorizationUrl: 'http://petstore.swagger.io/oauth/dialog' # 此流程所需的鉴权地址
        scopes: # 此 OAuth2 安全方案的作用范围
          'write:pets': modify pets in your account
          'read:pets': read your pets
```



OAuth Flow 对象示例：

```yaml
type: oauth2
flows: # 一个包含所支持的 flow types 的配置信息的对象。
  implicit: # OAuth 隐式流的配置
    authorizationUrl: https://example.com/api/oauth/dialog # 此流程所需的鉴权地址
    scopes: # 此 OAuth2 安全方案的作用范围
      write:pets: modify pets in your account
      read:pets: read your pets
  authorizationCode: # OAuth 授权码流程的配置
    authorizationUrl: https://example.com/api/oauth/dialog
    tokenUrl: https://example.com/api/oauth/token # 此流程所需的令牌地址。
    scopes:
      write:pets: modify pets in your account
      read:pets: read your pets
```


### 参考

- [OpenAPI 3.0 中文手册](https://fishead.gitbooks.io/openapi-specification-zhcn-translation/content/versions/3.0.0.zhCN.html#revisionHistory)
- [Break 易站 - Swagger 和 OpenAPI](https://www.breakyizhan.com/swagger/2806.html) ⭐️⭐️⭐️
- [OpenAPI Specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md#oasObject)
- [OpenAPI 规范 3.0 版接近最终发布](https://www.oschina.net/news/81692/openapi-3-come-soon)
- [OpenAPI Map](https://openapi-map.apihandyman.io/) - 通过动态导图的方式呈现 OpenAPI 规范
