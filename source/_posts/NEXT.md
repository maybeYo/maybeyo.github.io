---
title: NEXT
date: 2021-06-20 21:17:50
tags: 
  - NEXT
categories: 
  - NEXT
---
NEXT 配置修改
<!--more-->
参考地址
http://theme-next.iissnan.com/theme-settings.html#rss



# 1.Next主题风格

Next提供了四中主题风格scheme，可以在主题配置文件_config.yml文件中进行选择

分别是`Muse`、`Mist`、`Pisces`、`Gemini`

默认时Muse主题，我用的是Gemini主题，所以把Muse注释掉，Gemini去掉注释

```shell
# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```



# 2.设置菜单栏

## 取消菜单栏注释

```shell
menu:
  home: / || fa fa-home
  about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat

```



**在终端窗口下，定位到 Hexo 站点目录下。使用 hexo new page 新建一个页面，命名为 tags ：**

```shell
cd \hexoblog\maybe
hexo new page tags
```



**编辑刚新建的页面，将页面的类型设置为 tags ，主题将自动为这个页面显示标签云。页面内容如下：**

```shell
---
title: 标签
date: 2021-06-19 14:42:32
type: "tags"
---
```



****注意：**如果有集成评论服务，页面也会带有评论。 若需要关闭的话，请添加字段 `comments` 并将值设置为 `false`，如：**

```shell
---
title: 标签
date: 2021-06-19 14:42:32
type: "tags"
comments: false
---
```



# 3.头像修改

```shell
avatar:
  url: /images/avatar.png
```



# 4.动态背景

```shell
canvas_ribbon:
  enable: true
```



# 5.添加顶部加载条



```shell
nprogress:
  enable: true
  spinner: true
```





# 6.为博客加上萌萌的宠物

**在终端切换到你的博客的路径里，然后输入如下代码：**

```shell
npm install --save hexo-helper-live2d
```



# 7.添加搜索功能

**1、安装 hexo-generator-searchdb 插件**

```shell
npm install hexo-generator-searchdb --save
```
**2、打开 主题配置文件 找到Local search，将enable设置为true**



# 8.显示当然浏览进度

```shell
back2top:
  enable: true							
  # Back to top in sidebar.
  sidebar: false
  # Scroll percent label in b2t button.
  scrollpercent: true					
```



# 9.设置已读进度条

```shell
# Reading progress bar
reading_progress:
  enable: true
  # Available values: left | right
  startAt: left
  # Available values: top | bottom
  position: top
  reversed: false
  color: "#37c6c0"
  height: 3px
```



# 10.打赏设置

```shell
reward_settings:
  # If true, a donate button will be displayed in every article by default.
  enable: true
  animation: true
  comment: 请作者喝杯茶吧
  #comment: Buy me a coffee

reward:
  wechatpay: /images/wechatpay.png
  #alipay: /images/alipay.png
  #paypal: /images/paypal.png
  #bitcoin: /images/bitcoin.png
 
自己获取自己的支付收款码，放置在next/source/images中
```



# 11.自定义博客图标

**博客网站的图标可以在iconfont等网站选择和制作图标**

**文件路径：`\themes\next\source`，images修改主题images下文件替换**

```shell
favicon:
  small: /images/favicon-16x16-next.png
  medium: /images/favicon-32x32-next.png
  apple_touch_icon: /images/apple-touch-icon-next.png
  safari_pinned_tab: /images/logo.svg
  #android_manifest: /manifest.json
```



# 12.开启站点阅读时间

```yaml
npm install hexo-word-counter
hexo clean

symbols_count_time:
  separated_meta: true
  item_text_total: true
```



## 12.1 页面阅读统计 不蒜子统计

```yaml
busuanzi_count:
  enable: false              	   # 设true 开启
  total_visitors: true      	   # 总阅读人数（uv数）
  total_visitors_icon: fa fa-user  # 阅读总人数的图标
  total_views: true         	   # 总阅读次数（pv数）
  total_views_icon: fa fa-eye      # 阅读总次数的图标
  post_views: true         		   # 开启内容阅读次数
  post_views_icon: far fa-eye      # 内容页阅读数的图标
```



