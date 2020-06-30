---
title:  "DNS Hijacking의 테크닉"
excerpt: "'DNS Hijacking'은 DNS 트래픽에 대한 납치를 뜻한다. DNS는 단순히 도메인 명을 IPv4 형태 혹은 IPv6 형태의 주소로
변경해주는 단순한 서비스지만, 해당 서비스가 마비된다면 인터넷이 마비되어 버리는 재앙이 도레하기에 굉장히 치명적이라고 할 수 있다."
categories:
  - Network Security
tags:
  - Tech
  - '2019'
last_modified_at: 2019-01-29
---

'하이재킹'이란 보통 교통 수단에 대한 납치 행위를 뜻한다.
'DNS Hijacking'은 DNS 트래픽에 대한 납치를 뜻한다. DNS는 단순히 도메인 명을 IPv4 형태 혹은 IPv6 형태의 주소로
변경해주는 단순한 서비스지만, 해당 서비스가 마비된다면 인터넷이 마비되어 버리는 재앙이 도레하기에 굉장히 치명적이라고 할 수 있다.
(물론 접속하려는 도메인에 대한 IP를 알고 있다면... 문제가 없겠지만, 그걸 기억하고 다니는 사람들이 있을까..?
더군다나 요즘 클라우드플레어 같은 CDN들은 IP를 이용한 직접 접근을 보안을 위해 원천적으로 차단하는 경우도 있다.)
일례로 [1.25 인터넷 대란](https://namu.wiki/w/1.25%20%EC%9D%B8%ED%84%B0%EB%84%B7%20%EB%8C%80%EB%9E%80)을 들 수 있다.

DNS는 UDP라는 간편하지만 프로토콜을 사용하고 있다.
(물론 요즘에는 DNSSEC이나 TCP를 이용해서 DNS 서비스를 하거나 하는 등 여러가지 보안에 대한 방안이 나왔지만...)
상호 간 통신에서 신원을 모르는 자의 데이터를 받고 처리하는 건 굉장히 위험한 행위이다.
TCP의 핸드쉐이크 과정이나 헤더 구조, 오버헤드 등 이런 단점을 보완하기 위해 UDP가 출범하였지만
상호인증 없이 데이터를 주고 받는 것으로 인해 생기는 태생적인 취약점이라고 본다.
(대체로 DDoS, DRDoS 등 현재의 네트워크 공격은 대다수 UDP 기반으로 이루어지고 있다.)

![tcp_udp_headers]({{site.url}}/assets/images/2019/01/tcp_udp_headers.jpg)

*TCP와 UDP의 헤더 구조... 딱 봐도 UDP가 Simple 해보인다.*

여튼 각설하고 [최근 보았던 기사](http://www.etnews.com/20190125000204)에서 세계적 규모의 DNS 하이재킹이 성행하고 있다는
소식을 접하게 되었다. 보안인으로 근무를 하게 되면서 DNS 하이재킹에 대한 피해 소식은 [아시아나항공 DNS하이재킹](http://www.ilyoeconomy.com/news/articleView.html?idxno=29249)이 유일한 것이였다.
단순하게 DNS를 해킹해서 파밍을 시도한것인데.. 노력에 비해서는 굉장히 가성비(?)가 높은 공격이지 않은가..?

고전적인 수법이지만 Fireeye의 기술 블로그에서 해당 내용에 대해 잘 정리해놓은 글이 있어서,
내 블로그에도 한번 정리를 해 놓으려고 한다.

### Step 1. DNS A Records 변조

![Figure1b]({{site.url}}/assets/images/2019/01/Figure1b.jpg)

*공격자가 DNS 관리자의 관리자 계정에 접근하여 A record 변경*

1. 공격자는 pxy1 을 경유하여 공격 준비(Proxy)
2. 공격자는 취약한 자격증명을 사용하여 DNS 공급자의 관리자 권한 획득
3. `mail.victim.com` 의 A Record는 현재 `192.168.100.100` 을 가리키고 있음 (정상)
4. 공격자는 해당 도메인의 A Record를 `10.20.30.40(OP1)`으로 지정 (DNS 하이재킹)
5. 공격자는 pxy1 에서 op1으로 로그인
- 프록시는 열려있는 모든 포트에서 수신 대기하고 `mail.victim.com`을 미러링하도록 구현
6. certbot은 `mail.victim.com`에 대한 Lets Encrypt 인증서를 발급하는데 사용 (※ Certbot : Let’s Encrypt 인증서를 자동으로 발급/갱신해주는 봇)
7. 이제 사용자는 `mail.victim.com` 도메인 접근 시, op1을 경유하게 됨(op1이 Proxy역할). op1을 경유하게 된 트래픽은 다시 실제 메일서버의 로드밸런서로 전달되고 서비스됨
- Let’s Encrypt 인증서 사용 시 브라우저가 아무런 오류가 없이 연결을 설정하여 사용자는 이상을 인지 하지 못하고 약간의 지연만 감지
8. 메일 서버로 가던 트래픽을 도청하여 사용자 이름, 암호 및 도메인 자격 증명을 수집 (중간자 공격)

### Step 2. DNS NS Records 변조

![Figure2]({{site.url}}/assets/images/2019/01/Figure2.png)

*공격자가 TLD 등록업체를 해킹하여 NS record 변경*

1. Attacker는 pxy1에 로그인
2. 이번에는 공격자가 취약한 등록 기관 혹은 ccTLD를 악용
3. 네임서버 레코드 `ns1.victim.com`은 `192.168.100.200`으로 설정되어 있음. (해당 네임서버는 `mail.victim.com(192.168.100.100)` A Record 소유)
공격자는 NS 레코드를 변경하고 이를 `ns1.baddomain.com(10.1.2.3)`으로 지정. 해당 네임서브는 `mail.victim.com`가 요청될 때 `10.20.30.40(op1)`으로 응답 (`www.victim.com`으로 요청할 경우는 원래 IP인 `192.168.100.100`으로 응답)
4. 공격자는 pxy1 에서 op1으로 로그인
- 프록시는 열려있는 모든 포트에서 수신 대기하고 `mail.victim.com`을 미러링하도록 구현
5. ‘certbot’은 `mail.victim.com`에 대한 Lets Encrypt 인증서를 발급하는데 사용 (※ Certbot : Let’s Encrypt 인증서를 자동으로 발급/갱신해주는 봇)
6. 이제 사용자는 `mail.victim.com` 도메인 접근 시, op1을 경유하게 됨(op1이 Proxy역할). op1을 경유하게 된 트래픽은 다시 실제 메일서버의 로드밸런서로 전달되고 서비스됨
- Let’s Encrypt 인증서 사용 시 브라우저가 아무런 오류가 없이 연결을 설정하여 사용자는 이상을 인지 하지 못하고 약간의 지연만 감지
7. 메일 서버로 가던 트래픽을 도청하여 사용자 이름, 암호 및 도메인 자격 증명을 수집 (중간자 공격)

### Step 3. DNS Redirector

![Figure3]({{site.url}}/assets/images/2019/01/Figure3.png)

*DNS 요청을 모니터링하다 타깃 도메인에 접근 시 공격자가 관리하는 IP로 변조하여 회신*

1. `mail.victim.com`에 대한 DNS 요청이 op2로 전송 (이전에 변경된 A Record나 NS Record 기반)
2~4. 만약 도메인이 `.victim.com` zone에 속하면 op2는 공격자가 제어하는 ip 주소로 응답하고 사용자는 공격자가 인프라로 리다이렉션 됨
5~7. 만약 도메인이 `.victim.com` zone에 속하지 않는다면 (ex. Google.com) op2는 정상적인 dns로 ip주소를 가져오기 위해 dns 요청을 하고 정상dns 주소를 사용자에게 Return

### 조치방안
- DNS record 정상여부 검증
  * A record와 NS record 변경사항 검증
- DNS 관리자 계정 패스워드 변경
- DNS 관리자 계정에 다계층 인증 사용
- 인증서 투명성 로그 모니터링
  * TLS/SSL 인증서 부정 사용 여부 확인

### Reference
- [Global DNS Hijacking Campaign: DNS Record Manipulation at Scale](https://www.fireeye.com/blog/threat-research/2019/01/global-dns-hijacking-campaign-dns-record-manipulation-at-scale.html)
- [Mitigate DNS Infrastructure Tampering](https://cyber.dhs.gov/ed/19-01/)
