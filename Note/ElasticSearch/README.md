[TOC]
# 一、Elasticsearch与Solr对比

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517200320727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517200327698.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517200436998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517200451953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
**Elasticsearch 与 Solr 的比较总结**

- 二者安装都很简单；
- Solr 利用 Zookeeper 进行分布式管理，而 Elasticsearch 自身带有分布式协调管理功能;
- Solr 支持更多格式的数据，而 Elasticsearch 仅支持json文件格式；
- Solr 官方提供的功能更多，而 Elasticsearch 本身更注重于核心功能，高级功能多有第三方插件提供；
- Solr 在传统的搜索应用中表现好于 Elasticsearch，但在处理实时搜索应用时效率明显低于 Elasticsearch。
- Solr 是传统搜索应用的有力解决方案，但 Elasticsearch 更适用于新兴的实时搜索应用。

# 二、环境安装

## 2.1 Elasticsearch 7.12.1安装

下载地址：<https://www.elastic.co/cn/downloads/elasticsearch>

```xml
安装    
     1 解压
     2 进入bin目录
     3 启动 elasticsearch.bat

目录：
     bin 启动文件
     config 配置文件
         log4j2 日志配置文件
         jvm.options java 虚拟机相关的配置
         elasticsearch.yml elasticsearch 的配置文件！ 默认 9200 端口！ 跨域！
     lib 相关jar包
     logs 日志！
     modules 功能模块
     plugins 插件！
```

访问：http://127.0.0.1:9200/  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517200959978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

## 2.2 elasticsearch-head可视化界面安装

下载地址：<https://github.com/mobz/elasticsearch-head>

```xml
安装：
          1 elasticsearch-head-master 解压
          2 npm install   安装环境
          3.npm run start   启动
```

默认端口9100，想访问9200，出现跨域问题

解决：  
打开`elasticsearch-7.12.1\config\elasticsearch.yml`

```xml
http.cors.enabled: true   #允许跨域
http.cors.allow-origin: "*"   #允许所有人访问
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517201439203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

## 2.3 kibana 安装

下载地址：<https://www.elastic.co/cn/downloads/kibana>

```xml
 1、解压
 2、启动 kibana.bat
 3、访问测试 http://localhost:5601/
 4、开发工具！ （Post、curl、head、谷歌浏览器插件测试！）
 5、汉化,kibana-7.12.1-windows-x86_64\config\kibana.yml
   - i18n.locale: "zh-CN"
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517202658422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

# 三、ES核心概念

> 关系型数据库和elasticsearch客观的对比！一切都是json

| Relational DB      | Elasticsearch     |
| ------------------ | ----------------- |
| 数据库（database） | 索引（indices）   |
| 表（tables）       | 类型（types）     |
| 行（rows）         | 文档（documents） |
| 字段（columns）    | fields            |

`elasticsearch（集群）可以包含多个索引（数据库），每个索引中可以包含多个类型（表），每个类型下又包含多个文档（行），每个文档又包含多个字段（列）。`

**物理设计：**

elasticsearch在后台把每个索引划分成多个分片。每个分片可以在集群中的不同服务器间迁移，一个人就是一个集群，默认集群名就是elasticsearch

> 

**逻辑设计：**

一个索引类型中，包含多个文档，比如文档1，文档2，当我们索引一篇文档时，可以通过这样的一个顺序找到它：索引-》类型-》文档id，通过这个组合我们就能索引到某个具体的文档。`注意：ID不必是整数，实际上它是一个字符串`。

> 文档

就是我们的一条条的记录

之前说elasticsearch是面向文档的,那么就意味着索引和搜索数据的最小单位是文档, elasticsearch中,文档有几个重要属性:

- 自我包含, 一篇文档同时包含字段和对应的值,也就是同时包含key:value \!
- 可以是层次型的，一个文档中包含自文档,复杂的逻辑实体就是这么来的\! \{就是一 个json对象\! fastjson进行自动转换\!\}
- 灵活的结构,文档不依赖预先定义的模式,我们知道关系型数据库中,要提前定义字段才能使用,在elasticsearch中,对于字段是非常灵活的,有时候,我们可以忽略该字段,或者动态的添加一个新的字段。

