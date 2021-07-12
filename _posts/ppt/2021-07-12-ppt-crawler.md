---
layout:     ppt
title:      "【PPT】爬虫配置讲解"
subtitle:   ""
date:       2021-07-12 09:14:00
author:     "luohq"
header-img: "img/about-bg.jpg"
tags:
    - ppt
---
<textarea data-template>
### 业务背景
1. 数据组想要第一时间获取各地限行规则
2. 数据组整理了大量提供限行新闻的网站、微信公众号、新浪微博
3. 数据组仅需要网站内容中包含限行提示的内容（即文章详情中包含关键字'限行'等）
4. 网站内容的提取过程大致遵循：网站链接 -> 文章链接列表 -> 点击链接获取正文详情

--

### 开发背景
1. 开发一个通用的网站爬取工具，最好可以配置爬取规则
2. 爬虫工具选择Python Scrapy：对于网站的超链接列表提取基于Scrapy.CrawlSpider rules，对文章详情内容的提取基于ItemLoaders selector
3. 爬虫规则基于JSON（start_urls, crawl_rules, item_rules）
4. 需要提供一个爬虫规则验证工具（Scrapy+Flask），可以验证爬取规则对应的爬取结果是否正确

--

<font color='green'><b>相关依赖🍀 </b></font><br/>
1. [🔗Python Scrapy官方文档：https://docs.scrapy.org/en/latest/intro/overview.html](https://docs.scrapy.org/en/latest/intro/overview.html)<br/>
2. [🔗爬虫工具 - 添加数据来源：http://mx-datacollection-tool-ln.mxnavi.com/mainPage/DataSourceManagement](http://mx-datacollection-tool-ln.mxnavi.com/mainPage/DataSourceManagement)<Br/>
3. [🔗爬虫配置验证工具：http://mx-crawl-spider-validator.inner.mxnavi.com/start](http://mx-crawl-spider-validator.inner.mxnavi.com/start)<br/>


---

### 爬虫配置说明
1. 给出web网站初始界面链接URL
2. 根据css（或xpath）规则提取出初始界面中的超链接（a标签）列表
3. 依次打开提取出的超链接，并确定详情界面中的标题、发布日期、正文内容
4. 将爬虫规则添加到爬虫数据来源界面：http://mx-datacollection-tool-ln.mxnavi.com/mainPage/DataSourceManagement

--

### 爬虫后端逻辑
1. 后端爬虫会定时拉取配置
2. 爬虫提取配置规则中网站对应的详情页中的标题、发布日期、正文内容
3. 爬虫对标题、正文内容中的是否包含关键字进行过滤
4. 爬虫仅将包含关键字的网站详情页保存到DB

---

### 爬虫示例配置
```json
# 规则【本地宝-北京】示例如下（更多示例参见附件：crawl_config.txt）
{
    # 初始爬取URL（支持多个）
    "start_urls": [
        "http://bj.bendibao.com/news/list_17_175_1.htm"],

    # 界面中link提取规则（支持restrict_css, restrict_xpath, restrict_json），且支持多条规则，
    # 其中restrict_css, restrict_xpath支持html页面解析，而restrict_json支持ajax请求（restrict_json为自定义提取器，官方文档没有相关说明）
    # 关于crawl_rules属性可以参考：https://docs.scrapy.org/en/latest/topics/link-extractors.html#topics-link-extractors
    "crawl_rules": [{
            "restrict_css": "#listNewsTimeLy > li > div.info > h3 > a",
            # 目前callback仅支持parse_html（即根据item_rules提取页面中数据）
            "callback": "parse_html"
        }
    ],

    # html页面中的数据信息提取规则（支持{propName}_css|xpath）
    # 目前支持属性：title, text, pulish_date
    "item_rules": {
        # {propName}_{type}: [selector1, selector2,...]且取第一个selector有值的结果（多个selector兼容不同网页布局）
        "title_css": ["body > div.wrapper > div.content_l > div.title.daoyu > h1 > strong"],
        "text_css": ["#bo"],
        "publish_date_css": ["body > div.wrapper > div.content_l > div.title.daoyu > div.article-info > span.time"]
    }
}
```

--

在Scrapy中支持css扩展属性如下：<br/>
- （1）::text  获取元素文本信息
- （2）::attr(name)  获取元素属性值

---

<font color='red'><b>注意💣</b></font><br/>
1. mailbird需付费使用...
2. 其中也使用过yomail（已停止维护）、mailspring（不支持pop3）等等，最终选用mailbird
3. 破解完一定要设置不自动更新，否则mailbird自动升级后会导致破解失效😭

---

# OVER✌️

</textarea>
