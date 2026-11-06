## 1. DHCP 서버 구축
DHCP(Dynamic Host Configuration Protocol)는 네트워크에 연결된 컴퓨터에 IP 주소, 서브넷 마스크, 
게이트웨이, DNS 서버 등의 네트워크 설정을 자동으로 할당해 주는 프로토콜

이번 실습에서는 Server에 DHCP 서버를 구축하고, 
Server(B)와 Clinet를 DHCP 클라이언트로 설정하여 IP 주소를 자동으로 할당

### 1-1. VMware DHCP 설정 변경

VMware Workstation에서 다음 순서로 이동한다.
1. Edit → Virtual Network Editor
2. Change Settings 클릭
3. VMnet8(NAT)의 DHCP Settings 확인
4. 실습에서 VMware 자체 DHCP와 충돌하지 않도록 설정을 확인한다


### 1-2. Client 재부팅 및 IP 확인
```
reboot
```


```
ipconfig
```
DHCP 서버가 아직 없으므로 IP 주소를 정상적으로 할당받지 못하는걸 확인한다


### 1-3. Server(B)의 기존 네트워크 연결 확인
Server(B)는 기존에 192.168.111.200 고정 IP를 사용하고 있으므로 
네트워크가 정상적으로 작동하는걸 확인한다.

```
root

ping -c 3 www.google.com

```





### 1-5. 1-4. Server(B)를 DHCP 클라이언트로 변경

```
vi etc/sysconfig/network-scripts/ifcfg-ens160
```

```
BOOTPROTO=dhcp
```
로 수정

### 1-5. Server(B) 네트워크 연결 재시작
```
nmcli connection down ens160
nmcli connection up ens160
```

DHCP 서버가 아직 준비되지 않았으므로 IP를 할당받지 못하는걸 확인한다.


### 1-6. Server에서 DHCP 서버 설치
```
dnf -y install dhcp-server
```

### 1-7. DHCP 서버 설정
```
gedit /etc/dhcp/dhcp.conf
```
다음 내용을 작성한다.
```
ddns-update-style interim;

subnet 192.168.111.0 netmask 255.255.255.0 {
    option routers 192.168.111.2;
    option subnet-mask 255.255.255.0;
    range 192.168.111.55 192.168.111.99;
    option domain-name-servers 8.8.8.8;
    default-lease-time 10000;
    max-lease-time 50000;
}
```

| 설정 | 의미 |
|---|---|
| `subnet` | DHCP 서버가 관리할 네트워크 대역 |
| `option routers` | 기본 게이트웨이 |
| `option subnet-mask` | 서브넷 마스크 |
| `range` | 클라이언트에 할당할 IP 범위 |
| `option domain-name-servers` | DNS 서버 주소 |
| `default-lease-time` | 기본 IP 임대 시간(초) |
| `max-lease-time` | 최대 IP 임대 시간(초) |

### 1-8. DHCP 임대 정보 확인
```
ls -l  /var/lib/dhcpd/dhcpd.leases
```
dhcpd.leases: DHCP 서버가 클라이언트에 할당한 IP 임대 정보를 기록하는 파일

### 1-9. DHCP 서버 시작 및 자동 실행 설정
```
systemctl restart dhcpd
systemctl enable dhcpd
```

### 1-10. Client에서 IP 주소 할당 확인
```
su -c 'systemctl restart NetworkManger'

ifconfig
```
DHCP 서버에서 설정한 IP 범위인 192.168.111.55 ~ 192.168.111.99 중 하나를 할당받으걸 확인한다.




