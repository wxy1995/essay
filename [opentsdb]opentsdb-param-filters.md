### 问题引入
> 查询除了host为A&B之外的所有指标的cpu数据

因为版本之前比较旧，使用也比较简单，一直用tag进行相关查询。
起初希望看文档tag能否支持exclude，最后结论是不能。

tag只能做到，指定具体某些或者所有，不能实现exclude匹配。
因为不熟悉filter type，所以一直没有使用filter，最后发现filter功能强大很多。
但是需要注意一定，filters和tags不能同时使用。

### Filters
使用filter，可以这么设置：
```
filters: [
  {
    “tagk”: “host”,
    “type”: “not_literal_or”,
    “filter”: “A|B”,
    “groupBy”: true,
  },
  {
    “tagk”: “cpu”,
    “type”: “wildcard”,
    “filter”: “*”,
    “groupBy”: true,
  }
]
```

##### Filter Type注解（即tagv的过滤规则）：
- literal_or
    - 精确匹配多项迭代值，多项迭代值以 ‘|' 分隔，大小写敏感
- iliteral_or
    - 精确匹配多项迭代值，多项迭代值以 ‘|' 分隔，忽略大小写
- not_literal_or
    - 精确取非匹配，大小写敏感
- not_iliteral_or
    - 精确取非匹配，忽略大小写
- wildcard
    - 通配符匹配，大小写敏感
- iwildcard
    - 通配符匹配，忽略大小写
- regexp
    - 正则表达式匹配

### 其他场景举例
- 查询host为A&B的所有指标的cpu数据
```
filters: [
  {
    “tagk”: “host”,
    “type”: “literal_or”,
    “filter”: “A|B”,
    “groupBy”: true,
  },
  {
    “tagk”: “cpu”,
    “type”: “wildcard”,
    “filter”: “*”,
    “groupBy”: true,
  }
]
```
- 查询host以centos开头的所有指标的cpu数据
```
filters: [
  {
    “tagk”: “host”,
    “type”: “wildcard”,
    “filter”: “centos*”,
    “groupBy”: true,
  },
  {
    “tagk”: “cpu”,
    “type”: “wildcard”,
    “filter”: “*”,
    “groupBy”: true,
  }
]
```
- 查询host以-PC结尾的的所有指标的cpu数据
```
filters: [
  {
    “tagk”: “host”,
    “type”: “wildcard”,
    “filter”: “.-PC$”,
    “groupBy”: true,
  },
  {
    “tagk”: “cpu”,
    “type”: “wildcard”,
    “filter”: “*”,
    “groupBy”: true,
  }
]
```

### 参考文档
http://opentsdb.net/docs/build/html/user_guide/query/index.html