尽管我们可以随意的新增或者忽略某个字段,但是,每个字段的类型非常重要,比如一个年龄字段类型,可以是字符串也可以是整形。因为elasticsearch会保存字段和类型之间的映射及其他的设置。这种映射具体到每个映射的每种类型,这也是为什么在elasticsearch中,类型有时候也称为映射类型。

> 类型

类型是文档的逻辑容器,就像关系型数据库一样,表是行的容器。类型中对于字段的定 义称为映射,比如name映射为字符串类型。我们说文档是无模式的 ,它们不需要拥有映射中所定义的所有字段,比如新增一个字段,那么elasticsearch是怎么做的呢\?elasticsearch会自动的将新字段加入映射,但是这个字段的不确定它是什么类型, elasticsearch就开始猜,如果这个值是18 ,那么elasticsearch会认为它是整形。但是elasticsearch也可能猜不对 ，所以最安全的方式就是提前定义好所需要的映射,这点跟关系型数据库殊途同归了,先定义好字段,然后再使用,别整什么幺蛾子。

> 索引

就是数据库\!

索引是映射类型的容器, elasticsearch中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。然后它们被存储到了各个分片上了。我们来研究下分片是如何工作的。

**物理设计:节点和分片如何工作**

一个集群至少有一 个节点,而一个节点就是一个elasricsearch进程 ,节点可以有多个索引默认的,如果你创建索引,那么索引将会有个5个分片\( primary shard ,又称主分片\)构成的,每一个主分片会有一个副本\( replica shard ,又称复制分片）

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021051720453499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

上图是一个有3个节点的集群,可以看到主分片和对应的复制分片都不会在同一个节点内,这样有利于某个节点挂掉了,数据也不至于丢失。实际上, 一个分片是一个Lucene索引, 一个包含倒排索引的文件目录,倒排索引的结构使得elasticsearch在不扫描全部文档的情况下,就能告诉你哪些文档包含特定的关键字。

> 倒排索引

倒排索引详解：<https://www.cnblogs.com/zlslch/p/6440114.html>

elasticsearch使用的是一种称为倒排索引的结构,采用Lucene倒排索作为底层。这种结构适用于快速的全文搜索，一个索引由文档中所有不重复的列表构成,对于每一个词,都有一个包含它的文档列表。 例如,现在有两个文档，每个文档包含如下内容:

```xml
Study every day， good good up to forever  # 文档1包含的内容
To forever, study every day，good good up  # 文档2包含的内容
```

为为创建倒排索引,我们首先要将每个文档拆分成独立的词\(或称为词条或者tokens\) ,然后创建一一个包含所有不重复的词条的排序列表,然后列出每个词条出现在哪个文档:

| term    | doc\_1 | doc\_2 |
| ------- | ------ | ------ |
| Study   | √      | x      |
| To      | x      | x      |
| every   | √      | √      |
| forever | √      | √      |
| day     | √      | √      |
| study   | x      | √      |
| good    | √      | √      |
| every   | √      | √      |
| to      | √      | x      |
| up      | √      | √      |

现在，我们试图搜索 to forever，只需要查看包含每个词条的文档

| term    | doc\_1 | doc\_2 |
| ------- | ------ | ------ |
| to      | √      | x      |
| forever | √      | √      |
| total   | 2      | 1      |

两个文档都匹配,但是第一个文档比第二个匹配程度更高。如果没有别的条件,现在,这两个包含关键字的文档都将返回。

再来看一个示例,比如我们通过博客标签来搜索博客文章。那么倒排索引列表就是这样的一个结构:

| 博客文章\(原始数据\) | 博客文章\(原始数据\) | 索引列表\(倒排索引\) | 索引列表\(倒排索引\) |
| -------------------- | -------------------- | -------------------- | -------------------- |
| 博客文章ID           | 标签                 | 标签                 | 博客文章ID           |
| 1                    | python               | python               | 1，2，3              |
| 2                    | python               | linux                | 3，4                 |
| 3                    | linux，python        |                      |                      |
| 4                    | linux                |                      |                      |

如果要搜索含有python标签的文章,那相对于查找所有原始数据而言，查找倒排索引后的数据将会快的多。只需要查看标签这一栏,然后获取相关的文章ID即可。完全过滤掉无关的所有数据,提高效率\!

**elasticsearch的索引和Lucene的索引对比**

在elasticsearch中，索引\(库\)这个词被频繁使用,这就是术语的使用。在elasticsearch中 ,索引被分为多个分片,每份分片是一个Lucene的索引。因为`一个elasticsearch索引是由多个Lucene索引组成的`。别问为什么,谁让`elasticsearch使用Lucene作为底层呢`\!如无特指，说起索引都是指elasticsearch的索引。

接下来的一切操作都在kibana中Dev Tools下的Console里完成。基础操作\!

# 四、ik分词器

> 什么是IK分词器 \?

分词:即把一段中文或者别的划分成一个个的关键字,我们在搜索时候会把自己的信息进行分词,会把数据库中或者索引库中的数据进行分词,然后进行一个匹配操作,默认的中文分词是将每个字看成一个词,比如“我爱狂神”会被分为"我",“爱”,“狂”,“神” ,这显然是不符合要求的,所以我们需要安装中文分词器ik来解决这个问题。

如果要使用中文,建议使用ik分词器\!

IK提供了两个分词算法: `ik_ smart`和`ik_ max_ word`,其中`ik_ smart为最少切分`, `ik_ max_ _word为最细粒度划分`\!

什么是IK分词器：

- 把一句话分词
- 如果使用中文：推荐IK分词器
- 两个分词算法：ik\_smart（最少切分），ik\_max\_word（最细粒度划分）

ik分词器下载：<https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.12.1>  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517221645963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
下载完，在elasticsearch 的plugins目录下建一个ik文件夹，将压缩包的内容解压到ik文件夹中，重启ES，可以看到ik分词器被加载了！

kibana 中测试：

> ik\_smart（最少切分）

```java
//_analyze：分词器
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "我是社会主义接班人"
}

//结果
{
  "tokens" : [
    {
      "token" : "我",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "CN_CHAR",
      "position" : 0
    },
    {
      "token" : "是",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "社会主义",
      "start_offset" : 2,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "接班人",
      "start_offset" : 6,
      "end_offset" : 9,
      "type" : "CN_WORD",
      "position" : 3
    }
  ]
}
```

> ik\_max\_word（最细粒度划分）

```java
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "我是社会主义接班人"
}

//结果
{
  "tokens" : [
    {
      "token" : "我",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "CN_CHAR",
      "position" : 0
    },
    {
      "token" : "是",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "社会主义",
      "start_offset" : 2,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "社会",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 3
    },
    {
      "token" : "主义",
      "start_offset" : 4,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 4
    },
    {
      "token" : "接班人",
      "start_offset" : 6,
      "end_offset" : 9,
      "type" : "CN_WORD",
      "position" : 5
    },
    {
      "token" : "接班",
      "start_offset" : 6,
      "end_offset" : 8,
      "type" : "CN_WORD",
      "position" : 6
    },
    {
      "token" : "人",
      "start_offset" : 8,
      "end_offset" : 9,
      "type" : "CN_CHAR",
      "position" : 7
    }
  ]
}
```

> ik分词器增加自定义配置

在`elasticsearch-7.12.1\plugins\ik\config`目录下增加自定义的dic文件，然后配置文件中加载自定义dic文件，重启即可！  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517223609931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

> 加自定义词典前

```java
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "喜欢狂神说Java"
}

{
  "tokens" : [
    {
      "token" : "喜欢",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "狂",
      "start_offset" : 2,
      "end_offset" : 3,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "神",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "CN_CHAR",
      "position" : 2
    },
    {
      "token" : "说",
      "start_offset" : 4,
      "end_offset" : 5,
      "type" : "CN_CHAR",
      "position" : 3
    },
    {
      "token" : "java",
      "start_offset" : 5,
      "end_offset" : 9,
      "type" : "ENGLISH",
      "position" : 4
    }
  ]
}
```

> 加自定义词典后

```java
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "喜欢狂神说Java"
}

{
  "tokens" : [
    {
      "token" : "喜欢",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "狂神说",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "java",
      "start_offset" : 5,
      "end_offset" : 9,
      "type" : "ENGLISH",
      "position" : 2
    }
  ]
}
```

可以发现`狂神说`变成一个词了！！！

# 五、Rest风格说明

| method |                     url地址                      |          描述          |
| :----: | :----------------------------------------------: | :--------------------: |
|  PUT   |     localhost:9200/索引名称/类型名称/文档id      | 创建文档\(指定文档id\) |
|  POST  |         localhost:9200/索引名称/类型名称         | 创建文档（随机文档id） |
|  POST  | localhost:9200/索引名称/类型名称/文档id/\_update |        修改文档        |
| DELETE |     localhost:9200/索引名称/类型名称/文档id      |        删除文档        |
|  GET   |     localhost:9200/索引名称/类型名称/文档id      |   通过文档id查询文档   |
|  POST  |    localhost:9200/索引名称/类型名称/\_search     |     查询所有的数据     |

## 5.1 索引操作

### 5.1.1.创建索引

> PUT /索引名/类型名\(高版本都不写了，都是\_doc\)/文档id

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518201102525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518201223711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
**ES的类型：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518201237501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.1.2.创建索引规则

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518201750121.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.1.3.获取索引信息

> GET /索引名

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518201917310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.1.4.不指定文档类型，创建索引

> PUT /索引名/\_doc/文档名

ES会猜是什么类型的，有可能猜错  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518202502959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518202538272.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.1.5.cat命令

> GET \_cat/health 获取健康信息  
> GET \_cat/indices\?v 获取所有信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518202708477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518202806971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.1.6.修改索引

> PUT /test1/type1/1 未设置的属性，会被覆盖为空  
> POST /test3/\_doc/1 未设置的属性，会被覆盖为空  
> POST /test3/\_doc/1/\_update

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518203214604.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518203351432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
常用 POST /test3/\_doc/1/\_update，这种不会被覆盖：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518203811488.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518203831830.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.1.7.删除索引

> DELETE 索引名

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518203926661.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

## 5.2 文档操作

### 5.2.1.简单查询

> GET /kuangshen/\_doc/\_search?q=name:狂神

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518205203820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

> GET /kuangshen/\_doc/\_search

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518210328864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

两种简单查询，结果一样！name是text类型，进行了分词，所以查`狂神`，`法外狂徒张三`也能被查出来

### 5.2.2.多条件查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518213541351.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

**复杂查询**

### 5.2.3.过滤

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518210036809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
也可以写成这种形式

```xml
GET /kuangshen/_doc/_search
{
  "query":{
    "match":{
      "name":"狂神"
    }
  },
  "_source":{
    "includes":["name","age"]
  }
}
```

source中不包含哪种属性  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518210728399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

###  5.2.4.排序

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518210943229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.2.5.分页

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518211223181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.2.6.布尔查询

must:必须  
相当于and  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518211704929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
should：应该  
相当于or  
**es的查询都会进行分词，如果查name：“狂神”，会把所有的都查出来**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518212438274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
must\_not：不必须  
相当于 不等于not  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518212714511.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.2.7.区间查询

- gt大于
- gte大于等于
- lt小于
- lte小于等于  
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518213026792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.2.8.精确查询

term查询是直接通过倒排索引指定的词条进程精确查找的

搜索前，`term不会对搜索词进行分词拆解，term的搜索词必须在文档集合中，否则查不到！！`  
`而match会先对搜索词进行分词拆分，分词只要在文档集合中有就查出来，匹配程度越大，sorce越大`

详细使用参考：<https://www.cnblogs.com/chong-zuo3322/p/14031602.html>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518214440169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.2.9.高亮

```xml
"pre_tags":"<p style='color:red'>"  #前缀
"post_tags":"</p>"  #后缀
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518215358945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

### 5.2.10.分词

standard的会被分词  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518214726165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
keyword不会被分词  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518214802640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

# 六、springboot整合es

创建springboot项目，导入依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

查看maven的es版本是不是你下载的版本

```xml
<properties>
	<java.version>1.8</java.version>
	<elasticsearch.version>7.12.1</elasticsearch.version>
</properties>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518225909256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)  
新建一个es配置类，注入RestHighLevelClient 客户端

