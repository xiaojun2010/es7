# 第3章 Elasticsearch 篇之倒排索引与分词

## 3.1 书的目录与索引

### 3.1.1 书与索引

<img src="img03/01.png" alt="image-20230823103202733" style="zoom:30%;" />

如何查找”ACID“ 关键词所在页面：

<img src="img03/02.png" alt="image-20230823103341421" style="zoom:30%;" />

### 

目录页对应正排索引

索引页对应倒排索引

###  3.1.2 搜索引擎

正排索引

- 文档ID到文档内容、单词的关联关系

倒排索引

- 单词到文档ID的关联关系



## 3.2 正排与倒排索引简介

### 3.2.1 正排索引

文档ID到文档内容、单词的关联关系

<img src="img03/03.png" alt="image-20230823103839508" style="zoom:30%;" />

### 3.2.2 倒排索引

文档到文档ID的关联关系

<img src="img03/04.png" alt="image-20230823110712909" style="zoom:30%;" />

通过对文档内容进行分词：



<img src="img03/05.png" alt="image-20230823110759826" style="zoom:30%;" />

### 3.2.3 倒排索引-查询流程

查询包含”搜索引擎“的文档

- 通过倒排索引获得”搜索引擎“对应的文档ID有1和3
- 通过正排索引查询1和3的完整内容
- 返回用户最终结果



## 3.3 倒排索引详解

### 3.3.1 倒排索引组成

倒排索引是搜索引擎的核心，主要包括2部分：

- 单词词典（Term Dictionary）
- 倒排列表（Posting list）

#### 3.3.1.1 单词词典

单词词典（Term Dictionary）是倒排索引的重要组成

- 记录所有文档的单词，一般比较大
- 记录单词到倒排列表的关联信息



单词词典的实现一般是用 B+ Tree，示例如下图：

<img src="img03/06.png" alt="image-20230823111923101" style="zoom:30%;" />

#### 3.3.1.2 倒排列表

倒排列表（Posting List）记录了单词对应的的文档集合，由倒排索引项（Posting）组成

倒排索引项（Posting）主要包含如下信息：

- 文档id ，用于获取原始信息
- 单词频率（TF，Term Frequency），记录该单词在该文档中出现**次数**，用于后续**相关性算分**
- 位置（Position），记录单词在文档中的分词位置（多个），用于做词语搜索（Phrase Query）
- 偏移（Offset），记录单词在文档中的开始和结束位置，用于做高亮显示

以”搜索引擎“为例，如何构建Posting List的：

<img src="img03/07.png" alt="image-20230823114053403" style="zoom:30%;" />



<img src="img03/08.png" alt="image-20230823114244874" style="zoom:30%;" />

es存储的是一个json格式的文档，其中包含多个字段，**每个字段都会有自己的倒排序索引**：

<img src="img03/09.png" alt="image-20230823114607292" style="zoom:30%;" />

## 3.4 分词

### 3.4.1 什么是分词

分词是将文本转换成一系列单词（term or token）的过程，也可以叫做文本分析，在es里面称Analysis，如下图所示：

<img src="img03/10.png" alt="image-20230823115445579" style="zoom:30%;" />

### 3.4.2 分词器-组成

分词器是es中专门处理分词的组件，英文为 Analyzer，它的组成如下：

- Character Filter
  - 针对原始文本进行处理，比如去除 html 特殊标记符
- Tokenizer
  - 将原始文本按照一定规则切分单词
- Token Filter
  - 针对Tokenizer 处理的单词进行再加工，比如转小写、删除或新增（如：近义词、同义词）等处理

### 3.4.3 分词器-调用顺序

<img src="img03/11.png" alt="image-20230823121008326" style="zoom:30%;" />



## 3.5 Analyze API

es提供了一个测试分词的api接口，方便验证分词效果，endpoint 是_analyze

- 可以直接指定analyzer进行测试
- 可以直接指定索引中的字段进行测试
- 可以自定义分词器进行测试

<img src="img03/12.png" alt="image-20230823121417344" style="zoom:30%;" />

##  



## 3.6 自带分词器





## 3.7 中文分词



## 3.8 自定义分词之CharacterFilter 



## 3.9 自定义分词之Tokenizer 



## 3.10 自定义分词之 TokenFilter 



## 3.11 自定义分词



## 3.12 分词使用说明 



## 3.13 官方文档说明