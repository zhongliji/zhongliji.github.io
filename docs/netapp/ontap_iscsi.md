## major setup
```
1. aggr create
2. svm create
3. vol create
4. lif create
5. lun create
6. iscsi create 
7. lun igroup create
8. lun igroup add host initiator
9. lun map to igroup
 
liyang::> aggr create -aggregate aggr2 -diskcount 7 -raidtype raid_dp -maxraidsize 7 -node ly-01
liyang::> vserver create -vserver iscsi 
liyang::> net int create -vserver iscsi -lif iscsi -home-node liyang-01 -home-port e0c -role data -data-protocol iscsi -address 192.168.8.45 -netmask 255.255.255.0
liyang::> net int create -vserver iscsi -lif iscsi1 -home-node liyang-02 -home-port e0c -role data -data-protocol iscsi -address 192.168.8.46 -netmask 255.255.255.0
liyang::> vol create -vserver iscsi -volume iscsi01 -size 100g -aggregate liyang01 -junction-path /iscsi
liyang::> lun create -vserver iscsi -volume iscsi01 -lun lun1 -size 80g -ostype windows

liyang::> iscsi create -vserver iscsi -target-alias netapp9
liyang::> lun igroup create -vserver iscsi -igroup li -protocol iscsi -ostype windows
liyang::> lun igroup add -vserver iscsi -igroup li -initiator iqn.1991-05.com.microsoft:2008
liyang::> lun map -vserver iscsi -path /vol/iscsi01/lun1 -igroup li

liyang::> igroup show
liyang::> iscsi show
liyang::> iscsi interface show
liyang::> lun show
Liyang::>lun mapping show
```
