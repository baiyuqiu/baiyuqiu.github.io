---
title: Hexo的使用
tags:
    hexo
categories: 搭建博客
---
## 基础使用（命令）

### hexo s
作用：启动本地服务器，只能用于预览在`_posts`目录下的主题，默认地址： http://localhost:4000/。
+ `hexo s` 是 `hexo server` 的缩写，命令效果一致；
+ 对于主题或_config.yml的修改，需要重启本地服务器后才能预览效果；
+ 如果预览`草稿`中的主题，需要执行`hexo s --draft`；

<!--more-->

### hexo c
作用：清除缓存文件 db.json 和已生成的静态文件 public。
+ `hexo c` 是 `hexo clean` 的缩写，命令效果一致；

### hexo g
作用：生成网站静态文件到默认设置的 public 文件夹。
+ hexo g 是 hexo generate 的缩写，命令效果一致；
+ 便于查看网站生成的静态文件或者手动部署网站；
+ 如果使用自动部署，不需要先执行该命令；

### hexo d
作用：自动生成网站静态文件，并部署到设定的仓库。(该配置在根目录的`_config.yml`的`deploy`字段)
+ hexo d 是 hexo depoly 的缩写，命令效果一致；

### hexo new
在根目录命令行输入hexo new `<模板>` `<文章名>`

|       模板      |      描述                     |          路径              |
|       :--:     |      :--:                     |         :--:              |
|       post     |      新建文章                  |      /source/_posts/      |
|       draft    |      新建草稿                  |      /source/_drafts/      |
|       page     |      新建页面（标签页，分类页等)  |       /source/             |

当我们需要新建一个文章是，命令行输入
```
hexo new post myArticle
或
hexo new myArticle
```
此时在`source/_posts/`文件夹中多了一个`myArticle.md`文件，该.md文件是一个主题，我们可以在里面进行创作自己的主题文章。

当我们需要新建一个草稿时候，命令行输入
```
hexo new draft myDraft
```
此时在`source/_drafts/`文件夹中多了一个myDraft.md文件。我们跟创建`post`主题一样进行创作，当我们创建完成后，需要执行命令
```
hexo publish myDraft
```
操作完成后，`source/_drafts/myDraft.md`会被移动到`source/_posts/myDraft.md`，这样草稿中的文章才能被进行发布。

## 主题
### 官网下载地址
```
hexo主题网站： https://hexo.io/themes/
```
这里有几百个主题供你选择，或者我们可以去github上寻找合适的主题。

### Next主题设置
[Next主题仓库](https://github.com/next-theme/hexo-theme-next)

#### 安装
在根目录，执行命令
```
git clone git@github.com:next-theme/hexo-theme-next.git themes/next
```
操作完成后，会在`themes`目录下多一个`next`的主题。

#### 配置
修改根目录中的`_config.yml`：
```
language: zh-CN

theme: next
```
再重新部署下，就能看到最新的主题了。

### 阅读全文设置
想要在网站首页只显示每篇文章的部分内容，不要全部内容都展示出来。有两个解决方法：

1. 修改 _config.yml 文件设置
2. 直接在你的 md 博文中加一句

#### 方法一
用文本编辑器打开 themes/next 目录下的 _config.yml 文件，找到这段代码：
```
# Automatically Excerpt. Not recommend.
# Please use <!-- more --> in the post to control excerpt accurately.
auto_excerpt:
  enable: false
  length: 150
```
把enable的false改成true就行了，然后length是设定文章预览的文本长度。修改后重启 hexo 就好了。
但这种方法的效果是会格式化你文章的样式，直接把文字挤在一起显示，最后会有...

#### 方法一
在想要显示的文章预览部分后加上`<!--more-->`，这样首页和列表页展示的文章内容就是`<!--more-->`之前的文字，而之后的就不会显示了，同时也保留文章原样式。
