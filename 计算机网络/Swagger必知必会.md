通过 Swagger 建模工具编写 RESTful API 接口之前，应该先了解的相关知识。


### RESTful

既然是写 RESTful API 接口，那么 RESTful 相关的知识应该要很熟。

- [GitHub：RESTful API 设计参考文献列表 ⭐️⭐️⭐️](https://github.com/aisuhua/restful-api-design-references)


### JSON

服务器与客户端通过 API 进行交互时，数据传输的文本类型使用 XML 还是 JSON ？

通过这张 [比较 XML 和 JSON API](https://trends.google.com/trends/explore?date=all&q=xml%20api,json%20api) Google 的趋势图，你应该使用基于 JSON 的媒体类型（**Content-Type: application/json**）作为数据交换格式。

- [介绍 JSON](http://www.json.org/json-zh.html)
- [JSON 风格指南 - Google](https://github.com/darcyliu/google-styleguide/blob/master/JSONStyleGuide.md)


### YAML

你可以通过 JSON 或者 YAML 在 Swagger 上编辑 API 接口文档。\
YAML 的优势：专门用于编写配置文件，支持添加注释，而且语法更简洁，更利于书写和阅读。\
当然，Swagger 也提供 YAML 和 JSON 相互转换的工具。

- [YAML Ain’t Markup Language (YAML™) Version 1.2](https://yaml.org/spec/1.2/spec.html)
- [YAML—— 基本语法](https://blog.csdn.net/weweeeeeeee/article/details/86622743)
- [YAML 语言教程@阮一峰](http://www.ruanyifeng.com/blog/2016/07/yaml.html)
- [YAML 简介@IBM](https://www.ibm.com/developerworks/cn/xml/x-cn-yamlintro/index.html)


### 通用标语（commonMark）- 就是 markdown

如果你想要在 YAML 文档中编辑添加富文本（比如文本**加粗**），就需要了解 markdown 相关的语法知识。

- [CommonMark Spec](https://spec.commonmark.org/0.29/)
- [通用标注](http://www.commonmark.cn/w/)


### OpenAPI

OpenAPI：一套通过 Swagger 编写 RESTful API 文档的语法标准。

💡 推荐采用最新的 OpenAPI 3.0 规范。

> 2016 年 1 月，Swagger 将 Spec（Swagger API Spec）捐献给 Open API Initiative (OAI)，成为 Open API Spec 的基础。

> OpenAPI 基于 Swagger 2.0 构建，Swagger 是 SmartBear 贡献给 Linux 基金会的。意在构建具有中立管理模型的新组织，以引领 Swagger 更上一层楼。“开放 API 战略” 的创始成员包括对贡献具有兴趣的 Google、IBM 和 Microsoft。同时还成立了一个技术开发者社区（TDC，Technical Developer Community）以对规范做开发。TDC 对于任何有意向做出贡献的个人都是开放的，无需会员身份。

- [OpenAPI 3.0 中文手册](https://fishead.gitbooks.io/openapi-specification-zhcn-translation/content/versions/3.0.0.zhCN.html#revisionHistory)
- [OpenAPI Specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md#oasObject)
- [OpenAPI 规范 3.0 版接近最终发布
](https://www.oschina.net/news/81692/openapi-3-come-soon)
- [OpenAPI Map](https://openapi-map.apihandyman.io/) - 通过动态导图的方式呈现 OpenAPI 规范


### swagger

> Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。      
> Swagger 的目标是对 REST API 定义一个标准的和语言无关的接口，可让人和计算机无需访问源码、文档或网络流量监测就可以发现和理解服务的能力。   
> 当通过 Swagger 进行正确定义，用户可以理解远程服务并使用最少实现逻辑与远程服务进行交互。

#### 工具

- [Swagger 官网](https://swagger.io/)
- Swagger API Spec，描述 Rest API 的语言，标准就是上面所说的 OpenAPI；
- [swagger editor](https://editor.swagger.io/)，Swagger API Spec 的编辑器
- [swagger ui](http://petstore.swagger.io/)，将 Swagger API Spec 以 HTML 页面展现出来的模块 | [github](https://github.com/swagger-api/swagger-ui)
- [swagger2markup](https://github.com/Swagger2Markup/swagger2markup)


#### 文章

- [语雀：Swagger 3.0](https://www.yuque.com/alipay2088002421051737/dvn38k/ofpgqg)
- [使用 Swagger 生成 RESTful API 文档](https://www.xncoding.com/2017/06/09/web/swagger.html)
- [知乎专栏：Swagger：Rest API 的描述语言](https://zhuanlan.zhihu.com/p/21353795)
- [Swagger 从入门到精通](https://legacy.gitbook.com/book/huangwenchao/swagger/details)
