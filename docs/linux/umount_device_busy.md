# umount device busy
```
yum -y install psmisc
fuser -cu /data
/data: 1757c(root)

# kill process
fuser -ck /data
kill -9 1757

umount /data
```