# 13.文章原创声明

```yaml
# Creative Commons 4.0 International License.
# See: https://creativecommons.org/about/cclicenses/
creative_commons:
  # Available values: by | by-nc | by-nc-nd | by-nc-sa | by-nd | by-sa | cc-zero
  license: by-nc-sa
  # Available values: big | small
  size: small
  sidebar: false
  post: false
  # You can set a language value if you prefer a translated version of CC license, e.g. deed.zh
  # CC licenses are available in 39 languages, you can find the specific and correct abbreviation you need on https://creativecommons.org
  language:
```





# 14.修改底部标签样式

```yaml
# Use icon instead of the symbol # to indicate the tag at the bottom of the post
tag_icon: true
```



# 15.开启代码复制

```shell
codeblock:
  copy_button:
    enable: true
```



# 16.鼠标点击特效

```shell
npm install next-theme/hexo-next-fireworks
```



# 17.GitHub Fork Me

```yaml
# `Follow me on GitHub` banner in the top-right corner.
github_banner:
  enable: true
  permalink: https://github.com/hardtoamend
  title: Follow me on GitHub
```



# 18.bookmark

Bookmark是一个插件，允许用户保存他们的阅读进度。用户只需单击页面左上角的书签图标即可保存滚动位置。当他们下次访问您的博客时，他们可以自动恢复每个页面的最后滚动位置。

```yaml
# Bookmark Support
bookmark:
  enable: false
  # Customize the color of the bookmark.
  color: "#222"
  # If auto, save the reading progress when closing the page or clicking the bookmark-icon.
  # If manual, only save it by clicking the bookmark-icon.
  save: auto
```



# 19.添加lazyload

对于图片进行延迟加载，访问到图片位置时才去请求图片资源，这样可以提高博客的访问速度，节省流量。

```shell
git clone https://github.com/theme-next/theme-next-jquery-lazyload source/lib/jquery_laz
```



主题配置文件:

```yaml
# Vanilla JavaScript plugin for lazyloading images.
# For more information: https://apoorv.pro/lozad.js/demo/
lazyload: false
```



# 20.主题及标题栏背景图

> 首先主题配置文件取消注释

```yaml
custom_file_path:
  style: source/_data/styles.styl
```



> 在路径`~/hexo_blog/source/_data`创建/修改 styles.styl文件，并添加以下内容

