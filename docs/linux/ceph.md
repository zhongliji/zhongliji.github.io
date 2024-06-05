## config hosts
```
192.168.6.241    node1
192.168.6.242    node2
192.168.6.243    node3
```
## ssh-keygen and ssh-copy-id to node{1..3} created trust;
## config ceph local-yum on each node
```
[ceph]
name=ceph
baseurl=file:///root/ceph14
enabled=1
gpgcheck=0
```
## install ceph on each node
```
for i in {1..3};do ssh node$i yum -y install ceph;done
```
## mkdir ceph for ceph-deploy on deploy node
```
mkdir ceph
yum -y install ceph-deploy
```
## create monitor clutser
```
cd ceph
ceph-deploy new node1 node2 node3

vi ceph.conf
cluster = ceph
osd pool default size = 2
mon clock drift allowed = 2
mon clock drift warn backoff = 30
mon allow pool delete = true
cluster network = 192.168.100.0/24

ceph-deploy mon create-initial
```
## on deploy node transfer config files
```
ceph-deploy admin node{1..3}
ceph config set mon auth_allow_insecure_global_id_reclaim false
```
## add mgr cluster
```
ceph-deploy mgr create node{1..3}:mon_mgr
```
## add osd
```
for i in {1..3};do ceph-deploy disk zap node$i /dev/sdb;done
for i in {1..3};do ceph-deploy osd create --data /dev/sdb node$i;done
```
## view osd info
```
ceph osd tree
ceph osd df 
ceph df
ceph -s
ceph mon stat
systemctl restart ceph-mon.target
```
## create rbd pool
```
ceph osd pool create rbd 128 128
ceph osd pool set rbd size 2 
ceph osd pool application enable rbd rbd 
rbd create disk1 -s 100G --image-feature layering
rbd info disk1
rbd ls -l
rbd showmapped
rbd map disk1 
rbd unmap disk1
rbd rm disk1
```
## delete osd 
```
ceph osd out 1
systemctl stop ceph-osd@1  
ceph osd crush remove osd.1
ceph auth del osd.1
ceph osd rm osd.1
umount /dev/sdb1
```
## export osd crushmap
```
ceph osd getcrushmap -o map.bin
crushtool -d map.bin -o map.txt

#osd crushmap default rule type host ; one ceph node modify default rule type osd before osd create; edit map.txt with vi;

#import osd crushmap
crushtool -c map.txt -o map.bin.new
ceph osd setcrushmap -i map.bin.new
```
## set osd pool
```
ceph osd pool get rbd pg_num
ceph osd pool set rbd pg_num 256
ceph osd pool get rbd pgp_num
ceph osd pool set rbd pgp_num 256
```
## cephfs
```
ceph-deploy mds create node1 node2 node3
ceph osd pool create cephfs_data 128
ceph osd pool create cephfs_metadata 128
ceph fs new cephfs cephfs_metadata cephfs_data

ceph fs ls
ceph mds stat
```
## cephfs client
```
yum -y install ceph-fuse
cp /etc/ceph/ceph.client.admin.keyring
ceph-fuse -m 172.16.90.95:6789 /mnt/ceph

ceph-authtool -p /etc/ceph/ceph.client.admin.keyring > admin.key
mount -t ceph node11:6789:/ /mnt -o name=admin,secretfile=admin.key
echo "node11:6789:/ /mnt ceph name=admin,secretfile=/root/admin.key,noatime,_netdev 0 2" >> /etc/fstab
```
## delete ceph fs,stop all mds
```
systemctl stop ceph-mds@ceph3 

ceph fs rm cephfs --yes-i-really-mean-it
ceph osd pool delete cephfs_metadata cephfs_metadata --yes-i-really-really-mean-it
ceph osd pool delete cephfs_data cephfs_data --yes-i-really-really-mean-it
```