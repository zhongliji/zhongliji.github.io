## 安装工具包
```
yum install -y openldap openldap-clients openldap-servers migrationtools openldap-devel compat-openldap 
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
chown ldap. /var/lib/ldap/DB_CONFIG
systemctl start slapd
systemctl enable slapd
ss -nlpt |grep 389
```
## 配置ldap服务
# 首先要设置ldap的管理员密码。生成经处理后的明文密码(这里明文密码"123456") 其中 {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx 就是加密处理后的明文密码，之后会用到这个密码。
```
slappasswd -s 123456
{SSHA}wfYgDRZdRyxTuwVg1bzpXjjgAWGNYeN3
```
# 导入chrootpw.ldif文件
```
cd /etc/openldap/

vim chrootpw.ldif

//specify the password generated above for "olcRootPW" section
dn: olcDatabase={0}config,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}wfYgDRZdRyxTuwVg1bzpXjjgAWGNYeN3  *你自己的密码

ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif
```
# 导入基本Schema模式
```
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```
# 导入chdomain.ldif文件，这里我使用的域名是hex.com
```
vim chdomain.ldif

//replace to your own domain name for "dc=***,dc=***" section
//specify the password generated above for "olcRootPW" section
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth" read by dn.base="cn=admin,dc=hex,dc=com" read by * none

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=hex,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=admin,dc=hex,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA} wfYgDRZdRyxTuwVg1bzpXjjgAWGNYeN3   *你自己的密码

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {0}to attrs=userPassword,shadowLastChange by 
dn="cn=admin,dc=hex,dc=com" write by anonymous auth by self write by * none
olcAccess: {1}to dn.base="" by * read
olcAccess: {2}to * by dn="cn=admin,dc=hex,dc=com" write by * read

ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif
```
# 导入basedomain.ldif文件
```
vim basedomain.ldif

//replace to your own domain name for "dc=***,dc=***" section
dn: dc=hex,dc=com
objectClass: top
objectClass: dcObject
objectclass: organization
o: com
dc: hex

dn: cn=admin,dc=hex,dc=com
objectClass: organizationalRole
cn: Manager
description: Directory Manager

dn: ou=People,dc=hex,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=hex,dc=com
objectClass: organizationalUnit
ou: Group

导入时需输入之前配置的管理员密码
ldapadd -x -D cn=admin,dc=hex,dc=com -W -f basedomain.ldif
```
## 添加一个ldap用户(如下，添加用户alex)
```
vim ldapuser.ldif

//create new
//replace to your own domain name for "dc=***,dc=***" section
dn: uid=alex,ou=People,dc=hex,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
cn: alex
sn: Linux
userPassword: {SSHA} wfYgDRZdRyxTuwVg1bzpXjjgAWGNYeN3
loginShell: /bin/bash
uidNumber: 1000
gidNumber: 1000
homeDirectory: /home/alex

dn: cn=alex,ou=Group,dc=hex,dc=com
objectClass: posixGroup
cn: alex
gidNumber: 1000
memberUid: alex 

ldapadd -x -D cn=admin,dc=hex,dc=com -W -f ldapuser.ldif
```
# 修改用户密码
```
ldappasswd -H ldap://192.168.20.47 -x -D "cn=admin,dc=hex,dc=com" -W -S "uid=alex,ou=People,dc=hex,dc=com"

cat > passwd.ldif << EOF
dn: cn=barbara,ou=dell,dc=example,dc=org
changetype: modify
replace: userPassword
userPassword: liyang@008
EOF

ldapmodify -x -H ldap://localhost:389 -D "cn=barbara,ou=dell,dc=example,dc=org" -w liyang -f passwd.ldif
```
