## racadm cli
```
racadm>>set idrac.ipmilan.enable 1 

racadm>>get idrac.ipmilan

racadm>>serveraction poweron #powerdown powerstatus

racadm set idrac.vncserver.enable 1 

racadm set idrac.vncserver.password liyang

racadm set idrac.vncserver.port 5921  #default 5901

racadm get bios.procsettings.procvirtualization

set bios.procsettings.procvirtualization Disabled

set bios.procsettings.procvirtualization Enabled

racadm get BIOS.SysSecurity.AcPwrRcvry

racadm set BIOS.SysSecurity.AcPwrRcvry Last|On|Off

help iDRAC.serverboot.FirstBootDevice

set iDRAC.serverboot.FirstBootDevice PXE|VCD-DVD|F11|BIOS|Normal

get iDRAC.serverboot.FirstBootDevice

help remoteimage

remoteimage -s

remoteimage -d

racadm remoteimage -c -u "user" -p "pass" -l //shrloc/foo.iso

racadm set BIOS.BiosBootSettings.BootSeq HardDisk.List.1-2,NIC.Integrated.1-1-1,NIC.Integrated.1-2-1,NIC.Integrated.1-3-1,NIC.Integrated.1-4-1

racadm set bios.biosBootSettings.hddseq Disk.USBBack.2-1,RAID.Integrated.1-1,Disk.vFlash.KVM730-1

racadm getsysinfo
```