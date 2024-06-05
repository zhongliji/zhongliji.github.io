# install pacemaker
```
cat >> /etc/hosts << EOF
192.168.80.19 pcs1
192.168.80.20 pcs2
EOF

ssh-keygen
ssh-copy-id
```
# on each nodes
```
yum -y install pcs fence-agents-all lvm2-cluster gfs2-utils
systemctl enable pcsd
systemctl start pcsd
echo "liyang" | passwd --stdin hacluster
```
# on a node
```
pcs cluster auth pcs1 pcs2 -u hacluster -p liyang
pcs cluster setup --name liyang pcs1 pcs2
pcs cluster start --all
pcs cluster enable --all
pcs property set stonith-enabled=true

pcs status cluster
pcs status corosync
```
# enable cluster lock on each nodes
```
lvmconf --enable-cluster
lvmconf -h
lvmconfig |grep type
reboot
```
# stonith change to "ignore", it does not need for 2 nodes cluster
```
pcs property set no-quorum-policy=ignore
```
# disable auto failbask
```
pcs property set default-resource-stickiness="INFINITY"
```
# show settings
```
pcs property list
```
# set vip
```
pcs resource create vip ocf:heartbeat:IPaddr2 ip=192.168.80.200 cidr_netmask=24 op monitor interval=30s
pcs resource move vip pcs2
```
# show status
```
pcs status resources
pcs config show
```
# delete cluster node
```
pcs cluster standby node2
pcs cluster node remove node2
```
# add cluter node
```
pcs cluster auth node2 -u hacluster -p liyang
pcs cluster node add node2
pcs cluster start node2
pcs cluster enable node2
```
# delete cluster
```
pcs cluster stop
pcs cluster destroy
```