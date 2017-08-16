elk
===

elk总体架构
-----------

<https://www.elastic.co/cn/products>

**Beat**

基于go语言写的轻量型数据采集器，读取数据，迅速发送到Logstash进行解析，亦或直接发送到Elasticsearch进行集中式存储和分析。

**Logstash**

Logstash 是开源的服务器端数据处理管道，能够同时从多个来源采集数据、格式化数据，然后将数据发送到es进行存储。

**ElasticSearch**

Elasticsearch 是基于JSON的分布式搜索和分析引擎，是利用倒排索引实现的全文索引。

**Kibana**
Kibana 能够可视化 Elasticsearch 中的数据并操作。


elasticsearch
-------------
es在elk生态圈中处于核心地位，是开源大规模基于倒排索引的全文搜索分析引擎，他几乎能实时的支持存储搜索分析。   
**优势:**  
* 横向可扩展性: 增加服务器可直接配置在集群中
* 分片机制提供更好的分布性: 分而治之的方式来提升处理效率
* 高可用: 提供复制（replica）机制
* 实时性: 通过将磁盘上的文件放入文件缓存系统来提高查询速度

### 基本概念
* Index: 一系列文档的集合，类似于mysql中数据库的概念
* Type: 在Index里面可以定义不同的type，type的概念类似于mysql中表的概念，是一系列具有相同特征数据的结合。
* Document: 文档的概念类似于mysql中的一条存储记录，并且为json格式，在Index下的不同type下，可以有许多document。
* Shards: 在数据量很大的时候，进行水平的扩展，提高搜索性能
* Replicas: 防止某个分片的数据丢失，可以并行得在备份数据里及搜索提高性能

### elasticsearch查询语法

#### _cat API
查询当前es集群的相关消息，包括集群中的index数量、运行状态、当前集群所在的ip，目的在于将查询的结果以更加友好的方式输出。
* cat: 输出`_cat api`中所有支持的查询命令
* cat health: 检查es集群运行的状况
* cat count: 可以快速的查询集群或者index中文档的数量
* cat indices: 查询当前集群中所有index的数据，包括index的分片数、document的数量、存储所用的空间大小...
* 其他cat api参考官方文档: <https://www.elastic.co/guide/en/elasticsearch/reference/5.5/cat.html>

#### Search APIs
搜索数据，查询语法多，功能强大  
**REST request URI**: 轻便快速的URI查询方法   
**REST request body**: 可以有许多限制条件的json格式查询方法   

* "query": 在请求消息体中的`query`允许我们用`Query DSL`的方式查询。
  * "term": 查询时判断某个document是否包含某个具体的值，不会对被查询的值进行分词查询
  * "match" 将被查询值进行分词，然后用评分机制(TF/IDF)进行打分
  * "match_phrase": 查询指定段落
  * "Bool": 结合其他真值查询，通常和`must should mustnot`(与或非)一起组合出复杂的查询
  * "range": 查询时指定某个字段在某个特定的范围
    ``` 
    "range": {
          "FIELD": {# 指定具体过滤的字段
            "gte": 1,# gte: >=, gt: >
            "lte": 10
          }
        }
    ```
* "from": 以一定的偏移量来查看我们检索的结果，缺省从检索的第一条数据开始显示
* "size": 指定检索结果中输出的数据条数，缺省为10条
* "sort": 允许我们将检索的结果以指定的字段进行排序显示
* "_source": 指定检索结果输出的字段
* "script_fields": 该类型允许我们通过一个脚本来计算document中不存在的值，比如我们需要计算install/click得到cti之类的
``` 
"script_fields": {
    "FIELD": {# 指定脚本计算之后值得名称
      "script": {# 脚本内的运算
      }
    }
  }
```
* "aggs": 基于搜索查询，可以嵌套聚合来组合复杂的需求
```
"aggs": {
    "NAME": {# 指定结果的名称
      "AGG_TYPE": {# 指定具体的聚合方法，
        TODO: # 聚合体内制定具体的聚合字段
      }
    }
    TODO: # 该处可以嵌套聚合
  }
```
    

#### Query DSL
Query DSL是es提供的一套完整的基于json格式的结构化查询方法，包含两类不同的查询语义：  
* Leaf query clauses: 叶子查询句法就是在指定的字段中搜索指定的值，有`match, term or range`.    
* Compound query clauses: 复合查询句法会包含叶子句法或者复合句法，作用是为了多重查询，有`bool or dis_max`.

##### Query and filter context
查询语句的行为取决于它是使用查询型上下文还是过滤型上下文

* Query context: 在这种上下文环境中，查询语句的返回的结果是”结果和查询语句的匹配程序如何“，返回的结果数据中都会带上`_score`值，象征匹配程度；

* Filter context: 过滤型上下文环境中，查询语句则表面匹配与否（yes or no）。es内置式为`filter context`保留缓存用来提高查询性能，因此`filter context`
    查询的速度要快于`query context`

### elasticsearch查询示例

#### _cat api查询示例
**_cat查询当前es集群运行的状况**    
    
    Kibana’s Console: `GET /_cat/health?v`
    curl: `curl -XGET "127.0.0.1:9200/_cat/health?v"`

