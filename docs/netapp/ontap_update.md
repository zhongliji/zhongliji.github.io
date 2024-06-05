## 升级ontap之前，需先检查disk firmware，shelf firmware，DQP，若不符合则需进行升级
## updata disk firmware
```
storage disk option modify -node node_name -bkg-firmware-update on
set –privilege advanced
storage firmware download –node * –package-url http://<web-server>/path/all.zip
storage disk show -fields firmware-revision
storage disk firmware show-update-status
```

## update DQP
```
node run -node <nodename> -command rdfile /etc/qual_devices_v3
set –privilege advanced
storage firmware download –node * -package-url http://<web-server>/path/qual_devices.zip
node run -node <nodename> -command rdfile /etc/qual_devices_v3
```

## updata shelf firmware
```
storage firmware download -node * -package-url http://<web-server>/path/all_shelf_fw.zip
```

## check cluster health
```
cluster show
set adv
cluster ring show -unitname vldb
cluster ring show -unitname mgmt
cluster ring show -unitname vifmgr
storage aggregate show -stat !online
volume show -state !online
storage disk show -state broken
storage disk show -state maintenance|pending|
storage disk show -state maintenance|reconstructing|
vserver nfs show 
vserver cifs show 
vserver fcp show 
vserver iscsi show
network interface show -status-oper down 
network interface show -is-home false
cluster identity show
network interface show -role data –failover
network interface modify -vserver vserver_name –lif LIF_name -home-node first_node_to_upgrade -home-port port -auto-revert true
system services ntp server show
system node date show
system node image show
run -node * sysstat -x   #CPU使用超过50%，转离线方式升级
```

## upload image
```
system node image update -node * -package http://* -setdefault true
system node image show
storage failover show
storage failover show –instance
storage failover show -node * -field auto-giveback
storage failover modify -node * -auto-giveback false
system node autosupport invoke -type all -node <nodename> -message "Upgrading to 9.1P20"
```

## start to updata
```
network interface migrate-all -node <nodename>
storage failover takeover -ofnode <nodenameB>
storage failover giveback -ofnode <nodenameB>  
```

## check
```
storage failover show
system node image show
system node upgrade-revert show  
network  interface show -is-home false 
network  interface  revert -vserver * -lif *
Event log show
```

## Second node repeat go to start to updata