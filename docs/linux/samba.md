# samba config
```
yum -y install samba

useradd liyang
(echo "liyang"; echo "liyang") | smbpasswd -s -a liyang
mkdir /kvm/share
chown -R liyang /kvm/share
chmod 775 /kvm/share

cat > /etc/samba/smb.conf << EOF
[global]
        workgroup = WORKGROUP
        server string = Samba Server Version %v
        security = user
        username map = /etc/samba/smbusers
[share]
        comment = smb
        path = /kvm/share
        valid users = +liyang
        write list = +liyang
EOF
cat > /etc/samba/smbusers << EOF
liyang = admin
EOF

systemctl enable smb
systemctl start smb
```

# list samba user
```
pdbedit -L
```


# delete samba user
```
smbpasswd -x liyang
```
```