前边说了单点数据库方式不可取，那对上边的方式做一些高可用优化，换成主从模式集群。害怕一个主节点挂掉没法用，那就做双主模式集群，也就是两个Mysql实例都能单独的生产自增ID。
那这样还会有个问题，两个MySQL实例的自增ID都从1开始，会生成重复的ID怎么办？

解决方案：设置起始值和自增步长

MySQL_1 配置：
```
set @@auto_increment_offset = 1;     -- 起始值
set @@auto_increment_increment = 2;  -- 步长
```
MySQL_2 配置：
```
set @@auto_increment_offset = 2;     -- 起始值
set @@auto_increment_increment = 2;  -- 步长
```
这样两个MySQL实例的自增ID分别就是：
```
1、3、5、7、9 2、4、6、8、10
```
那如果集群后的性能还是扛不住高并发咋办？就要进行MySQL扩容增加节点，这是一个比较麻烦的事。

水平扩展的数据库集群，有利于解决数据库单点压力的问题，同时为了ID生成特性，将自增步长按照机器数量来设置。

增加第三台MySQL实例需要人工修改一、二两台MySQL实例的起始值和步长，把第三台机器的ID起始生成位置设定在比现有最大自增ID的位置远一些，但必须在一、二两台MySQL实例ID还没有增长到第三台MySQL实例的起始ID值的时候，否则自增ID就要出现重复了，必要时可能还需要停机修改。

优点：
* 解决DB单点问题

缺点：
* 不利于后续扩容，而且实际上单个数据库自身压力还是大，依旧无法满足高并发场景。