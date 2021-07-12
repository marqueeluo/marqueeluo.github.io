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
1. [🔗Python Scrapy官方文档：https://docs.scrapy.org/en/latest/intro/overview.html](https://docs.scrapy.org/en/latest/intro/overview.html)
2. [🔗爬虫工具 - 添加数据来源：http://mx-datacollection-tool.inner.mxnavi.com/mainPage/DataSourceManagement](http://mx-datacollection-tool.inner.mxnavi.com/mainPage/DataSourceManagement)
3. [🔗爬虫配置验证工具：http://mx-crawl-spider-validator.inner.mxnavi.com/start](http://mx-crawl-spider-validator.inner.mxnavi.com/start)


---

### 爬虫配置说明
1. 给出web网站初始界面链接URL
2. 根据css（或xpath）规则提取出初始界面中的超链接（a标签）列表
3. 依次打开提取出的超链接，并确定详情界面中的标题、发布日期、正文内容
4. 将以上3条组装成[🔗爬虫配置JSON规则](https://marqueeluo.github.io/2021/07/12/ppt-crawler/#/2)
5. 通过[🔗爬虫规则验证工具](https://marqueeluo.github.io/2021/07/12/ppt-crawler/#/3)验证爬取规则是否正确
4. 在爬虫工具中将爬虫配置JSON规则[🔗添加到爬虫数据来源](https://marqueeluo.github.io/2021/07/12/ppt-crawler/#/3)

--

### 爬虫后端逻辑
1. 后端爬虫会定时拉取配置
2. 爬虫提取配置规则中网站对应的详情页中的标题、发布日期、正文内容
3. 爬虫对标题、正文内容中的是否包含关键字进行过滤
4. 爬虫仅将包含关键字的网站详情页保存到DB

--

### 后端爬虫定时任务
![img](https://img-blog.csdnimg.cn/20210712140429799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

---

### 爬虫示例配置
```json
# 规则【本地宝-北京】示例如下
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

### 爬虫规则配置建议
1. 尽量以CSS选择器配置
2. 尽量减少选择器层级（可结合id、class等进行定位）

--

以下示例网站 - 蓬溪县人民政府 - 如下配置不建议：<br/>
```json
{
    "start_urls": ["http://www.pengxi.gov.cn/notice"],
    "crawl_rules": [
        {
            "restrict_xpath": "/html/body/div[2]/div[3]/div[1]/div/div[2]/div/div[2]/div/div/div/ul/li/a",
            "callback": "parse_html"
        }
    ],
    "item_rules": {
        "title_xpath": ["/html/body/div[2]/div[3]/div[1]/div/div/div/div/div[2]/table/tbody/tr/td/table/tbody/tr[2]/td/b"],
        "text_xpath": ["/html/body/div[2]/div[3]/div[1]/div/div/div/div/div[2]/table/tbody/tr/td/table/tbody/tr[4]/td/div"],
        "publish_date_xpath": ["/html/body/div[2]/div[3]/div[1]/div/div/div/div/div[2]/table/tbody/tr/td/table/tbody/tr[3]/td/table/tbody/tr/td/div/span[6]"]
    }
}
```
1. 实际获取不到链接
2. 层级太多可读性不好，出问题也不好排查

--

建议使用CSS选择器修改如下：<br/>
```json
{
    "start_urls": ["http://www.pengxi.gov.cn/notice"],
    "crawl_rules": [
        {
            "restrict_css": "div.portlet-column-last a",
            "callback": "parse_html"
        }
    ],
    "item_rules": {
        "title_css": ["td.content-title"],
        "text_css": ["div.content-body"],
        "publish_date_css": ["div.content-baseInfo"]
    }
}

```

--

若使用xpath，建议修改如下：<br/>
```json
{
    "start_urls": ["http://www.pengxi.gov.cn/notice"],
    "crawl_rules": [
        {
            "restrict_xpath": '//div[contains(@class, "classportlet-column-last")]//a',
            "callback": "parse_html"
        }
    ],
    "item_rules": {
        "title_xpath": ['//td[@class="content-title"]'],
        "text_xpath": ['//div[@class="content-body"]'],
        "publish_date_xpath": ['//div[@class="content-baseInfo"]']
    }
}

```

--

### 爬取规则分为2种方式
1. 基于html文档爬取（查看web浏览器中控制台中第一个请求，如果第一个请求response中含有链接，即为方式1）
2. 基于Ajax.json爬取（否则即为方式2，需要查看控制台中xhr请求识别json请求）

--

方式2 - 百家号 - 沭阳咨询<br/>
```json
{
    "start_urls": [
        "https://mbd.baidu.com/webpage?tab=main&num=10&uk=ZaalYuiJExafY8flVtp0CQ&source=pc&type=newhome&action=dynamic&format=json&otherext=h5_20201217112057&Tenger-Mhor=601671344"
    ],
    "request_headers": {},
    "request_cookies": {
        "BAIDUID": "EFA0180B2AD916F6F2C907000311BF6E:FG=1"
    },
    "crawl_rules": [{
            "restrict_json": "data.list[*].itemData.url",
            "callback": "parse_html"
        }
    ],
    "item_rules": {
        "title_css": ["#ssr-content > div.app-module_contentWrapper_2jN0Z > div.app-module_headerWrapper_3tNyU > div > h2"],
        "text_css": ["div.index-module_articleWrap_2Zphx"],
        "publish_date_css": ["#ssr-content > div.app-module_contentWrapper_2jN0Z > div.app-module_headerWrapper_3tNyU > div > div > div.index-module_authorTxt_V6XfG > div"]
    }
}
```

--

在Scrapy中支持css扩展属性如下：<br/>
- （1）::text  获取元素文本信息
- （2）::attr(name)  获取元素属性值

---

### 爬虫验证工具
![img](https://img-blog.csdnimg.cn/20210712105345616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

--

### 爬取结果
![img](https://img-blog.csdnimg.cn/20210712105500382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

---

### 添加爬虫数据来源
![img](https://img-blog.csdnimg.cn/20210712100013690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

--

<font color='red'><b>注意💣</b></font><br/>
1. 同类网站在录入【来源名称】时，需指定统一前缀（如：百家号-, 搜狐新闻-），以方便后续统计
2. 选择text_css的时候，不要把边栏的推荐列表也给选上，要选择正文内容，否则会引入无效的关键字

---

# OVER✌️

</textarea>
