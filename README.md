## elasticsearch官网最新版持续更新中
## [文档地址](https://github.com/13428282016/elasticsearch-CN/wiki/es-gettting-started)
## 阅读文档前先了解一下基本概念

### Glossary of terms

#### term
term是一个被索引的精确的值。terms：foo, Foo, FOO 是不等价的。Terms可以使用term query查询。例如："我爱中国"被分词为,我/爱/中国 ,那么有3个term，分别是：我，爱，中国 。

#### analysis

把字符串转换为terms的过程。基于它使用可那些分词器，短语：FOO BAR, Foo-Bar, foo,bar有可能分词为terms：foo和bar。这些terms会被存储在索引中。全文索引查询（不是term query）“FoO:bAR”，同样也会被分词为terms：foo,bar，并且也匹配存在索引中的terms。分词过程（包括索引或搜索过程）使es可以执行全文查询。


#### cluster

集群由一个或多个节点组成，它们都使用同一个集群名。每个集群都有单个master节点，他是自动被集群选举的，如果当前master节点发生故障，可以用候选master节点代替它。

#### document

文档是一个json文档，它存储在es中。它就好像是关系数据库中table中的一行。每个文档都保存在索引中，并且属于一个type和有一个id字段。文档是一个json对象（在其他语言中是hash/hashmap/管关联数组）它包含0个或多个字段，或key-value对。被索引的原始的json文档会被保存在_source字段，当执行getting或searching操作时会默认返回该字段。


#### id

识别文档的id。文档的index/type/id必须是唯一的，如果没有提供id，将会自动生成id

#### field

文档包含一系列的字段，或者key-value 对。每个值都有可以是单个值（string，intger，date）或者是一个像数组的nested 结构或者对象。字段和关系数据库中的列类似。不要和文档type混淆。


#### index

索引就像关系数据库中的一个表，它包含一个mapping，mapping定义索引的字段，它由多个type组成。索引是一个逻辑命名空间，它映射到一个或多个主分片和0个或多个复制分片。



#### mapping


mapping就像关系数据库中的schema。每个索引都有一个mapping，它定义了index中的type，和一系列索引level的选项。mapping可以显示定义或者在索引文档时自动生成。


#### node

节点是一个运行中的es实例，它属于一个集群。可以在一个服务器启动多个节点用于测试，但通常都是一个服务器一个节点。在启动的时候节点会使用单播查找一个具有相同集群名并且已存在的集群，并尝试加入它。


#### primary shard

每个文档都保存在单个主分片中。当你索引一个文档，他首先会在主分片索引，然后告诉所有该主分片的复制分片。默认，索引有5个主分片，你可以根据你的文档数量指定更小或更多的主分片。当索引创建之后，你不能改变主分片的数量。

#### replica shard

每个主分片都有一个或多个复制分片。复制分片是主分片的副本，用于以下两个目的：

1. 提升容错能力，复制分片提升为主分片，如果主分片不可用。 
2. 提升性能，get和search请求可以被主分片或者复制分片处理。默认，每个主分片都有一个复制分片，可以动态修改复制分片的数量。永远不要在相同的节点同时存放主分片和复制分片。


#### routing

当你索引一个文档，它被保存在单个主分片，并基于对routing的值进行hash计算映射到对应的分片。默认被hash的值来自文档id或者文档的父文档id（确保父文档和子文档存储在同一个分片）。这个值可以在索引期间指定routing参数或在mapping中使用routing字段修改。


#### shard

分片是单个lucene索引实例。ta是一个底层的“woker”单位，它由es自动管理。一个索引是一个逻辑的命名空间，它指向主分片和复制分片。而不是定义主分片和复制分片的数量，你从来都不需要直接引用主分片。相反，你的代码仅仅需要引用索引。es会在集群的所有节点中分发分片，并可以自动在节点之间移动分片，当节点出现故障时或者添加额外的节点。

#### source field

默认，你索引的JSON文档会被存储在_source字段，并会在所有的get和search请求中返回。这允许你直接从搜索结果中直接访问原始的对象，而不需要先查询id然后再获取文档。




#### text

text是普通非结构化的文本，例如段落。默认，text会被分词成terms，索引实际上储存的是terms。text 字段需要在索引期间进行分词，这样才可以进行全文搜索，查询字符串在搜索期间也需要被分词。
fuzhi
#### type

type呈现文档的类型，例如 email，user，或weet。search API 可以根据type过滤文档。索引可以包含多个type，每个type包含一系列的字段。同一个索引不同type字段名相同的字段必须具有相同的mapping。



#### docvalues

它保存某一列的数据，并索引它，用于加快聚合和排序的速度。

#### fileddata

它保存某一列的数据，并索引它，用于加快聚合和排序的速度。和docvalues不一样的是，fielddata保存的是text类型的字段分词后的terms，而不是保存源字段数据。



#### shardcopies

 分片副本集合，包含主分片和复制分片
 
 
#### segment

每个分片都分为多个segment存储。小的segment和定期合并成大的segment

#### master

master 节点用于执行轻量的集群操作，例如：创建删除索引，跟踪节点，决定分片分发到哪个节点等。每个集群同时只有一个master节点和0个或多个master候选节点。
