## 开启Thin模式
```
shlab3::> vol show -vserver nfs -volume nfsclone -fields space-guarantee 
vserver volume   space-guarantee 
------- -------- --------------- 
nfs     nfsclone volume 

shlab3::> vol modify  -vserver nfs -volume nfsclone -space-guarantee none
Volume modify successful on volume nfsclone of Vserver nfs.

shlab3::> vol show -vserver nfs -volume nfsclone -fields space-guarantee 
vserver volume   space-guarantee 
------- -------- --------------- 
nfs     nfsclone none  
```
## 开启重删
```
shlab3::> vol efficiency on -vserver svm_nas01 -volume vol1
shlab3::> vol efficiency modify -vserver svm_nas01 -volume vol1 -compression true -inline-compression true
shlab3::> vol efficiency show 
Vserver    Volume           State     Status      Progress           Policy
---------- ---------------- --------- ----------- ------------------ ----------
nfs        nfsclone         Enabled   Idle        Idle for 00:03:25  -
shlab3::> aggr show-efficiency -details
```
## 手动执行重删
```
shlab3::> sis start -vserver svm_nas01 -volume vol1
shlab3::> sis show -vserver svm_nas01 -volume vol1
```
## 创建clone
```
shlab3::> vol clone create -vserver svm_nas01 -flexclone vol1_clone -type RW -parent-volume vol1
```
## clone挂载junction-path
```
shlab3::> vol mount -junction-path /svm_nas01 -vserver svm_nas01 -volume vol1_clone

shlab3::> vol clone split start -vserver svm_nas01 -flexclone vol1_clone
shlab3::> vol clone split show
shlab3::> vol show -volume nfsclone1 -fields clone-volume 
vserver volume    clone-volume 
------- --------- ------------ 
nfs     nfsclone1 true

shlab3::> vol show -volume nfsclone1 -fields clone-parent-name
vserver volume    clone-parent-name 
------- --------- ----------------- 
nfs     nfsclone1 nfsbak 
```