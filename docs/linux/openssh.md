## update openssh 8.6p1 fixed 安全漏洞CVE-2021-28041
## install gcc
```
yum install gcc -y
yum install -y zlib-devel openssl-devel 
yum install pam-devel libselinux-devel zlib-devel openssl-devel -y
```
## backup ssh
```
mv /etc/ssh /etc/ssh.bak
mv /usr/bin/ssh /usr/bin/ssh.bak
mv /usr/sbin/sshd /usr/sbin/sshd.bak
```
## install openssh8.6p1
```
tar -zxvf openssh-8.6p1.tar.gz
cd openssh-8.6p1
./configure --prefix=/usr --sysconfdir=/etc/ssh --with-md5-passwords --with-pam \
--with-zlib --with-tcp-wrappers --with-ssl-dir=/usr/local/ssl --without-hardening

make && make install
```
## backup and modify config
```
cp -a contrib/redhat/sshd.init  /etc/init.d/sshd
cp -a contrib/redhat/sshd.pam /etc/pam.d/sshd.pam
chmod u+x /etc/init.d/sshd
mv /usr/lib/systemd/system/sshd.service /usr/lib/systemd/system/sshd.service.bak

vim /etc/ssh/sshd_config 
PermitRootLogin yes
PasswordAuthentication yes
UseDNS no
UsePAM yes
```
## start sshd
```
systemctl daemon-reload
systemctl restart sshd

ssh -V
```