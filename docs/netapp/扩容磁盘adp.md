# 扩容的盘柜不会自动分区，需手动进行分区 #
```
1. 进维护模式
set diag
2. 先给磁盘分配owner
disk assign 2.1.0
3. 已一个已经分区的磁盘作为参照物，给目标盘进行分区，目标盘会进行分区，并且分区owner和源盘一致
disk create-partition -source-disk 2.0.0 -target-disk 2.1.0