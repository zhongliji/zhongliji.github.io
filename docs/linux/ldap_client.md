# 安装软件
```
yum install -y nss-pam* authconfig-gtk openldap-clients
```
# 加入ldap域
```
ldapsearch -H ldap://192.168.20.47 -x -b "cn=admin,dc=hex,dc=com"
authconfig --enableldap --enableldapauth --ldapserver=192.168.20.47 --ldapbasedn="dc=hex,dc=com" --enablemkhomedir --update
```