```java
@Configuration
public class ElasticSearchClientConfig {
    @Bean
    public RestHighLevelClient restHighLevelClient(){
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("127.0.0.1",9200,"http"))
        );
        return client;
    }
}
```

## 6.1 索引API

和`索引请求`相关的API有5种，常用的有3种：添加，删除，判断是否存在  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210519204551944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

```java
@SpringBootTest
class SpringbootEsApplicationTests {

	@Autowired
	@Qualifier("restHighLevelClient")
	private RestHighLevelClient client;

	@Test
	void testCreateIndex() throws IOException {
		//创建索引请求
		CreateIndexRequest request = new CreateIndexRequest("kuang_index");
		//获取索引客户端对象
		IndicesClient indices = client.indices();
		//索引客户端执行创建索引请求
		CreateIndexResponse createIndexResponse = indices.create(request, RequestOptions.DEFAULT);
		System.out.println(createIndexResponse);
	}

	@Test
	void testExistIndex() throws IOException {
		//创建获取索引请求
		GetIndexRequest request = new GetIndexRequest("kuang_index");
		//客户端执行请求
		boolean exists = client.indices().exists(request, RequestOptions.DEFAULT);
		System.out.println("索引是否存在："+exists);
	}

	@Test
	void testDeleteIndex() throws IOException {
		//创建删除索引请求
		DeleteIndexRequest request = new DeleteIndexRequest("kuang_index");
		AcknowledgedResponse response = client.indices().delete(request, RequestOptions.DEFAULT);
		System.out.println("是否删除成功："+response.isAcknowledged());
	}

}
```

