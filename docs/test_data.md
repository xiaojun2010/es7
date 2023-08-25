```json

#create index
PUT /test_index

GET /test_index


DELETE /test_index


POST /test_index/_doc/1
{
  "username":"alfred1",
  "age":1
}

GET /test_index/_doc/a-MoIIoBY2F2cWlom7kT

GET /test_index/_doc/1


GET /test_index/_search
{
  "query": {
    "term": {
      "age":20
    }
  }
}

GET /test_index/_search

POST _bulk
{"index":{"_index":"test_index","_id":"6"}}
{"username":"alfred6","age":26}
{"delete":{"_index":"test_index","_id":"3"}}
{"update":{"_id":"2","_index":"test_index"}}



POST _bulk
{"index":{"_index":"test_index","_id":"6"}}
{"username6":"alfred","age":26}


# multi_get
GET /_mget
{
  "docs": [
    {"_index": "test_index","_id": "5"},
    {"_index": "test_index","_id": "6"}
  ]
}

POST _analyze
{
  "analyzer": "standard",
  "text": "hello world! hello xiaojun"
}


POST test_index/_analyze
{
  "field": "username",
  "text": "hello world!"
}

#自定义分词器
POST _analyze
{
  "tokenizer": "standard",
  "filter": ["lowercase"],
  "text":"Hello World!"
}

#分词器 ： standard
POST _analyze
{
  "analyzer": "standard",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone"
}


#分词器 ： simple analyzer
POST _analyze
{
  "analyzer": "simple",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone"
}

#分词器 ： whitespace
POST _analyze
{
  "analyzer": "whitespace",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone"
}


#分词器 ： stop

POST _analyze
{
  "analyzer": "stop",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone"
}

#keyword
POST _analyze
{
  "analyzer": "keyword",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone"
}

#pattern
POST _analyze
{
  "analyzer": "pattern",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone"
}


#自定义分词、自定义分词器
#通过自定义Character Filter 、Tokenizer 和 Token Filter 实现

POST _analyze
{
  "tokenizer": "keyword",
  "char_filter": ["html_strip"],
  "text":"<p>I&apos;m so <b>happy</b>!</p>"
}

# path_hierarchy 针对文件路径进行切割
# 输入文件路径进行匹配
POST _analyze
{
  "tokenizer": "path_hierarchy",
  "text": "/one/two/three"
}

#自定义分词
POST _analyze
{
  "text":"a Hello,world!",
  "tokenizer": "standard",
  "filter": [
      "stop",
      "lowercase",
      {
        "type":"ngram",
        "min_gram":3,
        "max_gram":4
      }
    ]
}

#自定义分词器
PUT test_index
{
  "settings": {
    "analysis": {
      "char_filter": {},
      "tokenizer": {},
      "filter": {},
      "analyzer": {}
    }
  }
}

DELETE test_index_1

#自定义分词器
PUT test_index_1
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer":{
          "type":"custom",
          "tokenizer":"standard",
          "char_filter":["html_strip"],
          "filter":[
              "lowercase",
              "asciifolding"
              ]
        }
      }
    }
  }
}

POST test_index_1/_analyze
{
  "analyzer": "my_custom_analyzer",
  "text": "Is this <b>a box </b>?"
}

DELETE test_index_2

#复杂的自定义分词器
PUT test_index_2
{
    "settings": {
      "analysis": {
        "analyzer": {
          "my_custom_analyzer":{
          "type":"custom",
          "char_filter":["emoticons"],
          "tokenizer":"punctuation",
          "filter":[
            "lowercase",
            "english_stop"
            ]
          }
        },
            "tokenizer":{
            "punctuation":{
            "type":"pattern",
            "pattern":"[ .,!?]"
          }
        },
         "char_filter":{
        "emoticons":{
          "type":"mapping",
          "mappings":[
            ":) => _happy_",
            ":( ==> _sad_"
            ]
        }
      },
        "filter":{
        "english_stop":{
          "type":"stop",
          "stopwords":"_english_"
        }
      }

    }
  }
}

POST test_index_2/_analyze
{
  "analyzer": "my_custom_analyzer",
  "text":"I'm a :) person ,and you ?"
}



PUT test_index_3
{
  "settings": {
    "index.max_ngram_diff":5,
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "my_tokenizer"
        }
      },
      "tokenizer": {
        "my_tokenizer": {
          "type": "ngram",
          "min_gram": 3,
          "max_gram": 6,
          "token_chars": [
            "letter",
            "digit"
          ]
        }
      }
    }
  }
}


POST test_index_3/_analyze
{
  "analyzer": "my_analyzer",
  "text":"I'm a :) person ,and you ?"
}

GET /test_index/_mapping


DELETE my_index

PUT my_index
{
  "mappings": {
    "dynamic":"strict",
    "properties": {
      "title":{
        "type": "text"
      },
      "name":{
        "type": "keyword"
      },
      "age":{
        "type": "integer"
      }
    }
  }
}

GET /my_index/_mapping


PUT my_index/_doc/1
{
  "title":"hello,world",
  "name":"name test",
  "age":20
}


GET my_index/_search

GET my_index/_search
{
  "query": {
    "match": {
      "desc": "here"
    }
  }
}


#copy_to
PUT my_index1
{
  "mappings": {
    "properties": {
      "first_name":{
        "type": "text",
        "copy_to": "full_name"
      },
      "last_name":{
        "type": "text",
        "copy_to": "full_name"
      },
      "full_name":{
        "type": "text"
      }
    }
  }
}

PUT my_index1/_doc/1
{
  "first_name":"John",
  "last_name":"Smith"
}

#同时包含John Smith 2个单词的才搜索出来
GET my_index1/_search
{
  "query": {
    "match": {
      "full_name": {
        "query": "John Smith",
        "operator": "and"
      }
    }
  }
}


DELETE my_index

PUT my_index
{
  "mappings": {
    "properties": {
      "cookie":{
        "type": "text",
        "index": false
      }
    }
  }
}

PUT my_index/_doc/1
{
  "cookie":"name=alfred"
}

GET /my_index/_search

GET /my_index/_search
{
  "query": {
    "match": {
      "cookie": "name"
    }
  }
}


PUT my_index
{
  "mappings": {
    "dynamic_date_formats": ["MM/dd/yyyy"],
    "numeric_detection": true
  }
}

PUT my_index/_doc/1
{
  "create_date":"09/25/2023",
  "my_float":"1.0",
  "my_integer":2
}


GET /my_index






DELETE test_index

PUT test_index
{
  "mappings": {
    "dynamic_templates":[
        {
          "strings_as_keywords":{
            "match_mapping_type":"string",
            "mapping":{
              "type":"keyword"
            }
          }
        }
      ]
  }
}

PUT test_index/_doc/1
{
  "name":"alfred",
  "message":"很漂亮"
}

GET test_index/_mapping

GET test_index/_search

DELETE test_index

PUT test_index
{
  "mappings": {
    "dynamic_templates":[
        {
          "message_as_text":{
             "match_mapping_type":"string",
             "match":"message*",
             "mapping":{
               "type":"text"
             }
          }
        },
        {
          "strings_as_keywords":{
            "match_mapping_type":"string",
            "mapping":{
              "type":"keyword"
            }
          }
        }
      ]
  }
}

PUT test_index/_doc/1
{
  "name":"alfred",
  "message":"很漂亮"
}

GET test_index/_mapping

#索引模板
DELETE _template/test_template

PUT _template/test_template
{
  "index_patterns": ["te*","bar*"],
  "order": 0,
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "_source": {
      "enabled": false
    },
    "properties": {
      "name":{
        "type": "keyword"
      }
    }
  }
}


DELETE _template/test_template2

PUT _template/test_template2
{
  "index_patterns": ["test*"],
  "order": 1,
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "_source": {
      "enabled": true
    }
  }
}

DELETE test_index

PUT test_index

GET test_index/_settings

GET test_index

# data
DELETE test_search_index

PUT test_search_index
{
  "settings": {
    "index":{
        "number_of_shards": "1"
    }
  }
}


DELETE test_search_index

POST test_search_index/_bulk
{"index":{"_id":"1"}}
{"username":"alfred way","job":"java engineer","age":18,"birth":"1990-01-02","isMarried":false}
{"index":{"_id":"2"}}
{"username":"alfred","job":"java senior engineer and java specialist","age":28,"birth":"1980-05-07","isMarried":true}
{"index":{"_id":"3"}}
{"username":"lee","job":"java and ruby engineer","age":22,"birth":"1985-08-07","isMarried":false}
{"index":{"_id":"4"}}
{"username":"alfred junior way","job":"ruby engineer","age":23,"birth":"1989-08-07","isMarried":false}

GET test_search_index/_doc/1

#泛查询 是基于所有字段查询
GET test_search_index/_search?q=alfred
{
  "profile": true
}
#
GET test_search_index/_search?q=username:(alfred way)
{
  "profile": true
}

#查询 username 包含alfred，or 全文包含 way 的文档
GET test_search_index/_search?q=username:alfred way
{
  "profile": true
}

#加双引号，就是词语查询
GET test_search_index/_search?q=username:"alfred way"
{
  "profile": true
}


GET test_search_index/_search?q=username:alfred AND way
{
  "profile": true
}


GET test_search_index/_search?q=username:(alfred AND way)
{
  "profile": true
}

GET test_search_index/_search?q=username:(alfred NOT way)
{
  "profile": true
}

GET test_search_index/_search?q=username:(alfred +way)
{
  "profile": true
}

GET test_search_index/_search?q=username:(alfred %2Bway)
{
  "profile": true
}

#username 包含alfred，或者 age>20的文档
GET test_search_index/_search?q=username:alfred age:>20
{
  "profile": true
}

#username 包含alfred，并且 age>20的文档
GET test_search_index/_search?q=username:alfred AND age:>20
{
  "profile": true
}


GET test_search_index/_search?q=birth:(>1980 AND 1990)
{
  "profile": true
}

#查询username 以alf开头的所有文档
GET test_search_index/_search?q=username:alf*
{
  "profile": true
}

#a开头，l后面任意字符, ?表示前面的a可有可无
GET test_search_index/_search?q=username:/[a]?l.*/
{
  "profile": true
}


GET test_search_index/_search?q=username:alfed~1
{
  "profile": true
}

GET test_search_index/_search?q=job:"java engineer"~1
{
  "profile": true
}

GET test_search_index/_search?q=job:"java engineer"~2
{
  "profile": true
}


#多字段查询
GET test_search_index/_search
{
  "query": {
    "multi_match": {
      "query": "alfred java",
      "fields": ["username","job"],
      "operator": "or",
      "type": "most_fields"
    }
  }
}


#多字段查询 原始得分
GET test_search_index/_search
{
  "query": {
    "function_score": {
      "query": {
        "multi_match": {
          "query": "alfred java",
          "fields": ["username","job"],
          "operator": "or",
          "type": "most_fields"
        }
      }
    }
  }
}
#多字段查询 function_score
GET test_search_index/_search
{
  "query": {
    "function_score": {
      "query": {
        "multi_match": {
          "query": "alfred java",
          "fields": ["username","job"],
          "operator": "or",
          "type": "most_fields"
        }
      },
      "functions": [
        {
          "field_value_factor": {
            "field": "username.keyword",
            "modifier": "log2p",
            "factor": 10
          }
        }
      ]
    }
  }
}


GET test_search_index/_search
{
  "query": {
    "multi_match": {
      "query": "alfred java",
      "fields": ["username^10","job^0.1"],
      "type": "most_fields"
    }
  }
}


GET /test_search_index/_search
{
  "query": {
    "term": {
      "username": {
        "value": "alfred"
      }
    }
  }
}

#Match Query

#查询 username 里有 alfred 或 way 的文档，涉及到分词
GET test_search_index/_search
{
  "profile": true,
  "query": {
    "match": {
      "username": "alfred way"
    }
  }
}
#同时包含 alfred 和 way 2个词
GET test_search_index/_search
{
  "query": {
    "match": {
      "username": {
        "query": "alfred way",
        "operator": "and"
      }
    }
  }
}

#minimum_should_match 控制匹配的单词数量
GET test_search_index/_search
{
  "query": {
    "match": {
      "job": {
        "query": "java ruby engineer",
        "minimum_should_match": "2"
      }
    }
  }
}

#explain 查看计算方法
GET test_search_index/_search
{
  "explain": true,
  "query": {
    "match": {
      "username": "alfred way"
    }
  }
}

#match phrase query 对字段做检索，有顺序要求
GET test_search_index/_search
{
  "query": {
    "match_phrase": {
      "job": "java engineer"
    }
  }
}

#通过slop参数控制单词间的间隔.slop : 1 允许有1个距离的差异
GET test_search_index/_search
{
  "query": {
    "match_phrase": {
      "job": {
         "query": "java engineer",
         "slop": 2
      }
    }
  }
}

#query string query ,类似于URI Search 中的q参数查询：username 包含alfred ，也包含way的文档

GET test_search_index/_search
{
  "query": {
    "query_string": {
      "default_field": "username",
      "query": "alfred AND way"
    }
  }
}

GET test_search_index/_search
{
  "profile": true,
  "query": {
    "query_string": {
      "fields": ["username","job"],
      "query": "alfred OR (java AND ruby)"
    }
  }
}

#simple query string
#例子：必须包含 way，可以包含 alfred
GET test_search_index/_search
{
  "profile": true,
  "query": {
    "simple_query_string": {
      "query": "alfred +way",
      "fields": ["username"]
    }
  }
}

GET test_search_index/_search
{
  "profile": true,
  "query": {
    "simple_query_string": {
      "query": "alfred +way AND java",
      "fields": ["username"]
    }
  }
}


#term query
#不对查询语句做分词处理,而 match会分词，查询username包含alfred的文档
GET test_search_index/_search
{
  "query": {
    "term": {
      "username": "alfred"
    }
  }
}

GET test_search_index/_search
{
  "query": {
    "term": {
      "username": "alfred way"
    }
  }
}


#terms 查询：一次传入多个单词进行查询
GET test_search_index/_search
{
  "query": {
    "terms": {
      "username": [
        "alfred",
        "way"
      ]
    }
  }
}

#range query 范围查询,主要针对数值 和 日期
GET test_search_index/_search
{
  "query": {
    "range": {
      "age": {
        "gte": 10,
        "lte": 20
      }
    }
  }
}

GET test_search_index/_search
{
  "query": {
    "range": {
      "birth": {
        "gte": "1990-01-01"
      }
    }
  }
}

GET test_search_index/_search
{
  "query": {
    "range": {
      "birth": {
        "gte": "now-40y"
      }
    }
  }
}


GET test_search_index/_search
{
  "query": {
    "range": {
      "birth": {
        "gte": "2010||-20y"
      }
    }
  }
}


#Constant Score Query
GET test_search_index/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "match":{
          "username":"alfred"
        }
      },
      "boost": 1.2
    }
  }
}

#查询模板
GET test_search_index/_search
{
  "query": {
    "bool": {
      "must": [
        {}
      ],
      "must_not": [
        {}
      ],
      "should": [
        {}
      ],
      "filter": [
        {}
      ]
    }
  }
}


#Bool Query Filter
#Es 针对filter 会有智能缓存，因此执行效率很高
#做简单匹配查询不考虑算分时，推荐使用filter替代query等
GET test_search_index/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "username": "alfred"
          }
        }
      ]
    }
  }
}

GET test_search_index/_search
{
  "profile": true,
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "username": "alfred"
          }
        },
        {
          "match": {
            "job": "specialist"
          }
        }
      ]
    }
  }
}

GET test_search_index/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "job": "java"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "job": "ruby"
          }
        }
      ]
    }
  }
}

#should 查询，文档必须满足至少一个条件,minimum_should_match 可以控制满足条件的个数或百分比
GET test_search_index/_search
{
  "query": {
    "bool": {
      "should": [
        {"term": {"job": "java"}},
        {"term": {"job": "ruby"}},
        {"term": {"job": "specialist"}}
      ],
      "minimum_should_match": 2
    }
  }
}

#同时包含should和must时，文档不必满足should中的条件，但是如果满足条件，会增加相关得分
GET test_search_index/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {"username": "alfred"}}
      ],
      "should": [
        {"term": {"job": "ruby"}}
      ]
    }
  }
}

GET test_search_index/_search
{
  "_source": false,
  "query": {
    "bool": {
      "must": [
        {"term": {"username": "alfred"}}
      ],
      "should": [
        {"term": {"job": "ruby"}}
      ]
    }
  }
}

GET test_search_index/_search
{
  "_source": ["username","age"],
  "query": {
    "bool": {
      "must": [
        {"term": {"username": "alfred"}}
      ],
      "should": [
        {"term": {"job": "ruby"}}
      ]
    }
  }
}



GET test_search_index/_search
{
  "_source": {
    "includes": "*i*",
    "excludes": "birth"
  },
  "query": {
    "bool": {
      "must": [
        {"term": {"username": "alfred"}}
      ],
      "should": [
        {"term": {"job": "ruby"}}
      ]
    }
  }
}



GET test_search_index/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "title": "Search"
        }},
        {
          "match": {
            "content": "Elasticsearch"
          }
        }
      ],
      "filter": [
        {"term": {
          "status": "published"
        }},
        {
          "range": {
            "publish_date": {
              "gte": "1980-01-01"
            }
          }
        }
      ]
    }
  }
}

#count API
GET test_search_index/_count
{
  "query": {
    "match": {
      "username": "alfred"
    }
  }
}

#dfs query-then-fetch
GET test_search_index/_search?search_type=dfs_query_then_fetch
{
  "query": {
    "match": {
      "username": "alfred"
    }
  },
  "sort": [
    {
      "birth": {
        "order": "desc"
      }
    }
  ]
}

GET test_search_index/_search
{
  "docvalue_fields": [

      "username.keyword",
      "age"
    ]
}

#scroll 快照
GET test_search_index/_search?scroll=5m
{
  "size": 1
}
POST _search/scroll
{
  "scroll":"5m",
  "scroll_id":"FGluY2x1ZGVfY29udGV4dF91dWlkDXF1ZXJ5QW5kRmV0Y2gBFjhsa1psR0RnUkZtc3A4cTZ0bkdRSmcAAAAAAAEURxZEZDJOV0JqMFQwQ2locFJvUllNMGdn"
}

#search_after
GET test_search_index/_search
{
  "size": 1,
  "sort": [
    {
      "age": "desc",
      "_id":"desc"
    }
  ]
}

GET test_search_index/_search
{
  "size": 1,
  "search_after":[28,"2"],
  "sort": [
    {
      "age": {
        "order": "desc"
      },
      "_id":{
        "order": "desc"
      }
    }
  ]
}

GET test_search_index/_search
{
  "size": 1,
  "search_after":[23,"4"],
  "sort": [
    {
      "age": {
        "order": "desc"
      },
      "_id":{
        "order": "desc"
      }
    }
  ]
}

PUT /blog_index
{
  "mappings": {
    "properties": {
      "title":{
        "type": "text",
        "fields": {
          "keyword":{
            "type":"keyword",
            "ignore_above":100
          }
        }
      },
      "publist_date":{
        "type": "date"
      },
      "author":{
        "type": "keyword",
        "ignore_above":100
      },
      "abstract":{
        "type": "text"
      },
      "url":{
        "enabled":false
      }
    }
  }
}

PUT blog_index/_doc/1
{
  "title":"blog title",
  "content":"blog content"
}

GET blog_index/_search

GET blog_index/_search?_source=title

DELETE /blog_index

PUT /blog_index
{
  "mappings": {
    "_source": {"enabled": false},
    "properties": {
      "title":{
        "type": "text",
        "fields": {
          "keyword":{
            "type":"keyword",
            "ignore_above":100
          }
        },
        "store": true
      },
      "publist_date":{
        "type": "date",
        "store": true
      },
      "author":{
        "type": "keyword",
        "ignore_above":100,
        "store": true
      },
      "abstract":{
        "type": "text",
        "store": true
      },
      "content":{
        "type": "text",
        "store": true
      },
      "url":{
        "type": "keyword",
        "doc_values": false,
        "norms": false,
        "ignore_above": 100,
        "store": true
      }
    }
  }
}



PUT blog_index/_doc/1
{
  "title":"blog title",
  "content":"blog content"
}

GET blog_index/_search

GET blog_index/_search
{
  "stored_fields": ["title"],
  "query": {
    "match": {
      "content": "content"
    }
  },
  "highlight": {
    "fields": {
      "content": {}
    }
  }
}

GET blog_index/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "com": "TEXT"
          }
        }
      ]
    }
  }
}



DELETE blog_index
PUT blog_index/_doc/2
{
  "title":"Blog Number One",
  "author":"alfred",
  "comments":[
      {
        "username":"lee",
        "date":"2017-01-02",
        "content":"awesome article!"
      },
      {
        "username":"fax",
        "date":"2017-04-02",
        "content":"thanks!"
      }
    ]
}

GET blog_index/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "comments.username": "lee"
          }
        },
        {
          "match": {
            "comments.content": "thanks"
          }
        }
      ]
    }
  }
}

DELETE blog_index_nested

PUT /blog_index_nested
{
  "mappings": {
    "properties": {
     "title":{
        "type": "text",
        "fields": {
          "keyword":{
            "type":"keyword",
            "ignore_above":100
          }
        },
        "store": true
      },
      "publist_date":{
        "type": "date",
        "store": true
      },
      "author":{
        "type": "keyword",
        "ignore_above":100,
        "store": true
      },
      "abstract":{
        "type": "text",
        "store": true
      },
      "content":{
        "type": "text",
        "store": true
      },
      "url":{
        "type": "keyword",
        "doc_values": false,
        "norms": false,
        "ignore_above": 100,
        "store": true
      },
      "comments":{
        "type": "nested",
        "properties": {
          "username":{
            "type":"keyword",
            "ignore_above": 100
          },
          "date":{
            "type":"date"
          },
          "content":{
            "type": "text"
          }
        }
      }
    }
  }
}

PUT blog_index_nested/_doc/2
{
  "title":"Blog Number One",
  "author":"alfred",
  "comments":[
      {
        "username":"lee",
        "date":"2017-01-02",
        "content":"awesome article!"
      },
      {
        "username":"fax",
        "date":"2017-04-02",
        "content":"thanks"
      }
    ]
}

GET blog_index_nested/_search

GET blog_index_nested/_search
{
  "query": {
    "nested": {
      "path": "comments",
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "comments.username": "lee"
              }
            },
            {
              "match": {
                "comments.content": "awesome"
              }
            }
          ]
        }
      }
    }
  }
}

GET blog_index_nested/_search
{
  "query": {
    "nested": {
      "path": "comments",
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "comments.username": "lee"
              }
            },
            {
              "match": {
                "comments.content": "thanks"
              }
            }
          ]
        }
      }
    }
  }
}


PUT blog_index_parent_child
{
  "mappings": {
    "properties": {
      "join":{
        "type": "join",
        "relations":{
          "blog":"comment"
        }
      }
    }
  }
}

#parent child
DELETE blog_index_parent_child

PUT blog_index_parent_child
{
  "mappings": {
    "properties": {
      "join":{
        "type": "join",
        "relations":{
          "blog":"comment"
        }
      }
    }
  }
}

GET /blog_index_parent_child

PUT blog_index_parent_child/_doc/1
{
  "title":"blog",
  "join":"blog"
}

PUT blog_index_parent_child/_doc/2
{
  "title":"blog2",
  "join":"blog"
}

#创建子文档
PUT blog_index_parent_child/_doc/comment-1?routing=1
{
  "comment":"comment world",
  "join":{
    "name":"comment",
    "parent":1
  }
}

PUT blog_index_parent_child/_doc/comment-2?routing=2
{
  "comment":"comment hello",
  "join":{
    "name":"comment",
    "parent":2
  }
}
GET /blog_index_parent_child/_search

#parent_id 查询：查询父文档id=2的所有子文档类型为comment 的子文档
GET blog_index_parent_child/_search
{
  "query": {
    "parent_id":{
      "type":"comment",
      "id":2
    }
  }
}
#has_child 包含了子文档的所有父文档
GET blog_index_parent_child/_search
{
  "query": {
    "has_child": {
      "type": "comment",
      "query": {
        "match": {
          "comment":"world"
        }
      }
    }
  }
}
#has_parent 父文档包含*** 的子文档
GET blog_index_parent_child/_search
{
  "query": {
    "has_parent": {
      "parent_type": "blog",
      "query": {
        "match": {
          "title": "blog"
        }
      }
    }
  }
}


GET blog_index/_search

GET blog_index/_doc/2

#reindex
POST blog_index/_update_by_query?conflicts=proceed

GET blog_index/_doc/2


POST _reindex
{
  "source": {
    "index": "blog_index"
  },
  "dest": {
    "index": "blog_new_index"
  }
}



GET blog_new_index/_search


GET blog_index/_search


POST blog_index/_update_by_query?conflicts=proceed&wait_for_completion=false


GET _tasks/Dd2NWBj0T0CihpRoRYM0gg:1260096


DELETE demo_common

# key value 方式
PUT demo_common
{
  "mappings": {
    "properties": {
      "url":{
        "type": "keyword"
      },
      "@timestamp":{
        "type": "date"
      },
      "cookies":{
        "properties": {
          "username":{
            "type":"keyword"
          },
          "startTime":{
            "type":"date"
          },
          "age":{
            "type":"integer"
          }
        }
      }
    }
  }
}

PUT demo_common/_doc/1
{
  "url":"/home",
  "@timestamp":"2017-09-10",
  "cookies":{
    "username":"time",
    "statTime":"2017-09-10T12:09:02",
    "age":12
  }
}

PUT demo_common/_doc/2
{
  "url":"/home",
  "@timestamp":"2017-10-10",
  "cookies":{
    "username":"tom",
    "statTime":"2017-08-10T12:09:02",
    "age":20
  }
}


GET demo_common/_search
{
  "query": {
    "range": {
      "cookies.age": {
        "gte": 10,
        "lte": 20
      }
    }
  }
}


# key value 方式
DELETE demo_key_value

PUT demo_key_value
{
  "mappings": {
    "properties": {
      "url":{
        "type": "keyword"
      },
      "@timestamp":{
        "type": "date"
      },
      "cookies":{
        "type": "nested",
        "properties": {
          "cookieName":{
            "type":"keyword"
          },
          "cookieValueKeyword":{
            "type":"keyword"
          },
          "cookieValueInteger":{
            "type":"integer"
          },
          "cookieValueDate":{
            "type":"date"
          }
        }
      }
    }
  }
}


PUT demo_key_value/_doc/1
{
  "url":"/home",
  "@timestamp":"2017-09-10",
  "cookies":[
      {
        "cookieName":"username",
        "cookieValueKeyword":"time"
      },
      {
        "cookieName":"statTime",
        "cookieValueDate":"2017-09-10T12:09:02"
      },
      {
        "cookieName":"age",
        "cookieValueInteger":12
      }
    ]
}

PUT demo_key_value/_doc/2
{
  "url":"/index",
  "@timestamp":"2017-10-10",
  "cookies":[
      {
        "cookieName":"tom",
        "cookieValueKeyword":"time"
      },
      {
        "cookieName":"statTime",
        "cookieValueDate":"2017-08-10T12:09:02"
      },
      {
        "cookieName":"age",
        "cookieValueInteger":20
      }
    ]
}

GET demo_key_value/_search
{
  "query": {
    "nested": {
      "path": "cookies",
      "query": {
        "bool": {
          "filter": [
            {
              "term": {
                "cookies.cookieName": "age"
              }
            },
            {
              "range": {
                "cookies.cookieValueInteger": {
                  "gte": 15,
                  "lte": 20
                }
              }
            }
          ]
        }
      }
    }
  }
}


DELETE flattened_test_index

PUT flattened_test_index
{
  "mappings": {
    "properties": {
      "dynamic":"false",
      "title":{
        "type": "keyword"
      },
      "updateTime":{
        "type": "integer"
      }
    }
  }
}

#新建一个doc，设置一个不被mapping的字段 testDynamicFalse

POST flattened_test_index/_doc/4
{
  "testDynamicFalse":"DynamicFalse"
}

#对doc为4的文档进行docId的检索，发现此文档已经写入es，并可以返回
GET flattened_test_index/_doc/4

#对testDynamicFalse字段进行检索，发现不能检索到。验证dynamic："false"的结果：dynamic 设置为 false 后，新来的非 mapping 预设字段数据可以写入，但是：不能被检索，仅支持 Get 获取文档的方式通过 _source 查看详情内容。

GET flattened_test_index/_search
{
  "query": {
    "term": {
      "FIELD": {
        "testDynamicFalse":"DynamicFalse"
      }
    }
  }
}

#设置为 strict 后，再插入未mapping字段数据，会报错如下


DELETE flattened_test_index

PUT flattened_test_index
{
  "mappings": {
    "dynamic":"strict",
    "properties": {
      "title":{
        "type": "keyword"
      },
      "updateTime":{
        "type": "integer"
      }
    }
  }
}

POST flattened_test_index/_doc/4
{
  "testDynamicFalse":"DynamicFalse"
}

DELETE flattened_test_index

PUT flattened_test_index
{
  "mappings": {
    "dynamic":"strict",
    "properties": {
      "lables":{
        "type": "flattened"
      },
      "title":{
        "type": "keyword"
      },
      "updateTime":{
        "type": "integer"
      }
    }
  }
}

POST flattened_test_index/_doc/1
{
  "title":"test1",
  "updateTime":110000000,
  "lables":{
    "cargo":"costa",
    "driver":[
        "nestle",
        "kitKat"
      ],
      "timestamp":{
        "start":1541458026,
        "end":1541457010
      }
  }
}

POST flattened_test_index/_doc/2
{
  "title":"world",
  "updateTime":1123456000,
  "lables":{
    "cargo":"nike",
    "driver":[
        "tesla",
        "haval"
      ],
      "timestamp":{
        "start":1541458765,
        "end":1541457912
      }
  }
}


GET flattened_test_index/_mapping

#检索 这里针对单个字段labels、子字段labels.cargo、labels.timestamp.start做了terms和range查询，均可以检索到

GET flattened_test_index/_search
{
  "query": {
    "term": {
      "lables": "kitKat"
    }
  }
}


GET flattened_test_index/_search
{
  "query": {
    "term": {
      "lables.cargo": "nike"
    }
  }
}

GET flattened_test_index/_search
{
  "query": {
    "range": {
      "lables.timestamp.start": {
        "from": 10,
        "to": 1541458765,
        "include_lower":false,
        "include_upper":true,
        "boost": 1
      }
    }
  }
}

#更新labels字段，添加其他字段，并查看mapping及检索 这里在labels字段下新增了truck字段，并赋值；post新索引后，发现mapping字段依然只有labels字段，没有新增字段；并且检索做terms查询，依旧可以检索到
POST flattened_test_index/_doc/3
{
  "title":"test3",
  "lables":{
    "truck":[
        "flatcar",
        "trailer"
      ]
  }
}

GET flattened_test_index/_mapping

GET flattened_test_index/_search
{
  "query": {
    "term": {
      "lables": "flatcar"
    }
  }
}

GET flattened_test_index/_search
{
  "query": {
    "term": {
      "lables.truck": "trailer"
    }
  }
}

#使用script脚本时，可以从正排中获取doc字段
GET flattened_test_index/_search
{
  "query": {
    "match_all": {}
  },
  "_source": ["lables.updateTime"],
  "sort": [
    {
      "_script": {
        "script":{
          "id":"flattened_test_script"
        },
        "type":"number",
        "order": "desc"
      }
    }
  ]
}

#聚合统计只能看出labels中有多少buckets，对应的key看不到。
GET flattened_test_index/_search
{
  "size": 10,
  "aggs": {
    "test_group": {
      "terms": {
        "field": "lables"
      }
    }
  }
}

#无法进行number类型的统计、数值计算
GET flattened_test_index/_search
{
  "size": 0,
  "aggs": {
    "avgs": {
      "avg": {
        "field": "lables.timestamp.start"
      }
    }
  }
}

#无法使用highLight 高亮语法
GET flattened_test_index/_search
{
  "query": {
    "term": {
      "lables": "costa"
    }
  },
  "highlight": {
    "fields": {
      "lables": {}
    }
  }
}



PUT /movie
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "title":{"type": "text","analyzer": "english"},
      "tagline":{"type": "text","analyzer": "english"},
      "release_date":{
        "type": "date",
        "format": "8yyyy/MM/dd||yyyy/M/dd||yyyy/MM/d||yyyy/M/d"
      },
      "popularity":{"type": "double"}
    }
  }
}

GET /movie/_mapping





DELETE /movie

#索引建立：
PUT /movie
{
   "settings" : {
      "number_of_shards" : 1,
      "number_of_replicas" : 1
   },
   "mappings": {
     "properties": {
       "title":{"type":"text","analyzer": "english"},
       "tagline":{"type":"text","analyzer": "english"},
       "release_date":{"type":"date",        "format": "8yyyy/MM/dd||yyyy/M/dd||yyyy/MM/d||yyyy/M/d"},
       "popularity":{"type":"double"},
       "cast":{
         "type":"object",
         "properties":{
           "character":{"type":"text","analyzer":"standard"},
           "name":{"type":"text","analyzer":"standard"}
         }
       },
       "overview":{"type":"text","analyzer": "english"}
     }
   }
}

#1. match查询，按照字段上定义的分词分析后去索引内查询
GET /movie/_search
{
  "query":{
    "match":{"title":"steve"}
  }
}

#2. term查询，不进行词的分析，直接去索引查询，及搜索关键词和索引内词的精确匹配
GET /movie/_search
{
  "query":{
    "match":{"title":"steve zissou"}
  }
}

GET /movie/_search
{
  "query":{
    "term":{"title":"steve zissou"}
  }
}
# 的结果区别

# 3.match分词后的and和or
GET /movie/_search
{
  "query":{
    "match":{"title":"basketball with cartoom aliens"}
  }
}
# 使用的是or
GET /movie/_search
{
  "query":{
    "match": {
      "title": {
        "query": "basketball with cartoom aliens",
        "operator": "and"
      }
    }
  }
}
#使用and

#4.最小词项匹配
GET /movie/_search
{
  "query":{
    "match": {
      "title": {
        "query": "basketball with cartoom aliens",
        "operator": "or" ,
        "minimum_should_match": 2
      }
    }
  }
}

#5.短语查询
GET /movie/_search
{
  "query":{
    "match_phrase":{"title":"steve zissou"}
  }
}
#短语前缀查询
GET /movie/_search
{
  "query":{
    "match_phrase_prefix":{"title":"steve zis"}
  }
}

#6.多字段查询
GET /movie/_search
{
  "query":{
    "multi_match":{
      "query":"basketball with cartoom aliens",
      "field":["title","overview"]
    }
  }
}


#操作不管是字符与还是或，按照逻辑关系命中后相加得分

GET /movie/_search
{
  "explain": true,
  "query":{
    "match":{"title":"steve"}
  }
}
#查看数值，tfidf多少分，tfnorm归一化后多少分

#多字段查询索引内有query分词后的结果，因为title比overview命中更重要，因此需要加权重
GET /movie/_search
{
  "query":{
    "multi_match":{
      "query":"basketball with cartoom aliens",
      "fields":["title^10","overview"],
      "tie_break":0.3
    }
  }
}


#继续深入查询：
#1.Bool查询

#must：必须都是true
#must not： 必须都是false
#should：其中有一个为true即可，但true的越多得分越高
GET /movie/_search
{
  "query":{
    "bool": {
      "should": [
        { "match": { "title":"basketball with cartoom aliens"}},
        { "match": { "overview":"basketball with cartoom aliens"}}  
      ]
    }
  }
}


#2.不同的multi_query的type
#和multi_match得分不一样
#因为multi_match有很多种type
#best_fields:默认，取得分最高的作为对应的分数，最匹配模式,等同于dismax模式
GET /movie/_search
{
  "query":{
    "dis_max": {
      "queries": [
        { "match": { "title":"basketball with cartoom aliens"}},
        { "match": { "overview":"basketball with cartoom aliens"}}  
      ]
    }
  }
}

#然后使用explan看下 ((title:steve title:job) | (overview:steve overview:job))，打分规则
GET /movie/_validate/query?explain
{
  //"explain": true,
  "query":{
    "multi_match":{
      "query":"steve job",
      "fields":["title","overview"],
      "operator": "or",
      "type":"best_fields"
    }
  }
}
#以字段为单位分别计算分词的分数，然后取最好的一个,适用于最优字段匹配。


#将其他因素以0.3的倍数考虑进去
GET /movie/_search
{
  "query":{
    "dis_max": {
      "queries": [
        { "match": { "title":"basketball with cartoom aliens"}},
        { "match": { "overview":"basketball with cartoom aliens"}}  
      ],
      "tie_breaker": 0.3
    }
  }
}




#most_fields:取命中的分值相加作为分数，同should match模式，加权共同影响模式

#然后使用explain看下 ((title:steve title:job) | (overview:steve overview:job))~1.0，打分规则
GET /movie/_validate/query?explain
{
  //"explain": true,
  "query":{
    "multi_match":{
      "query":"steve job",
      "fields":["title","overview"],
      "operator": "or",
      "type":"most_fields"
    }
  }
}
#以字段为单位分别计算分词的分数，然后加在一起，适用于都有影响的匹配



#cross_fields:以分词为单位计算栏位总分
#然后使用explain看下 blended(terms:[title:steve, overview:steve]) blended(terms:[title:job, overview:job])，打分规则
GET /movie/_validate/query?explain
{
  //"explain": true,
  "query":{
    "multi_match":{
      "query":"steve job",
      "fields":["title","overview"],
      "operator": "or",
      "type":"most_fields"
    }
  }
}
#以词为单位，分别用词去不同的字段内取内容，拿高的分数后与其他词的分数相加，适用于词导向的匹配



GET /forum/article/_search
{
  "query": {
    "multi_match": {
      "query": "Peter Smith",
      "type": "cross_fields",
      "operator": "or",
      "fields": ["author_first_name", "author_last_name"]
    }
  }
}
#//要求Peter必须在author_first_name或author_last_name中出现
#//要求Smith必须在author_first_name或author_last_name中出现

#//原来most_fiels，可能像Smith //Williams也可能会出现，因为most_fields要求只是任何一个field匹配了就可以，匹配的field越多，分数越高

GET /movie/_search
{
  "explain": true,
  "query":{
    "multi_match":{
      "query":"steve job",
      "fields":["title","overview"],
      "operator": "or",
      "type":"cross_fields"
    }
  }
}
#看一下不同的评分规则



#3.query string
#方便的利用AND(+) OR(|) NOT(-)
GET /movie/_search
{
  "query":{
    "query_string":{
      "fields":["title"],
      "query":"steve AND jobs"

    }
  }
}



#过滤查询

#filter过滤查询

#单条件过滤
GET /movie/_search
{
  "query":{
    "bool":{
      "filter":{
          "term":{"title":"steve"}
      }
    }
  }
}
#多条件过滤
GET /movie/_search
{
  "query":{
    "bool":{
      "filter":[
        {"term":{"title":"steve"}},
        {"term":{"cast.name":"gaspard"}},
        {"range": { "release_date": { "lte": "2015/01/01" }}},
        {"range": { "popularity": { "gte": "25" }}}
        ]
    }
  },
  "sort":[
    {"popularity":{"order":"desc"}}
  ]
}

#带match打分的的filter
GET /movie/_search
{
  "query":{
    "bool":{
      "must": [
        { "match": { "title":   "Search"        }},
        { "match": { "tagline": "Elasticsearch" }}  
      ],
      "filter":[
        {"term":{"title":"steve"}},
        {"term":{"cast.name":"gaspard"}},
        {"range": { "release_date": { "lte": "2015/01/01" }}},
        {"range": { "popularity": { "gte": "25" }}}
        ]
    }
  }
}

#返回0结果

GET /movie/_search
{
  "query":{
    "bool":{
      "should": [
        { "match": { "title":   "Search"        }},
        { "match": { "tagline": "Elasticsearch" }}  
      ],
      "filter":[
        {"term":{"title":"steve"}},
        {"term":{"cast.name":"gaspard"}},
        {"range": { "release_date": { "lte": "2015/01/01" }}},
        {"range": { "popularity": { "gte": "25" }}}
        ]
    }
  }
}

有结果，但是返回score为0，因为bool中若有filter的话，即便should都不满足，只是返回为0分而已
修改为
GET /movie/_search
{
  "query":{
    "bool":{
      "should": [
        { "match": { "title":   "life"        }},
        { "match": { "tagline": "Elasticsearch" }}  
      ],
      "filter":[
        {"term":{"title":"steve"}},
        {"term":{"cast.name":"gaspard"}},
        {"range": { "release_date": { "lte": "2015/01/01" }}},
        {"range": { "popularity": { "gte": "25" }}}
        ]
    }
  }
}
#可以看到分数



#function score自定义打分

GET /movie/_search
{
  "query":{
    "function_score": {
      //原始查询得到oldscore
      "query": {      
        "multi_match":{
        "query":"steve job",
        "fields":["title","overview"],
        "operator": "or",
        "type":"most_fields"
      }
    },
    "functions": [
      {"field_value_factor": {
          "field": "popularity",   //对应要处理的字段
          "modifier": "log2p",    //将字段值+2后，计算对数
          "factor": 10    //字段预处理*10
        }
      }
    ],

    "score_mode": "sum",   //不同的field value之间的得分相加
    "boost_mode": "sum"    //最后在与old value相加
  }
}
}




















```
