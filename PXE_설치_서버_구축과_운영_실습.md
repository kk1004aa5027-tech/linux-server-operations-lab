## 1. PXE 설치 서버

PXE(Preboot Execution Environment)는 CD나 USB 없이 
네트워크를 통해 운영체제를 설치할 수 있도록 해주는 기술이다.

### 1-1. Server - 필요한 패키지 설치  
```
dnf -y install syslinux dhcp-server tftp-server vsftpd
```

### 1-2. Server - 방화벽 비활성화
```
systemctl stop firewalld
systemctl disable firewalld
```

### 1-3. Server - DHCP 서버 설정 
```
gedit /etc/dhcp/dhcpd.conf
```
다음 내용을 추가한다.
```
subnet 192.168.111.0 netmask 255.255.255.0 {
    option routers 192.168.111.2;
    option subnet-mask 255.255.255.0;
    range dynamic-bootp 192.168.111.120 192.168.111.199;
    option domain-name-servers 192.168.111.2;
    allow booting;
    allow bootp;
    next-server 192.168.111.100;
    filename "pxelinux.0";
}
```

### 1-4. Server - FTP 서버 설정  
```
vi /etc/vfstpd/vsftpd.conf
```
다음 항목을 수정한다.
```
annoymouns_eanble = YES
```

### 1-5. Server - CentOS 설치 이미지 마운트
CD-ROM을 마운트 해제한다.
```
umount /dev/cdrom
```
FTP 공유 디렉터리에 CD-ROM을 마운트한다.
```
mount /dev/cdrom  /var/ftp/pub/
```
### 1-6. Server - PXE 부팅 파일 복사 
CentOS 설치 이미지에서 필요한 부팅 파일을 TFTP 서버 디렉터리로 복사한다.
```
cp /var/ftp/pub/images/pxeboot/vmlinuz  /var/lib/tftpboot/
cp /var/ftp/pub/images/pxeboot/initrd.img  /var/lib/tftpboot/
cp /var/ftp/pub/isolinux/ldlinux.c32  /var/lib/tftpboot/
cp /usr/share/syslinux/pxelinux.0    /var/lib/tftpboot/
```

### 1-7. Server - PXE 부팅 설정 파일 생성  
```
cd /var/lib/tftpboot/pxelinux.cfg/
```
설정 파일 생성
```
touch default
```
```
vi default
```
다음 내용을 입력한다.
```
DEFAULT CentOS8_Auto_Install

LABEL CentOS8_Auto_Install
    KERNEL vmlinuz
    APPEND initrd=initrd.img inst.repo=ftp://192.168.111.100/pub
```


### 1-8. Server - 서비스 실행 및 자동 시작 설정
```
systemctl restart dhcpd
systemctl restart vsftpd
systemctl restart tftp
systemctl enable dhcpd
systemctl enable vsftpd
systemctl enable tftp

```


### 1-9. VMware - DHCP 기능 비활성화
VMware Workstation에서 다음과 같이 설정한다.
1. Edit → Virtual Network Editor
2. VMnet8 선택
3. Change Settings 클릭
4. DHCP 기능 끄기

### 1-10. PXE 설치 테스트

새로운 가상머신을 생성하고 네트워크 부팅을 진행한다.

PXE 서버가 정상적으로 설정되어 있다면 클라이언트가 네트워크를 통해 
부팅 파일을 다운로드하고 CentOS 설치 화면으로 진입한다.




## 2. 킥 스타트 (Kickstart)
Kickstart는 운영체제 설치에 필요한 설정을 미리 작성해 두고, 설치 과정에서 자동으로 적용하는 기능이다.

PXE와 함께 사용하면 여러 대의 컴퓨터에 운영체제를 자동으로 설치할 수 있다.

### 2-1. Server - Kickstart 설정 파일 복사
기존 설치 과정에서 생성된 Kickstart 설정 파일을 FTP 공유 디렉터리로 복사한다.
```
cp /root/anaconda-ks.cfg  /var/ftp/centos.ks
cd /var/ftp
```

### 2-2. 2-2. Server - Kickstart 설정 파일 수정
```
gedit centos.ks
```
기존 설치 미디어 관련 설정을 FTP 설치 주소로 변경한다.
```
url --url="ftp://192.168.111.100/pub"
```

패키지 설치 항목을 수정한다.
```
%packges
@Server with GUI
@GNOME Applications
mc
%end
```





### 2-3. Server - PXE 설정 파일 수정
```
cd /var/lib/tftpboot/pxelinux.cfg
```

```
gedit default
```

기존 APPEND 항목에 Kickstart 설정 파일 주소를 추가한다.
```
DEFAULT CentOS8_Auto_Install

LABEL CentOS8_Auto_Install
    KERNEL vmlinuz
    APPEND initrd=initrd.img inst.repo=ftp://192.168.111.100/pub inst.ks=ftp://192.168.111.100/centos.ks
```

### 2-4. Kickstart 자동 설치 테스트
1. 새로운 가상머신 생성
2. 가상머신 이름을 testserver로 설정
3. 하드 디스크 용량을 80GB로 설정
4. 네트워크 부팅 진행
정상적으로 설정되었다면 PXE를 통해 부팅한 후 Kickstart 설정에 따라 CentOS가 자동으로 설치된다.