## 6.2 文档API

文档的增删改查！

```java
//测试添加文档
@Test
void testAddDocument() throws IOException {
	User user = new User("张三", 18);
	//创建一个请求
	IndexRequest request = new IndexRequest("kuang_index");
	//设置id，也可不设置，系统随机设置
	request.id("1");
	//设置超时时间
	request.timeout("1s");
	//将数据用json字符串形式放入请求中
	request.source(JSON.toJSONString(user), XContentType.JSON);
	//执行请求
	IndexResponse response = client.index(request, RequestOptions.DEFAULT);
	System.out.println("添加文档："+response.toString());
	System.out.println("添加文档："+response.status());
	//结果
	//添加文档：IndexResponse[index=kuang_index,type=_doc,id=1,version=1,result=created,seqNo=0,primaryTerm=1,shards={"total":2,"successful":1,"failed":0}]
	//添加文档：CREATED
}

//测试删除文档
@Test
void testDeleteDocument() throws IOException {
	DeleteRequest request = new DeleteRequest("kuang_index", "1");
	request.timeout("1s");
	DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);
	System.out.println(response.status());
}

//测试修改文档
@Test
void testUpdateDocument() throws IOException {
	UpdateRequest request = new UpdateRequest("kuang_index", "1");
	request.timeout("1s");
	//只修改name，age被置0
	//只修改age，name正常
	//request.doc(JSON.toJSONString(new User("王五")),XContentType.JSON);
	request.doc(JSON.toJSONString(new User(10)),XContentType.JSON);

	UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
	System.out.println(response.status());
}

//测试获取文档
@Test
void testEGETDocument() throws IOException {
	GetRequest request = new GetRequest("kuang_index","1");
	boolean exists = client.exists(request, RequestOptions.DEFAULT);
	System.out.println("文档是否存在："+exists);
	GetResponse response = client.get(request, RequestOptions.DEFAULT);
	System.out.println(response); //一个hits
	System.out.println(response.getSourceAsString());//hits中的数据
	//{"_index":"kuang_index","_type":"_doc","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":{"age":18,"name":"张三"}}
	//{"age":18,"name":"张三"}
}
```

