原文：[Markdown-Syntax](https://github.com/slatedocs/slate/wiki/Markdown-Syntax)



这是在 Slate 中使用 Markdown 语法的参考。

### 标题

对于标题：

```markdown
# Level 1 Header
## Level 2 Header
### Level 3 Header
```

请注意，仅 1 级和 2 级标题将出现在目录中。

### 段落文字

对于普通文本，只需在一行上输入段落。

```markdown
This is some paragraph text. Exciting, no?
```

请确保段落上方和下方有空白行。

### 示例代码

对于示例代码：

~~~markdown
	```ruby
	# This is some Ruby code!
	```

	```python
	# This is some Python code!
	```
~~~

示例代码将出现在正文右侧的黑暗区域中。我们建议将示例代码放在 Markdown 文件中标题的正下方。

有关支持的语言的完整列表，请参见 [rouge](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers)。

### 代码注释

对于代码注释：

```markdown
> 这是一个代码注释。它会出现在代码示例旁边的右侧区域中。
```

代码注释与段落本质上是相同的，但是它们将与代码示例一起出现在右侧区域。

### 格式化代码注释（右列）及其说明（中间列）

为了正确格式化右栏中的代码和中间一栏中的文本，应首先使用代码段，例如：

~~~markdown
    # 我的标题

    ## 我的子标题

    ```csharp
        代码片段
    ```

    ```java
        代码片段
    ```

    ```php
    <?php
        代码片段
    ?>
    ```

    ```ruby
    #代码片段
    ```

    不管中心列有什么内容. Lorem ipsum
~~~

如果你将中间列的内容放在第一位，然后再放置代码段，会导致该代码段与中心列的最后一行对齐；

如果该代码段位于最前面，则该代码与中心列的第一行对齐。

### 表格

Slate 使用 PHP Markdown Extra 样式表：

```markdonw
Table Header 1 | Table Header 2 | Table Header 3
-------------- | -------------- | --------------
Row 1 col 1 | Row 1 col 2 | Row 1 col 3
Row 2 col 1 | Row 2 col 2 | Row 2 col 3
```

请注意，管道不需要在每条线上相互对齐。如果你不喜欢这种语法，你也可以在 markdown 中直接使用普通的html `<table>` 语法。

### 格式化文本

```markdown
This text is **粗体**, this is *斜体*, this is an `内嵌代码`.
```

你可以在表格，段落，列表中的任何位置使用这些格式设置规则，尽管它们会在代码块中逐字显示。

### 列表

```markdown
1. This
2. Is
3. An
4. Ordered
5. List

* This
* Is
* A
* Bullet
* List
```

### 嵌套列表

您可以通过将项目符号（或数字）缩进4个空格来在Markdown中创建子列表：

```markdown
* First
    1. 1
    1. 2
* Second
    * a
    * b
    * c
```

### 链接

```markdown
This is an [内部链接](#error-code-definitions), this is an [外部链接](http://google.com).
```

### 注意和警告

你也可以在 markdown 文档中嵌入一些 HTML 文本来添加突出显示的警告和注释：

```markdown
<aside class="notice">
You must replace meowmeowmeow with your personal API key.
</aside>
```

使用 `class ="notice"` 会显示蓝色标记，使用 `class ="warning"` 会显示红色警告，使用 `class ="success"` 会显示绿色标记。

HTML 块内的 Markdown 不会被处理。

### 需要帮助？

如果你对任何语法有疑问，或者感到困惑，请提出问题以告知我们。谢谢！


