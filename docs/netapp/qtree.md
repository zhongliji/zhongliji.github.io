# 禁用quota
```
quota off -volume nfs -vserver svm1
```
# 创建qtree和导出策略
```
qtree create -volume nfs -qtree 201 -security-style unix -export-policy nfs
qtree create -volume nfs -qtree 202 -security-style unix -export-policy nfs
qtree create -volume nfs -qtree 203 -security-style unix -export-policy nfs
qtree create -volume nfs -qtree 204 -security-style unix -export-policy nfs
```
# 创建quota规则
```
quota policy rule create -policy-name default -volume nfs -type tree -target 201 -disk-limit 1GB
quota policy rule create -policy-name default -volume nfs -type tree -target 202 -disk-limit 1GB
quota policy rule create -policy-name default -volume nfs -type tree -target 203 -disk-limit 1GB
quota policy rule create -policy-name default -volume nfs -type tree -target 204 -disk-limit 1GB 
```
# 启用quota
```  
quota on -volume nfs -vserver svm1
```