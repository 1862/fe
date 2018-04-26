# meta

## 介绍

**Meta elements** are tags used in [HTML](https://en.wikipedia.org/wiki/HTML_element) and [XHTML](https://en.wikipedia.org/wiki/XHTML) documents to provide structured [metadata](https://en.wikipedia.org/wiki/Metadata) about a [Web page](https://en.wikipedia.org/wiki/Web_page).

## 用途

meta元素可用于指定页面描述, 关键字, 和`<head>`未提供的其他元数据

这些元数据将服务于浏览器 (如何布局或重载页面), 搜索引擎和其它网络服务

##	属性

- `name`属性决定种类 (把 content 属性关联到一个名称)

  - 标准名称
    - application-name 定义正运行在该网页上的网络应用名称
    - author 作者名称
    - description 页面内容描述
    - generator 指出生成页面的软件
    - keywords 页面关键字
  - 扩展名称 ([WHATWG Wiki MetaExtensions](https://wiki.whatwg.org/wiki/MetaExtensions))
    - viewport
      - 供有关视口初始大小的提示, 仅供移动设备使用
      - eg: ```<meta name="viewport" content="width=device-width, initial-scale=1">```
        - initial-scale 定义设备宽度(纵向模式下的设备宽度或横向模式下的设备高度)与视口大小之间的缩放比率
    - robots 定义搜索引擎爬虫的索引方式
    - 其他名称
      - Baidu: `mobile-agent`, `baiduspider`
      - Twitter: `twitter:card`, `twitter:image`, `twitter:creator:id`
      - Google: `application-url`, `google-site-verification`, `googlebot`

-  `http-equiv`定义响应HTTP头部(指示服务器在发送实际的文档之前先在要传送给浏览器的 MIME 文档头部包含名称/值对)

  > 相当于http的文件头作用

  - content-Type 设定网页字符集 (使用`<meta>`元素的`charset` 属性代替)
  - X-UA-Compatible 浏览器采取何种版本渲染当前页面
  - expires 设定网页的到期时间, 过期后网页必须到服务器上重新传输
  - refresh
    - 如果`content` 只包含一个正整数, 则是重新载入页面的时间间隔(秒)
    - 如果`content` 包含一个正整数并且跟着一个字符串, 则是重定向到指定链接的时间间隔(秒)
  - cache-control 指定请求和响应遵循的缓存机制


- `content`属性为 `http-equiv` 或 `name` 属性提供了与其相关的值的定义

- `scheme` (HTML5 废弃)

- `charset` (HTML5 加入) 声明当前文档所使用的字符编码

  - 推荐 ```<meta charset='utf-8'>```

    ​



