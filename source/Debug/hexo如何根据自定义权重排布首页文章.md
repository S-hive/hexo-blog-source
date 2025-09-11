---
title: hexo如何根据自定义权重排布首页文章
date: 2025-09-11
---
## 修改配置文件

在`hexo`源目录下，打开`_config.yml`配置文件。

找到代码块：

```yaml
# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 10
  order_by: -date
```

把末尾行代码改成：

```ymal
  order_by: -weight
```

## 在文章 Front-matter 中添加 weight 属性

在需要排序的文章 Markdown 文件头部，添加 `weight` 字段并设置数值（数字越大权重越高，会排在前面）：


```markdown
---
title: 我的第一篇文章
date: 2023-09-10
weight: 10  # 权重值，可自定义
---
```


## 重启 Hexo 服务生效

执行 `hexo clean && hexo s` 重启服务，首页文章会按照 `weight` 数值从高到低排序。