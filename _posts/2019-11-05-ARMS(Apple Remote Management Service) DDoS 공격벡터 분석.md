---
title:  "ARMS(Apple Remote Management Service) DDoS 공격벡터 분석"
excerpt: "최근 해외 다수 금융권을 대상으로 러시아 해킹그룹인 `Fency Bear`로 위장한 해킹그룹에 의한 비트코인을 요구하는 디도스 공격이 발생하고 있다는 기사를 보게 되었다."
toc: true
toc_sticky: true
categories:
  - Network Security
tags:
  - Tech
  - DDoS
  - '2019'
last_modified_at: 2019-11-05
---

최근 해외 다수 금융권을 대상으로 러시아 해킹그룹인 `Fency Bear`로 위장한 해킹그룹에 의한 비트코인을 요구하는 디도스 공격이 발생하고 있다는 기사를 보게 되었다.

![zdnet_news_image]({{site.url}}/assets/images/2019/11/Screen Shot 2019-11-05 at 3.29.14 PM.png)
*출처 : [A DDoS gang is extorting businesses posing as Russian government hackers
](https://www.zdnet.com/article/a-ddos-gang-is-extorting-businesses-posing-as-russian-government-hackers/){: target="_blank"}*

기사내용을 보면 DDoS 공격 팩터로 `SSDP`, `NTP`, `DNS`, `CLDAP` 등의 UDP 프로토콜과 `TCP SYN/ACK Flooding`, `ICMP Flooding(SMURF ATTACK)` 등의 프로토콜을 이용한다고 나와있는데 그중 `WSD(UDP/3702)`와 `ARMS(UDP/3283)` 이라는 생소한 프로토콜이 보여서 그중 ARMS를 분석했던 내역을 기록해두려고 한다.

### 구글링

일단 구글링으로 `ARMS`에 대해 검색해보면 정보가 별로 나오지 않는다. `UDP 3283` 으로 검색해 보면 [Apple 소프트웨어 제품에서 사용하는 TCP 및 UDP 포트](https://support.apple.com/ko-kr/HT202944){: target="_blank"} 라는 페이지가 나온다. 해당 페이지는 애플에서 사용하는 포트들을 정리해놓은 것 같다. UDP 3283 을 찾아보니 아래와 같이 나온다.

![apple_used_port_list]({{sitle.url}}/assets/images/2019/11/Screen Shot 2019-11-05 at 5.24.53 PM.png)
*Apple 사용 포트 리스트*

`Apple Remote Desktop`이라는 걸 보니 원격접속에 사용되는 프로토콜인 것 같다. `RFC 문서`는 따로 없는 것으로 보인다. 음... 관련된 키워드로 구글링을 더 하다가 올해 6월경에 나왔던 분석보고서를 찾을 수 있었다.

![netscout_a_call_to_arms]({{site.url}}/assets/images/2019/11/Screen Shot 2019-11-05 at 5.29.23 PM.png)
*출처 : [A Call to ARMS: Apple Remote Management Service UDP Reflection/Amplification DDoS Attacks](https://www.netscout.com/blog/asert/call-arms-apple-remote-management-service-udp){: target="_blank"}*

해당 블로깅의 내용을 요약해보면 아래와 같다.
- 공격자는 macOS에서 실행되는 Apple Remote Desktop(ARD) 기능을 활성화 한 시스템을 활용
- 최대 **40k** 대의 MACOS 노출 / 최대 **70 Gbps** 공격 탐지
- 대략 **35.5**배의 증폭률
- 시스템의 대부분은 대학 및 기업 네트워크에 있으며 잘못된 NAT 설정으로 인하여 포트가 외부에 노출 (아마도 `Memcached DRDoS`때의 경우 처럼 내부망에서만 활용되어질 목적으로 설계된 프로토콜이 잘못된 네트워크 설정으로 외부에 노출된 경우일 것으로 생각)

증폭률이 어마어마하다. 국내에는 얼마나 있는지 한번 [Shodan](http://shodan.io){:target="_blank"}에서 찾아보자.

### 쇼단검색

쇼단을 이용해서 취약한 단말들의 정보를 수집해 볼 수 있다.
API도 제공해주고 있어서 여러 기능을 이용할 수 있지만, 유료 멤버십에 가입을 해야 한다. 여기서는 간단히 국내의 취약한 단말대수 정도만 검색해 볼 것이기에 연동된 무료계정을 이용했다. *구글처럼 검색 필터문을 사용할 수 있으나 회원가입이 필요하다*

![shodan_search]({{site.url}}/assets/images/2019/11/Screen Shot 2019-11-05 at 5.44.32 PM.png)
*쇼단 검색결과*

국내(country:"KR")에서 UDP 3283 포트(port:3283)가 Open된 단말을 검색해보았다. **649** 대의 단말이 검색되는 것을 볼 수 있다. IP 정보도 모두 나온다. 유료 회원이 아니면 2페이지까지만 볼 수 있다.

### 패킷수집

RFC 문서가 있다면 어떠한 프로토콜 취약점을 이용하여 증폭공격을 하는지 알 수 있을텐데, 존재하지 않아서 국제구간에서 UDP 3283 포트를 목적지로 하는 패킷을 수집해 보았다.

![udp_3283_packet]({{site.url}}/assets/images/2019/11/udp_3283_1.png)
*UDP 3283을 목적지로 하는 Sample Packet*

주로 페이로드가 `|00 00|` 인 패킷과 `|00 14 00 01 03|` 인 패킷을 볼 수 있었다. 프로토콜의 구조와 문법을 모르기에 무슨 의미인지는 알 수가 없다.

### 실험

확실한 것은 직접 실험을 해보는 것이기에 간단하게 본인의 맥북을 대상으로 실험을 해보기로 했다.

![mac_sharing]({{site.url}}/assets/images/2019/11/Screen Shot 2019-10-31 at 7.58.29 PM.png)
*MACOS Sharing 설정*

구글링을 해보니 해당 프로토콜을 활성화하기 위해서는 Sharing 설정을 On 해주면 된다고 한다. 해당 설정을 키고 오픈된 포트를 보았다.

![mac_open_port](static/images/2019/11/Screen Shot 2019-10-31 at 10.09.05 PM.png)
*MACOS Open Port 확인*

3283 포트가 Listen 상태인 것을 볼 수 있다. 그렇다면 로컬로 위에 패킷에서 발견된 Payload를 직접 UDP로 발신하면 증폭률을 알 수 있을 것이다. 파이썬으로 간단한 소켓 프로그램을 짜 보았다.

```python
#!/usr/bin/env python
#! python asmr_ping.py [ip]
import socket
import sys
import binascii

# |00 14 00 01 03|
PACKETDATA = "\x00\x14\x00\x01\x03"
PORT = 3283

RECV_SIZE = 65535

def chkAmplifier(ip):
    try:
        # SOCK CREATE AND CONNECTION
        sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, 0)
        sock.connect((ip, PORT))
        sock.settimeout(3)

        # SEND PACKET
        sent = sock.send(PACKETDATA.encode())
        # RECV RESPONSE
        recv_data = sock.recv(RECV_SIZE)
    except:
        sock.close()
        return 0
    # CONNECTION CLOSE
    sock.close()

    print("[*] SENT LENGTH : %s" % sent)
    print("[!] RECV LENGTH : %s" % len(recv_data))
    print("[*] Amplification : %0.2f" % (len(recv_data) / sent))

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: arms_ping.py <IP>")
        sys.exit()

    chkAmplifier(sys.argv[1])
```

```python
#!/usr/bin/env python
#! python asmr_ping_2.py [ip]
import socket
import sys
import binascii

# |00 00|
PACKETDATA = bytearray([0xff, 0xff])
PORT = 3283

RECV_SIZE = 65535

def chkAmplifier(ip):
    try:
        # SOCK CREATE AND CONNECTION
        sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, 0)
        sock.connect((ip, PORT))
        sock.settimeout(3)

        # SEND PACKET
        sent = sock.send(PACKETDATA.encode())
        # RECV RESPONSE
        recv_data = sock.recv(RECV_SIZE)
    except:
        sock.close()
        return 0
    # CONNECTION CLOSE
    sock.close()

    print("[*] SENT LENGTH : %s" % sent)
    print("[!] RECV LENGTH : %s" % len(recv_data))
    print("[*] Amplification : %0.2f" % (len(recv_data) / sent))

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: arms_ping.py <IP>")
        sys.exit()

    chkAmplifier(sys.argv[1])
```

간단하게 목적지 IP를 입력하면 송/수신 패킷의 길이와 증폭률을 출력해주게 했다. 로컬 대상으로 실행한 결과는 다음과 같았다.

![arms_ping_test]({{site.url}}/assets/images/2019/11/Screen Shot 2019-10-30 at 5.23.24 PM.png)
*ARMS 증폭률 테스트*

`|00 00|`의 경우 2배의 증폭률이 나오는 것으로 보아 공격에 활용되기 보다는 공격에 이용할 반사체(?)를 찾는 Scan성 패킷인 것 같다. `|00 14 00 01 03|` 의 경우 증폭률이 70배가 넘는다. 소스IP를 공격지로 스푸핑해서 공격에 활용이 가능한 패턴으로 보인다.

### 방어 패턴 찾기

공격을 유발하는 패킷의 패턴을 찾았으니, 이번에는 해당 공격에 대해 어떻게 방어할지 고민을 해봐야한다. 당연한 소리겠지만 반사체로 악용되지 않기 위해서는 저런 UDP 기반의 사용하지 않는 프로토콜은 기본적으로 Off 해 놓고, 설사 사용하더라도 방화벽 등의 접근제어를 통해 외부에서 접근하지 못하게 해야한다. 외부에 Open되어 있는 단말의 경우 편의를 위해 NAT 설정이 되있다던지 하는 경우가 많다...

그렇다면 내 서버나 PC로 공격을 받는 상황에는 어떻게 해야할까? 방법은 여러가지다. 방화벽으로 해당 소스포트로 들어오는 패킷을 Drop해 버린다던지.. 해당 flow의 대역폭을 줄인다던지. 혹은 공격패킷의 공통적인 패턴을 찾아 해당 패턴을 기반으로 차단하는 방법이 있다. 개인적으로 나는 후자의 방법을 선호한다. IP/Port 기반 차단에 비해 정교하면서도, 오차단에 의한 리스크도 줄일 수 있기 때문이다.

이러한 공통점을 찾기 위해 취약한 단말에 `|00 14 00 01 03|`을 보냈을 때 응답패킷을 한번 보기로 했다.

![pattern_1]({{site.url}}/assets/images/2019/11/download.jpeg)
![pattern_2]({{site.url}}/assets/images/2019/11/download (1).jpeg)

위와 같이 특정 offset 에서 2개의 특정 패턴이 동일하게 출현하는 것을 볼 수 있었다. 일단은 급하게 공격이 들어오면 `소스 포트 + 오프셋 + 패턴` 조합으로 1차적인 방어는 가능할 것 같다. 물론 상세 프로토콜 구조를 보고, 해당 hex값들이 무엇을 의미하는 지 파악해야지 오차단에 의한 Risk를 줄일 수 있다.

#### 공격 탐지/방어를 위한 Snort 정책

```
alert udp any any -> any 3283 (content:"|0014000103|")

alert udp any any -> any 3283 (content:"|ffff|")

alert udp any 3283 -> any any (content:"|000101660031|"; offset:0; depth:6;)

alert udp any 3283 -> any any (content:"|000103ea0031|"; offset:0; depth:6;)
```
