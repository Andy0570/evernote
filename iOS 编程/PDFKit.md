https://developer.apple.com/documentation/pdfkit?language=objc

# PDFKit

在应用程序中显示和操作PDF文档。



# Topics

## Views

| 属性               | 注释                                       |
| ---------------- | ---------------------------------------- |
| PDFView          | PDFView对象将PDF Kit的功能封装到单个小部件中，您可以使用Interface Builder将其添加到应用程序中。 |
| PDFThumbnailView | PDFThumbnailView对象包含一组缩略图，每个缩略图代表PDF文档中的页面。 |



## Content Model

| 属性           | 注释                                       |
| ------------ | ---------------------------------------- |
| PDFDocument  | PDFDocument对象表示PDF数据或PDF文件，并定义写入，搜索和选择PDF数据的方法。 |
| PDFPage      | PDFPage是NSObject的子类，定义了用于呈现PDF页面的方法，并使用注释，文本和选择。 |
| PDFOutline   | PDFOutline对象是可以表示PDF文档结构的树结构层次结构中的元素。    |
| PDFSelection | PDFSelection对象标识PDF文档中文本的连续或非连续选择的文字。    |



## Annotations - 注释

| 属性             | 注释                             |
| -------------- | ------------------------------ |
| PDFAnnotation  | PDF文档中的注释。                     |
| PDFAction      | 激活PDF注释或单击大纲项目时执行的跳转操作。        |
| PDFDestination | PDFDestination对象描述PDF页面上的跳转目录。 |
| PDFBorder      | 可选的注释边框，完全在注释矩形内绘制。            |



## Reference

[PDFKit Enumerations](https://developer.apple.com/documentation/pdfkit/pdfkit_enumerations?language=objc)
