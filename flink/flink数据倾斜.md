# flink 数据倾斜
## 数据倾斜的定位
1. 通过flink web ui 自带的反压监控来定位反压
2. 然后看每一个subtask的接收和发送的数据量，如果subtask之间数据量有较大差距，就出现了数据倾斜
## 解决方案
1. 检查设置的并发度。如果并发度比分区数低的时候，就会出现消费不均匀的情况。原则上source的并发度和kafka的分区数保持一致或者是kafka分区数是source并发度的整数倍
2. key分布不均匀
   1. 如果是不需要聚合的话，就加随机前缀就可以了
   2. 如果需要聚合的话，就两阶段聚合。先加前缀或者后缀做一次聚合，然后再删除前缀或后缀在进行聚合。