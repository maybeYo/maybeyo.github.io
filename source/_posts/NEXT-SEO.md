---
title: NEXT-SEO
date: 2021-07-16 17:18:18
tags: 
  - NEXT
categories: 
  - NEXT
---

NEXT-SEO

<!--more-->



参考链接：[百度链接：jingyan.baidu.com](https://jingyan.baidu.com/article/a3761b2bf716631576f9aa3a.html)

参考链接：[博客链接：www.himmy.cn](https://www.himmy.cn/2019/07/06/hexo%E5%8D%9A%E5%AE%A2%E7%99%BE%E5%BA%A6%E6%94%B6%E5%BD%95/)

### 链接提交给百度

在百度中搜索自己博客的域名，如：[mabeyo.github.io](https://maybeyo.github.io//)，或者[site:mabeyo.github.io](https://maybeyo.github.io/)，如果百度找不到该博客的相关信息就说明你的博客地址还未被百度收录，会有如下提示，按提示点击提交网站到相关页面就可以提交我们的博客地址



- 没有找到该URL。您可以直接访问 [mabeyo.github.io](https://maybeyo.github.io/)，还可[提交网址](https://ziyuan.baidu.com/linksubmit/url)给我们。



### 添加网站及验证所有权

登录百度搜索资源平台，然后进入[站点管理](https://ziyuan.baidu.com/site)页面，点击添加网站按钮添加我们的博客

- 第一步：输入网站地址，如 [mabeyo.github.io](https://maybeyo.github.io//)
- 第二步：选择站点属性，最多可选三项，如影视动漫、信息技术等
- 第三步：验证网站，就是验证网站的所有权，说明该网站是我们的，这是重点，下面详细说明

验证网站有三种方式：文件验证、HTML标签验证、CNAME验证



**文件验证**

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















