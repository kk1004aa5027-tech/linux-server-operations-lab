
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

### 1-3. CentOS 저장소(Repository) 설정

**실습 환경:** Server

패키지 설치 및 업데이트를 위해 CentOS 8.0.1905 저장소를 설정한다.

설정 파일 생성 및 편집:

```bash
gedit This.repo
```

다음 내용을 입력한다.

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

**설정 목적**
- CentOS 패키지 저장소 지정
- BaseOS, AppStream 등 저장소 경로 설정
- DNF를 통한 패키지 설치 환경 구성

[PowerTools]
name=CentOS-$releasever - PowerTools
baseurl=https://archive.kernel.org/centos-vault/8.0.1905/PowerTools/x86_64/os/
	  http://linuxsoft.cern.ch/centos-vault/8.0.1905/PowerTools/x86_64/os/
gpgcheck=0
​
