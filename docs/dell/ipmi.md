## ipmi for linux
```
yum -y install ipmitool
ipmitool -H 192.168.6.11 -I lanplus -U root -P calvin power status
ipmitool -H 192.168.6.11 -I lanplus -U root -P calvin power down
ipmitool -H 192.168.6.11 -I lanplus -U root -P calvin power up
```
## ipmi for windows
```
ipmish -ip 192.168.6.6 -u root -p calvin power status
ipmish -ip 192.168.6.6 -u root -p calvin power on
ipmish -ip 192.168.6.6 -u root -p calvin power off
ipmish -ip 192.168.6.6 -u root -p calvin power cycle 
```
## ipmi for ssh idrac racadm
```
set idrac.ipmilan.enable 1 
get idrac.ipmilan.enable

serveraction poweron
serveraction powerstatus
serveraction powerdown

set idrac.vncserver.enable 1 
set idrac.vncserver.password liyang
set idrac.vncserver.port 5921  #default 5901

get bios.procsettings.procvirtualization
set bios.procsettings.procvirtualization Disabled
set bios.procsettings.procvirtualization Enabled
```