批量插入

```java
//测试批量插入
@Test
void testBulkAddDocument() throws IOException {
	List list = new ArrayList();
	list.add(new User("狂神1",7));
	list.add(new User("狂神2",8));
	list.add(new User("狂神3",9));
	list.add(new User("狂神4",10));
	list.add(new User("狂神5",11));

	//创建批处理请求
	BulkRequest request = new BulkRequest();
	request.timeout("1s");

	for (int i = 0; i < list.size(); i++) {
		//向批处理请求中添加创建文档请求
		request.add(new IndexRequest("kuang_index")
					.id(""+(i+1))
					.source(JSON.toJSONString(list.get(i)),XContentType.JSON)
		);
	}

	//执行批处理请求
	BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);
	System.out.println("是否失败："+response.hasFailures());
}
```

条件查询

```java
//测试条件查询
@Test
void testSearchDocument() throws IOException {
	//创建查询请求
	SearchRequest request = new SearchRequest("kuang_index");
	//构建搜索条件
	SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
	//高亮
	sourceBuilder.highlighter();
	//排序
	sourceBuilder.sort("age", SortOrder.DESC);
	//分页
	sourceBuilder.from(1);
	sourceBuilder.size(3);
	//精确查询
	//TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "狂神2");
	//普通查询，会分词
	MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("name", "狂神");
	sourceBuilder.query(matchQueryBuilder);
	//设置超时时间
	sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));

	//搜索条件放入请求中
	request.source(sourceBuilder);
	SearchResponse response = client.search(request, RequestOptions.DEFAULT);
	System.out.println("测试条件查询："+JSON.toJSONString(response));
	for (SearchHit documentFields:response.getHits().getHits()) {
		System.out.println(documentFields.getSourceAsString());
	}
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210519221316219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNjY1NzQ1,size_16,color_FFFFFF,t_70)

# 七、kibana常用命令

```bash
GET _search
{
  "query": {
    "match_all": {}
  }
}

