# bridge script
```
read -p "please input add br name: " br

cat > /etc/sysconfig/network-scripts/ifcfg-$br << EOF
DEVICE=$br
TYPE=Bridge
ONBOOT=yes
BOOTPROTO=none
IPADDR=192.168.50.1
PREFIX=24
EOF

ifup $br
brctl show |grep $br