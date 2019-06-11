- [第2章 索引](#%E7%AC%AC2%E7%AB%A0-%E7%B4%A2%E5%BC%95)
  - [2.1 Elasticsearch索引](#21-elasticsearch%E7%B4%A2%E5%BC%95)
  - [2.2 映射配置-mapping](#22-%E6%98%A0%E5%B0%84%E9%85%8D%E7%BD%AE-mapping)
    - [2.2.2 索引结构映射](#222-%E7%B4%A2%E5%BC%95%E7%BB%93%E6%9E%84%E6%98%A0%E5%B0%84)
      - [3. 核心类型](#3-%E6%A0%B8%E5%BF%83%E7%B1%BB%E5%9E%8B)
      - [7. 使用分析器](#7-%E4%BD%BF%E7%94%A8%E5%88%86%E6%9E%90%E5%99%A8)
  - [2.5 段合并介绍](#25-%E6%AE%B5%E5%90%88%E5%B9%B6%E4%BB%8B%E7%BB%8D)
    - [2.5.3 合并策略](#253-%E5%90%88%E5%B9%B6%E7%AD%96%E7%95%A5)
    - [2.5.4 合并调度器](#254-%E5%90%88%E5%B9%B6%E8%B0%83%E5%BA%A6%E5%99%A8)
    - [2.5.5 合并因子](#255-%E5%90%88%E5%B9%B6%E5%9B%A0%E5%AD%90)
  - [2.6 路由介绍](#26-%E8%B7%AF%E7%94%B1%E4%BB%8B%E7%BB%8D)
- [第3章 搜索](#%E7%AC%AC3%E7%AB%A0-%E6%90%9C%E7%B4%A2)
  - [3.2 理解查询过程](#32-%E7%90%86%E8%A7%A3%E6%9F%A5%E8%AF%A2%E8%BF%87%E7%A8%8B)
    - [3.2.3 搜索类型](#323-%E6%90%9C%E7%B4%A2%E7%B1%BB%E5%9E%8B)


# 第2章 索引

## 2.1 Elasticsearch索引

## 2.2 映射配置-mapping

### 2.2.2 索引结构映射
模式映射（schema mapping，或简称映射）用于定义索引结构。

#### 3. 核心类型
现在来讨论Elasticsearch中可用的每个核心类型，以及它们用来定义行为的属性。
(1) 公共属性
在继续描述所有核心类型之前，先讨论一些可用来描述所有类型（二进制除外）的公共属性。
- index_name：该属性定义将存储在索引中的字段名称。若未定义，字段将以对象的名字
来命名。
- index：可设置值为analyzed和no。另外，对基于字符串的字段，也可以设置为
not_analyzed。如果设置为analyzed，该字段将被编入索引以供搜索。如果设置为no，
将无法搜索该字段。默认值为analyzed。在基于字符串的字段中，还有一个额外的选项
not_analyzed。此设置意味着字段将不经分析而编入索引，使用原始值被编入索引，在
搜索的过程中必须全部匹配。索引属性设置为no将使include_in_all属性失效。
- store：这个属性的值可以是yes或no，指定了该字段的原始值是否被写入索引中。默认
值设置为no，这意味着在结果中不能返回该字段（然而，如果你使用_source字段，即
使没有存储也可返回这个值），但是如果该值编入索引，仍可以基于它来搜索数据。
- boost：该属性的默认值是1。基本上，它定义了在文档中该字段的重要性。boost的值
越高，字段中值的重要性也越高。
- null_value：如果该字段并非索引文档的一部分，此属性指定应写入索引的值。默认的
行为是忽略该字段。
- copy_to：此属性指定一个字段，字段的所有值都将复制到该指定字段。
- include_in_all：此属性指定该字段是否应包括在_all字段中。默认情况下，如果使
用_all字段，所有字段都会包括在其中。2.4节将更详细地介绍_all字段。

(2) 字符串
字符串是最基本的文本类型，我们能够用它存储一个或多个字符。字符串字段的示例定义如
下所示：
"contents" : { "type" : "string", "store" : "no", "index" : 
 "analyzed" } 
除了公共属性，基于字符串的字段还可以使用以下属性。
- term_vector ：此属性的值可以设置为 no （默认值）、 yes 、 with_offsets 、
with_positions和with_positions_offsets。它定义是否要计算该字段的Lucene词
向量（term vector）。如果你使用高亮，那就需要计算这个词向量。
图灵社区会员 打顺顺(lvshun@live.cn) 专享 尊重版权
- omit_norms：该属性可以设置为true或false。对于经过分析的字符串字段，默认值为
false，而对于未经分析但已编入索引的字符串字段，默认值设置为true。当属性为true
时，它会禁用Lucene对该字段的加权基准计算（norms calculation），这样就无法使用索引
期间的加权，从而可以为只用于过滤器中的字段节省内存（在计算所述文件的得分时不
会被考虑在内）。
- analyzer：该属性定义用于索引和搜索的分析器名称。它默认为全局定义的分析器名称。
- index_analyzer：该属性定义了用于建立索引的分析器名称。
- search_analyzer：该属性定义了的分析器，用于处理发送到特定字段的那部分查询字
符串。
- norms.enabled：此属性指定是否为字段加载加权基准（norms）。默认情况下，为已分
析字段设置为true（这意味着字段可加载加权基准），而未经分析字段则设置为false。
- norms.loading：该属性可设置eager和lazy。第一个属性值表示此字段总是载入加权
基准。第二个属性值是指只在需要时才载入。
- position_offset_gap：此属性的默认值为0，它指定索引中在不同实例中具有相同名
称的字段的差距。若想让基于位置的查询（如短语查询）只与一个字段实例相匹配，可
将该属性值设为较高值。
- index_options：该属性定义了信息列表（postings list）的索引选项（2.2.4节将详细讨
论）。可能的值是docs（仅对文档编号建立索引），freqs（对文档编号和词频建立索引），
positions（对文档编号、词频和它们的位置建立索引），offsets（对文档编号、词频、
它们的位置和偏移量建立索引）。对于经分析的字段，此属性的默认值是positions，对
于未经分析的字段，默认值为docs。
- ignore_above：该属性定义字段中字符的最大值。当字段的长度高于指定值时，分析器
会将其忽略。


#### 7. 使用分析器
(1) 开箱即用的分析器
Elasticsearch允许我们使用众多默认定义的分析器中的一种。如下分析器可以开箱即用。
- standard：方便大多数欧洲语言的标准分析器（关于参数的完整列表，请参阅http://www. 
lasticsearch.org/guide/en/elasticsearch/reference/current/analysis-standard-analyzer.html）。
- simple：这个分析器基于非字母字符来分离所提供的值，并将其转换为小写形式。
- whitespace：这个分析器基于空格字符来分离所提供的值。
- stop：这个分析器类似于simple分析器，但除了simple分析器的功能，它还能基于所
提供的停用词（stop word）过滤数据（参数的完整列表，请参阅http://www.elasticsearch. 
rg/guide/en/elasticsearch/reference/current/analysis-stop-analyzer.html）。
- keyword：这是一个非常简单的分析器，只传入提供的值。你可以通过指定字段为
not_analyzed来达到相同的目的。
- pattern：这个分析器通过使用正则表达式灵活地分离文本（参数的完整列表，请参阅
http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/analysis-pattern-analyzer.
html）。
- language：这个分析器旨在特定的语言环境下工作。该分析器所支持语言的完整列表可参
考http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/analysis-lang-analyzer. tml。

(2) 自定义分析器


## 2.5 段合并介绍
### 2.5.3 合并策略
合并策略描述了应执行合并过程的时机。Elasticsearch允许配置以下三种不同的策略。
- tiered：这是**默认合并策略**，合并尺寸大致相似的段，并考虑到每个层（tier）允许的最大段数量；
- log_byte_size：这个合并策略下，随着时间推移，将产生由索引大小的对数构成的索引，其中存在着一些较大的段以及一些合并因子较小的段等；
- log_doc：这个策略类似于log_byte_size合并策略，但根据索引中的文档数而非的实际字节数来操作。
### 2.5.4 合并调度器
合并调度器指示Elasticsearch合并过程的方式，有如下两种可能。
- 并发合并调度器：这是**默认的合并过程**，在独立的线程中执行，定义好的线程数量可以
并行合并。
- 串行合并调度器：这一合并过程在调用线程（即执行索引的线程）中执行。合并进程会
一直阻塞线程直到合并完成。

### 2.5.5 合并因子
每种合并策略都有好几种设置，我们已经说过在这里不一一介绍，但是合并因子是个例外，
它指定了索引过程中段合并的频率。
**合并因子较小时**，搜索的速度更快，占用的内存也更少，但索引的速度会减慢；
**合并因子较大时**，则索引速度加快，这是因为发生的合并较少，但搜索的速度变慢，占用的内存也会变大。
对于log_byte_size和 log_doc合并策略，可以通过index.merge.policy.merge_factor参数来设置合并因子。
index.merge.policy.merge_factor: 10 
上述例子将合并因子的值设置成10，10也是默认值。建议在批量索引时设置更高的merge_ factor属性值，普通的索引维护则设置较低的属性值。

## 2.6 路由介绍

# 第3章 搜索

## 3.2 理解查询过程

### 3.2.3 搜索类型
为了控制查询如何执行，可以使用search_type请求参数，并将其设置为下列值之一。

- query_then_fetch：第一步，执行查询得到对文档进行排序和分级所需信息。这一步
在所有的分片上执行。然后，只在相关分片上查询文档的实际内容。不同于query_and_ 
fetch，此查询类型返回结果的最大数量等于size参数的值。如果没有指定搜索类型，
就默认使用这个类型，前面描述过。
- query_and_fetch：这通常是最快也最简单的搜索类型实现。查询在所有分片上并行执
行（当然，任意一个主分片，只查询一个副本），所有分片返回等于size值的结果数。返
回文档的最大数量等于size的值乘以分片的数量。
- dfs_query_and_fetch：这个跟query_and_fetch类似，但相比query_and_fetch，
它包含一个额外阶段，在初始查询中执行分布式词频的计算，以得到返回文件的更精确
的得分，从而让查询结果更相关。
- dfs_query_then_fetch：与前一个dfs_query_and_ fetch一样，dfs_query_then_fetch
类似于相应的query_then_fetch，但比query_ then_fetch多了一个额外的阶段，就像
dfs_query_and_fetch一样。
- count：这是一个特殊的搜索，只返回匹配查询的文档数。如果你只需要结果数量，而不
关心文档，应该使用这个搜索类型。
- scan：这是另一个特殊的搜索类型，只有在要让查询返回大量结果时才用。它跟一般的
查询有点不同，因为在发送第一个请求之后，Elasticsearch响应一个滚动标识符，类似于
关系型数据库中的游标。所有查询需要在_search/scroll REST端点运行，并需要在请
求主体中发送返回的滚动标识符。

P78