参考链接：[博客参考：bella722.github.io](https://bella722.github.io/post/4f44d92e.html)



```css
// 添加背景图片
body {
      background: url(https://source.unsplash.com/random/1600x900?wallpapers);//自己喜欢的图片地址
      background-size: cover;
      background-repeat: no-repeat;
      background-attachment: fixed;
      background-position: 50% 50%;
}


//首页banner渐变色
.site-brand-container {
    background: linear-gradient(200deg, #f58220, #f58220 );
}

// 修改主体透明度
.main-inner{
    background: #fff;
    opacity: 0.95;
}

// 修改菜单栏透明度，会引起本地搜索菜单bug
//.header-inner {
//    opacity: 0.95;
//}

// 主页文章添加阴影效果
.post {
   margin-top: 60px;
   margin-bottom: 60px;
   padding: 25px;
   -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
   -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
}
```



## **设置所有边框为圆角**

打开文件，路径：`\themes\next\source\css_variables\Gemini.styl `，添加以下代码：

```yaml
// 修改主题页面布局为圆角
$border-radius-inner = 15px 15px 15px 15px;
$border-radius = 15px;
```





# 21.开启文章目录

```yaml
toc:
  enable: true
  # Automatically add list number to toc.
  number: false
  # If true, all words will placed on next lines if header width longer then sidebar width.
  wrap: false
  # If true, all level of TOC in a post will be displayed, rather than the activated part of it.
  expand_all: false
  # Maximum heading depth of generated toc.
  max_depth: 6
```



# 22.foot页脚设置

```yaml
footer:
  # Specify the year when the site was setup. If not defined, current year will be used.
  #since: 2020

  # Icon between year and copyright info.
  icon:
    # Icon name in Font Awesome. See: https://fontawesome.com/icons
    name: fa fa-heart
    # If you want to animate the icon, set it to true.
    ## 图标的一个动画效果，类似于心跳
    animated: true
    # Change the color of icon, using Hex Code.
    # 图标颜色，可格局需要自行修改
    color: "#ff0000"

  # If not defined, `author` from Hexo `_config.yml` will be used.
  copyright:

  # Powered by Hexo & NexT
  # Powered by Hexo 字样，不喜欢可以设置为 false
  powered: false

  # Beian ICP and gongan information for Chinese users. See: https://beian.miit.gov.cn, http://www.beian.gov.cn
 #备案信息，如果网站有备案号，可以在这里填写备案号
  beian:
    enable: false
    icp:
    # The digit in the num of gongan beian.
    gongan_id:
    # The full num of gongan beian.
    gongan_num:
    # The icon for gongan beian. See: http://www.beian.gov.cn/portal/download
    gongan_icon_url:
```



# 23.标签云

参考地址：

[hexo-tag-cloud]:https://github.com/D0n9X1n/hexo-tag-cloud

[github参考](https://github.com/D0n9X1n/hexo-tag-cloud)

**下载插件**

```shell
npm install hexo-tag-cloud --save
```



**将以下代码插入到`next/layout/_macro/sidebar.swig`**

```css
{% if site.tags.length > 1 %}
  <script type="text/javascript" charset="utf-8" src="{{ url_for('/js/tagcloud.js') }}"></script>
  <script type="text/javascript" charset="utf-8" src="{{ url_for('/js/tagcanvas.js') }}"></script>
  <div class="widget-wrap">
    <h3 class="widget-title">Tag Cloud</h3>
    <div id="myCanvasContainer" class="widget tagcloud">
      <canvas width="250" height="250" id="resCanvas" style="width:100%">
        {{ list_tags() }}
      </canvas>
    </div>
  </div>
{% endif %}

```



# 24.侧边栏社交链接

**修改 `themes\next\_config.yml `主题配置文件**

```yaml
# Social Links
# Usage: `Key: permalink || icon`
# Key is the link label showing to end users.
# Value before `||` delimiter is the target permalink, value after `||` delimiter is the name of Font Awesome icon.
social:
  #GitHub: https://github.com/yourname || fab fa-github
  #E-Mail: mailto:yourname@gmail.com || fa fa-envelope
  #Weibo: https://weibo.com/yourname || fab fa-weibo
  #Google: https://plus.google.com/yourname || fab fa-google
  #Twitter: https://twitter.com/yourname || fab fa-twitter
  #FB Page: https://www.facebook.com/yourname || fab fa-facebook
  #StackOverflow: https://stackoverflow.com/yourname || fab fa-stack-overflow
  #YouTube: https://youtube.com/yourname || fab fa-youtube
  #Instagram: https://instagram.com/yourname || fab fa-instagram
  #Skype: skype:yourname?call|chat || fab fa-skype
  QQ: http://wpa.qq.com/msgrd?v=3&uin=此处填写qq账号&site=qq&menu=yes || fab fa-qq
  GitHub: https://github.com/hardtoamend || fab fa-github

social_icons:
  enable: true
  icons_only: false
  transition: false

###设置友情链接
# Blog rolls
links_settings:
  icon: fa fa-globe
  title: Links
  # Available values: block | inline
  layout: block

#设置网址
links:
  #Title: https://example.com
```



# 25.点击图片放大

```yaml
# FancyBox is a tool that offers a nice and elegant way to add zooming functionality for images.
# For more information: https://fancyapps.com/fancybox/
fancybox: true
```

# 26 postBodyEnd

```html
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
    {% endif %}
</div>

```

```yaml
# hexo-next-config yaml
  postBodyEnd: source/_data/post-body-end.njk
```
