
## 1. VMware 가상 환경 구축

### 1-1. 가상 네트워크 환경 설정

**호스트 OS에서 네트워크 정보 확인**

Windows CMD에서 다음 명령어 실행

```cmd
ipconfig /all
```

- 네트워크 어댑터: VMware Virtual Ethernet Adapter for VMnet8
- IPv4 주소: `192.168.111.1`

**VMware Workstation 네트워크 설정**

| 항목 | 설정값 |
|---|---|
| Network | VMnet8 (NAT) |
| Subnet IP | `192.168.111.0` |
| Subnet Mask | `255.255.255.0` |

### 1-2. 네트워크 구성 정보

| 구분 | IP 주소 | 역할 |
|---|---|---|
| Host OS | `192.168.111.1` | 호스트 컴퓨터 |
| Gateway | `192.168.111.2` | 게이트웨이 및 DNS 서버 |
| DHCP Server | `192.168.111.254` | 동적 IP 할당 |
| Server | `192.168.111.100` | Linux 서버 |
| Server(B) | `192.168.111.200` | Linux 보조 서버 |
| Client | 동적 IP | Linux 클라이언트 |
| WinClient | 동적 IP | Windows 클라이언트 |


## 1-3. CentOS Repository 설정

### 설정 목적
- CentOS 패키지 저장소(Repository) 지정
- BaseOS, AppStream 등 저장소별 패키지 다운로드 경로 설정
- DNF를 통한 패키지 설치 및 업데이트 환경 구성

```ini
[BaseOS]
name=CentOS-$releasever - Base
baseurl=https://archive.kernel.org/centos-vault/8.0.1905/BaseOS/x86_64/os/
        http://linuxsoft.cern.ch/centos-vault/8.0.1905/BaseOS/x86_64/os/
gpgcheck=0

[AppStream]
name=CentOS-$releasever - AppStream
baseurl=https://archive.kernel.org/centos-vault/8.0.1905/AppStream/x86_64/os/
        http://linuxsoft.cern.ch/centos-vault/8.0.1905/AppStream/x86_64/os/
gpgcheck=0

[extras]
name=CentOS-$releasever - Extras
baseurl=https://archive.kernel.org/centos-vault/8.0.1905/extras/x86_64/os/
        http://linuxsoft.cern.ch/centos-vault/8.0.1905/extras/x86_64/os/
gpgcheck=0

[centosplus]
name=CentOS-$releasever - Plus
baseurl=https://archive.kernel.org/centos-vault/8.0.1905/centosplus/x86_64/os/
        http://linuxsoft.cern.ch/centos-vault/8.0.1905/centosplus/x86_64/os/
gpgcheck=0

[PowerTools]
name=CentOS-$releasever - PowerTools
baseurl=https://archive.kernel.org/centos-vault/8.0.1905/PowerTools/x86_64/os/
        http://linuxsoft.cern.ch/centos-vault/8.0.1905/PowerTools/x86_64/os/
gpgcheck=0
```

---

## 1-4. 네트워크 설정

### ① 네트워크 설정 파일 수정

네트워크 인터페이스 설정 파일을 열어 IP 주소 및 네트워크 정보를 변경한다.

**설정 파일 경로**
```bash
cd /etc/sysconfig/network-scripts
gedit ifcfg-ens160
```

**네트워크 설정 내용**

```ini
BOOTPROTO="none"
IPADDR="192.168.111.100"
NETMASK="255.255.255.0"
GATEWAY="192.168.111.2"
DNS1="192.168.111.2"
```

| 항목 | 설정값 | 설명 |
|---|---|---|
| BOOTPROTO | `none` | DHCP를 사용하지 않고 고정 IP 설정 |
| IPADDR | `192.168.111.100` | 서버 IP 주소 |
| NETMASK | `255.255.255.0` | 서브넷 마스크 |
| GATEWAY | `192.168.111.2` | 기본 게이트웨이 |
| DNS1 | `192.168.111.2` | DNS 서버 주소 |

### ② 네트워크 설정 적용

변경한 네트워크 설정을 적용하기 위해 연결을 재시작한다.

```bash
nmcli connection down ens160
nmcli connection up ens160
```

### ③ SELinux 설정 변경

SELinux(Security-Enhanced Linux)는 리눅스 시스템의 접근 제어를 강화하는 보안 기능이다.

실습 환경에서 원활한 진행을 위해 SELinux를 비활성화한다.

**설정 파일 수정**

```bash
gedit /etc/sysconfig/selinux
```

**수정 내용**

```ini
SELINUX=disabled
```

> **참고:** SELinux를 비활성화하면 보안 수준이 낮아질 수 있으므로 학습용 환경에서만 적용하고, 실제 운영 환경에서는 신중하게 설정해야 한다.

### ④ 방화벽 설정 도구 설치

방화벽 설정 및 관리를 위한 GUI 도구인 `firewall-config`를 설치한다.

```bash
dnf -y install firewall-config
```

**설정 목적**
- 방화벽 설정 및 관리 도구 설치
- GUI 환경에서 방화벽 규칙 확인 및 설정

