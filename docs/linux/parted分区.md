# 分区
```
parted /dev/sdb
(parted) mklabel gpt           # 将MBR磁盘格式化为GPT
(parted) print                       #打印当前分区
(parted) mkpart primary 0 4.5TB                # 分一个4.5T的主分区  -1则创建所有空间
(parted) mkpart primary 4.5TB 12TB      # 分一个7.5T的主分区
(parted) print                         #打印当前分区
(parted) quit 退出
```
# 格式化
```
mkfs.ext4 /dev/sdb1
mkfs.ext4 /dev/sdb2
```
# 挂载
```
mount /dev/sdb1 /1
mount /dev/sdb1 /2
```
# 写入/etc/fstab
```
/dev/sdb1       /bk        ext4           defaults       0 0
/dev/sdb2      /mail       ext4           defaults       0 0
```
