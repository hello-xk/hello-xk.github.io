---
layout: post
title: 一步一步构建Jekyll主题
categories: jekyll github markdown rouge
image: jekyll.jpg
date: 2016-9-3 15:47:05
pid: 20160903-154705
# excerpt: ""
# you can override the settings in _config.yml here !!
---
讲述当前Jekyll站点的制作过程

{% include toc %}

## 搭建本地的Jekyll环境

因为图方便所以个人直接在Ubuntu下搭建了环境，Windows平台可以网上搜索教程，个人推荐Windows安装虚拟机然后安装Ubuntu。

1. 安装ruby环境
  `sudo apt-get install ruby`
2. 安装Jekyll
  `sudo gem install jekyll`
3. 安装kramdown
  `sudo gem install kramdown`
4. 安装rouge
  `sudo gem install rouge`
5. 测试Jekyll是否安装完成
  `jekyll --version`

> 从上面可以看出，这是一套基于Ruby语言的工具集。
> **题外话：**gem install是ruby用来管理ruby工具集的工具，而npm install是nodejs用来管理js工具集的工具。

## 本地跑起来
```
jekyll new mytheme
cd mytheme
jekyll server
```
运行上面的命令，然后访问[127.0.0.1:4000](http://127.0.0.1:4000)，就能看到一个由Jekyll搭建的博客了。

## Github Pages环境本地化

上面构建的只是Jekyll的本地环境，当push到Github Pages后环境会有所变化，为了本地看到的效果和托管在Github Pages看到的效果一致，我们最好搭建本地的Github Pages环境。

1. 升级ruby到2.0.0以上
  如果`ruby --version`查看版本低于2.0.0，那么需要升级ruby。
2. 安装ruby工具集管理工具Bundler
  `sudo gem install bundler`
3. 创建Gemfile
  在上面的mytheme根目录下创建一个Gemfile文件，内容为：
  `source 'https://rubygems.org'`
  `gem 'github-pages', group::jekyll_plugins`
4. 安装Github Pages的工具集
  在Gemfile所在的目录，即Jekyll主题的根目录，执行下面的命令：
  `bundle install`
5. 跑起来
  `bundle exec jekyll serve`

如果出现`bundle exec jekyll serve`能启动，而`jekyll serve`不能启动，则删除Gemfile和Gemfile.lock重新运行`jekyll serve`即可。
更多Github Pages本地化环境搭建，可参考[github-helper-setting-up-your-github-pages-site-locally-with-jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll)

## 需要一个网页原型

Github Pages和Jekyll本地环境已经搭建完成，访问[127.0.0.1:4000](http://127.0.0.1:4000)也能够看到一个简单的博客，接下来就是思考自己的博客应该长哪样。

一般来说，要定制自己的博客，最好先设计出博客的网页原型，所谓网页原型即是使用html、css甚至js来完成静态的网页效果。当前博客的原型只有三个页面：`index.html`、`article.html`和`post.html`。

最后，**Jekyll模板已经跑起来了，网页原型也有了，怎么将两者结合起来呢？**在整合之前，我们需要先了解Jekyll博客系统。

## 初步认识Jekyll

### Jekyll是什么

Jekyll的描述是，将纯文本转化为静态博客网站，不需要数据库和评论功能。
其实更贴切的描述应该是这样

> Jekyll是一个静态博客系统，不支持写入，所以没有数据库和评论功能。

### 静态网页

静态网页和动态网页的区别是，静态网页无论是否访问，它就已经存在那里，并且内容已经固定不可改。所以Jekyll在每次增加文章时就已经生成对应的静态网页，而不是每次访问时动态生成的。

举个例子

> 当前Jekyll模板有一个页面：categories.html（目录和对应的文章列表页）
>
> 当新增一篇demo.md文章后，Jekyll会重新生成新的博客站点，demo.md会被转化为demo.html，而categories.html会被重新生成，内容是包含demo这篇新文章的列表。
>
> **所以**，Jekyll的页面都是在访问前就已经重新生成了，这就是静态。

### 没有数据库

如果`数据库`指的是像MySQL那种可读写的数据库，Jekyll确实没有。但是如果`数据库`指的只是存储数据的地方，Jekyll其实是有的，只不过是`只读`的。

Jekyll内的`_config.yml`配置、各种内置对象(`site`、`page`、`categories`等)、用户自定义的内容（变量、集合、文本、网页等），都可以看做是Jekyll的数据库，开发者可以访问这个数据库组织自己的页面内容，**除了可以在Jekyll构建站点时直接访问，还可以写到json格式的文件暴露出来在网页初始化时访问**。

> 但有一点要注意：Jekyll内所有可访问的变量都是`静态`的，也即是`只读`的，所以不可以重新赋值！

### 没有评论功能

Jekyll是无法写入的，所以无法支持评论功能。任何写入操作都只能借助第三方服务。

## 理解Markdown是如何工作的

这是一段markdown文本：

```markdown
## Markdown Demo

This is a `markdown` demo

> try it
```

使用markdown转换器转换后得到的html是这样的：

```html
<h2>Markdown Demo</h2>
<p>This is a <code>markdown</code> demo</p>
<blockquote>
    <p>try it</p>
</blockquote>
```

直接使用浏览器打开是这样的：

![markdown-plain][markdown-plain]

**Markdown转换结果只是单纯的html页面，关于页面的样式，需要我们自己提供css**
添加下面的css

```css
<style>
h2 {
	color: red;
}
blockquote {
	background-color: #fef3f3;
	border-left: 4px solid #d6a6a6;
	margin: 0;
}
</style>
```

为页面添加css后html是这样的：

![markdown-css][markdown-css]

总结

> markdown解析器只是将文本转换为html，并不会为html添加默认的css样式。
> **另外注意，不同的markdown转换器得到的html标签的属性可能不一样，有的转换器可能会在标签中加入转换器名称做标识，具体要以转换后的结果为准。**

比如，Markdown的TOC功能得到的列表是这样的

```html
<ul id="markdown-toc">
<li>目录</li>
</ul>
```

## 理解Highlight语法高亮是如何实现的

markdown里代码块是这样的：

```
 ```css
  <style>
 ```
```

通过rouge语法高亮引擎，得到的html内容是这样的：

```html
<div class="language-css highlighter-rouge">
  <pre class="highlight">
    <code>
      <span class="o">&lt;</span>
      <span class="nt">style</span>
      <span class="o">&gt;</span>>
    </code>
  </pre>
</div>
```

rouge语法高亮引擎附带了对应的rouge.css：

```css
.highlight {
  color: #D53FB7;
}
.highlight .o {
  color: #f92672;
}
.highlight .nt {
  color: #f92672;
}
```

**于是，页面的代码块就根据关键字、变量、字符串等有了不一样的颜色**

总结

> 语法高亮引擎的作用，只是根据代码的语言，分割出与之对应的关键字、变量、字符串等，并赋予对应的css样式，最后调整css的颜色就形成了代码高亮的效果。

Tips

rouge.css导出需要执行命令，可以参考[rouge文档](https://github.com/jneen/rouge)

```shell
$ rougify foo.rb
$ rougify style monokai.sublime > rouge.css
```


## 开始制作自己的Jekyll主题

### 新建Jekyll模板
**按照上面的指引，新建一个模板，并搭建好Github Pages的本地环境：**

`jekyll new mytheme`

### 认识Jekyll模板的结构
下面是用`tree`命令输出的目录结构，只是位置进行了调整

```
├── index.html
├── about.md
├── _config.yml       # Jekyll核心配置文件
├── feed.xml
├── Gemfile           # Github Pages本地化的文件
├── Gemfile.lock      # Github Pages本地化的文件
├── _sass
│   ├── _base.scss                  # markdown对应的css
│   ├── _layout.scss
│   └── _syntax-highlighting.scss   # 语法高亮对应的css
├── css
│   └── main.scss           # 其实就是css
├── _includes
│   ├── footer.html         # 页脚html片段
│   ├── header.html         # 页头html片段
│   ├── head.html           # html片段
│   ├── icon-github.html    # html片段
│   ├── icon-github.svg     # github图标
│   ├── icon-twitter.html   # html片段
│   └── icon-twitter.svg    # twitter图标
├── _layouts
│   ├── default.html        # default页面
│   ├── page.html           # page页面
│   └── post.html           # post页面
├── _posts
│   └── 2016-08-24-welcome-to-jekyll.markdown
└── _site             # Jekyll最终生成的静态网站
    ├── about
    │   └── index.html
    ├── css
    │   └── main.css  # 如果不喜欢上面的那堆scss，那么复制这个过去就够了
    ├── feed.xml
    ├── Gemfile
    ├── Gemfile.lock
    ├── index.html
    └── jekyll
        └── update
            └── 2016
                └── 08
                    └── 24
                        └── welcome-to-jekyll.html
```

#### `_layouts`目录
该目录下的页面是”包含”其它内容的关系，一般是页面框架

#### `_includes`目录
该目录下的片段是“被包含”的关系，可以是任何格式的文件，片段也可以include片段。
include的语法：{% raw %}  `{% include head.html %}` {% endraw %}

_layouts和_includes与普通页面的关系图：
![jekyll-layout-include][jekyll-layout-include]

> 注意：default.html里访问index.html生成的内容是直接访问`content`，而不是`page.content`或`post.content`，这两者的关系大概是前者才是经过处理后的html片段，而后者是原始的文本，包含未解析的liquid语法。

#### `_config.yml`配置文件
Jekyll站点的配置文件，可以存储数据，用于配置Jekyll的插件和运行环境

```
# 自定义变量
domain: "http://jokinkuang.info" # the domain URL for your site
base: ""  # the relative path that different with domain so you can test locally
postfile: "/db/Postfile" # the Database
sitefile: "/db/Sitefile" # the Database

# 配置
excerpt_separator: "\n\n" # you can specify your own separator here, default is "\n\n" String
permalink: /:year/:month/:day/:title.html
highlighter: rouge
markdown: kramdown
kramdown:
  input: GFM
  hard_wrap: true # a newline in markdown text would be changed to <br>
gems:
  - jemoji # 要站点支持Github表情，必须添加

# 上传到Github Pages时Github会进行的配置项
# 详见：Github Help With Configuring Jekyll

# Github Pages默认配置项，Jekyll的配置可以覆盖
# kramdown:
#   input: GFM
#   hard_wrap: false
# gems:
#   - jekyll-coffeescript
#   - jekyll-paginate

# Github Pages不可改变项，会覆盖Jekyll的配置
# lsi: false
# safe: true
# source: [your repo's top level directory]
# incremental: false
# highlighter: rouge
# gist:
#  noscript: false
# kramdown:
#  math_engine: mathjax
```

### 简化模板
去掉不必要的文件，简化当前模板，得到结构：

```
├── index.html
├── _config.yml       # Jekyll核心配置文件
├── feed.xml
├── Gemfile           # Github Pages本地化的文件
├── Gemfile.lock      # Github Pages本地化的文件
├── css
│   └── markdown.css    # 提取上面_site/css/main.css中设置html部分
│   └── highlight.css   # 提取上面_site/css/main.css中语法高亮部分
├── _includes
├── _layouts
│   └── post.html       # 文章页框架
├── _posts
│   └── 2016-08-24-welcome-to-jekyll.markdown

```

### 深入理解Jekyll

#### 两种方式创建页面

方式一 | **任何路径**下添加`xxx.html`，访问地址为`该路径/xxx.html`
方式二 | **任何路径**下添加`xxx/index.html`，访问地址为`该路径/xxx`，无需后缀

#### `site`变量
来自`_config.yml`配置文件和Jekyll内置变量，下面是常用的属性：

site.posts | 从新到旧排序的posts文章列表集合
site.categories | 文章头部`categories: [cate1 cate2]`的所有cate值的集合
site.tags | 同上，文章头部`tags: [tag1 tag2]`的所有tag值的集合
site.XXX | `_config.yml`配置文件中`XXX: val`的val值，val可以是字符串/数组/集合

#### `page`变量
指代当前页面的变量，在index.html里使用page，page指的就是index.html这个页面，常用属性：

page.content | 页面**源码**(含有markdown/liquid等语句)
page.title | 页面标题
page.excerpt | 页面摘要**源码**，可通过_config.yml配置摘要算法
page.url | 页面的**相对路径**
page.date | 页面的时间和日期
page.categories | 页面的categories数组，`linux/ruby/_posts/ruby.md`文章会把linux和ruby加入categories
page.tags | 页面的tags数组
page.path | 页面的实际路径(源码路径)

> 注意：当前页面的Front Matter中设置的xxx: val可以通过page.xxx访问val值

#### `Front Matter`(Yaml头信息)
每个页面都可以有自己的头信息，可以自定义值，覆盖Jekyll的值，和覆盖_config.yml里面的值

```ruby
---
layout: post
title: 一步一步构建Jekyll主题
categories: [jekyll github markdown rouge]
date: 2016-9-3 15:47:05
excerpt: ""   # 覆盖清掉文章的摘要
---
```

#### `Liquid`语法
Jekyll内变量操作是使用[Liquid语法](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)
主要有两种：

1. 显示变量的值
  {% raw %}`{{ 变量名 }}`{% endraw %}
  如果要组成字符串，可以这样：{% raw %}`"字符串头部{{ 变量名 }}字符串尾部"`{% endraw %}
  也可以使用Filter：{% raw %}`{{ "字符串头部" | append : 变量名 | append : "字符串尾部" }}`{% endraw %}
  显示文章的标题 {% raw %}{{ page.title }}{% endraw %}

2. 使用变量的值进行计算
  文章的标题计算 {% raw %}{% assign titleAndDate = page.title | append: page.date %}{% endraw %}
  `assign x = y`是声明一个变量并赋值，**声明必须赋值！**
  `xxx | append: "str"`是Liquid语法中的Filter，可以理解为管道，也可以简单理解为`对象|方法:参数`
  Filter可以连续执行：`xxx | append: "str1" | append: "str2"`

### 将网页原型加入模板
Jekyll模板的基本结构已经完成，可以加入网页原型进行结合。

1. 提取相同的内容到`_includes`目录
2. 需要复用的页面框架，比如post文章页，放到`_layouts`目录
3. 一些配置字符串，放在`_config.yml`文件内
4. 使用**Liquid语法**在页面中访问`site`，`page`等信息组织内容
5. 剩下的就是`html`/`css`/`js`的内容了

### 修改原型
###

## 参考文档

阅读·[Jekyll的中文文档](http://jekyll.bootcss.com/) | [英文文档](http://jekyllrb.com/)
阅读·[Liquid的文档1](https://help.shopify.com/themes/liquid) | [文档2](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers) | [文档3](https://shopify.github.io/liquid/)
阅读·[语法高亮引擎rouge的文档](https://github.com/jneen/rouge) |
阅读·[Markdown转换器kramdown的文档（支持maruku语法）](http://kramdown.gettalong.org/) |
阅读·[Markdown转换器maruku的文档（支持TOC语法）](http://maruku.rubyforge.org/maruku.html) |


[markdown-parser]: {{ site.w3c_url }}images/markdown/markdown-parser.jpg "markdown转换例子"
[markdown-plain]: {{ site.w3c_url }}images/markdown/markdown-plain.jpg "html效果"
[markdown-css]: {{ site.w3c_url }}images/markdown/markdown-css.jpg "添加css后html效果"
[jekyll-layout-include]: {{ site.w3c_url }}images/jekyll/jekyll-layout-include.jpg "layout与include关系图"
