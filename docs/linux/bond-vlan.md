## ifcfg-p2p1
```
TYPE=Ethernet
BOOTPROTO=none
NAME=p2p1
DEVICE=p2p1
ONBOOT=yes
MASTER=bond0
SLAVE=yes
```
## ifcfg-p2p2
```
TYPE=Ethernet
BOOTPROTO=none
NAME=p2p2
DEVICE=p2p2
ONBOOT=yes
MASTER=bond0
SLAVE=yes
```
## ifcfg-bond0
```
TYPE=Bond
BOOTPROTO=none
NAME=bond0
DEVICE=bond0
ONBOOT=yes
BONDING_MASTER=yes
BONDING_OPTS="mode=4 miimon=100"
```
## ifcfg-bond.10
```
BOOTPROTO=none
DEVICE=bond0.10
ONBOOT=yes
IPADDR=192.168.200.101
PREFIX=24
VLAN=yes
```