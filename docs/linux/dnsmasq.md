# install dnsmasq
```
yum -y install dnsmasq
```
# global config
```
cat > /etc/dnsmasq.conf << EOF
all-servers
resolv-file=/etc/dnsmasq_resolv.conf
no-dhcp-interface=
no-hosts
cache-size=10000
log-queries
log-facility=/var/log/dnsmasq.log
conf-dir=/etc/dnsmasq.d
EOF
```
# config upstream dns-server
```
cat > /etc/dnsmasq_resolv.conf << EOF
nameserver 61.139.2.69
nameserver 8.8.8.8
EOF
```
# config A records PTR records
```
cat > /etc/dnsmasq.d/address.conf << EOF
#A records
address=/c03/192.168.20.143
address=/c04/192.168.20.144
address=/c05/192.168.20.151

#PTR records
ptr-record=143.20.168.192.in-addr.arpa,c03
ptr-record=144.20.168.192.in-addr.arpa,c04
ptr-record=151.20.168.192.in-addr.arpa,c05
EOF
```
# dnsmasq syntax check
```
dnsmasq --test
systemctl enable dnsmasq
systemctl start dnsmasq
```
# client check
```
yum -y install bind-utils

cat > /etc/resolv.conf << EOF
nameserver 192.168.20.143
EOF

dig c04 @192.168.20.143
dig 144.20.168.192.in-addr.arpa ptr @192.168.20.143
```
# config dhcp and tftp boot ipxe with dnsmasq
```
cat > /etc/dnsmasq.conf << EOF
# disable DNS server
port=0

# listen on PXEBOOT vlan (vlan110) only
listen-address=192.168.20.143
interface=eth0

# enable built-in tftp server
enable-tftp
tftp-root=/tftpboot

# DHCP range 192.168.20.180 ~ 192.168.20.190
dhcp-range=192.168.20.180,192.168.20.190,255.255.255.0,24h

# Default gateway
dhcp-option=3,192.168.20.1

# Broadcast address
dhcp-option=28,192.168.20.255

# Tag dhcp request from iPXE
dhcp-match=set:ipxe,175

# 1st boot file - Legacy BIOS client
dhcp-boot=tag:!ipxe,tag:BIOS,ipxe.kpxe,192.168.20.143

# 2nd boot menu file
dhcp-boot=tag:ipxe,linux.ipxe
EOF
```
# config web service with apache
```
yum -y install httpd
sed -i 's/\/var\/www\/html/\/tftpboot/g' /etc/httpd/conf/httpd.conf
systemctl enable httpd
systemctl start httpd
```