---
title:  "CentOS 7 Minimal 초기 셋팅"
excerpt: "업무를 하면서 서버를 다룰일은 거의 없지만, 보통 필요한 서버를 구축할때에는
CentOS 7 최소 버전으로 설치하는 후에 필요한 패키지를 설치하는 편이다."
categories:
  - Linux
tags:
  - Tech
  - CentOS
  - '2019'
last_modified_at: 2019-02-10
---

업무를 하면서 서버를 다룰일은 거의 없지만, 보통 필요한 서버를 구축할때에는
CentOS 7 최소 버전으로 설치하는 후에 필요한 패키지를 설치하는 편이다.

참고로 CentOS 6와 CentOS 7은 많은 [차이점](https://www.lesstif.com/pages/viewpage.action?pageId=22053120)을 가지고 있는데, `systemctl` 로 서비스를 제어하는 것과 `firewalld`
라는 방화벽 모듈이 추가된 등 기존의 CentOS 6/RHEL 6 에 익숙했던 사용자라면 초반 마이그레이션에 꽤 어려움을 느낄 수 있다.

항상 서버를 셋팅하면서 잊어먹게 되어 이곳에 따로 기록을 해두려고 한다.

### Step 1. CentOS 7 Minimal ISO 다운로드

![CentOS 7 Minimal ISO Download Page]({{ "/static/posts/20190210/figure1.png"| absolute_url }})

*CentOS 7 Minimal ISO Download Page*

당연한 이야기겠지만, 일단 Minimal ISO 이미지를 받아야한다. 별도로 Boot USB를 만드는 과정은 생략하겠다.
[CentOS-7-x86_64-Minimal-1810.iso](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso)
를 CentOS 7 공식 홈페이지에서 받는다.

### Step 2. 설치

![CentOS 7 Minimal 설치]({{ "/static/posts/20190210/figure2.png"| absolute_url }})
![CentOS 7 Minimal 설치]({{ "/static/posts/20190210/figure3.png"| absolute_url }})

*CentOS 7 설치 과정*

이미지를 이용하여 설치를 한다. 여기서는 VirtualBox를 이용하여 설치를 하였다.
실제 설치시에는 USB 부팅 등을 이용하여서 설치를 하면 된다. Timezone 이나 Root 계정에 대한 설정 등을 이 과정에서 하게 된다.
특별한 것은 없으므로 이 과정 또한 생략하도록 하겠다.

### Step 3. Network 설정

설치를 마치고 root 계정으로 로그인 (혹은 일반 계정으로 로그인 후, sudo 를 이용하여 작업하여도 무방하다. sudo 설정은 생략)

![CentOS 7 Login]({{ "/static/posts/20190210/figure4.png"| absolute_url }})

```bash
$ vi /etc/sysconfig/network-scripts/ifcfg-enpXXX
```

#### 고정 IP 설정
```bash
TYPE=Ethernet
BOOTPROTO=none      # BOOTPROTE를 static에서 none으로 변환
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=996b6722-4260-44bc-9311-7f9dedc333ea
DEVICE=eth0
ONBOOT=yes
IPADDR=10.20.30.41
NETMASK=255.255.255.0
GATEWAY=10.20.30.254
DNS1=168.126.63.1
DNS2=168.126.63.2
```
#### 유동 IP(DHCP) 설정
```bash
...
ONBOOT=yes      # ONBOOT를 no에서 yes로 변경 후 저장
...
```
```bash
$ systemctl restart network
$ systemctl restart NetworkManager
```

설정 후 `ping 8.8.8.8` 등을 통해 외부로 Ping 이 되는지 확인한다.

### Step 4. (선택) Virtualbox Guest Extension 설치를 위한 환경설정

실제 서버라면 건너뛰어도 되겠지만, Host O/S 안에서 가상머신으로 사용하고 있을 경우,
클립보드의 사용이나 해상도 조절을 위해 Guest Extension 을 설치해주는 것이 좋다.

Oracle VirtualBox 기준으로 설명을 남기도록 하겠다.


먼저 `epel-repository` 설치하여 필요한 패키지를 설치한다.
```bash
$ yum -y install epel-release
```

커널을 포함한 각 패키지의 최신 버전으로 업그래이드하고 `reboot` 하여 업그레이드된 커널을 사용해준다.
```bash
$ yum -y update
```
그 밖에 필요한 개발자 패키지 등을 설치한다.
```bash
$ yum install make gcc kernel-headers kernel-devel perl dkms bzip2
```

`KERN_DIR` 환경 변수를 설정하여 준다.
```bash
$ export KERN_DIR=/usr/src/kernels/$(uname -r)
```

### Step 5. (선택) Virtualbox Guest Extension 설치

크게 GUI를 통해 간편히 설치하는 방법이 있겠지만, CentOS 7 minimal은 CLI 기반인 만큼 CLI를 통하여 설치하는 방법으로
진행하려고 한다.

먼저 Host 머신에서 `VirtualBox Guest Editions CD Image` 를 Guest O/S 로 삽입한다.
그런 다음 Geust O/S 에서

```bash
$ mount -r /dev/cdrom /media
$ cd /media/
$ ./VboxLinuxAdditions.run
$ reboot
```
재부팅 훙 해상도가 Host O/S 에 맞게 설정이 될 것이다.

### Step 6. 네트워크 도구 설치

`ping`이나 `traceroute` 같은 네트워크 디버깅이 가능한 툴을 설치해준다.
보통 있는 줄 알았는데.. minimal 버전에는 포함이 되어있지 않은 것 같다.
```bash
$ yum install -y net-tools
```

### Step 7. firewalld 비활성화

RHEL 7 부터는 방화벽을 관리하는 데몬이 `firewalld` 로 변경되었다. 기존에 `iptables`에 비해 유저 친화적이라고 하지만 나는 기존에 쓰던 `iptables`를 더 선호하므로 해당 기능을 꺼 놓고자 한다.

```bash
$ systemctl stop firewalld
$ systemctl mask firewalld
$ systemctl disabled firewalld
```

### Step 8. iptables service 설치

```bash
$ yum install -y iptables-services
```

### Step 9. iptables 생성 및 설정

```bash
$ systemctl enable iptables
$ systemctl start iptables
$ iptables --flush
$ iptables-save > /etc/sysconfig/iptables
```

### Step 10. SELinux 비활성화

SELinux 는 Linux의 보안을 강화해 주는 보안 강화 커널이고 zero-day 공격 및 buffer overflow 등 어플리케이션 취약점으로 인한 해킹을 방지해 주는 핵심 구성요소이다.
특정 서비스가 SELinux 때문에 동작하지 않는다면 SELinux 를 끄기 보다는 해당 서비스가 SELinux 하에서 잘 동작하도록 설정을 수정하는걸 권장한다.

> SELinux Mode
> - Enforcing (기본값) – SELinux가 켜져있고 정책에 위반된 모든 작업을 차단함
> - Permissive – SELinux가 켜져있지만 정책에 위반된 사항에 대해 경고만 하도록 함 (audit 로그에 기록만 하는 상태)
> - Disable – SELinux가 완전히 꺼진 상태

여기서는 기본 `Enforcing` 모드로 되어 있던 것을 `Permissive` 로만 전환해주도록 하겠다.

```bash
$ setenforce 0
```

### Step 11. 기타 Util 설치

```bash
$ yum install -y telnet
$ yum install -y wget
```

이로써 기본적인 셋팅은 완료한 듯하다.
추가적으로 기록할 게 있으면 계속 이 포스팅에 추가하도록 하겠다.

### Reference
- [Install VirtualBox Guest Additions in CentOS, RHEL & Fedora](https://www.tecmint.com/install-virtualbox-guest-additions-in-centos-rhel-fedora/)
- [CentOS SELinux 설정 및 해제하기](https://www.lesstif.com/pages/viewpage.action?pageId=6979732)
