# 1. 分布式特性

- es支持集群模式，是一个分布式系统，其好处主要有2个：

  - 增大系统容量，如内存、磁盘、使得es集群可以支持PB级的数据
  - 提高系统可用性，即使部分节点停止服务，整个集群依然可以正常服务

- es集群由多个es实例组成

  - 不同集群通过集群名字来区分，可通过cluster.name 进行修改，默认为elasticsearch
  - 每个es实例本质上是一个JVM进程，且有自己的名字，通过node.name进行修改

- cerebro

  https://github.com/lmenezes/cerebro

  **Installation**

  - Download from https://github.com/lmenezes/cerebro/releases
  - Extract files
  - Run bin/cerebro(or bin/cerebro.bat if on Windows)
  - Access on [http://localhost:9000](http://localhost:9000/)

  ###



# 2. 启动一个节点

- 运行如下命令可以启动一个es节点实例

  - bin/elasticsearch -E cluster.name=my_cluster -E node.name=node1

    <img src="img06/02.png" alt="image-20230820122021736" style="zoom:40%;" />

    

    <img src="img06/01.png" alt="image-20230820121533381" style="zoom:40%;" />
    
    <img src="img06/03.png" alt="image-20230820122059377" style="zoom:30%;" />

- Es 集群相关的数据称为 cluster state，主要记录如下信息：

  - 节点信息，比如节点名称、链接地址等
  - 索引信息，比如索引名称、配置等



# 3 Master Node

- 可以修改cluster state 的节点称为 master 节点，一个集群只能有一个

- cluster state 存储在每个节点上，master 维护最新版本并同步给其他节点

- master 节点是通过集群中所有节点选举产生的，可以被选举的节点称为 master-eligible 节点，相关配置如下：

  - node.master:true

    <img src="img06/04.png" alt="image-20230820123209245" style="zoom:30%;" />

    <img src="img06/05.png" alt="image-20230820123509077" style="zoom:30%;" />

  -



# 4 创建一个索引

- 通过如下api创建一个索引

  ```
  PUT test_index
  ```

  <img src="img06/06.png" alt="image-20230820123733011" style="zoom:30%;" />

-

# 5 Coordinating Node

- 处理请求的节点即为 Coordinating 节点，该节点为所有节点的默认角色，不能取消

  - 路由请求到准确的节点处理，比如创建索引的请求到master节点

    <img src="img06/07.png" alt="image-20230820124233931" style="zoom:30%;" />

  -

-

# 6 Data Node

- 存储数据的节点即为 data 节点，默认节点都是data 类型，相关配置如下：

  - node.data : true

    <img src="img06/08.png" alt="image-20230820124413357" style="zoom:30%;" />

  -

-

# 7 单点问题

- 如果node1停止服务，集群停止服务



# 8 新增一个节点

- 运行如下命令可以启动一个es节点实例

  - bin/elasticsearch -E cluster.name=my_cluster -E node.name=node2
  
  - <img src="img06/10.png" alt="image-20230820124740021" style="zoom:30%;" />
  
    <img src="img06/09.png" alt="image-20230820124707800" style="zoom:30%;" />
  
    <img src="img06/11.png" alt="image-20230820124822927" style="zoom:30%;" />
    -













# 9 提高系统可用性

- 服务可用性

  - 2个节点情况下，允许其中1个节点停止服务

- 数据可用性

  - 引入副本（replication）解决
  - 每个节点上都有完备的数据

- 副本

  - 如下图所示，node2上是test_index的副本

    <img src="img06/12.png" alt="image-20230820125119001" style="zoom:30%;" />

  -

- 增大系统容量

  - 如何将数据分布于所有节点上？
    - 引入分片（Shar）解决问题
  - 分片是es支持PB级数据的基石
    - 分片存储了部分数据，可以分布于任意节点上
    - 主分片数在索引创建时指定且后续不允许再修改，默认为5
    - 分片有主分片和副分片之分，以实现数据的高可用
    - 副本分片的数据由主分片同步，可以有多个，从而提高读取的吞吐量
    - 副本分片数随时可以调整

- 分片

  下图演示的是3个节点的集群中 test_index 的分片分布情况，创建时我们指定了3个分片和1个副本，api如下所示：

  <img src="img06/13.png" alt="image-20230820125804192" style="zoom:30%;" />

  再加入一个节点：

  <img src="img06/14.png" alt="image-20230820130000416" style="zoom:30%;" />

  <img src="img06/15.png" alt="image-20230820130042429" style="zoom:30%;" />





- 此时增加节点是否是否能提高test_index的数据容量？

  - **不能**，因为3个分片，已经分布在3台节点上，新增的节点无法利用。

    <img src="img06/16.png" alt="image-20230820130502158" style="zoom:25%;" />

    <img src="img06/17.png" alt="image-20230820130714374" style="zoom:25%;" />

  -

- 此时增加副本数是否能提高test_index的读取吞吐量？

  - **不能，**因为新增的副本也是分布在这3个节点上，还是利用了同样的资源。如果要增加吞吐量，还**`需要新增节点`**。

    <img src="img06/18.png" alt="image-20230820130852947" style="zoom:25%;" />

- 分片数的设定很重要，需要提前规划好
  - 过小会导致后续无法通过增加节点实现水平扩容
  - 过大会导致一个节点上分布过多分片，造成资源浪费，同时会影响查询性能
- 

# 10 Cluster Health 集群监控状态

- 通过如下api 可以查看集群监控状况，包括以下3种：

  - green ：所有主副分片都正常分配

  - yellow：所有主分片都正常分配，但是有副分片未正常分配

  - red ： 有主分片未分配

    <img src="img06/19.png" alt="image-20230820131552026" style="zoom:40%;" />

    <img src="img06/20.png" alt="image-20230820131839229" style="zoom:30%;" />

    注意：只是集群状态，并非集群不能访问，**<font color='red' size=5>Red状态也可以对外提供服务</font>**

- 

# 11 故障转移

- 集群由3个节点组成，如下所示，此时集群状态是green

  <img src="img06/21.png" alt="image-20230820132454739" style="zoom:30%;" />

- node1所在机器宕机导致服务终止，此时集群如何处理？

  - Node2 和 node3 发现node1 无法响应一段时间后会发起master 选举，比如这里选择 node2 为master节点。此时由于主分片p0 下线，集群状态变为Red

    <img src="img06/22.png" alt="image-20230820133716365" style="zoom:30%;" />

  - node2发现主分片p0 未分配，会将R0 提升为主分片。此时由于所有主分片都正常分配，集群状态变为Yellow

    <img src="img06/23.png" alt="image-20230820133938408" style="zoom:30%;" />

  - Node2 为 P0 和 P1生成新的副本，集群状态变为绿色

    <img src="img06/24.png" alt="image-20230820134053608" style="zoom:30%;" />

  - 

- 

# 12 文档分布式存储

- 文档最终会存储在分片上，如下图所示：

  - Document 1 最终存储在分片 P1 上

    <img src="img06/25.png" alt="image-20230820135254419" style="zoom:30%;" />

  - Document 1是如何存储到分片P1上的？选择P1的依据是什么？

    - 需要文档到分片的映射算法

  - 目的

    - 使得文档均匀分布在所有分片上，以充分利用资源

  - 算法

    - 随机选择或者 round-robin 算法？
      - 不可取，因为需要维护文档到分片的映射关系，成本巨大
    - 根据文档值实时计算对应的分片

    

- 文档到分片的映射算法

  - es 通过如下公式计算文档对应的分片
    - shard = hash（routing）% numbet_of_primary_shards
    - Hash 算法保证可以将数据均匀地分散在分片中
    - routing 是一个关键参数，默认是文档id，也可以自行指定
  - 

- 

