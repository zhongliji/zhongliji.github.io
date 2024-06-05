# awk /etc/fstab
```
# 分隔符 行 列
awk -F[=' '] 'NR==10 {print $2}' /etc/fstab
a=`blkid |grep vdb | awk -F\" '{print $2}'`
b=`awk -F[=' '] 'NR==10 {print $2}' /etc/fstab`
sed -i "s/$b/$a/g" fstab

multipath -ll | awk -F['()'] '/mpathb/{print $2}'
```
## 行尾追回字符串
```
a=`docker node ls | awk -F[=' '] 'NR==2 {print $1}'`
sed s/$/" $a"/g label > labelnew

label:
docker node update --label-add nodetype=InDrive  
docker node update --label-add nodelabels=Middleware 
docker node update --label-add noderedis=true 
docker node update --label-add isapm=true 
docker node update --label-add nodeportainer=Portainer

labelnew:
docker node update --label-add nodetype=InDrive   755qsxpc9hvdd12ouyqrtktot
docker node update --label-add nodelabels=Middleware  755qsxpc9hvdd12ouyqrtktot
docker node update --label-add noderedis=true  755qsxpc9hvdd12ouyqrtktot
docker node update --label-add isapm=true  755qsxpc9hvdd12ouyqrtktot
docker node update --label-add nodeportainer=Portainer  755qsxpc9hvdd12ouyqrtktot
```
