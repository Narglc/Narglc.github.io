---
title: 使用hexo+github搭建博客 笔记
tags: 
  - hexo
  - github
categories: Coding
---

## 常用命令汇总

命令 | 简写 | 作用
---|---|--- 
hexo deploy | hexo d | 部署到github等
hexo server | hexo s | 开启服务器,<br> --debug 开启调试模式
hexo clean	|		 | 清楚缓存文件及静态文件
hexo generate| hexo g | 生成静态网页,<br>-f 等同于`hexo clean && hexo g`;<br> -d 等同于`hexo g && hexo d`
hexo new "new file title" | | 创建新文章，若题目中无空格，可省去双引号
hexo new page tags | | 在`source`创建`tags`文件夹，且生成`index.md`的文章

<font color="red">注意：</font>可以做个命令统计脚本，根据使用频次排序上述命令。

## 本地资料备份
已在github添加`hexo`分支，本地资料`push`到`hexo`分支，hexo部署由`hexo d`自动推送到`master`分支。 

``` bash
git push origin hexo
```



## 安装 hexo

```
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

## 更换NexT 主题

``` bash
cd blog
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

**修改配置文件** 
 打开**\_config.yml**: 
 修改主题一行为：
``` bash
theme: next
```

## 写文章
``` bash
hexo new [layout] <title>
```
> 默认**layout**使用 **\_config.yml** 中的 **default\_layout** 参数
> 若标题中包含空格，请使用引号括起来。

``` bash
hexo new page --path about/me "About me"  # 自定义新文章路径
```

**举例**
``` bash
hexo new first.md
```
编辑**first.md**文件即可


## 本地启动服务器

