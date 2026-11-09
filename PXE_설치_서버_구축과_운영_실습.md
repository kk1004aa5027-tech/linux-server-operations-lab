## 1. PXE 설치 서버

자동으로 설치되게 하는 설치 서버


### 1-1.  
```
dnf -y install syslinux dhcp-server tfrp-server vsftpd
```

### 1-1.  
```
systemctl stop firewalld
systemctl disable firewalld
```

### 1-1.  
```
gedit /etc/dhcp/dhcpd.conf
```
```

```

### 1-1.  
```
vi /etc/vfstpd/vsftpd.conf
```
```
annoymouns_eanble = YES
```
로 수정
### 1-1.  
```
umount /dev/cdrom
```
```
mount /dev/cdrom  /var/ftp/pub/
```
### 1-1.  
```
cp /var/ftp/pub/images/pxeboot/vmlinuz  /var/lib/tftpboot/
cp /var/ftp/pub/images/pxeboot/initrd.img  /var/lib/tftpboot/
cp /var/ftp/pub/isolinux/ldlinux.c32  /var/lib/tftpboot/
cp /usr/share/syslinux/pxelinux.0    /var/lib/tftpboot/
```

### 1-1.  
```
cd /var/lib/tftpboot/pxelinux.cfg/
```
```
touch default
```
### 1-1.  
```
vi default
```
```
DEFAULT  CentOS8_Auto_Install
LABEL  CentOS8_Auto_Install
kernel  vmlinuz
APPEND  initrd-initrd.img  repo=ftp://192.168.111.100/pub
```


### 1-1.  
```
systemctl restart dhcpd
systemctl restart vsftpd
systemctl restart tftp
systemctl enable dhcpd
systemctl enable vsftpd
systemctl enable tftp

```


### 1-1.  vmworkstaion
edit -> virtual network editor -_ nmnet8
-> change setting -> dhcp 기능 끄기


### 1-1.  가상머신 만들기

play하면 자동 부팅되는걸 확인가능




## 2. 킥 스타트

### 2-1.
```
cp /root/anaconda-ks.cfg  /var/ftp/centos.ks
cd /var/ftp
```

### 2-2.
```

```



### 2-2.
```
cd /var/lib/tftpboot/pxelinux.cfg
```



### 2-2.
```

```

