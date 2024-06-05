# centos7 net script
```
read -p "interface name:" name
read -p "ip addr:" addr
read -p "gateway:" gateway
read -p "dns:" dns

cat > /etc/sysconfig/network-scripts/ifcfg-$name << EOF
TYPE=Ethernet
BOOTPROTO=static
NAME=$name
DEVICE=$name
ONBOOT=yes
IPADDR=$addr
PREFIX=24
GATEWAY=$gateway
DNS1=$dns
EOF

systemctl restart network
```