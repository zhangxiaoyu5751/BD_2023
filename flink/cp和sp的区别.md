1. CheckPoint的侧重点是“容错”，当Flink作业意外失败，并重启时能直接从早先打下的CheckPoint恢复运行，且不影响作业逻辑的准确性。而SavePoint侧重点是“维护”，当Flink作业需要在人工干预下手动重启、升级、迁移或A/B测试时，先将状态整体写入可靠存储，维护完毕之后再从SavePoint恢复现场。
2. SavePoint是“通过CheckPoint机制”创建的，所以SavePoint本质上是特殊的CheckPoint。
3. CheckPoint面向Flink Runtime本身，由Flink的各个TaskManager定时触发快照并自动清理，一般不需要用户干预；SavePoint面向用户，完全根据用户的需要触发与清理。
4. CheckPoint 的频率往往比较高（因为需要尽可能保证作业恢复的准确度），所以CheckPoint的存储格式非常轻量级，但作为 trade-off 牺牲了一切可移植（portable）的东西，比如不保证改变并行度和升级的兼容性。 SavePoint 则以二进制形式存储所有状态数据和元数据，执行起来比较慢而且“贵”，但是能够保证 portability ，如并行度改变或代码升级之后，仍然能正常恢复。
5. CheckPoint是支持增量的（通过RocksDB），特别是对于超大状态的作业而言可以降低写入成本。SavePoint并不会连续自动触发，所以不支持增量。