``` bash
hexo server
# 可简写为
hexo s
```
> 默认网址：![http://localhost:4000/]
> -p 重设端口号
> -s 只使用静态文件

## 部署到github
1. 安装 github部署工具
``` bash
npm install hexo-deployer-git --save
```
2. 修改**\_config.yml**
``` bash
deploy:
  type: git
  repo: https://github.com/Narglc/Narglc.github.io
```
3. 部署到github
``` bash
hexo deploy
# 可简写为：
hexo d
```

## generate vs deploy
``` bash
hexo generate		# 生成静态文件
	-d,--deploy  	# 文件生成后立即部署网站
# 可简写为：
hexo g
```
> hexo g 仅仅生成静态文件，用于本地部署；hexo d 可推送到github部署

## clean
``` bash
hexo clean			#清除缓存文件 (db.json) 和已生成的静态文件 (public)。
```

## 代码高亮样式
```
# normal | night | night eighties | night blue | night bright  # 5种可用
highlight_theme: night
```


## 添加阅读全文按钮 [暂未开启]
只显示文章一部分，多余的需要点击**阅读全文**来查看，需要在文章中添加
``` html
<!--more-->
```

## 设置网站缩略图标
修改站点配置文件**\_config.yml**中**small**、**medium**、**apple_touch_icon**对应图片地址：
``` bash
favicon:
  small: /images/favicon-16x16-next.png
  medium: /images/favicon-32x32-next.png
  apple_touch_icon: /images/apple-touch-icon-next.png
  safari_pinned_tab: /images/logo.svg
```

## 本地搜索功能
### 安装 hexo-generator-searchdb 插件
``` bash
npm install hexo-generator-searchdb --save
```
### 修改next主题配置文件**\_config.yml**:
``` bash
local_search:
  enable: true
```
### 站点配置文件**\_config.yml**的`Extensions`后添加：
``` bash
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

## 侧边栏社交链接
修改next主题配置文件**\_config.yml**:
``` bash
social:
  E-Mail: mailto:qingcheliuzhi@qq.com || envelope
  GitHub: https://github.com/Narglc || github
  豆瓣: https://www.douban.com/people/Narglc/ || douban
social_icons:
  enable: true
  icons_only: false
  transition: false
  微博: weibo		# 可设置上述对应的图标
```
<font color="red">注意：</font> **||** 前面是链接地址，后面是`FontAwsone`字符库的图标名


## 打赏
修改站点配置文件**\_config.yml**:
``` bash
# Reward
reward_comment: Donate comment here
wechatpay: /images/wechatpay.jpg
alipay: /images/alipay.jpg
```

## 文章末尾添加版权信息
### 实现
手动修改主题目录下的 layout/\_macro/post.swig 文件，找到 post-footer 标签，添加以下内容div内代码段：
> 即，将div内代码段插入footer标签开始处。
``` html
<footer class="post-footer">
	<div>    
	 {# 此处判断是否在索引列表中 #}
	 {% if not is_index %}
	<ul class="post-copyright">
	  <li class="post-copyright-author">
	      <strong>本文作者：</strong>{{ theme.author }}
	  </li>
	  <li class="post-copyright-link">
	    <strong>本文链接：</strong>
	    <a href="{{ url_for(page.path) }}" title="{{ page.title }}">{{ page.path }}</a>
	  </li>
	  <li class="post-copyright-license">
	    <strong>版权： </strong>
	    本站文章均采用 <a href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/" rel="external nofollow" target="_blank">CC BY-NC-SA 3.0 CN</a> 许可协议，请勿用于商业，转载注明出处！
	  </li>
	</ul>
	{% endif %}
	</div>
[footer 原有内容]
</footer>
```
### 添加显示格式
修改主题目录下的 source/css/\_custom/custom.styl 文件:
``` css
.post-copyright {
    margin: 1em 0 0;
    padding: 0.5em 1em;
    border-left: 3px solid #ff1700;
    background-color: #f9f9f9;
    list-style: none;
}
```


## 站点访问计数
使用[不蒜子](http://busuanzi.ibruce.info/)脚本实现计数
### 新实现
`Next` 主题在版本6.0以上已经**内置了不蒜子访客统计的代码**，修改主题配置文件**\_config.yml**即可
``` bash
busuanzi_count:
  # count values only if the other configs are false
  enable: true
  # custom uv span for the whole site
  site_uv: true
  site_uv_header: <i class="fa fa-user"></i> 访问人数
  site_uv_footer: 人
  # custom pv span for the whole site
  site_pv: true
  site_pv_header: <i class="fa fa-eye"></i> 总访问量
  site_pv_footer: 次
  # custom pv span for one page only
  page_pv: true
  page_pv_header: <i class="fa fa-file-o"></i> 阅读数
  page_pv_footer:

```
<!--
### 实现[老版本实现]
打开 themes/next/layout/\_partials/footer.swig，将下面这段代码添加到里面，位置可自行决定，本blog放在倒数第二个div位置上
``` html
<div>
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<span id="busuanzi_container_site_pv" style='display:none'>
    本站总访问量 <span id="busuanzi_value_site_pv"></span> 次
    <span class="post-meta-divider">|</span>
</span>
<span id="busuanzi_container_site_uv" style='display:none'>
    有<span id="busuanzi_value_site_uv"></span>人看过我的博客啦
</span>
</div>
```
### 文章的访问次数[老版本实现]
以上只是显示站点的访问次数，如果想显示每篇文章的访问次数
打开 themes/next/layout/\_macro/post.swig，在第一行增加is_pv字段
```
{% macro render(post, is_index, is_pv, post_extra_class) %}
```
然后将这段代码插入到`busuanzi_count`的下一段：
```
{% if is_pv %}
  <span class="post-meta-divider">|</span>
  <span id="busuanzi_value_page_pv"></span>次阅读
{% endif %}
```
然后再打开 themes/next/layout/post.swig，这个文件是文章的模板，给render方法传入参数（对应刚才添加的is_pv字段）
![](/images/blog_page_view_count.png)
最后再打开 themes/next/layout/index.swig，这个文件是首页的模板，给render方法传入参数（对应刚才添加的is_pv字段）
![](/images/blog_page_view_count_2.png)

## 评论功能？？？
-->

参考链接:
[Mac 系统下搭建hexo个人博客](https://www.jianshu.com/p/77db3862595c)
[hexo文章末尾添加版权信息](http://stevenshi.me/2017/05/26/hexo-add-copyright/)
[Hexo的Next主题详细配置](https://www.jianshu.com/p/3a05351a37dc)


