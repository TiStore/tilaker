# 基于 TiDB 和 Flink CDC 的数据湖构建

## 团队
团队名字：TiLaker
队长姓名： 吴雪莲(AndreMouche)
成员：
* AndreMouche: 资深可乐炸鸡程序媛，分布式存储研发工程师，开源软件爱好者。
* CabinfeverB: 资深分布式调度系统专家，开源软件爱好者。
* SteNicholas: 开源软件爱好者，全栈工程师，Apache ShardingSohere & Apache RocketMQ Commttier
* LeonardBang: 开源软件爱好者，实时计算工程师，Flink CDC Maintainer & Apache Flink Committer

战队宣言：请让 TiDB 拥抱数据湖


### 背景
TiDB 是 PingCAP 公司自主设计、研发的开源分布式关系型数据库，同时支持在线事务处理与在线分析处理，供水平可扩展性、强一致性和高可用性，兼容 MySQL 5.7 协议和 MySQL 生态，是当下非常热门的数据库产品。数据湖是大数据领域近年来非常火热的技术，传统数仓无法实现增量数据的实时更新，也无法支持灵活的元数据格式，数据湖技术正是在这一背景下诞生的。数据库的增量变更是数据湖中增量数据的主要来源，遗憾的是目前 TiDB 的入湖路径还是比较割裂，全量一般用 Dumpling组件，增量一般用TiCDC组件，两者还是割裂的链路，TiDB 也无法通过实时物化视图完成数据入湖的实时清洗和加工，而这正是 Flink CDC 所能提供的核心能力，因此我们非常自然地想到通过 Flink CDC 帮助 TiDB 用户更轻松地完成数据湖的构建。


### 目标
* 1. 为 TiDB 插上入湖的翅膀
* 2. 为用户提供实时物化视图
* 3. 支持异构数据源的融合分析
* 4. 纯 SQL 实现 TiDB 入湖

### 实施方案
 1.基于 TiCDC 组件开发面向 TiDB 的 Flink CDC Connector，暂时取名为'tidb-cdc', TiDB CDC Connector 提供全量读取历史数据和实时读取增量数据的能力，在全量和增量数据切换时，保证一条不丢，一条不多的eactly-once 语义。 
 2.TiDB CDC Connector 提供 SQL API，用户只需要几行 Flink SQL 就能捕获 TiDB 中全量的历史数据和增量数据。得益于Flink SQL 的changelog 机制，Flink SQL 可以和数据库的变更数据无缝衔接，通过 Flink SQL 定义的 tidb-cdc 表就是 TiDB 中对应表的实时物化视图，每次数据库中的变更都会让 tidb-cdc 表自动更新。
 3.Flink CDC 项目提供了MySQL,MariaDB,Postgres,Oracle,Mongo等数据库的支持，这意味着再支持TiDB后，用户可以实现异构数据源的融合，比如部分表在MySQL中，部分表在TiDB中，可以做实时的Join,Union等Streaming 加工。
 4.作为一个优秀的计算引擎，Flink 可以提供强大的计算能力和优秀的pipeline能力，支持业界多种数据湖产品(Hudi，Iceberg)，并且提供SQL API 支持。这让 TiDB 的用户只需要使用SQL就可以方便地将数据实时写入数据湖，轻松实现数据湖的构建。
 

