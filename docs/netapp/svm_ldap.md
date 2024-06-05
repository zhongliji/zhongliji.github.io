# svm创建ldap client
```
vserver services name-service ldap client create -client-config hex -ldap-servers 192.168.20.47 -schema RFC-2307 -port 389 -query-timeout 3 -min-bind-level anonymous  -bind-as-cifs-server false -base-dn "DC=hex,DC=com"  -base-scope subtree
vserver services name-service ldap client show
```
# svm和ldap绑定
```
vserver services name-service ldap create -vserver nfs -client-config hex
vserver services name-service ldap check -vserver nfs -fields ldap-status-details
```
# 修改svm的ns-switch
```
vserver services name-service ns-switch modify -vserver nfs -database group -sources files,ldap
vserver services name-service ns-switch modify -vserver nfs -database passwd -sources files,ldap
vserver services name-service ns-switch modify -vserver nfs -database netgroup   -sources files,ldap
vserver services name-service ns-switch modify -vserver nfs -database namemap    -sources files,ldap
vserver services name-service ns-switch show
```
# web界面或cli配置基于user的配额，需要先用户登录ldap客户端，等存储上有文件夹后，再配置quota
```
quota off -volume nfs -vserver svm1
指定用户
quota policy rule create -policy-name default -volume vol1 -type user -target zhongwen -qtree "" -user-mapping off -disk-limit 1GB
所有用户
quota policy rule create -policy-name default -volume vol1 -type user -target "" -qtree "" -user-mapping off -disk-limit 1GB       
quota policy rule show
quota on -volume nfs -vserver svm1
```
# 查看配额报告
```
quota report -vserver nfs -volume vol1
```