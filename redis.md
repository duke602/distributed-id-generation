Redis也同样可以实现，原理就是利用redis的 incr命令实现ID的原子性自增。
```
127.0.0.1:6379> set seq_id 1     // 初始化自增ID为1
```
```
OK
```
```
127.0.0.1:6379> incr seq_id      // 增加1，并返回递增后的数值
```
```
(integer) 2
```
用redis实现需要注意一点，要考虑到redis持久化的问题。redis有两种持久化方式RDB和AOF
* RDB会定时打一个快照进行持久化，假如连续自增但redis没及时持久化，而这会Redis挂掉了，重启Redis后会出现ID重复的情况。
* AOF会对每条写命令进行持久化，即使Redis挂掉了也不会出现ID重复的情况，但由于incr命令的特殊性，会导致Redis重启恢复的数据时间过长。
