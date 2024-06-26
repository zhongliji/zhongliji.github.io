## set NFSv4 support on ontap
```
cluster::>nfs server modify -vserver nfs -v4.0 enabled 
cluster::>nfs server modify -vserver nfs -v4-id-domain nfsv4domain.netapp.com 
```
## config linux host
```
yum -y install nfs-utils

vi /etc/idmapd.conf
Domain = nfsv4domain.netapp.com

systemctl restart rpcidmapd
mount -t nfs4 192.168.6.105:/nfs01 /mnt
```
## create NFSv4 with script
```
vserver create -vserver nfs
export-policy rule create -vserver nfs -policyname default -protocol nfs3 -clientmatch 0.0.0.0/0 -rorule  none -rwrule none -superuser none
vol create -vserver nfs -volume nfs01 -size 1t -aggregate MCCIP_A_01_ssd_aggr -junction-path /nfs01
net int create -vserver nfs -lif nfs_lif -home-node MCCIP_A-01 -home-port e0c -role data -data-protocol nfs -address 192.168.8.215 -netmask 255.255.255.0

vserver nfs create -access true -v3 enabled -v4.0 enabled -tcp enabled -vserver nfs
vserver nfs modify -showmount enabled
nfs server modify -vserver nfs -v4-id-domain nfsv4domain.netapp.com 
vserver nfs show 

export-policy create -policyname liyang -vserver nfs
export-policy rule create -vserver nfs -policyname liyang -protocol nfs3,nfs4 -clientmatch 0.0.0.0/0 -rorule any -rwrule any -superuser any

vol modify -volume nfs01 -policy liyang
vol modify -volume nfs_root -policy liyang
export-policy rule show -policyname liyang -instance
vol show -fields policy
```
