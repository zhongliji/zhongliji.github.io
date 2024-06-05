## mt control tape
```
yum -y install mt-st 安装
mt –f /dev/st0 offline 弹出磁带
mt –f /dev/st0 status 查看状态
mt –f /dev/st0 erase 擦除磁带
mt –f /dev/st0 rewind 倒带
```
### backup with tar
```
tar cvf /dev/st0 /home 备份/home目录
tar cvf /dev/st0 test1.tar.gz 备份
tar rvf /dev/st0 test2.tar.gz 增量备份
tar xvf /dev/st0 test3.tar.gz 恢复
tar tvf /dev/st0 读取磁带机上的文件
tar vf /dev/st0 --delete test3.tar.gz 删除磁带机上的文件
```