**_cat查询当前es集群中所有的indices**

    Kibana’s Console: `GET /_cat/indices?v`
    curl: `curl -XGET "127.0.0.1:9200/_cat/indices?v"`

#### _search api查询示例
**创建index**
```
PUT /customer?pretty
```
output:  
``` 
{
  "acknowledged": true,
  "shards_acknowledged": true
}
```
**插入数据**  
日常任务中，有时候往es插入数据的时候会出现504网关超时，这时候就需要手动的插入少量数据
```
PUT /rta_daily_report/campaign/164983850_rba_20170808?pretty
{
  "doc": {
    "cid": 164983850,
    "advertiser_id": 799,
    "trace_app_id": "com.zeptolab.cats.google",
    "network_cid": "6656665",
    "platform": 1,
    "direct": 2,
    "last_second_domain": "",
    "jump_type": 2,
    "direct_trace_app_id": "",
    "mode": 0,
    "third": "kuaptrk.com",
    "hops": 9,
    "yyyymmdd": "2017-08-07T16:00:00",
    "type": "rba",
    "click": 2
  }
}
```
output:  
```
{
  "_index": "rta_daily_report",
  "_type": "campaign",
  "_id": "164983851_rba_20170808",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
**删除数据**  
指定document_id删除:
```
DELETE /rta_daily_report/campaign/164983850_rba_20170808?pretty
```
query中满足一定条件删除  
``` 
POST rta_daily_report/_delete_by_query
{
  "query": { 
    "match": {
      "message": "some message"
    }
  }
}
```
    
**根据具体document_id查询**     
```
GET rta_daily_report/campaign/145603275_m_normal_20170804?pretty
```
output:  
``` 
{
  "_index": "rta_daily_report",
  "_type": "campaign",
  "_id": "145603275_m_normal_20170804",
  "_version": 1,
  "found": true,
  "_source": {
    "cid": 145603275,
    "advertiser_id": 457,
    "trace_app_id": "id1105855019",
    "network_cid": "plr_gs_ios_cn_osv9",
    "platform": 2,
    "direct": 1,
    "last_second_domain": "tracking.lenzmx.com",
    "jump_type": 7,
    "direct_trace_app_id": "id1105855019",
    "mode": 3,
    "third": "3444.tlnk.io",
    "hops": 1,
    "yyyymmdd": "2017-08-03T16:00:00",
    "type": "m_normal",
    "click": 2,
    "impression": 3,
    "revenue": 0,
    "install": 0
  }
}
```
**查询所有数据**  
URI:
```
GET rta_daily_report/campaign/_search?q=*&pretty
```
request boy:
``` 
GET rta_daily_report/campaign/_search
{
  "query": {
    "match_all": {}
  }  
}
```
output:  
``` 
"hits": {
    "total": 2705059,
    "max_score": 1,
    "hits": [
      {
        "_index": "rta_daily_report",
        "_type": "campaign",
        "_id": "163016610_rba_20170801",
        "_score": 1,
        "_source": {
          "cid": 163016610,
          "advertiser_id": 799,
          "trace_app_id": "mappstreet.videoeditor",
          "network_cid": "6287283",
          "platform": 1,
          "direct": 2,
          "last_second_domain": "",
          "jump_type": 2,
          "direct_trace_app_id": "",
          "mode": 0,
          "third": "aff.adsbreak.com",
          "hops": 8,
          "yyyymmdd": "2017-07-31T16:00:00",
          "type": "rba",
          "click": 0
        }
      },
      ....]
      }
```
**查询特定字段，并且指定排序字段**   
在indices为rta_daily_report中搜索type:rba,以日期升序输出1个查询结果  
URI:  
``` 
 GET rta_daily_report/_search?q=type:rba&sort=yyyymmdd:asc&pretty
