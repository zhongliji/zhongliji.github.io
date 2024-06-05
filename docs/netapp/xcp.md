## install xcp on linux
```
yum -y install nfs-utils
systemctl enable nfs-server
systemctl start nfs-server
tar xzvf xcp-1.6.3.tgz
./xcp activate
mv ./license /opt/Netapp/xFile/xcp
./xcp activate
./xcp show localhost
```
## set catalog
```
cat /opt/NetApp/xFile/xcp/xcp.ini
[xcp]
catalog=192.168.6.103:/vol01/xcpvol/
```
## xcp cli
```
./xcp scan 192.168.6.103:/vol01
./xcp copy -newid test 192.168.6.103:/vol_old 192.168.6.108:/vol_new
./xcp sync -id test
```
## ontap vol Qos
```
qos policy-group show
qos policy-group create -policy-group xcp -max-throughput 100MB/s
qos policy-group modify -policy-group xcp -max-throughput 50MB/s
vol modify -volume vol01 -qos-policy-group xcp
vol show -vserver nas01 -volume vol01 -fields qos-policy-group
```
