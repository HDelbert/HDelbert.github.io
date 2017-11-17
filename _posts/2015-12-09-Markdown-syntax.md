---
layout: post
title: "Markdown语法"
date: 2015-12-9 16:01:20 +08:00
tags: markdown
---

**段落和换行**

一个 Markdown 段落是由一个或多个连续的文本行组成，它的前后要有一个以上的空行。普通段落不该用空格或制表符来缩进。

两个以上的空格然后回车，表示换行。

**标题**

	# 这是H1
    ## 这是H2
    ###### 这是H6

**区块引用**

    > This is the first level of quoting.
    >
    > > This is nested blockquote.
    >
    > Back to the first level.

引用的区块内也可以使用其他的 Markdown 语法，包括标题、列表、代码区块等。

**列表**

*   无序

    	* Red
    	* Green
    	* Blue

*   有序

    	1. Bird
    	2. McHale
    	3. Parish

列表项目可以包含多个段落，每个项目下的段落都必须缩进 4 个空格或是 1 个制表符。

**代码区块**

要在 Markdown 中建立代码区块很简单，只要简单地缩进 4 个空格或是 1 个制表符就可以。

    这是一个普通段落：

        这是一个代码区块。

**分隔线**

你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。

    * * *
    ---

**链接**

Markdown 支持两种形式的链接语法行内式和参考式两种形式。例如：

    This is [an example](http://example.com/ "Title") inline link.
    [This link](http://example.net/) has no title attribute.
    This is [an example] [id] reference-style link.
    [id]: http://example.com/  "Optional Title Here"

**强调**

Markdown 使用星号（\*）和底线（\_）作为标记强调字词的符号，被 \* 或 \_ 包围的字词会被转成斜体，用两个 \* 或 \_ 包起来的话，则会被转成加粗。

    **abc** *dc*
    _abc_ __dc__

**代码**

如果要标记一小段行内代码，你可以用反引号把它包起来（\`）。
如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段。

    This is `code`.
    `` This is `code`. ``

**图片**

Markdown 使用一种和链接很相似的语法来标记图片，同样也允许两种样式：行内式和参考式。

    ![Alt text](/path/to/img.jpg)
    ![Alt text](/path/to/img.jpg "Optional title")
    ![Alt text][id]
    [id]: url/to/image  "Optional title attribute"

**反斜杠**

Markdown 可以利用反斜杠来插入一些在语法中有其它意义的符号。

## 扩展

**表格**

	| Tables        | Are           | Cool  |
	| ------------- |:-------------:| -----:|
	| col 3 is      | right-aligned | $1600 |
	| col 2 is      | centered      |   $12 |
	| zebra stripes | are neat      |    $1 |
