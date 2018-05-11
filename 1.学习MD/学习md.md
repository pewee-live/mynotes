# mark down学习笔记  

## 标题 
	总共六级标题，建议在井号后加一个空格，这是最标准的 Markdown 语法。

	# 一级标题
	## 二级标题
	### 三级标题
	#### 四级标题
	##### 五级标题
	###### 六级标题

## 文本样式
	** 加粗 **
	* 斜体 *
	~~ 删除线 ~~
	` 底纹 `

## 列表
>在Markdown 下，无序列表直接在文字前加 「 - 」 或者 「 * 」 即可，有序列表则直接在文字前加 「1.」「2.」「3.」 。符号要和文字之间加上一个字符的空格。  

*   Red
*   Green
*   Blue  

*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
	
	Vestibulum enim wisi, viverra nec, fringilla in, laoreet
    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
    sit amet velit.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.

*   A list item with a blockquote:

    > This is a blockquote
    > inside a list item.

1. Bird
11. McHale
111. Parish

如果要放代码区块的话，该区块就需要缩进两次，也就是 8 个空格或是 2 个制表符： 


			Vestibulum enim wisi, viverra nec, fringilla in, laoreet
		    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
		    sit amet velit.
		

## 引用
只要在文本内容之前加 「 > （大于号）」 即可将文本变成引用文本。
>这是一个引用
>>应用引用


##水平线
三个「 - 」或「 * 」都可以画出一条水平分割线
***
##代码区块
	缩进 4 个空格或是 1 个制表符就可以,例如这就是一个代码区块
这是一个普通区块
##图片与链接
Markdown 支持两种形式的链接语法： 行内式和参考式两种形式。

不管是哪一种，链接文字都是用 [方括号] 来标记。

要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可，例如：

This is [an example](http://example.com/ "Title") inline link.

[This link](http://example.net/) has no title attribute.
如果你是要链接到同样主机的资源，你可以使用相对路径：
See my [About](/about/) page for details.

行内式的图片语法看起来像是：

	![Alt text](/path/to/img.jpg)
	
	![Alt text](/path/to/img.jpg "Optional title")

详细叙述如下：

1.    一个惊叹号 !
2.    接着一个方括号，里面放上图片的替代文字
3.    接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上 选择性的 'title' 文字。

##反斜杠
Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：  

1.	\   反斜线
2.	`   反引号
3.	*   星号
4.	_   底线
5.	{}  花括号
6.	[]  方括号
7.	()  括弧
8.	#   井字号
9.	+   加号
10.	-   减号
11.	.   英文句点
12.	!   惊叹号

##注释
	<!--注释-->
##脚注
	脚注总是成对出现的，「[^1] 」作为标记，可以点击跳至末尾注解。「 [^1]: 」填写注解，不论写在什么位置，都会出现在文章的末尾。  

它有脚注[^11]  
它没有脚注
[^11]:这个是脚注