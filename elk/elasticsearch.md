# elk基本介绍与使用入门
* elk生态圈的介绍
* elasticsearch基础概念与使用
* elasticsearch中的查询方法
* kibana的介绍与使用
* logstash
* filebeat

## elk生态圈
    todo，粗略介绍elk生态圈的运行流程，filebeat->logstash->elasticsearch->kibana
    
## elasticsearch基本概念
todo：简单介绍原理，应用范围

### Index 
    Index 是一系列文档的集合，类似于mysql中数据表的概念，在es中Index对的名字必须是小写的

### Type
    在Index里面可以定义不同的type，type的概念类似于目录或者分区，作用是一系列拥有相同字段域的集合，便于特定的查找
 
## Document
    文档的概念类似于mysql中的一条存储记录，并且为json格式，在Index下的不同type下，可以有许多document。
    
## Shards & Replicas
    对于Index里数据的分片和备份。
    
    分片：是在数据量很大的时候，进行水平的扩展，提高搜索性能
    备份：防止某个分片的数据丢失，可以并行得在备份数据里及搜索提高性能

## 问题？
* 如何定时向es中批量导入数据(python)    
* 当出现少量504插入数据失败时，如何向es中插入少量数据    

## 实例
    todo: 在windows下用python想es导入数据
          在kibana，或者curl方式导入单条数据


## 检索数据

* REST request URI: 轻便快速的URI查询方法
* REST request body: 功能强大，可以有许多限制条件的json格式查询方法



* match: 会进行分词，然后查询，最后在利用评分机制(TF/IDF)来进行评分
* term: 代表完全匹配，即不进行分词器分析，文档中必须包含整个搜索的词汇

**REST request URI**
``` 
GET rta_daily_report/_search?q=type:rba&size=1&sort=yyyymmdd:asc&pretty
```

**REST request body**



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
*bool query*: 可以组合`must,should,must_not,filter`进行更加复杂的逻辑查询,

    must: 与

    should: 或
  
    must not: 非
下例是查询类型为b2t，时间为2017-08-03，所有单子的click、revenue相关数据
``` 
GET rta_daily_report/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "type": "b2t"
          
        }},
        {"match": {
          "yyyymmdd": "2017-08-03"
        }}
        
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

下例是类似于sql中的聚合查询
``` 

```


    
*Search Type*: 相关性算法的查询类型，因为es是一个分布式搜索引擎，因此针对某一次查询，需要给出查询结果相似度的度量


## Query DSL (阐释search api中不同的json字段大致的含义)

*Leaf query clauses*
    
    叶子查询句法就是在指定的字段中搜索制定的值，有`match, term or range`.    

*Compound query clauses*
    
    复合查询句法会包含叶子句法或者复合句法，作用是为了多重查询，有`bool or dis_max`.

### Query and filter context
查询语句的行为取决于它是使用查询上下文还是过滤上下文

*Query context*
    
    在这种上下文环境中，查询语句的返回的结果是”结果和查询语句的匹配程序如何“，返回的结果数据中都会带上`_score`值，象征匹配程度；

*Filter context*
    
    过滤型上下文环境中，查询语句则表面匹配与否（yes or no）。es内置式为`filter context`保留缓存用来提高查询性能，因此`filter context`
    查询的速度要快于`query ccontext`
    
**`_source`**
 
    类似于`sql`中`SELECT fields`，即指定查询结果输出具体的字段



* "_source": [field, field...] :指定输出字段，类似于sql select. eg: "_source": ["account_number", "balance"]

* "query"



## [cat APIS](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/cat.html)
    ?pretty: 以json格式输出返回结果            
            
## curl
    检查es运行状态：curl -XGET 127.0.0.1:9200/_cat/health?v

## **kibana** 
    `<REST Verb> /<Index>/<Type>/<ID>`
    dev tool
    * 根据具体document id单个查询 : GET index/type/id
    * 添加：PUT 
    * 修改：POST 
    
    针对具体id批量操作: _bulk

## kibana制图
    todo

## filebeat原理介绍 
 
## logstash数据格式化
 

## 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  