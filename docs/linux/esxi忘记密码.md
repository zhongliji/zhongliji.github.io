## 1.使用centos引导盘进入救援模式
## 2.修改密码文件
```
mkdir -p /mnt/sda5
 
mount /dev/sda5 /mnt/sda5
 
cp /mnt/sda5/state.tgz /tmp
cd /tmp
tar xf state.tgz  #这个解压会解出一个local.tgz文件
tar xf local.tgz   #这个解压会解出一个在/tmp/etc文件夹
vi etc/shadow
root:后面跟的一堆编码就是密码，这里把清空就可以，记住一定不删除"："号
```
## 3.重新打包
```
cd /tmp
rm /tmp/state.tgz /tmp/local.tgz
tar czf local.tgz etc/
tar czf state.tgz local.tgz
cp state.tgz /mnt/sda5/
reboot
```