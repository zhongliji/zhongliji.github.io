## disable auto-giveback
```
storage failover modify -node * -auto-giveback false
storage failover show -node * -fields  auto-giveback
```
## Cmode HA update ontap
```
cluster image package get -url http://192.168.1.121/96P3_q_image.tgz
cluster image show
cluster image package show-repository 
cluster image update -version 9.6P3
cluster image show-update-progress
```
##  MCCIP update ontap MCCIP_A-01 and MCCIP_B-01 first
## update MCCIP_A-01
```
set adv
system node image update -node MCCIP_A-01 -package http://10.16.1.100/lnvgy_fw_storage_ontap.nonencrypted-9.8P3_anyos_noarch.tgz -setdefault true

storage failover takeover -ofnode MCCIP_A-01
storage failover giveback -ofnode MCCIP_A-01
```
## update MCCIP_B-01
```
set adv
system node image update -node MCCIP_B-01 -package http://10.16.1.100/lnvgy_fw_storage_ontap.nonencrypted-9.8P3_anyos_noarch.tgz -setdefault true

storage failover takeover -ofnode MCCIP_B-01
storage failover giveback -ofnode MCCIP_B-01
```
## autosupport collect
```
autosupport invoke -node MCCIP_A-01 -type all -uri file:///mroot/etc/log/MCCIP_A-01_20210623.7z
autosupport invoke -node MCCIP_A-02 -type all -uri file:///mroot/etc/log/MCCIP_A-02_20210623.7z

system node autosupport history show -node MCCIP_A-01
system node autosupport history show -node MCCIP_A-02

https://ip/spi/
```
