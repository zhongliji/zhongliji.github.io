## install perccli on linux
```
rpm -ivh perccli-1.17.10-1.noarch.rpm
ln -sf /opt/MegaRAID/perccli/perccli64 /usr/bin/perccli
```

## install perccli on esxi6.x
```
esxcli software vib install -v /vmfs/volumes/datastore1/vmware-perccli_007.1020.vib 
cd /opt/lsi/perccli
./perccli /c0 add vd r5 size=500gb name=data drives=32:0,32:1,32:2
```

## Commands Description
```
add        Adds/creates a new element to controller like VD,Spare..etc
delete     Deletes an element like VD,Spare
show       Displays information about an element
set        Set a particular value to a property 
get        Get a particular value to a property 
/cx        Controller specific commands
/ex        Enclosure specific commands
/sx        Slot/PD specific commands
/vx        Virtual drive specific commands
/dx        Disk group specific commands
/fall      Foreign configuration specific commands
/px        Phy specific commands
```

## raid controller info
```
perccli /c0 show
```
## pd info
```
perccli /c0/eall/sall show
perccli /c0/e64/s0 show
perccli /c0/e64/s0 set good [force]
perccli /c0/e64/s0 set online
perccli /c0/e64/s0 set offline
perccli /c0/e64/s0 set jbod
```

## vd info
```
perccli /c0/vall show
perccli /c0/v0 show
perccli /c0/v1 show
perccli /c0 add vd r5 size=all drives=32:01,32:02,32:03
perccli /c0 add vd r5 size=8tb name=data drives=32:01,32:02,32:03
perccli /c0 /v1 del
```

## add hotsparedrive
```
perccli /c0/e64/s23 add hotsparedrive
perccli /c0/e64/s23 delete hotsparedrive
```

## foreign info
```
perccli /c0/fall show 
perccli /c0/fall import
perccli /c0/fall delete
```
# clear vd0 cache
```
perccli /c0 show preservedcache
perccli /c0/v0 delete preservedcache
```

## ttylog
```
perccli /c0 show termlog
```

## add vd with python
```
log = open("raid",'w')

raid=[]
for i in range(24):
  raid.append("64"+":"+str(i))

word=""
for i in range(len(raid)):
  word=word+(raid[i]+',')
word=word.strip(",")
log.write("disk="+word+'\n')
log.write("perccli /c0 add vd r6 size=all name=data drives=$disk")

log.close()
```