```
request bofy:
```
GET rta_daily_report/_search
{
  "query": {
    "match": {
      "type": "rba"
    }
  },
  "sort": [
    {
      "yyyymmdd": {
        "order": "desc"
      }
    }
  ]
}
```

**指定输出字段**  
查询类型为rba/b2t，按照日期降序排列，输出制定字段，并且只输出5条查询结果，如果要匹配段落，则用`"match_phrase": { "address": "mill lane" }`
```
GET rta_daily_report/_search
{
  "query": {
    "match": {
      "type": "rba b2t"
    }
  },
  "sort": [
    {
      "yyyymmdd": {
        "order": "desc"
      }
    }
  ],
  "_source": ["yyyymmdd", "type", "cid", "click", "revenue"],
  "size": 5
} 
```
output:  
``` 
"hits": {
    "total": 1327184,
    "max_score": null,
    "hits": [
      {
        "_index": "rta_daily_report",
        "_type": "campaign",
        "_id": "54870921_b2t_20170804",
        "_score": null,
        "_source": {
          "revenue": 76500,
          "yyyymmdd": "2017-08-03T16:00:00",
          "type": "b2t",
          "click": 22616,
          "cid": 54870921
        },
        "sort": [
          1501776000000
        ]
      },
```
**bool组合复杂查询**   
下例是查询类型为b2t，收入必须大于0的所有单子的click、revenue相关数据
``` 
GET rta_daily_report/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "type": "b2t"
        }}
     ],
     "must_not": [
       {
         "range": {
           "revenue": {
             "lte": 0
           }
         }
       }
     ]
    }
  },
  "sort": [
    {
      "yyyymmdd": {
        "order": "desc"
      }
    }
  ],
  "_source": ["yyyymmdd", "type", "cid", "click", "revenue"],
  "size": 10       
}
```
**聚合查询**  
下例是类似于sql中的聚合查询，查询每天不同类型对应的intall总量
``` 
GET /rta_daily_report/_search
{
  "size": 0,
  "aggs": {
    "sum_install": {
      "date_histogram": {
        "field": "yyyymmdd",
        "interval": "day"
      },
      "aggs": {
        "types": {
          "terms": {
            "field": "type.keyword",
            "size": 10
          },
          "aggs": {
            "install": {
              "sum": {
                "field": "install"
              }
            }
          }
        }
      }
    }
  }
}
```
output
``` 
"aggregations": {
    "sum_install": {
      "buckets": [
        {
          "key_as_string": "2017-07-31T00:00:00.000Z",
          "key": 1501459200000,
          "doc_count": 659553,
          "types": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
              {
                "key": "rba",
                "doc_count": 321811,
                "install": {
                  "value": 73835
                }
              },
              {
                "key": "m_normal",
                "doc_count": 321711,
                "install": {
                  "value": 18964
                }
              },
```

**script查询**  
下例通过document中的click,install字段，计算出文档中不存在的数据。
``` 
GET /rta_daily_report/campaign/_search?pretty
{
    "query" : {
      "bool": {
        "must": [
          {
            "range": {
              "click": {
                "gt": 0
              }
            }
          },
          {
            "range": {
              "install": {
                "gt": 0
              }
            }
          }
        ]
    }},
    "size": 100, 
    "script_fields": {
      "cti": {
        "script": {
          "lang": "painless",
          "inline": "1.0 * doc['install'].value / doc['click'].value"
        }
      }
    }
}
```
output  
``` 
"hits": {
    "total": 23036,
    "max_score": 2,
    "hits": [
      {
        "_index": "rta_daily_report",
        "_type": "campaign",
        "_id": "160647918_rta_20170801",
        "_score": 2,
        "fields": {
          "cti": [
            0.0005970149253731343
          ]
        }
      },
      {
        "_index": "rta_daily_report",
        "_type": "campaign",
        "_id": "162293741_rta_20170801",
        "_score": 2,
        "fields": {
          "cti": [
            0.00007796055196070789
          ]
        }
      },
```
**查询一段时间内的聚合数据**
``` 
GET rta_daily_report/campaign/_search
{
  "size": 0,
  "aggs": {
    "snaptime": {
      "date_range": {
        "field": "@timestamp",
        "ranges": [
          {
            "from": "now-30d/d",
            "to": "now"
          }
        ]
      },
      "aggs": {
        "sum_revenue": {
          "sum": {
            "field": "revenue"
          }
        }
      }
    }
  }
}

output:

"aggregations": {
    "snaptime": {
      "buckets": [
        {
          "key": "2017-07-17T00:00:00.000Z-2017-08-16T03:30:16.995Z",
          "from": 1500249600000,
          "from_as_string": "2017-07-17T00:00:00.000Z",
          "to": 1502854216995,
          "to_as_string": "2017-08-16T03:30:16.995Z",
          "doc_count": 18685619,
          "sum_revenue": {
            "value": 6631665219
          }
        }
      ]
    }
  }
```
**查询某段时间内聚合数据，并且script计算额外字段**
``` 
GET rta_daily_report/campaign/_search
{

  "size": 0,
  "aggs" : {
    "cvr_per_month" : {
      "date_range" : {
        "field": "@timestamp",
        "ranges": [
          {
            "from": "now-30d/d",
            "to": "now"
          }
        ]
      },
      "aggs": {
        "sum_click": {
          "sum": {
            "field": "click"
          }
        },
        "sum_install": {
          "sum": {
            "field": "install"
          }
        },
        "cvr": {
          "bucket_script": {
            "buckets_path": {
              "install": "sum_install",
              "click": "sum_click"
            },
           "script": "1.0 * params.install / params.click"
          }
        }
      }
    }
  }
}

output: 
"aggregations": {
    "cvr_per_month": {
      "buckets": [
        {
          "key": "2017-07-17T00:00:00.000Z-2017-08-16T03:37:22.732Z",
          "from": 1500249600000,
          "from_as_string": "2017-07-17T00:00:00.000Z",
          "to": 1502854642732,
          "to_as_string": "2017-08-16T03:37:22.732Z",
          "doc_count": 18685619,
          "sum_click": {
            "value": 15067388421
          },
          "sum_install": {
            "value": 7602055
          },
          "cvr": {
            "value": 0.0005045370032012133
          }
        }
      ]
    }
  }
```

kibana
------

logstash
--------
TODO:

常见问题
--------
