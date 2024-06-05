## nfs config
```
yum -y install nfs-utils

systemctl enable nfs-server
systemctl start nfs-server

cat > /etc/exports << EOF
/nfs 192.168.6.0/24(rw,no_root_squash,async)
EOF

exportfs -a
exportfs -v

showmount -e 192.168.8.224
```
