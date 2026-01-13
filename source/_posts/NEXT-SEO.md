---
title: HEXO-NEXT Github Pages 收录
date: 2021-07-16 17:18:18
tags: 
  - NEXT
categories: 
  - NEXT
---

NEXT-SEO

<!--more-->

## 安装 hexo-generator-sitemap 插件和配置_config.yml

```shell
npm install hexo-generator-sitemap --save
```

### 修改_config.yml 文件

> 打开根目录的 _config.yml 文件，加入以下代码：

```yaml
sitemap:
  path: 
    - sitemap.xml
#     - sitemap.txt
#   template: ./sitemap_template.xml
#   template_txt: ./sitemap_template.txt
#   rel: false
#   tags: true
#   categories: true
```

## 向 Google 提交站点地图

打开 [Google Search Console](https://search.google.com/search-console/) ，箭头处填入域名

![Google Search Console](/images/google_search_console.png)

### 选择 HTML 标记认证，将会得到

```html
<meta name="google-site-verification" content="XXXXXXXXXXXXXXXXXXXXXXX" />
```

复制 `XXXXXXXXXXXXXXXXXXXXXXX` 的值，将其粘贴到 `_config.next.yml` 的 `google_site_verification:`

> 运行

```shell
hexo clean && hexo g && hexo d
```

后点击验证

![google_site_verification](/images/google_site_verification.png)

在弹出的对话框中点击前往资源页面

点击左侧的站点地图，在添加新的站点地图处填入 `sitemap.xml` 的地址

![google_sitemap](/images/google_sitemap.png)

## 向 Bing 提交站点地图

打开 [Bing Webmasters](https://www.bing.com/webmasters) 登录后选择从 GSC 导入你的网站

![bing_export](/images/bing_export.png)

### 使用 IndexNow 快速提交至 Bing

> 安装 hexo-indexnow 插件

```shell
npm install hexo-indexnow --save
```

### 获取 API Key

打开 [Bing IndexNow](https://www.bing.com/indexnow/getstarted) ，找到 API Key 点击下载箭头将文件下载到 `source` 目录并复制 API Key

### 配置 Hexo

将以下代码复制到根目录的 `_config.yml`

```yaml
hexo_indexnow:
  count: latest # 数字或者 "latest"(=1)
  txt_name: indexnow.txt # 链接文件名
  apikey: xxxxxx # 你的 apikey
  server: bing # indexnow 服务器，可选值有:bing、yandex、indexnow

deploy:
  - type: indexnow_url_submitter
```

### 设置 robots

在 `/source` 目录下新建 `robots.txt` 文件，将下列代码复制到文件中

```txt
User-agent: *
Allow: /
Allow: /archives/
Allow: /categories/
Allow: /tags/
Allow: /about/

Disallow: /vendors/
Disallow: /js/
Disallow: /css/
Disallow: /fonts/
Disallow: /fancybox/

Sitemap: https://www.eyunbook.com/sitemap.xml
```

## 链接提交给百度

在百度中搜索自己博客的域名，如：[mabeyo.github.io](https://maybeyo.github.io//)，或者[site:mabeyo.github.io](https://maybeyo.github.io/)，如果百度找不到该博客的相关信息就说明你的博客地址还未被百度收录，会有如下提示，按提示点击提交网站到相关页面就可以提交我们的博客地址

- 没有找到该URL。您可以直接访问 [mabeyo.github.io](https://maybeyo.github.io/)，还可[提交网址](https://ziyuan.baidu.com/linksubmit/url)给我们。

### 添加网站及验证所有权

登录百度搜索资源平台，然后进入[站点管理](https://ziyuan.baidu.com/site)页面，点击添加网站按钮添加我们的博客

- 第一步：输入网站地址，如 [mabeyo.github.io](https://maybeyo.github.io//)
- 第二步：选择站点属性，最多可选三项，如影视动漫、信息技术等
- 第三步：验证网站，就是验证网站的所有权，说明该网站是我们的，这是重点，下面详细说明

验证网站有三种方式：文件验证、HTML标签验证、CNAME验证

> 文件验证

为保持验证通过的状态,成功验证后请不要删除HTML文件

此处用的HTML标签验证

在next主题配置文件中`\themes\next\layout\_partials\head\head.njk`粘贴验证标签

```css
{%- if theme.baidu_site_verification %}
    <meta name="baidu-site-verification" content="code-Z5xxxxx" />
{%- endif %}
```

在next主题配置文件`\themes\next\_config.yaml`中修改

```yaml
# Baidu Webmaster tools verification.
# See: https://ziyuan.baidu.com/site
baidu_site_verification: Z5sxxxxxx
```

重新部署

```shell
hexo clean all
hexo g
hexo d
```

访问是否添加成功 [view-source:https://maybeyo.github.io/](view-source:https://maybeyo.github.io/)

完成验证

### 链接提交：sitemap

安装sitemap生成器插件

```shell
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```

重新generate，会在public目录下生成`sitemap.xml`、`baidusitemap.xml`两个文件

sitemap.xml文件内容

重新部署

```shell
hexo clean all
hexo g
hexo d
```

最后在自动提交->sitemap页面填入sitemap的地址

```css
maybeyo.github.io/sitemap.xml
maybeyo.github.io/baidusitemap.xml
```

参考链接：
  [百度链接：jingyan.baidu.com](https://jingyan.baidu.com/article/a3761b2bf716631576f9aa3a.html)
  [博客链接：www.himmy.cn](https://www.himmy.cn/2019/07/06/hexo%E5%8D%9A%E5%AE%A2%E7%99%BE%E5%BA%A6%E6%94%B6%E5%BD%95/)
  [CSDN链接](https://blog.csdn.net/linchaolong/article/details/148014233)
  [知乎链接](https://zhuanlan.zhihu.com/p/464341936)
  [Next SEO配置文档](https://theme-next.js.org/docs/theme-settings/seo)
  [Hexo SEO优化](https://www.eyunbook.com/Hexo%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%E4%BC%98%E5%8C%96.html)
