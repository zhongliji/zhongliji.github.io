## config timezone
```
rm -f /etc/localtime

cat > /etc/sysconfig/clock << EOF
Zone=Asia/Shanghai
EOF

ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

hwclock -w
```
## config ntp server
```
read -p "pls input ntp server ip: " ip
read -p "pls input allow subnet:[192.168.6.0/24] " net
rpm -ivh libseccomp.rpm  
rpm -ivh chrony.rpm

cat > /etc/chrony.conf << EOF
server $ip iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
allow $net 
local stratum 10
logdir /var/log/chrony
EOF

systemctl enable chronyd 
systemctl restart chronyd

ntp2.aliyun.com
chronyc sources
```