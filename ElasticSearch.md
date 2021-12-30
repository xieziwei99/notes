## 概念

- Node
- Cluster
- Index：相当于数据库的概念
- Document：Index 中单条记录称为 Document，同一个 Index 里面的 Document，不要求有相同的结构（scheme）
- type：分组，同一个 Index 的 Document 可以按照不同 type 分组。Elastic 6.x 版只允许每个 Index 包含一个 Type，7.x 版将会彻底移除 Type。



## 基本使用

```sh
# 查看所有 index
GET /_cat/indices?v

# 查看所有 index 的每一个 type
GET /_mapping

# 要有名为 accounts 的 index
GET /accounts/_mapping

PUT accounts
{
  "mappings": {
    "person": {
      "properties": {
        "user": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "title": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "desc": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        }
      }
    }
  }
}

PUT /accounts/person/1
{
  "user": "张三",
  "title": "程序员",
  "desc": "研发-后端"
}

# POST 可不指定 id，则随机生成 id
POST /accounts/person
{
  "user": "李四",
  "title": "程序员",
  "desc": "测试"
}

GET /accounts/person/1

DELETE /accounts/person/1

GET /accounts/person/_search

# size 默认是 10
GET /accounts/person/_search
{
  "query": {
    "match": {
      "desc": "后端"
    }
  },
  "size": 20
}

# 从第1号开始返回，（基于0）
GET /accounts/person/_search
{
  "from": 1
}

# 后端和测试在这里，默认是 or 的关系
# 且 match 中不支持写多个字段，比如同时写 desc 和 title
GET /accounts/person/_search
{
  "query": {
    "match": {
      "desc": "后端 测试"
    }
  }
}

# must 表示 and，should 表示 
GET /accounts/person/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "程序"
          }
        },
        {
          "match": {
            "desc": "测试"
          }
        }
      ]
    }
  }
}
```

```sh
GET /city/_doc/_search

POST /city/_doc
{
  "id": 3,
  "provinceId": 433330,
  "cityName": "长沙",
  "description": ""
}

# 范围查询
GET /city/_doc/_search
{
  "query": {
    "range": {
      "id": {
        "gte": 3,
        "lte": 8
      }
    }
  }
}

# 前缀查询
GET /city/_doc/_search
{
  "query": {
    "prefix": {
      "cityName": "荆"
    }
  }
}

# exists
{
  "query": {
    "bool": {
      "must": [
        {
          "exists": {
            "field": "slideRecoLive"
          }
        }
      ]
    }
  },
  "sort": {
    "time": "desc"
  }
}
```