#集群健康值排查
#查看集群状态
GET /_cluster/health
#查看索引信息，找出异常索引
GET /_cat/indices\?v
# 查看索引设置
GET /test1/_settings
# 重建索引
 POST _reindex
  {
    "source": {
      "index": "test1"
    },
    "dest": {
      "index": "test2"
    }
  }
# 查看重建索引的设置
  GET /test2
  
# 删除索引
  DELETE /test1
  
# 创建索引别名
  POST /_aliases
  {
    "actions": [
      {
        "add": {
          "index": "test2",
          "alias": "test1"
        }
      }
    ]
  }
# 重新设置索引分片信息
PUT test2/_settings
{
      "number_of_replicas" : 2
}
GET /test2/_settings

# 分词
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "中华人民共和国万岁"
}

DELETE /test1/_doc/Ij7J8XoBzdABBHQNWIjv

# 添加
POST /test1/_doc
{
  "name":"搁着查询呢1",
  "age":2,
  "birthday":"2021-07-28",
  "address":"擦的撒"
}
# 更新
POST /test1/_doc/5/_update
{
  "doc":{
    "age":3
  }
}

GET _cat/health
# 创建索引
PUT /test1
{
  "mappings": {
    "properties": {
      "name":{
        "type": "text"
      },
      "age":{
        "type": "long"
      },
      "birthday":{
        "type": "date"
      },
      "address":{
        "type": "text"
      }
    }
  }
}


GET /test1/_search?q=name:查询
GET /test1/_search
{
  "query": {
    "match": {
      "name": "查询"
    }
  }
  , "sort": [
    {
      "age": {
        "order": "asc"
      }
    }
  ],
  "_source": ["name","age"]
}

GET /test1/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "查询"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "age": {
              "gte": 21,
              "lte": 40
            }
          }
        }
      ]
    }
  },
  "highlight": {  
    "pre_tags": "<span class='key' style='color:red'>",
    "post_tags": "</span>" ,
    "fields": {
      "name": {}
    }
  }
}
```

