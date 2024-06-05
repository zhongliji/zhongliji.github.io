## goto view mode  
```
sys
sysname
display current-configuration
interface gi 1/0/1
interface ten 1/0/1
dis this
dis int brief
undo shutdown 
```
## user config
```
dis local-user
local-user admin class manage
password simple admin
service-type telnet http
authorization-attribute user-role level-15
authorization-attribute user-role guest-manager
authorization-attribute user-role network-operator

line vty 0 63
authentication-mode schema
user-role network-operator

telnet server enable
ip http enable
dhcp enable
dhcp relay client-information record
```
## vlan config
```
vlan 10 
int vlan 10
ip add 192.168.8.1 255.255.255.0
dis ip route
dis vlan

interface range g1/0/1 to g1/0/2 
port link-type access
port access vlan 10

interface g1/0/48 
port link-type trunk
port trunk permit vlan all
```
## link-aggregation
```
int Bridge-Aggregation 1
port link-type trunk
port trunk permit vlan all
link-aggregation mode dynamic

int range GigabitEthernet 1/0/47 to GigabitEthernet 1/0/48
port link-type trunk
port trunk permit vlan all
port link-aggregation group 1
```