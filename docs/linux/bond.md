# bond7
```
read -p "bond name:" name
read -p "input first interface:" int1
read -p "input second interface:" int2
read -p "input bond type:" type
read -p "ip addr:" addr

modprobe --first-time bonding

cat > /etc/sysconfig/network-scripts/ifcfg-$name << EOF
TYPE=Bond
BOOTPROTO=none
NAME=$name
DEVICE=$name
ONBOOT=yes
IPADDR=$addr
PREFIX=24
BONDING_MASTER=yes
BONDING_OPTS="mode=$type miimon=100"
EOF

cat > /etc/sysconfig/network-scripts/ifcfg-$int1 << EOF
TYPE=Ethernet
BOOTPROTO=none
NAME=$int1
DEVICE=$int1
ONBOOT=yes
MASTER=$name
SLAVE=yes
EOF

cat > /etc/sysconfig/network-scripts/ifcfg-$int2 << EOF
TYPE=Ethernet
BOOTPROTO=none
NAME=$int2
DEVICE=$int2
ONBOOT=yes
MASTER=$name
SLAVE=yes
EOF
```