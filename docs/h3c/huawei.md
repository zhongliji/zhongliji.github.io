## eNSP reset config
```
<sw>reset saved-configuration
<sw>reboot # answer: n and y
```
## vlan trunk port-channel config on switch
```
undo info enable
sys
quit
save

di current-config
di int brief
di vlan

int vlan 10
undo vlan 10
int vlan 10
ip ad 192.168.10.1

int g0/0/1
port link-type access
port default vlan 10
di this

int g0/0/2
port link-type trunk
port trunk allow-pass vlan 10 20
di this

int eth-trunk 1
trunkport g0/0/3
trunkport g0/0/4
port link-type trunk
port trunk allow-pass vlan 10 20
di this

int g0/0/3
eth-trunk 1
int g0/0/4
eth-trunk 1
int eth-trunk 1
port link-type trunk
port trunk allow-pass vlan 10 20
di this
```
## vrrp config on switch1
```
interface vlanif 10
ip address 192.168.10.253 255.255.255.0
vrrp vrid 10 virtual-ip 192.168.10.1

interface Vlanif 20
ip address 192.168.20.253 255.255.255.0
vrrp vrid 20 virtual-ip 192.168.20.1
```
## vrrp config on switch2
```
interface Vlanif 10
ip address 192.168.10.254 255.255.255.0
vrrp vrid 10 virtual-ip 192.168.10.1

interface Vlanif 20
ip address 192.168.20.254 255.255.255.0
vrrp vrid 20 virtual-ip 192.168.20.1

di vrrp brief
di ip int brief
```
## route-static config on router1
```
di ip int brief
di ip routing-table

int g0/0/0
ip ad 192.168.11.1 24
int g0/0/1
ip ad 192.168.1.1 24
ip route-static 192.168.2.0 24 192.168.11.2
ip route-static 192.168.12.0 24 192.168.11.2
```
## ospf config on router1
```
int g0/0/0
ip ad 192.168.11.1 24

int g0/0/1
ip ad 192.168.1.1 24

ospf 1 router-id 1.1.1.1
area 0
network 192.168.11.0 0.0.0.255
network 192.168.1.0 0.0.0.255
```
## ospf config on router2
```
int g0/0/0
ip ad 192.168.11.2 24

int g0/0/1
ip ad 192.168.12.2 24

ospf 1 router-id 2.2.2.2

area 0
network 192.168.11.0 0.0.0.255
area 1
network 192.168.12.0 0.0.0.255
```
## ospf config on router3
```
int g0/0/0
ip ad 192.168.12.1 24

int g0/0/1
ip ad 192.168.2.1 24

ospf 1 router-id 3.3.3.3
area 1
network 192.168.12.0 0.0.0.255
network 192.168.2.0 0.0.0.255
```
## view ospf
```
dis ospf peer 
dis ip routing-table
```
