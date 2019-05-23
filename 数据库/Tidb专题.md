## 为何考虑使用TiDB
1. 高度兼容Mysql - 无需修改代码即可从Mysql迁移到TiDB
2. 无限水平扩展 - 不必考虑分库分表，轻松应对海量数据场景
3. 分布式事务 - 100%支持标准的ACID事务，数据强一致性保证
4. 高可用 - 基于Raft的多数派选举协议

## TiDB的架构是怎样的
TiDB集群分为三个组件：TiDB Server、PD Server、TiKV Server

#### TiDB Server
负责接收sql请求，处理sql相关逻辑，并通过PD找到存储计算所需数据的TiKV地址，与TiKV交互获取数据，最终返回结果。TiDB Server是无状态的，其本身并不存储数据，只负责计算，可以无限水平扩展。

#### PD Server
是整个集群的管理模块，主要用于管理集群元数据，同时负责对TiKV集群进行调度和负载。

#### TiKV Server
负责存储数据，从外部看是一个分布式的提供事务支持的key-value存储引擎，使用raft协议做复制，互为副本。
