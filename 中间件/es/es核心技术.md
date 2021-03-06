### 正排索引和倒排索引

正排索引

> 文档id到文档内容和单词的关联

倒排索引

> 单词到文档id的关联

- 单词词典

  记录所有文档的单词，记录单词到倒排列表的关联关系

- 倒排列表

​      记录单词对应的文档结果，由倒排索引项组成

- 倒排索引项

  文档id

  词频TF 单词在文档中出现的次数，用户相关性评分

  位置 单词在文档中分词的位置，用户语句搜索

  偏移 记录单词开始结束位置，实现高亮显示

###es基本原理

#### 分片

分片是底层基本的读写单元，分片的目的是分割巨大的索引，让读写可以并行进行，读写请求最终落在某个分片上。

es索引包含很多的分片，一个分片是一个Lucene的索引，它本身就是一个完整的索引引擎，可以独立的执行建立索引

和搜索任务。lucene索引又由很多分段组成，每个分段是一个倒排索引。

es将数据分为主从两部分，即主分片和副分片，写过程中先写主分片，成功后再写副分片。

#### 动态更新索引

新增的文档将会写到一个新的倒排索引中，查询时每个倒排索引都会被轮流查询，查询完对结果进行合并。

由于分段的不变性，更新，删除等操作实际上是将数据标记为删除，记录到单独的位置，因此删除数据不会立刻释放磁盘空间。

#### 近实时搜索

在写操作中，一般会先在内存中缓冲一段数据，再将这些数据写入硬盘中，每次写入硬盘的这批数据称为一个分段。

#### 段合并

在es中，每秒清空一次写缓冲，将这些数据写入文件，这个过程称为refresh。每次refresh会产生一个新的lucene分段。同时每个分段都会消耗文件句柄，内存。每个搜索请求都轮流检查每个分段，查询完成再对结果进行合并。分段越多，搜索消耗的时间也越多，所以需要通过一定的策略将较小的分段合并成大的分段。在合并的过程中，标记为删除的数据不会被写入新的分段，当合并结束，旧的分段数据被删除，标记删除的数据才从磁盘删除。

### 文档CRUD

- 支持自动生成文档id和手动生成文档id
- _create如果文档已经存在，会操作失败
- index和create不同：如果文档不存在就索引新的文档，否则现有文档会被删除，新的文档被索引，版本信息加1
- update文档必须存在，更新不会删除原有的文档，而是真正的数据更新

| index  | PUT  my_index/_doc/1                   |
| :----- | -------------------------------------- |
| create | PUT  my_index/_create/1                |
|        | POST my_index/_doc/(不指定Id,自动生成) |
| read   | GET my_index/_doc/1                    |
| update | POST my_index/update/1                 |
| delete | DELETE  my_index/_doc/1                |

###分词器

| 分词器名称          | 描述                            |
| ------------------- | ------------------------------- |
| Standard Analyzer   | 默认分词器，按词切分，小写处理  |
| Simple Analyzer     | 按照非字母切分，小写处理        |
| Stop Analyzer       | 小写处理，停用词过滤(the a is)  |
| Whitespace Analyzer | 按照空格切分，不转小写          |
| Keyord Analyzer     | 不分词，直接将输入当输出        |
| Patter Analyzer     | 正则表达式，默认\W+(非字符分割) |

### search API

| 语法                   | 范围               |
| ---------------------- | ------------------ |
| /_search               | 集群上所有的索引   |
| /index/_search         | index              |
| /index1,index2/_search | Index1,index2      |
| /index*/_search        | 以lindex开头的索引 |

#### URI Search

#### term和phrase

- term 根据文档中的字段关键词查询

  ```xquery
  GET /movies/_search?q=title:(Beautiful Mind)
  {
  	"profile":"true"
  }
  ```

  等价于

  ```xquery
  GET /movies/_search?q=title:(Beautiful OR Mind)
  {
  	"profile":"true"
  }
  ```


  布尔操作支持 

- `AND`

- `OR`

- `NOT`

分组操作支持

- `+`表示must

- `-`表示must not

- filedname:(+A -B)

范围查询

- 区间表示：[]表示闭区间，{}表示开区间

  ```
  year:{2019 TO 2016}
  ```

  ```
  year:[* TO 2019]
  ```

- 算数符号

  ```
  year:>2019
  ```

  ```
  year:(>2010 && <=2019)
  ```

- 模糊匹配和近似查询

  ```
  title:beautiful~1
  ```

  ```
  title:"loads rings"~2
  ```

- phrase 根据文档中的字段关键词查询，同时关键词出现的顺序需要一致

  ```xquery
  GET /movies/_search?q=title:"Beautiful Mind"
  {
  	"profile":"true"
  }
  ```

  

### DSL

 - term查询（精确查询，搜索前不会对搜索词进行分词）

   ```xquery
   POST movies/_search
   {
     "query": {
       "term": {
           "title": "one love"
       }
     }
   }
   ```

 - match查询（模糊查询，搜索前对搜索词进行分词，搜索词的分词再文档中存在即可）

   ```xquery
   POST movies/_search
   {
     "query": {
       "match": {
         "title": "last christmas"
       }
     }
   }
   ```

 - match_phrase(短语查询，搜索前对搜索词进行分词，并要求所有的分词都在文档中出现，同时还需要满足分词在文档中出现的顺序和搜索词中出现的一致且各搜索词必须紧邻，可以通过`slop`参数设置间隔多少个词仍算作匹配成功)

   ```xquery
   POST movies/_search
   {
     "query": {
       "match_phrase": {
         "title":{
           "query": "one love",
           "slop": 1
         }
       }
     }
   }
   ```

- query_string

  ```xquery
  GET /movies/_search
  {
  	"profile": true,
  	"query":{
  		"query_string":{
  			"fields":[
  				"title",
  				"year"
  			],
  			"query": "2012"
  		}
  	}
  }
  ```

- simple_query_string

  ```xquery
  #simple_query_string 默认的operator是 OR
  POST users/_search
  {
    "query": {
      "simple_query_string": {
        "query": "Ruan Yiming",
        "fields": ["name"],
        "default_operator": "AND"
      }
    }
  }
  ```

  



