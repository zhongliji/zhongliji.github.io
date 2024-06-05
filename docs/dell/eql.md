## EQ log
```
1．ssh grpadmin@groupIP；
2．diag
3. field-unpack-x0.6.exe  *.dgo # ftp get to windows client
4. unpacked-diags.txt
```
## EQ network
```
grpparams def-mgmt-gateway ipaddress 172.16.253.254  （EQL管理网口网关配置）
grpparams dcb disable
manager router 0.0.0.0 0.0.0.0 172.16.253.254 （S6000管理网口网关配置）
```
## EQ cli with root
```
user:root passwd:grpadmin
update.sh -override-downgrade    #downgrade FW
raidtool   #view raid policy
raidtool -H 6 -f   #force spare set
raidtool -E 0 -c "10" -p 5 #0:lun 10:disk slot 5:raid level
raidtool -W 0 #clear lost block
raidtool -w 0 #list lost block
diskview -j   #view disk block

disktool -3 Turnonpdtest
disktool -r 12    #remove 12 disk
pdtest -z 12	    #清除prem/fail的信息
disktool -a 12    #add 12 disk

pm mem     #view member

pm vol     #view volume

eqlinit status  #view service demo

eqlinit restart netmgtd   #restart management network

ifconfig -a    #view network

ifconfig -l    #view network

cord -g   #change active controller

/sbin/PSSDiag.sh   #trigger log

cd /mgtdb/update

cli-settings events off

ls 

pwd

rm

clearlostdata

收集EQL dump文件：

group>su ex sh

group#ftp ftpServerIP

ftp> hash 512k
Hash mark printing on (524288 bytes/hash mark).
ftp> bin
200 Type set to I
ftp> put |"cdtool -xr" crashfile     #特别注意竖线左边有空格右边无空格；
```