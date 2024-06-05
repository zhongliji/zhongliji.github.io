## config aliyun yum source
```
wget http://mirrors.aliyun.com/repo/Centos-7.repo
wget http://mirrors.aliyun.com/repo/epel.repo
```
## yum download rpm packages
```
mkdir /root/httpd
yum -y install --downloadonly --downloaddir=/root/httpd httpd
```
## install createrepo
```
yum -y install createrepo
cd /root
createrepo -pdo httpd/ httpd/ 
createrepo --update httpd/
tar czvf httpd.tar httpd
```
## config local yum source
```
cat > /etc/yum.repos.d/httpd.repo << EOF
[httpd]
name=httpd
baseurl=file:///root/httpd
enabled=1
gpgcheck=0
EOF
```
## update yum source
```
yum clean all
yum repolist
yum update
```
## Centos8
```
dnf makecache
dnf install ftp screen lrzsz 
```