# distributed-id-generation
分布式ID生成算法

# 为什么要用分布式ID？
在说分布式ID的具体实现之前，我们来简单分析一下为什么用分布式ID？分布式ID应该满足哪些特征？
## 1. 什么是分布式ID？
拿MySQL数据库举个栗子：   
在我们业务数据量不大的时候，单库单表完全可以支撑现有业务，数据再大一点搞个MySQL主从同步读写分离也能对付。   
但随着数据日渐增长，主从同步也扛不住了，就需要对数据库进行分库分表，但分库分表后需要有一个唯一ID来标识一条数据，数据库的自增ID显然不能满足需求；特别一点的如订单、优惠券也都需要有唯一ID做标识。此时一个能够生成全局唯一ID的系统是非常必要的。那么这个全局唯一ID就叫分布式ID。   

## 2. 那么分布式ID需要满足那些条件？
* 全局唯一：必须保证ID是全局性唯一的，基本要求
* 高性能：高可用低延时，ID生成响应要块，否则反倒会成为业务瓶颈
* 高可用：100%的可用性是骗人的，但是也要无限接近于100%的可用性
* 好接入：要秉着拿来即用的设计原则，在系统设计和实现上要尽可能的简单
* 趋势递增：最好趋势递增，这个要求就得看具体业务场景了，一般不严格要求

# 分布式ID都有哪些生成方式？
今天主要分析一下以下9种，分布式ID生成器方式以及优缺点：
* UUID
* 数据库自增ID
* 数据库多主模式
* 号段模式
* Redis
* 雪花算法（SnowFlake）
* 滴滴出品（TinyID）
* 百度 （Uidgenerator）
* 美团（Leaf）
