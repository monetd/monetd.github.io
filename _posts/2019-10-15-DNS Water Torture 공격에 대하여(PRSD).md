---
title:  "DNS Water Torture 공격에 대하여(PRSD)"
excerpt: "`DNS Water Torture` 통칭 DNS 물고문(?) 공격에 대해 포스팅을 해보려고 한다."
categories:
  - Network Security
tags:
  - Tech
  - DDoS
  - '2019'
last_modified_at: 2019-10-15
---

`DNS Water Torture` 통칭 DNS 물고문(?) 공격에 대해 포스팅을 해보려고 한다. 단순하면서도 상당히 매력적인(?) 공격이라고 생각한다. 실제로 관제업무를 수행하면서 많이 대응해보기도 했고... `DNS Water Toruture` 이라는 명칭도 있지만 `PRSD(Pseudo Random Subdomain) 공격`이라는 명칭이 더욱 직관적으로 들리기도 한다.

![zdnet_mirai_ddos]({{site.url}}/assets/images/2019/11/zdnet-mirai-ddos.png)
![amazone_s3_ddos]({{site.url}}/assets/images/2019/11/amazone-s3-ddos.png)
*출처(상) : [Mirai DNS Water Torture finance sector attack dominated Q1: Akamai
](https://www.zdnet.com/article/mirai-dns-water-torture-finance-sector-attack-dominated-q1-akamai/){: target="_blank"}*
*출처(하) : [Bezos DDoS'd: Amazon Web Services' DNS systems knackered by hours-long cyber-attack
](https://www.theregister.co.uk/2019/10/22/aws_dns_ddos/){:target="_blank"}*

최근에 해당 공격으로 `Amazon AWS S3` 의 서비스 도메인이 공격당한 적이 있다. 과거의 포스팅에서도 언급한적 있지만... UDP라는 프로토콜이 가지는 발신자를 검증하지 않는 특수성과 하나의 DNS라는 서비스에 있는 큰 의존성을 공격한다는 점에 공격에 대한 Flow 를 기록해 두려고 한다.

### 정상적인 DNS Flow

서술하기에 앞서 DNS의 기본 개념이나 동작방식을 알고 있다고 가정하고 서술을 하려고 한다. 기본적인 DNS 서비스의 동작원리를 모르거나, `cache DNS` 라던지... `authoritative DNS` 의 용어가 생소하다면 [도메인과 네임서버(오픈튜토리얼즈))
](https://opentutorials.org/module/288/2802){:target="_blank"} 를 먼저 참고해주기 바란다.

만약 우리가 네이버쇼핑을 이용하려고 한다고 가정해보자. 가장 먼저 브라우저를 띄우고 URL창에 `shopping.naver.com`을 입력할 것이다. 그러면 우리의 PC는 설정되어 있는 DNS 서버(여기서는 KT DNS인 168.126.63.1)에게 해당 도메인에 맵핑된 IP가 무엇인지 물어 볼 것이다. PC에게 질의를 받은 Cache DNS는 자신이 `shopping.naver.com`에 대한 IP를 가지고 있다면 A Record 값으로 물어본 클라이언트에게 답을 줄 것이다. 하지만 없다면 상위 레벨의 DNS로 iterative하게 질의를 하게 된다. 간단히 그림으로 그려보면 아래와 같다.

![dns_request_flow]({{site.url}}/assets/images/2019/11/dns-request-flow.png)

1. 내 PC 에서 `shopping.naver.com` (설정된)KT DNS로 질의
2. KT DNS는 root DNS에 질의
3. root DNS는 `.com` 서버에 대한 IP 값 응답
4. `.com` DNS에 질의
5. `.com` DNS는 `.naver.com` 서버에 대한 IP 값 응답
6. `.naver.com` DNS에 질의
7. `.naver.com` DNS는 `.shopping.naver.com` 서버에 대한 IP 값 응답
8. `.shopping.naver.com` DNS에 질의
9. `.shopping.naver.com` DNS는 `shopping.naver.com` 에 대한 IP 값 응답
10. KT DNS는 내 PC에게 `shopping.naver.com`에 대한 IP 값 응답
11. 내 PC는 네이버쇼핑의 웹서버로 접속

실제로도 이렇게 될까?
`dig`라는 툴로 이러한 과정을 디버깅해볼 수 있다. 실험해보자.

### DIG test
```
❯ dig @168.126.63.1 shopping.naver.com +trace

; <<>> DiG 9.10.6 <<>> @168.126.63.1 shopping.naver.com +trace
; (1 server found)
;; global options: +cmd
.			499756	IN	NS	d.root-servers.net.
.			499756	IN	NS	h.root-servers.net.
.			499756	IN	NS	c.root-servers.net.
.			499756	IN	NS	e.root-servers.net.
.			499756	IN	NS	b.root-servers.net.
.			499756	IN	NS	l.root-servers.net.
.			499756	IN	NS	f.root-servers.net.
.			499756	IN	NS	a.root-servers.net.
.			499756	IN	NS	m.root-servers.net.
.			499756	IN	NS	j.root-servers.net.
.			499756	IN	NS	k.root-servers.net.
.			499756	IN	NS	i.root-servers.net.
.			499756	IN	NS	g.root-servers.net.
;; Received 811 bytes from 168.126.63.1#53(168.126.63.1) in 412 ms

com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	m.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	a.gtld-servers.net.
com.			86400	IN	DS	30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com.			86400	IN	RRSIG	DS 8 1 86400 20191126170000 20191113160000 22545 . LWPm4uhPYc0oAdzsyNO358tIcxOimMy/iMgMqLFpk6yv6GNyomSmG59m sa3pu5LAqjmiEn/nTM0bPWLUmJWj4knwaJJdAZ7/Gy8Y96JDaNGfFS+W sywq06R5ejig7dNOuz46lI/gz3RamgjwJ70sIyk+mBMEDJ8SUYcxFy9Y 2mu/r/3q1siSlQQnAJSZlAfqzw8H1Co6NGyNz4NtbCjSnu1ua3X3X6cQ GK7iSxnWX0CSE2ieFxjxl9XpkKuAm82DxAfUB7MiJwIsp+t6G+Qb+uiA gAh0Yc2EwcTia4RgNwBY/yyz1nkKDw8FXVku9NDdXVY5eRrB2SSW5kr8 +khqtA==
;; Received 1178 bytes from 2001:500:2::c#53(c.root-servers.net) in 587 ms

naver.com.		172800	IN	NS	ns2.naver.com.
naver.com.		172800	IN	NS	ns1.naver.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0Q1GIN43N1ARRC9OSM6QPQR81H5M9A  NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20191119054905 20191112043905 12163 com. DWeezLYg0WoPrctVhXYCQrY7V3R1Ue6M7Wik02NqCEI1wvkGFPY+C0IZ gQF3yb8KZdoymT4YbWIxoTRn7J13y0q1Bt1D406GiMtL5lW9t7mTFDx1 I2B6kZwlT8y+EScHJgNPhgMZRuClmKR3PNZv34q3eEicFHk27wXq7NkU vB7l6mWl0rthDhRfVJ7JiMz+qg7B7+5y2MBnNw4IBhkVFg==
6P7ERTLUIGM2RTE9MP0JB6J2THEI5ARD.com. 86400 IN NSEC3 1 1 0 - 6P7JUS8SAG32Q2TFACTRJU5B2NOMGOJ9  NS DS RRSIG
6P7ERTLUIGM2RTE9MP0JB6J2THEI5ARD.com. 86400 IN RRSIG NSEC3 8 2 86400 20191119064601 20191112053601 12163 com. OHo1Ji4KU1P0nIi274N5cysgAj/ks9iO30QYZ3P6flM6KXRfUFPOv2Oi HEnK+vs5F0yFAkwVzrAbrRGdbrHbCKt3IBRNf5RFjqE5P+af9FMNVMtb rsG/iB8/q+pYObj8cADMGo9ibQlhen7Tu3vMcB5YwJcawiFyJQfmqOSz sx1xAchbedBaeA3RL3vBH2P+7ufog0G2BqhMGnJIaBwEzQ==
;; Received 664 bytes from 192.35.51.30#53(f.gtld-servers.net) in 85 ms

shopping.naver.com.	300	IN	CNAME	shopping.naver.com.nheos.com.
;; Received 86 bytes from 64:ff9b::7dd1:f906#53(ns2.naver.com) in 37 ms
```

*`shopping.naver.com`에 대한 질의 결과 CNAME으로 `shopping.naver.com.nheos.com` 을 주고있다. 그렇다면 해당 도메인으로 다시 질의해 보자.*

```
❯ dig @168.126.63.1 shopping.naver.com.nheos.com +trace

; <<>> DiG 9.10.6 <<>> @168.126.63.1 shopping.naver.com.nheos.com +trace
; (1 server found)
;; global options: +cmd
.			330478	IN	NS	d.root-servers.net.
.			330478	IN	NS	f.root-servers.net.
.			330478	IN	NS	i.root-servers.net.
.			330478	IN	NS	j.root-servers.net.
.			330478	IN	NS	k.root-servers.net.
.			330478	IN	NS	b.root-servers.net.
.			330478	IN	NS	c.root-servers.net.
.			330478	IN	NS	e.root-servers.net.
.			330478	IN	NS	m.root-servers.net.
.			330478	IN	NS	h.root-servers.net.
.			330478	IN	NS	g.root-servers.net.
.			330478	IN	NS	a.root-servers.net.
.			330478	IN	NS	l.root-servers.net.
;; Received 811 bytes from 168.126.63.1#53(168.126.63.1) in 45 ms

com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	m.gtld-servers.net.
com.			172800	IN	NS	a.gtld-servers.net.
com.			86400	IN	DS	30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com.			86400	IN	RRSIG	DS 8 1 86400 20191126170000 20191113160000 22545 . LWPm4uhPYc0oAdzsyNO358tIcxOimMy/iMgMqLFpk6yv6GNyomSmG59m sa3pu5LAqjmiEn/nTM0bPWLUmJWj4knwaJJdAZ7/Gy8Y96JDaNGfFS+W sywq06R5ejig7dNOuz46lI/gz3RamgjwJ70sIyk+mBMEDJ8SUYcxFy9Y 2mu/r/3q1siSlQQnAJSZlAfqzw8H1Co6NGyNz4NtbCjSnu1ua3X3X6cQ GK7iSxnWX0CSE2ieFxjxl9XpkKuAm82DxAfUB7MiJwIsp+t6G+Qb+uiA gAh0Yc2EwcTia4RgNwBY/yyz1nkKDw8FXVku9NDdXVY5eRrB2SSW5kr8 +khqtA==
;; Received 1188 bytes from 2001:dc3::35#53(m.root-servers.net) in 198 ms

nheos.com.		172800	IN	NS	gns1.nheos.com.
nheos.com.		172800	IN	NS	gns2.nheos.com.
nheos.com.		172800	IN	NS	gns3.nheos.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0Q1GIN43N1ARRC9OSM6QPQR81H5M9A  NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20191119054905 20191112043905 12163 com. DWeezLYg0WoPrctVhXYCQrY7V3R1Ue6M7Wik02NqCEI1wvkGFPY+C0IZ gQF3yb8KZdoymT4YbWIxoTRn7J13y0q1Bt1D406GiMtL5lW9t7mTFDx1 I2B6kZwlT8y+EScHJgNPhgMZRuClmKR3PNZv34q3eEicFHk27wXq7NkU vB7l6mWl0rthDhRfVJ7JiMz+qg7B7+5y2MBnNw4IBhkVFg==
7LV5UJ6G0DP7HU3K6BQNO2ERCBJ8EBC9.com. 86400 IN NSEC3 1 1 0 - 7LV6OM5UPGE8DCCNAS6SV26OO9RLVOTF  NS DS RRSIG
7LV5UJ6G0DP7HU3K6BQNO2ERCBJ8EBC9.com. 86400 IN RRSIG NSEC3 8 2 86400 20191119053319 20191112042319 12163 com. bHsnk68R6nJ0g8X7Gr3OsfMJFn61OpBixQhlEDNU/nOZ/p4gKwK5kwzr zh+cgfKfcdqpTbmIXKBDoyOljGlhz0YGQKMA1cZRJTkxtphD8CDLCS0D j0G+lalA9b2OtJ3I54InspsZwcd20FMRDcbPAXcq08sq6YAWSWMrjjMS FDNPWXKjSR+SYWZEDlj7Qei0gZiNuW/pXN/DBd16ItLuyA==
;; Received 711 bytes from 192.35.51.30#53(f.gtld-servers.net) in 89 ms

shopping.naver.com.nheos.com. 180 IN	A	175.158.5.162
shopping.naver.com.nheos.com. 180 IN	A	117.52.137.140
;; Received 89 bytes from 64:ff9b::6706:ae56#53(gns1.nheos.com) in 79 ms
```

*결론적으로 `shopping.naver.com` 에 대한 IP 값으로 2개의 IP 값을 알려준다.*

### 공격 Flow

만약 `qwertyyasdgzx.shopping.naver.com` 같은 이상한 값(?)의 도메인을 질의하면 무슨일이 일어날까?

제일 먼저, 질의를 받은 Cache DNS는 Root DNS를 시작으로 모든 상위 DNS를 순회할 것이다. 자신에게 그런 값의 도메인은 없기 때문이다. 그리고 최종적인 질의를 받은 해당 도메인의 네임서버
 당연히 해당 도메인에 대한 IP가 없다는 NXDOMAIN 값을 줄 것이다.

![nxdomain_flow]({{site.url}}/assets/images/2019/11/nxdomain-flow.png)

하지만 이러한 없는 도메인을 한꺼번에 다량으로 퍼붙게 된다면, 해당 도메인의 네임서버의 응답률은 급격히 저하되게 된다. 일종의 `Dos(Denial of Service)`가 되는 것이다. 응답을 받지 못한 Cache DNS는 자신이 보낸 요청이 중간에 소실된 것으로 생각되고 지속적으로 해당 도메인의 네임서버에 요청을 보내게 된다. 더불어 불필요하게 `iterative request(상위 DNS서버에 계속 질의하는 행위)`를 반복하게 되어 Cache DNS 자체의 성능에도 영향을 미치게 된다.

공격자는 이 점을 이용하여 다량의 랜덤스트링 값의 서브도메인을 생성하여 해당 도메인을 공격하게 된다. 공격 대상이 되는 도메인의 네임서버가 마비됨은 물론 ISP의 Cache DNS까지 마비가 될 수 있는 공격이기에 매우 치명적이다.

![dns_watertorture_attack]({{site.url}}/assets/images/2019/11/dns-watertorture-attack.png)

![dns_water_torture_attack_packet]({{site.url}}/assets/images/2019/11/Screen Shot 2019-11-14 at 3.49.38 PM.png)
*실제 공격발생 시 패킷, `s3.amazonaws.com`에 대한 공격이다.*

물론 공격자는 취약한 IoT 장비 감염시켜 봇넷으로 만든 뒤 해당 봇넷에서 인터넷에 Open 되어 있는 Resolver를 통해 공격을 하게 된다.(주로 `DNS Relay` 기능이 있는 유/무선 공유기)

* *DNS Relay : 해당 유/무선 공유기가 마치 DNS Cache 서버처럼 동작하게 된다.**

![akamai_report]({{site.url}}/assets/images/2019/11/Screen Shot 2019-11-14 at 5.43.24 PM.png)
*Akamai 보고서 중 DNS Water Torture 공격 Flow*

실제로 2016년 Mirai 악성코드에 감염된 IoT 장비들이 Dyn사의 DNS를 공격에 트위터 등 여러 IT 기업들의 서비스들이 마비되었던 사례가 있다.

[DNS 업체 Dyn, DDoS 공격을 받다(KrCert)](https://www.krcert.or.kr/data/trendView.do?bulletin_writing_sequence=24667){:target="_blank"}

### 대응방안
각 서버 앞단의 보안장비에서 특정 패턴과 Offset으로 차단하는 방법도 있으나, 상황에 따라 오탐이 발생할 수도 있다. 가장 중요한 건 DNS 서버 자체의 설정을 조정하는 것이다. 아래는 `secure64` 에서 권고하는 공격을 방어할 수 있는 DNS 서버 설정이다.

```
- Check your timeout settings. Most resolvers allow you to specify the initial and subsequent timeout intervals. Make sure that these values are not too high (if they are, they will tie up resolver resources longer than necessary before a query fails).

- Increase the number of outstanding recursive queries if you have sufficient RAM on your server. This will give the resolver more resources to work with.

- Specify a non-zero TTL for the negative responses so that if a client requests the same non-responsive name more than once, the SERVFAIL answer is cached. By RFC, you should be able to specify up to a 5 minute TTL.
```

### Reference
- [Whitepaper: DNS Reflection, Amplification, & DNS Water-torture](https://www.akamai.com/it/it/multimedia/documents/technical-publication/dns-reflection-vs-dns-mirai-technical-publication.pdf){:target="_blank"}
- [Water Torture: A Slow Drip DNS DDoS Attack](https://secure64.com/2014/02/25/water-torture-slow-drip-dns-ddos-attack/){:target="_blank"}
