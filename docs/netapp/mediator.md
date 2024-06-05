## 关闭防火墙和selinux 
```
systemctl stop firewalld
systemctl disable firewalld
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
```

## copy iso to linux 
```
mount -o loop /root/CentOS-7-x86_64-DVD-1810.iso /media
虚拟光驱挂载iso
mount /dev/cdrom /media
df -hT
```

## 配置yum源 
```
vi /etc/yum.repo.d/media.repo
[media]
name=media
baseurl=file:///media
gpgcheck=0
enable=1

vi /etc/yum.repo.d/python.repo
[python]
name=python
baseurl=file:///root/root/python
gpgcheck=0
enable=1

yum clean all
yum makecache
```

## 安装python和mediator
```
yum install python36
yum install python36-devel

./ONTAP-MEDIATOR-1.1

systemctl status mediator-scst.service
```
## mediator配置 
```
metrocluster configuration-settings mediator add -mediatoraddress ip-address-of-mediator-host
metrocluster configuration-settings mediator remove
mediator_change_password
mediator_change_user
uninstall_ontap_mediator
```

## 前后对比 
```
MCCIP_A::> metrocluster show
Configuration: IP-fabric
Cluster                        Entry Name             State
------------------------------ ---------------------- ---------------------
Local: MCCIP_A
                                 Configuration State  configured
                                                Mode  normal
                                 AUSO Failure Domain  auso-disabled
Remote: MCCIP_B
                                 Configuration State  configured
                                                Mode  normal
                                 AUSO Failure Domain  auso-disabled
                                 
MCCIP_A::> metrocluster show
Configuration: IP-fabric
Cluster                        Entry Name             State
------------------------------ ---------------------- ---------------------
Local: MCCIP_A
                                 Configuration State  configured
                                                Mode  normal
                                 AUSO Failure Domain  auso-on-cluster-disaster
Remote: MCCIP_B
                                 Configuration State  configured
                                                Mode  normal
                                 AUSO Failure Domain  auso-on-cluster-disaster
```