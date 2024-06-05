## 安装需求
```
OpenSSL 1.0.2到3.0版、用于基本验证
适用于时间戳颁发机构(TSA)操作的OpenSSL 1.1.0或更高版本
用于OCSP验证的公有 Internet访问
```
# 安装openssl 依赖包
```
yum -y install perl gcc gcc-c++ glibc make zlib
```
# 编译安装
```
tar xzvf openssl-1.1.1k.tar.gz
cd openssl-1.1.1k.tar.gz
./config --prefix=/usr/local/openssl
make && make install
```
# 移除旧版本，建立新版本软链接
```
rm -rf /usr/bin/openssl
ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
ldconfig -v
openssl version
```
# copy认证
```
openssl version -d
cp /etc/pki/tls/cert.pem /usr/local/openssl/ssl
```
## 安装mediator1.5
#  添加 C7.6.1810 —基本存储库。C7.6.1810 —基础存储库包含 ONTAP 调解器所需的 kernel-devel 软件包
```
vi /etc/yum.repos.d/Centos-vault.repo
[C7.6.1810-base]
name=CentOS-7.6.1810 - Base
baseurl=http://vault.centos.org/7.6.1810/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
enabled=1

yum repolist
```
#  tar xzvf ontap-mediator-1.5.0.tgz
#  查找用于注册证书的OCSP URL
```
openssl x509 -noout -ocsp_uri -in csc-prod-chain-ONTAP-Mediator.pem
```
#  生成证书的OCSP请求
```
openssl ocsp -issuer csc-prod-chain-ONTAP-Mediator.pem -CAfile csc-prod-chain-ONTAP-Mediator.pem -cert csc-prod-ONTAP-Mediator.pem -reqout req.der
```
#  连接到OCSP Manager以发送OCSP请求
```
openssl ocsp -issuer csc-prod-chain-ONTAP-Mediator.pem -CAfile csc-prod-chain-ONTAP-Mediator.pem -cert csc-prod-ONTAP-Mediator.pem -url http://ocsp.entrust.net -resp_text -respout resp.der
```
#  验证CSC的信任链以及本地主机的到期日期
```
openssl verify -untrusted csc-prod-chain-ONTAP-Mediator.pem -CApath /etc/pki/tls csc-prod-ONTAP-Mediator.pem

openssl verify -untrusted tsa-prod-chain-ONTAP-Mediator.pem -CApath /etc/pki/tls tsa-prod-ONTAP-Mediator.pem
```
#  验证 ontap-mediator-1.5.0.sig.tsr 和 ontap-mediator-1.5.0.tsr 使用关联证书的文件
```
openssl ts -verify -data ontap-mediator-1.5.0.sig -in ontap-mediator-1.5.0.sig.tsr -CAfile tsa-prod-chain-ONTAP-Mediator.pem -untrusted tsa-prod-ONTAP-Mediator.pem

openssl ts -verify -data ontap-mediator-1.5.0 -in ontap-mediator-1.5.0.tsr -CAfile tsa-prod-chain-ONTAP-Mediator.pem -untrusted tsa-prod-ONTAP-Mediator.pem
```
#  根据密钥验证签名
```
openssl dgst -sha256 -verify ONTAP-Mediator-production.pub -signature ontap-mediator-1.5.0.sig ontap-mediator-1.5.0
```
#  安装mediator
```
./ontap-mediator-1.5.0 -y
systemctl status ontap_mediator
systemctl enable ontap_mediator
```
## mediator账号为mediatoradmin
