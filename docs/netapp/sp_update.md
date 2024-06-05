## SP下升级
```
 ssh admin@SP_IP_address 
 sp update http://web_server_name/path/SP_FW.tar.gz
#IF New firmware is older than existing firmware
 sp update http://web_server_name/path/SP_FW.tar.gz -f
 sp status
```
 ## ontap下
```
 system node image get -package http://web_server_name/path/SP_FW.zip -replace-package true -node node_name 
 system service-processor image update-progress show -node node1
 sp show
```

## SP Internal Switch Update
```
*> priv set diag
*> sp switch-version
每个版本的sp都有对应的switch-version，需要自行去mysupport上查。下例为5.8版本
For AFF A300,FAS8200                 : Version16 SB_XXII ETHSW RZL
For AFF A200,FAS2650,FAS2620         : Version18 SB_XXII ETHSW RZM
如果版本匹配，则验证是否已应用更新
*> sp switch write 0x1c 0x9 0xc005
*> sp switch read 0x1c 0xa
如果结果输出为“ 0x87a0”，则停止。 交换机配置已更新。 否则，请继续进行交换机更新
*> sp switch-update
*> sp switch-version
*> sp switch-verify 
*> sp switch reset

*> sp switch write 0x1c 0x9 0xc005
*> sp switch read 0x1c 0xa

=>0x87a0

*> priv set admin
```