## install fio
```
rpm -ivh libaio-0.3.109-13.el7.x86_64.rpm
rpm -ivh libaio-devel-0.3.109-13.el7.x86_64.rpm

tar xzvf fio-2.1.10.tar.gz
cd fio-2.1.10
./configure
make && make install
```
## testing fio
```
fio -ioengine=libaio -bs=4k -size=5G -direct=1 -thread -rw=rw -rwmixread=30  -numjobs=1 -directory=/data -name="BS 4KB RW test" -iodepth=1

fio -ioengine=libaio -bs=1M -size=5G -direct=1 -thread -rw=rw -rwmixread=30  -numjobs=1 -filename=/dev/sdf -name="BS 1M RW test" -iodepth=1
```
## 参数说明
```
filename=/dev/sdb 测试文件名称，通常选择需要测试的盘的data目录。
direct=1 是否使用directIO，测试过程绕过OS自带的buffer，使测试磁盘的结果更真实; 
rw=randwrite 测试随机写的I/O
rw=randrw 测试随机写和读的I/O
bs=16k 单次io的块文件大小为16k
bsrange=512-2048 同上，提定数据块的大小范围
size=5G 每个线程读写的数据量是5GB。
numjobs=1 每个job（任务）开1个线程，这里用了几，后面每个用-name指定的任务就开几个线程测试。所以最终线程数=任务数（几个name=jobx）* numjobs。 
name=job1：一个任务的名字，重复了也没关系。如果fio -name=job1 -name=job2，建立了两个任务，共享-name=job1之前的参数。-name之后的就是job2任务独有的参数。 
thread  使用pthread_create创建线程，另一种是fork创建进程。进程的开销比线程要大，一般都采用thread测试。 
runtime=1000 测试时间为1000秒，如果不写则一直将5g文件分4k每次写完为止。
ioengine=libaio 指定io引擎使用libaio方式。libaio：Linux本地异步I/O。请注意，Linux可能只支持具有非缓冲I/O的排队行为（设置为“direct=1”或“buffered=0”）；rbd:通过librbd直接访问CEPH Rados 
iodepth=16 队列的深度为16.在异步模式下，CPU不能一直无限的发命令到SSD。比如SSD执行读写如果发生了卡顿，那有可能系统会一直不停的发命令，几千个，甚至几万个，这样一方面SSD扛不住，另一方面这么多命令会很占内存，系统也要挂掉了。这样，就带来一个参数叫做队列深度。
Block Devices（RBD），无需使用内核RBD驱动程序（rbd.ko）。该参数包含很多ioengine，如：libhdfs/rdma等
rwmixwrite=30 在混合读写的模式下，写占30%
group_reporting 关于显示结果的，汇总每个进程的信息。
此外
lockmem=1g 只使用1g内存进行测试。
zero_buffers 用0初始化系统buffer。
nrfiles=8 每个进程生成文件的数量。
磁盘读写常用测试点：
1. Read=100% Ramdon=100% rw=randread (100%随机读)
2. Read=100% Sequence=100% rw=read （100%顺序读）
3. Write=100% Sequence=100% rw=write （100%顺序写）
4. Write=100% Ramdon=100% rw=randwrite （100%随机写）
5. Read=70% Sequence=100% rw=rw, rwmixread=70, rwmixwrite=30
（70%顺序读，30%顺序写）
6. Read=70% Ramdon=100% rw=randrw, rwmixread=70, rwmixwrite=30
(70%随机读，30%随机写)
```
