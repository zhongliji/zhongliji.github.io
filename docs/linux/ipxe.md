# install service
```
yum -y install dhcp tftp-server xinetd httpd samba
```
# configure samba
```
useradd liyang
mkdir /share

(echo "liyang"; echo "liyang") | smbpasswd -s -a liyang
chown -R liyang /share
chmod 775 /share
cat > /etc/samba/smb.conf << EOF
[global]
        workgroup = WORKGROUP
        server string = Samba Server Version %v
        security = user
        username map = /etc/samba/smbusers
        display charset = cp936
        unix charset = cp936
        dos charset = cp936
[ipxe]
        comment = smb
        path = /share
        valid users = +liyang
        write list = +liyang
EOF
cat > /etc/samba/smbusers << EOF
liyang = admin
EOF
```
# configure httpd
```
sed -i 's/\/var\/www\/html/\/share/g' /etc/httpd/conf/httpd.conf
```
# configure dhcp
```
cat > /etc/dhcp/dhcpd.conf << EOF
```
# DHCP Server Configuration file.
```
option domain-name "sog.org";
option domain-name-servers 61.139.2.69;
default-lease-time 600;
max-lease-time 7200;
subnet 192.168.30.0  netmask 255.255.255.0 {
  range 192.168.30.150 192.168.30.180;
  option subnet-mask 255.255.255.0;
  option routers 192.168.30.1;
  next-server 192.168.30.111;
if exists user-class and option user-class = "iPXE" {
  filename="http://192.168.30.111/linux.ipxe";
} else {
  filename="ipxe.kpxe";
}
}
EOF
```
# configure tftp-server
```
cat > /etc/xinetd.d/tftp << EOF
service tftp
{
        socket_type             = dgram
        protocol                = udp
        wait                    = yes
        user                    = root
        server                  = /usr/sbin/in.tftpd
        server_args             = -s /share -c
        disable                 = no
        per_source              = 11
        cps                     = 100 2
        flags                   = IPv4
}
EOF
```
# enable and start service
```
systemctl enable smb
systemctl enable httpd
systemctl enable dhcpd
systemctl enable xinetd
systemctl start smb
systemctl start httpd
systemctl start dhcpd
systemctl start xinetd
```
# check tcp/udp port
```
ss -nlpt |grep 80
ss -nlpt |grep 139
ss -nlpt |grep 445
ss -nlpu |grep 67
ss -nlpu |grep 69
```