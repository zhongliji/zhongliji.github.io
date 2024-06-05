## Custom CentOS 8.5
```
yum -y install rsync genisoimage createrepo

mount CentOS-8.5.2111-x86_64-dvd1.iso /mnt
cd /root/
mkdir iso
cd iso

rsync -av --progress /mnt/ /root/iso/ --exclude BaseOS --exclude AppStream
mkdir -p BaseOS/Packages
mkdir -p AppStream/Packages

rpm -qa --queryformat %{NAME}-%{VERSION}-%{RELEASE}.%{ARCH}'\n' > rpms.txt

#!/bin/bash

RPM_FILE="/root/rpms.txt"
for pkg in `cat $RPM_FILE`; do
    RPM=`find /mnt/ -name $pkg*.rpm`
    if [[ $RPM =~ .*BaseOS.* ]];then
       cp -ap $RPM /root/iso/BaseOS/Packages/
    elif [[ $RPM =~ .*AppStream.* ]];then
       cp -ap $RPM /root/iso/AppStream/Packages/
    else
       echo "$pkg not found"
    fi
done

cp /mnt/BaseOS/repodata/*comps*.xml BaseOS/comps_base.xml
cp /mnt/AppStream/repodata/*comps*.xml AppStream/comps_app.xml
createrepo -g comps_app.xml AppStream/
createrepo -g comps_base.xml BaseOS/

cp /mnt/AppStream/repodata/*modules.yaml* AppStream/
cd AppStream/
gunzip f5feca6db2e28a166c0c701c124169c0c4c93165429797c78d25daaa202ff289-modules.yaml.gz
cp f5feca6db2e28a166c0c701c124169c0c4c93165429797c78d25daaa202ff289-modules.yaml modules.yaml
cd ..
modifyrepo_c --mdtype=modules AppStream/modules.yaml AppStream/repodata/

cd isolinux/
chmod 755 isolinux.cfg
vi isolinux.cfg
label linux
  menu label ^Install CentOS 8 by LiYang
  kernel vmlinuz
  append initrd=initrd.img inst.repo=cdrom ks=cdrom:/c8.cfg quiet

cd ..
mkisofs -o /root/c85custom.iso -b isolinux/isolinux.bin -c isolinux/boot.cat --no-emul-boot --boot-load-size 4 --boot-info-table -J -R -V "CentOS-8-liyang" .
```
## c8.cfg
```
#version=DEVEL
# System authorization information
auth --enableshadow --passalgo=sha512
# Use USB installation media
# harddrive --partition=/dev/sdb1 --dir=/c85
url --url=http://192.168.6.171/
# Use graphical install
#graphical
text
# Run the Setup Agent on first boot
firstboot --enable
# Keyboard layouts
keyboard --vckeymap=us --xlayouts='us'
# System language
lang en_US.UTF-8

# Network information
network  --hostname=c85
network  --bootproto=dhcp --device=eno1 --onboot=on
repo --name=epel --baseurl=https://mirrors.aliyun.com/epel/8/Everything/x86_64/
# Root password
rootpw liyang

firewall --disabled
selinux --disabled
xconfig --startxonboot

# System timezone
timezone Asia/Shanghai --isUtc --nontp
# System bootloader configuration
bootloader --location=mbr --boot-drive=sda
#autopart --type=lvm
# Partition clearing information
clearpart --all --initlabel --drives=sda
part /boot/efi --fstype efi --size=250 --ondisk sda
part /boot --fstype xfs --size=250 --ondisk sda
part pv.01 --size 2000 --grow --ondisk sda

volgroup vg1 pv.01

logvol / --vgname=vg1 --size=100000 --name=lv1
logvol swap --vgname=vg1 --size=8000 --name=swap --fstype=swap

reboot
%pre
wget -O perccli http://192.168.6.171/perccli
chmod 755 perccli
#./perccli /c0 /v0 del force
#./perccli /c0 add vd r1 drives=32:0-1
%end

%packages
@core
ftp
wget
%end

%post

cat > /root/net8 << EOW
read -p "interface name:" name
read -p "ip addr:" addr
read -p "gateway:" gateway
read -p "dns:" dns

cat > /etc/sysconfig/network-scripts/ifcfg-\$name << EOF
TYPE=Ethernet
BOOTPROTO=static
NAME=\$name
DEVICE=\$name
ONBOOT=yes
IPADDR=\$addr
PREFIX=24
GATEWAY=\$gateway
DNS1=\$dns
EOF

systemctl restart network
EOW

cat > /root/bond8 << EOW
read -p "bond name:" name
read -p "input first interface:" int1
read -p "input second interface:" int2
read -p "input bond type:" type
read -p "ip addr:" addr

modprobe --first-time bonding

cat > /etc/sysconfig/network-scripts/ifcfg-\$name << EOF
TYPE=Bond
BOOTPROTO=none
NAME=\$name
DEVICE=\$name
ONBOOT=yes
IPADDR=\$addr
PREFIX=24
BONDING_MASTER=yes
BONDING_OPTS="mode=\$type miimon=100"
EOF

cat > /etc/sysconfig/network-scripts/ifcfg-\$int1 << EOF
TYPE=Ethernet
BOOTPROTO=none
NAME=\$int1
DEVICE=\$int1
ONBOOT=yes
MASTER=\$name
SLAVE=yes
EOF

cat > /etc/sysconfig/network-scripts/ifcfg-\$int2 << EOF
TYPE=Ethernet
BOOTPROTO=none
NAME=\$int2
DEVICE=\$int2
ONBOOT=yes
MASTER=\$name
SLAVE=yes
EOF
EOW

rm -f /etc/yum.repos.d/*.repo
cat > /etc/yum.repos.d/c8.repo << EOF
[BaseOS]
name=BaseOS
baseurl=http://192.168.6.171/BaseOS/
gpgcheck=0
enabled=1
[AppStream]
name=AppStream
baseurl=http://192.168.6.171/AppStream/
gpgcheck=0
enabled=1
[epel]
name=epel
baseurl=https://mirrors.aliyun.com/epel/8/Everything/x86_64/
gpgcheck=0
enabled=1
EOF
%end
```