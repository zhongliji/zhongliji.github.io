## major setup
```
1. aggr create
2. svm create
3. volume create
4. data lif create
5. cifs create
6. cifs share create
7. cifs user create
8. cifs share access-control create
9. volume modify security-style ntfs
```
## cifs config
```
liyang::> aggr create -aggregate shlab3_02_aggr1 -diskcount 7 -raidtype raid_dp -maxraidsize 7 -node shlab3-02
liyang::> vserver create -vserver cifs -rootvolume root1-aggregate liyang01 -rootvolume-security-style unix
liyang::> vol create -vserver cifs -volume vol1 -size 100g -aggregate liyang01 -junction-path /cifs
liyang::> net int create -vserver cifs -lif cifs -home-node liyang-01 -home-port e0c -role data -data-protocol nfs,cifs -address 192.168.8.44 -netmask 255.255.255.0

liyang::> vserver cifs create -cifs-server nas_cifs -workgroup WORGROUP  #command cifs
liyang::> vserver cifs create -cifs-server nas_cifs -domain remotelab.net -ou CN=Computers
liyang::> vserver cifs share create -share-name samli008 -path /cifs  #command cifs->share
liyang::> vserver cifs users-and-groups local-user create -user-name liyang -is-account-disabled false -vserver cifs
liyang::> vserver cifs share access-control create -vserver cifsli -share samli008 -user-or-group "liyang" -permission Full_Control 
shlab2::> vol modify  -volume cifs_vol -security-style ntfs
shlab2::> vol show -volume cifs_vol -fields security-style
```