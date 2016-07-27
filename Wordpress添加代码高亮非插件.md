#Pre
![][1]
自从换了主题，决定用markdown写文章之后，就再也不用原来的使用网站转换代码成高亮了然后插入文本了。但是新主题对代码效果不好。
终于在使用Remarkable这款linux下的markdown编辑器的时候，发现可以保存成有代码高亮格式的网页，通过导出成网页功能查看后发现是利用了[highlight这个项目](https://highlightjs.org/)。

# 使用说明
## 下载
从 https://highlightjs.org/ 下载文件，当然可以自定义自己需要的语言，下载完成后解压目录结构如下：
```
.
├── CHANGES.md
├── highlight.pack.js
├── LICENSE
├── README.md
├── README.ru.md
└── styles/
```
我们只需要两部分`highlight.pack.js`和`styles/`文件夹中的一个样式，可以在官网查看到demo，这里我们选择`androidstudio.css`样式。
## 上传
将`highlight.pack.js`和`androidstudio.css`文件上传到你wordpress的`wp-content/uploads/`文件夹下

## 修改引用
在你wordpress主题的`header.php`里`</header>`之前，添加如下内容：
```html
<link href="http://yourdomain.com/wp-content/uploads/androidstudio.css" rel="stylesheet"/>
<script src="http://yourdomain.com/wp-content/uploads/highlight.pack.js">  </script>
<script>   hljs.initHighlightingOnLoad();  </script>
```
## 删除缓存
如果你用了wp-super cache之类的话，删除缓存，否则只需要刷新网页即可。

# 语言高亮
一般脚本会自动高亮，但是可以在写markdown格式博客的时候，就直接指定好语言，如果你是用markdown写，代码这种格式
\`\`\`
yourcode
\`\`\`
在第一个\`\`\`后面添加之前自定义的支持的语言即可。


[1]: http://www.findspace.name/wp-content/uploads/2015/06/wordpressCodeHighLigh.png "wordpressCodeHigh"